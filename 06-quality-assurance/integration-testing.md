# Panduan Integration Testing (Integration Testing Guide)

Integration testing memverifikasi interaksi antara dua atau lebih modul, komponen, atau sistem independen. Tujuannya adalah memastikan bahwa bagian-bagian sistem yang bekerja dengan benar secara terisolasi tetap dapat berkolaborasi secara sempurna saat digabungkan.

---

## 1. Ruang Lingkup dan Batasan (Testing Boundaries)

Pengujian integrasi berfokus pada titik temu (*seams/boundaries*) antar komponen:

```
+-------------------------------------------------------------+
|                      Aplikasi Utama                         |
|                                                             |
|   +------------+        +---------------+                   |
|   | Controller | <----> | Domain Service|                   |
|   +------------+        +-------+-------+                   |
|                                 |                           |
+---------------------------------|---------------------------+
          |                       |                      |
          v                       v                      v
 [ Database Fisik ]    [ External HTTP API ]   [ Message Broker ]
 (PostgreSQL / Redis)  (Payment Gateway / SMS) (RabbitMQ / Kafka)
```

1. **Service <-> Database**: Menguji query SQL kompleks, transaksi, constraint database, dan relasi tabel.
2. **Service <-> External API**: Menguji parsing payload respons, penanganan timeout, dan status error dari pihak ketiga.
3. **Service <-> Message Broker**: Menguji publikasi event, serialisasi pesan JSON, dan konsumsi pesan oleh listener.

---

## 2. Pola Pengujian Database Nyata: Testcontainers

Hindari menggunakan *in-memory database* (seperti SQLite atau H2) untuk menguji aplikasi yang menggunakan PostgreSQL atau MySQL di lingkungan produksi. Perbedaan fitur seperti trigger, JSONB, atau lock mechanism akan memicu bug lolos ke produksi.

Gunakan pola **Testcontainers** untuk menjalankan instance database nyata di dalam Docker container sementara:

```typescript
// Contoh implementasi menggunakan Vitest dan @testcontainers/postgresql
import { PostgreSqlContainer } from '@testcontainers/postgresql';
import { Client } from 'pg';

describe('UserRepository Database Integration', () => {
  let container;
  let client: Client;

  beforeAll(async () => {
    // Menjalankan container database PostgreSQL sementara
    container = await new PostgreSqlContainer('postgres:16-alpine').start();
    client = new Client({ connectionString: container.getConnectionUri() });
    await client.connect();
    // Jalankan migrasi skema tabel
    await client.query(`
      CREATE TABLE users (
        id SERIAL PRIMARY KEY,
        email VARCHAR(255) UNIQUE NOT NULL
      );
    `);
  }, 30_000);

  afterAll(async () => {
    await client.end();
    await container.stop();
  });

  it('harus mencegah duplikasi email lewat DB constraint', async () => {
    await client.query("INSERT INTO users (email) VALUES ('dev@example.com')");
    
    // Verifikasi penanganan unique constraint violation
    await expect(
      client.query("INSERT INTO users (email) VALUES ('dev@example.com')")
    ).rejects.toThrow(/duplicate key value/);
  });
});
```

---

## 3. Isolasi Data Pengujian: Rollback vs Database Recreation

Terdapat dua strategi menjaga kebersihan data antar pengujian:

| Metode | Cara Kerja | Kelebihan | Kekurangan | Kapan Digunakan |
| :--- | :--- | :--- | :--- | :--- |
| **Transaction Rollback** | Buka DB transaction di `beforeEach`, lakukan operasi, dan panggil `ROLLBACK` di `afterEach`. | Sangat cepat (tanpa biaya migrasi ulang). | Tidak dapat menguji kode yang mengelola transaksinya sendiri. | Default pilihan untuk CRUD harian. |
| **Database Recreation / Truncate** | Menghapus isi seluruh tabel (`TRUNCATE TABLE ... CASCADE`) setiap selesai tes. | Menguji transaksi riil dan auto-increment secara bersih. | Sedikit lebih lambat dibanding transaction rollback. | Saat menguji multi-step transaction atau worker asynchronous. |

---

## 4. Simulasi Layanan Eksternal: WireMock & MSW

Ketika menguji interaksi dengan API pihak ketiga yang berbayar atau memicu efek riil (seperti payment gateway), gunakan mock server di level jaringan:

