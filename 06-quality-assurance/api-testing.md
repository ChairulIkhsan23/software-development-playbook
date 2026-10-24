# Panduan Pengujian API (API Testing Guide)

API (Application Programming Interface) adalah penghubung utama antara antarmuka pengguna dan logika backend. Pengujian API berfokus pada keandalan, keamanan, performa, dan kebenaran kontrak data tanpa perlu memuat antarmuka pengguna grafis.

---

## 1. Tingkatan Pengujian API (Testing Levels)

Pengujian endpoint HTTP yang komprehensif harus memvalidasi empat tingkatan:

1. **Status Code HTTP**: Memastikan response code mencerminkan hasil eksekusi sesuai standar RFC (misal: `201 Created` untuk POST sukses, `404 Not Found` untuk resource nihil, `422 Unprocessable Content` untuk validasi bisnis gagal).
2. **Response Headers**: Memeriksa keberadaan header wajib seperti `Content-Type: application/json`, header keamanan (`X-Content-Type-Options: nosniff`), dan header pagination.
3. **Validasi Skema (Schema Validation)**: Memastikan struktur payload response mematuhi kontrak data (Zod atau JSON Schema) tanpa field hilang atau tipe data yang tertukar.
4. **Validasi Nilai Data (Data Validation)**: Memastikan konten bisnis di dalam response body akurat, data tersaring sesuai user, dan kalkulasi angka tepat.

---

## 2. Kategori Pengujian: Positif, Negatif, dan Kasus Batas

| Kategori | Skenario Uji | Ekspektasi Respons |
| :--- | :--- | :--- |
| **Positive Test** | Kirim payload lengkap dan valid. | `200 OK` / `201 Created`, data tersimpan. |
| **Negative Test (Bad Input)** | Hilangkan field wajib atau kirim format salah (misal: string pada field integer). | `400 Bad Request` / `422 Unprocessable Entity` dengan rincian pesan error ramah pengguna. |
| **Negative Test (Resource Nihil)** | Akses UUID resource yang tidak pernah ada di database. | `404 Not Found`. |
| **Edge Case** | Kirim string batas maksimum (misal: 255 karakter), whitespace saja, atau angka 0. | Sistem menangani secara elegan tanpa `500 Internal Server Error`. |
| **Security Injection Test** | Kirim karakter khusus `' OR '1'='1` atau tag `<script>` pada input teks. | Input disanitasi dan dicegah oleh parameterized query. |
| **Query Param Boundary** | Kirim query pagination `?limit=-1` atau `?page=9999999`. | Sistem membatasi nilai default secara aman tanpa crash. |

---

## 3. Pengujian Autentikasi dan Otorisasi (AuthN & AuthZ)

Uji setiap endpoint terproteksi dengan variasi skenario keamanan berikut:

```
[Permintaan Masuk]
       |
       +---> Token Hilang? --------------------> Balas 401 Unauthorized
       |
       +---> Token Kadaluwarsa / Rusak? -------> Balas 401 Unauthorized
       |
       +---> Token Valid, tetapi Role Salah? --> Balas 403 Forbidden
       |
       +---> Token Valid & Role Sesuai --------> Lanjutkan 200 OK
```

- **Missing Token**: Header `Authorization` tidak dikirim sama sekali.
- **Expired / Tampered Token**: JWT token kadaluwarsa atau signature telah diubah secara manual.
- **Wrong Role (RBAC)**: User biasa mencoba mengakses endpoint admin (misal: `DELETE /api/v1/users/123`). Harus menghasilkan `403 Forbidden`, bukan `500` atau `401`.

---

## 4. Pengujian Rate Limit dan Header Khusus

Pastikan API terlindungi dari serangan penyalahgunaan (*abuse*) dan mematuhi standar keamanan modern:
- **Rate Limit Triggering**: Kirim permintaan beruntun melebihi batas (misal: 100 request/menit). Verifikasi kembalian status `429 Too Many Requests`.
- **Standar Header RateLimit**: Periksa keberadaan header standar RFC: `RateLimit-Limit`, `RateLimit-Remaining`, dan `Retry-After`.
- **CORS (Cross-Origin Resource Sharing)**: Periksa respons pre-flight CORS (`OPTIONS`) untuk memastikan header `Access-Control-Allow-Origin` dan `Access-Control-Allow-Methods` terkonfigurasi dengan benar sesuai whitelist domain.
- **Security Headers**: Pastikan endpoint memuat header perlindungan seperti `Strict-Transport-Security` (HSTS) dan `X-Content-Type-Options: nosniff`.

