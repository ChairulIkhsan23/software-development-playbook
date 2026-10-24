# Panduan End-to-End (E2E) Testing

End-to-End (E2E) testing menguji seluruh alur kerja aplikasi dari antarmuka pengguna (UI) hingga ke database dan integrasi backend secara menyeluruh, mensimulasikan interaksi nyata yang dialami oleh pengguna akhir.

---

## 1. Ruang Lingkup: Critical User Journeys

E2E testing membutuhkan sumber daya eksekusi yang tinggi dan membutuhkan pemeliharaan ekstra. Jangan mencoba menguji setiap kombinasi form atau variasi validasi input lewat browser.

Fokuskan E2E testing **hanya** pada alur bernilai bisnis tinggi (*Critical User Journeys*):
- Alur registrasi akun, verifikasi email, dan login pertama kali.
- Alur pencarian produk, memasukkan barang ke keranjang (*cart*), hingga transaksi checkout berhasil.
- Alur reset password akun yang terkunci.
- Alur pembayaran tagihan berkala.

Semua variasi input edge case lainnya cukup ditangani pada layer **Unit** dan **Integration Test**.

---

## 2. Perbandingan Framework E2E Modern

| Kriteria | Playwright (Sangat Direkomendasikan) | Cypress | Selenium WebDriver |
| :--- | :--- | :--- | :--- |
| **Arsitektur** | Mengontrol browser via CDP (Chrome DevTools Protocol) & WebSocket | Berjalan langsung di dalam loop runtime browser | Protokol HTTP WebDriver eksternal |
| **Dukungan Browser** | Chromium, WebKit (Safari), Firefox secara native | Chrome, Edge, Firefox, Electron (WebKit eksperimental) | Semua browser utama |
| **Multi-Tab & Frame** | Sangat baik (native context) | Terbatas dan kompleks | Didukung penuh |
| **Kecepatan & Paralelisme** | Sangat cepat, isolasi browser context terpisah | Cepat, tetapi isolasi browser lebih berat | Paling lambat karena overhead HTTP |
| **Rekomendasi Tim** | **Pilihan Utama** untuk web modern | Bagus untuk fokus frontend sederhana | Warisan masa lalu / *Legacy systems* |

---

## 3. Menghindari Tes yang Flaky (Unstable Tests)

*Flaky test* adalah tes yang kadang lolos dan kadang gagal pada kode program yang sama persis tanpa ada perubahan. Ikuti aturan emas berikut:

### A. Jangan Gunakan Hard Sleep
```typescript
// SANGAT BURUK: Menunggu secara statis
await page.waitForTimeout(5000); 

// BENAR: Menggunakan auto-waiting locator bawaan Playwright
await expect(page.getByRole('button', { name: /konfirmasi pembayaran/i })).toBeVisible();
```

### B. Menunggu Respons Jaringan Spesifik (Network Waits)
Jika tombol memicu panggilan API asynchronous, tunggu respons HTTP yang relevan sebelum memeriksa perubahan UI:
```typescript
// Menunggu payload respons backend tiba
const [response] = await Promise.all([
  page.waitForResponse(res => res.url().includes('/api/v1/checkout') && res.status() === 200),
  page.getByRole('button', { name: /bayar sekarang/i }).click(),
]);
```

### C. Hierarki Pemilihan Elemen (Selectors)
Pilihlah selektor berdasarkan ketahanan terhadap perubahan desain styling:
1. **User-Facing Roles (Pilihan Terbaik)**: `page.getByRole('button', { name: /simpan/i })`
2. **Text Content**: `page.getByText('Pesanan berhasil dibuat')`
3. **Data Test ID Khusus**: `page.getByTestId('order-submit-button')` (tambahkan atribut `data-testid="order-submit-button"` di HTML)
4. **HINDARI**: CSS path yang rapuh seperti `.container > div:nth-child(3) > button.btn-primary` atau XPath dinamis.

---

## 4. Pola Desain Page Object Model (POM)

Untuk menjaga tes tetap terawat seiring berkembangnya aplikasi, bungkus elemen dan interaksi UI ke dalam representasi class terstruktur:

