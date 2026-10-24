# Panduan Unit Testing (Unit Testing Guide)

Unit testing adalah proses pengujian pada unit terkecil dalam kode program secara terisolasi—biasanya berupa fungsi tunggal, method, atau class. Dokumen ini merangkum prinsip, pola, dan contoh konkret pengujian unit untuk developer pemula hingga tim kecil.

---

## 1. Prinsip Utama Pengujian Unit (F.I.R.S.T)

Sebuah unit test berkualitas harus mematuhi akronim **F.I.R.S.T**:
- **Fast**: Berjalan dalam hitungan milidetik. Suite ratusan tes harus selesai dalam beberapa detik di mesin lokal.
- **Independent**: Tidak bergantung pada tes lain. Tes dapat dieksekusi secara acak tanpa mempengaruhi hasil tes berikutnya.
- **Repeatable**: Memberikan hasil yang sama di mana pun dijalankan (laptop developer, CI server, atau staging).
- **Self-Validating**: Menghasilkan status Boolean tegas: *Pass* (lolos) atau *Fail* (gagal). Tidak memerlukan interpretasi manual terhadap log.
- **Thorough / Timely**: Menguji skenario sukses, skenario gagal, serta batas-batas kondisi ekstrem (*edge cases*).

---

## 2. Pola Arrange-Act-Assert (AAA)

Susun setiap file pengujian menggunakan pola **Arrange, Act, Assert** agar struktur pengujian mudah dipahami oleh pembaca lain:

1. **Arrange**: Siapkan input, konfigurasi dependensi, dan kondisi awal.
2. **Act**: Panggil fungsi atau jalankan perilaku yang ingin diuji.
3. **Assert**: Verifikasi bahwa nilai kembalian atau efek samping sesuai ekspektasi.

```typescript
// Contoh implementasi Jest / Vitest
import { calculateOrderDiscount } from './order-calculator';

describe('calculateOrderDiscount', () => {
  it('harus memberikan diskon 10% jika pesanan melebihi batas promo', () => {
    // 1. Arrange (Persiapan kondisi awal)
    const subtotal = 200_000;
    const isMember = true;

    // 2. Act (Jalankan pemanggilan fungsi yang diuji)
    const discount = calculateOrderDiscount(subtotal, isMember);

    // 3. Assert (Verifikasi hasil ekspektasi)
    expect(discount).toBe(20_000);
  });
});
```

---

## 3. Apa yang Harus Diuji vs Apa yang TIDAK Boleh Diuji

| Kategori | Wajib Diuji? | Alasan & Rekomendasi |
| :--- | :---: | :--- |
| **Domain & Business Logic** | **Ya** | Perhitungan diskon, validasi data, transformasi state bisnis. |
| **Edge Cases & Kondisi Batas** | **Ya** | Angka nol, array kosong, nilai negatif, string tanpa karakter. |
| **Third-Party Libraries** | **Tidak** | Jangan menguji apakah `lodash.debounce` atau `dayjs` bekerja. Percayakan pada author library. |
| **Framework Internals** | **Tidak** | Jangan uji apakah Express routing memanggil handler. Uji controller logic secara terpisah. |
| **Private Methods Langsung** | **Tidak** | Uji method *public* yang memanggil method private tersebut. Menguji private method membuat refactoring rapuh. |
| **Getter / Setter Trivial** | **Tidak** | Properti sederhana tanpa logika bisnis hanya menambah overhead tanpa nilai uji. |

---

## 4. Isolasi dan Determinisme Pengujian

Unit test harus sepenuhnya deterministik. Hindari ketergantungan pada faktor eksternal:

### A. Waktu dan Kalender (System Clock)
Waktu yang bergerak bebas akan membuat tes kedaluwarsa sewaktu-waktu. Bekukan waktu menggunakan timer tiruan:
```typescript
beforeEach(() => {
  jest.useFakeTimers();
  jest.setSystemTime(new Date('2026-05-15T09:00:00Z'));
});

afterEach(() => {
  jest.useRealTimers();
});
```

### B. Nilai Acak (Math.random) & UUID
Jangan membiarkan pengujian mengandalkan bilangan acak sejati. Injeksi dependency seed generator atau mock fungsi pengacak:
```typescript
jest.spyOn(Math, 'random').mockReturnValue(0.75);
```

---

## 5. Taksonomi Test Doubles (Pengganti Komponen Asli)

Sering kali istilah *mock* digunakan sembarangan. Gerard Meszaros membaginya menjadi lima jenis:

1. **Dummy**: Objek pengisi argumen yang tidak pernah benar-benar digunakan fungsinya (misal: objek User kosong hanya untuk memenuhi parameter wajib).
2. **Fake**: Implementasi yang memiliki cara kerja nyata tetapi disederhanakan, misalnya `InMemoryUserRepository` pengganti PostgreSQL.
3. **Stub**: Objek yang mengembalikan jawaban terprogram dan terprediksi tanpa memproses logika apa pun (misal: mengembalikan status `{ active: true }`).
4. **Spy**: Pembungkus fungsi asli yang merekam histori pemanggilan (berapa kali dipanggil, parameter apa yang dikirim).
5. **Mock**: Objek dengan ekspektasi perilaku yang telah diprogram sebelumnya. Tes akan gagal jika interaksi tidak terjadi sesuai ekspektasi.