- **Mock Service Worker (MSW)**: Menangkap HTTP request di layer Node.js/Browser menggunakan interceptor tanpa mengubah URL target aplikasi.
- **WireMock**: Container mock server HTTP mandiri yang dapat membalas skenario khusus (delay latency, 500 error, corrupted payload).

```typescript
// Contoh simulasi penanganan API Payment Gateway dengan MSW
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  http.post('https://api.payment.com/v1/charges', () => {
    return HttpResponse.json({ status: 'DECLINED', reason: 'INSUFFICIENT_FUNDS' }, { status: 402 });
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

---

## 5. Dasar Contract Testing (Pact & OpenAPI)

Contract testing memastikan bahwa dua sistem (misal: Frontend dan Backend, atau dua Microservices) saling sepakat mengenai format pesan tanpa perlu saling terhubung langsung secara terus-menerus.

```
[ Consumer (Frontend/Client) ]                      [ Provider (Backend API) ]
           |                                                   |
           v                                                   v
 Menghasilkan Kontrak JSON (Pact File) ---------> Memverifikasi Kontrak di CI
 "Harus ada field: { id: int, balance: float }"    "Endpoint /account lolos verifikasi!"
```

- **Pact**: Framework consumer-driven contract testing. Consumer mendefinisikan ekspektasi format JSON; Provider memverifikasi bahwa endpoint mereka memenuhi ekspektasi tersebut pada CI pipeline.
- **OpenAPI Schema Validation**: Menguji apakah controller backend mengembalikan response body yang 100% mematuhi spesifikasi `openapi.yaml`. Menggunakan validator seperti `express-openapi-validator` atau `dredd`.

---

## 6. Pengujian Integrasi Antrean Pesan (Message Brokers)

Ketika menguji antrean asynchronous (RabbitMQ / Redis PubSub / Kafka):
1. Pastikan publisher berhasil melempar payload JSON yang valid ke exchange.
2. Pastikan subscriber/consumer berhasil menerima pesan, memproses transaksi, dan mengirim konfirmasi *acknowledgement* (`ack`).
3. Uji skenario *poison message* (payload rusak): pastikan pesan dialihkan ke Dead Letter Queue (DLQ) dan tidak memblokir antrean utama.

---

## 7. Optimasi Performa Integration Testing

Pengujian integrasi terkenal lebih lambat daripada unit test. Terapkan strategi berikut untuk menjaga kecepatan:
1. **Reuse Container**: Gunakan fitur reuse container selama sesi pengujian lokal agar container tidak dibuat ulang di setiap file tes.
2. **Parallel Execution dengan Dedicated Schema**: Jika menjalankan tes secara paralel, berikan skema atau nama database berbeda untuk masing-masing worker thread (misal: `test_db_worker_1`, `test_db_worker_2`).
3. **Database Connection Pooling**: Batasi ukuran pool koneksi pengujian agar tidak kehabisan socket port di runner CI.
4. **Penyortiran Test Suite**: Pisahkan perintah eksekusi CLI: `npm run test:unit` (harian) dan `npm run test:integration` (sebelum push atau di CI).

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Mengabaikan Constraint Database**: Melakukan mock pada repository layer sehingga bug seperti kegagalan `NOT NULL` atau `FOREIGN KEY` tidak pernah terdeteksi saat pengujian.
2. **Ketergantungan pada Urutan Eksekusi Data**: Tes kedua berhasil hanya karena memanfaatkan data sisa yang dibuat oleh tes pertama.
3. **Mengakses API Produksi Pihak Ketiga**: Mengirimkan SMS asli atau token kredit pembayaran riil saat menjalankan test suite otomatis di CI.
4. **Timeout Terlalu Ketat**: Menetapkan timeout default 2 detik pada operasi container yang membutuhkan waktu inisialisasi jaringan.
5. **Tidak Menutup Koneksi Resource**: Lupa memanggil `client.end()` atau `container.stop()` yang menyebabkan kebocoran memori di runner CI.

---

## 9. Dokumen Terkait

- [Test Strategy](./test-strategy.md) — Penentuan alokasi beban pengujian piramida kualitas.
- [Unit Testing Guide](./unit-testing.md) — Pengujian fungsional unit terisolasi.
- [API Testing Guide](./api-testing.md) — Spesifikasi pengujian endpoint web dan HTTP status.
- [Definition of Done](./definition-of-done.md) — Kriteria integrasi dalam alur rilis.