```typescript
// models/LoginPage.ts
import { Page, Locator } from '@playwright/test';

export class LoginPage {
  readonly page: Page;
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly submitButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.emailInput = page.getByLabel('Alamat Email');
    this.passwordInput = page.getByLabel('Kata Sandi');
    this.submitButton = page.getByRole('button', { name: /masuk/i });
  }

  async goto() {
    await this.page.goto('/login');
  }

  async login(email: string, pass: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(pass);
    await this.submitButton.click();
  }
}
```

---

## 5. Manajemen Data Pengujian (Test Data Management)

Pengujian E2E membutuhkan data yang stabil dan terisolasi agar tidak saling tumpang tindih saat dijalankan berulang:

1. **API-Driven Seeding**: Alih-alih melakukan klik manual di browser untuk membuat akun baru atau membuat produk katalog sebelum menguji alur checkout, gunakan HTTP request langsung via API test hook untuk memasukkan data uji.
2. **Dynamic Prefixing**: Buat data unik menggunakan timestamp atau UUID (misal: `test-user-17192831@example.com`) untuk menghindari error duplicate entry.
3. **Teardown & Cleanup**: Hapus data yang dibuat setelah suite pengujian selesai, atau gunakan database sandbox yang di-reset berkala.

---

## 6. Menjalankan E2E di CI/CD Pipeline

Konfigurasikan pipeline CI agar efisien dan menyimpan bukti saat terjadi error:

```yaml
# .github/workflows/e2e.yml
name: E2E Tests
on: [pull_request]

jobs:
  playwright:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 7
```

- **Headless Execution**: Selalu jalankan browser tanpa window grafis di CI.
- **Simpan Artefak Kegagalan (Failure Artifacts)**: Konfigurasikan penyimpanan trace archive (`trace.zip`), tangkapan layar, dan video hanya saat pengujian berstatus *failed*.

---

## 7. Dasar Visual Regression Testing

Visual testing memverifikasi bahwa perubahan kode CSS atau Tailwind tidak merusak tata letak antarmuka secara tidak sengaja:

```typescript
test('halaman dashboard harus sesuai tampilan baseline desain', async ({ page }) => {
  await page.goto('/dashboard');
  // Menangkap snapshot dan membandingkannya dengan gambar baseline
  await expect(page).toHaveScreenshot('dashboard-desktop.png', {
    maxDiffPixelRatio: 0.05, // Toleransi perbedaan piksel 5% untuk font rendering
  });
});
```

Jika terjadi perubahan desain yang memang disengaja, perbarui baseline dengan perintah:
```bash
npx playwright test --update-snapshots
```

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Mencoba Menguji Semuanya via E2E**: Menulis ratusan skenario E2E yang memakan waktu eksekusi 2 jam di CI, membuat tim mengabaikan hasil testing.
2. **Ketergantungan State Akun Bersama**: Beberapa tes menggunakan satu akun admin yang sama secara bersamaan, sehingga perubahan password di tes A menggagalkan tes B.
3. **Mengabaikan Mode Mobile / Responsif**: Hanya menguji browser desktop dan melewatkan masalah layout pada viewport layar ponsel.
4. **Tidak Memeriksa Respons Jaringan**: Melakukan klik tombol submit berulang-ulang tanpa menunggu API backend selesai memproses permintaan pertama.
5. **Selektor Berbasis Class Styling**: Menggunakan selektor `.btn-blue-500` yang langsung rusak ketika desainer mengubah tema tombol menjadi hijau.

---

## 9. Dokumen Terkait

- [Test Strategy](./test-strategy.md) — Alokasi proporsi E2E pada piramida pengujian.
- [Integration Testing Guide](./integration-testing.md) — Pengujian integrasi subsistem tanpa browser.
- [Regression Testing Guide](./regression-testing.md) — Pengelolaan suite regresi otomatis berkala.
- [Bug Reporting Standard](./bug-reporting.md) — Format pelaporan kegagalan pengujian yang informatif.