### Pengujian Idempotency Key pada Operasi Kritis (POST / Charges)
Untuk endpoint transaksi pembayaran atau mutasi data sensitif, uji pengiriman ulang request dengan `Idempotency-Key` yang identik:
1. Permintaan pertama: Diproses normal dan mengembalikan status `201 Created`.
2. Permintaan kedua (dengan key sama): Tidak boleh mendebit dana dua kali; harus mengembalikan hasil respons yang sama persis seperti request pertama dengan cache header yang sesuai.

---

## 5. Otomasi Pengujian API dengan Supertest / Newman

Pengujian API otomatis dapat diintegrasikan langsung ke dalam pipeline CI/CD menggunakan framework berbasis kode seperti Supertest atau runner Newman:

### A. Pengujian Terprogram dengan Supertest & Zod
```typescript
import request from 'supertest';
import { z } from 'zod';
import app from '../src/app';

const UserProfileSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1),
  email: z.string().email(),
  role: z.enum(['ADMIN', 'CUSTOMER']),
});

describe('GET /api/v1/users/me', () => {
  it('harus menolak request tanpa bearer token dengan status 401', async () => {
    const res = await request(app).get('/api/v1/users/me');
    expect(res.status).toBe(401);
    expect(res.body.error).toBe('UNAUTHORIZED');
  });

  it('harus mengembalikan profil pengguna dan mematuhi skema data jika token valid', async () => {
    const validToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
    const res = await request(app)
      .get('/api/v1/users/me')
      .set('Authorization', `Bearer ${validToken}`);

    expect(res.status).toBe(200);
    const parsedData = UserProfileSchema.safeParse(res.body.data);
    expect(parsedData.success).toBe(true);
  });
});
```

### B. Otomasi Postman Collection Menggunakan Newman di CI
Bagi tim yang mendokumentasikan API via Postman, jalankan collection secara otomatis di CI:
```bash
# Menjalankan koleksi pengujian API Postman secara headless di pipeline CI
npx newman run ./postman/collections/user-api.json \
  --environment ./postman/environments/staging.json \
  --reporters cli,junit \
  --reporter-junit-export ./reports/api-results.xml
```

---

## 6. Pengantar Pengujian Beban & Performa (Load Testing)

Sebelum meluncurkan API ke publik, pastikan API mampu menahan lonjakan trafik tanpa mengalami degradasi waktu respons:

### Tipe Pengujian Performa:
- **Load Test**: Menguji performa sistem di bawah kapasitas beban normal harian yang diharapkan.
- **Stress Test**: Meningkatkan beban terus-menerus hingga menemukan titik di mana sistem mulai tumbang.
- **Spike Test**: Memberikan lonjakan trafik instan secara tiba-tiba (seperti event flash sale) untuk menguji autoscaling.

```javascript
// Contoh script k6 sederhana (load-test.js)
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  vus: 25, // 25 virtual users simultan
  duration: '30s',
  thresholds: {
    http_req_duration: ['p(95)<300'], // 95% request harus selesai di bawah 300ms
    http_req_failed: ['rate<0.01'],    // Kegagalan error harus di bawah 1%
  },
};

export default function () {
  const res = http.get('https://staging-api.example.com/v1/products');
  check(res, {
    'status is 200': (r) => r.status === 200,
  });
  sleep(1);
}
```

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Hanya Menguji Happy Path**: Mengabaikan validasi error handling saat database down atau format JSON request rusak.
2. **Status Code yang Tidak Konsisten**: Mengembalikan HTTP status `200 OK` dengan payload `{ status: "error", message: "User not found" }`. Hal ini merusak standar REST.
3. **Membocorkan Stack Trace**: Menampilkan baris error SQL atau jejak server internal pada response body saat terjadi error 500 di lingkungan production.
4. **Hardcoding Nilai ID**: Menguji endpoint `GET /users/1` yang mengasumsikan data dengan ID 1 selalu ada di database uji.
5. **Tidak Memeriksa Header Content-Type**: Menerima request dengan format text biasa tanpa memastikan header `Content-Type: application/json`.

---

## 8. Dokumen Terkait

- [Integration Testing Guide](./integration-testing.md) — Pengujian batas sistem dan mock API.
- [Test Case Design](./test-case-design.md) — Teknik partisi dan analisis batas input API.
- [Bug Reporting Standard](./bug-reporting.md) — Dokumentasi kegagalan request dan response payload.
- [Security & Privacy Design](../04-technical-design/security-and-privacy.md) — Standar keamanan endpoint dan token.