---

## 6. Contoh Nyata: Mock dan Spy pada Service Layer

```typescript
// orderService.spec.ts
import { OrderService } from './order-service';
import { PaymentGateway } from './payment-gateway';
import { NotificationService } from './notification-service';

describe('OrderService.checkout', () => {
  it('harus memotong saldo dan mengirim email konfirmasi jika pembayaran sukses', async () => {
    // Arrange: Stub payment gateway agar selalu sukses
    const mockPaymentGateway: jest.Mocked<PaymentGateway> = {
      charge: jest.fn().mockResolvedValue({ success: true, transactionId: 'TX-123' }),
    } as any;

    // Spy pada notification service untuk memastikan email terkirim
    const notificationSpy: jest.Mocked<NotificationService> = {
      sendReceipt: jest.fn().mockResolvedValue(true),
    } as any;

    const service = new OrderService(mockPaymentGateway, notificationSpy);

    // Act
    const result = await service.checkout({ userId: 'USR-1', amount: 50_000 });

    // Assert
    expect(result.status).toBe('COMPLETED');
    expect(mockPaymentGateway.charge).toHaveBeenCalledTimes(1);
    expect(mockPaymentGateway.charge).toHaveBeenCalledWith('USR-1', 50_000);
    expect(notificationSpy.sendReceipt).toHaveBeenCalledWith('USR-1', 'TX-123');
  });
});
```

---

## 7. Parameterized / Data-Driven Testing

Ketika sebuah aturan bisnis memiliki banyak variasi input, gunakan `test.each` agar kode pengujian ringkas dan tidak terjadi duplikasi:

```typescript
describe('validateAgeRequirement', () => {
  // Tabel pengujian: [inputUsia, ekspektasiValiditas, labelDeskripsi]
  test.each([
    [17, false, 'di bawah umur minimum'],
    [18, true,  'tepat di batas umur minimum'],
    [25, true,  'usia dewasa valid'],
    [65, true,  'tepat di batas umur pensiun'],
    [66, false, 'melebihi batas umur pensiun'],
    [-1, false, 'usia negatif tidak valid'],
  ])('input usia %i menghasilkan status %s (%s)', (age, expected, description) => {
    const result = validateAgeRequirement(age);
    expect(result).toBe(expected);
  });
});
```

---

## 8. Panduan Pemilihan Matcher Jest/Vitest

| Kasus Pemeriksaan | Matcher yang Tepat | Catatan |
| :--- | :--- | :--- |
| Tipe primitif (string, number, boolean) | `expect(val).toBe(expected)` | Memeriksa kesamaan referensi / `===` |
| Objek / Array (isi nilai) | `expect(obj).toEqual(expected)` | Memeriksa nilai seluruh field secara rekursif |
| Objek dengan class prototype sama | `expect(obj).toStrictEqual(expected)` | Sangat ketat, memeriksa tipe class dan properti undefined |
| String mengandung substring/regex | `expect(str).toMatch(/pola/)` | Berguna untuk memvalidasi pesan error dinamis |
| Array mengandung elemen | `expect(arr).toContain(item)` | Memeriksa keberadaan satu elemen dalam array |

---

## 9. Target Code Coverage yang Realistis

- **Target Sehat**: **75% - 85% Line Coverage** pada berkas yang memuat domain bisnis dan logika kalkulasi.
- **Jebakan 100% Vanity Coverage**:
  - Mengejar 100% coverage memaksa developer menulis tes untuk baris trivial seperti konfigurasi atau logger boilerplate.
  - Test suite menjadi sangat lambat dan rapuh terhadap perubahan kecil arsitektur.
  - Lebih berharga memiliki 1 tes yang memeriksa kondisi batas kritis daripada 10 tes yang hanya menaikkan metrik persentase.

---

## 10. Kesalahan Umum (Common Mistakes)

1. **Memiliki Lebih dari Satu Alasan Kegagalan**: Satu test case sebaiknya hanya memverifikasi satu konsep atau perilaku spesifik.
2. **Ketergantungan Urutan Eksekusi**: Test B gagal jika Test A tidak dijalankan duluan. Selalu gunakan `beforeEach` untuk me-reset state.
3. **Over-Mocking**: Me-mock seluruh dependensi hingga kode yang diuji tidak lagi mencerminkan logika yang sebenarnya berjalan.
4. **Tidak Menguji Skenario Negatif**: Hanya menguji jalur lancar (*happy path*), mengabaikan input *null*, string kosong, atau error dari layanan luar.

---

## 11. Dokumen Terkait

- [Test Strategy](./test-strategy.md) — Arsitektur dan piramida pengujian perangkat lunak.
- [Integration Testing Guide](./integration-testing.md) — Pengujian batas sistem nyata dan database.
- [Test Case Design](./test-case-design.md) — Teknik partisi ekivalensi dan analisis nilai batas.
- [Definition of Done](./definition-of-done.md) — Kriteria kelayakan unit test sebelum merge.
