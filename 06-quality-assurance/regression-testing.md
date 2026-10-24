# Panduan Regression Testing (Regression Testing Guide)

Regression testing adalah proses pengujian ulang terhadap fitur-fitur yang sudah ada sebelumnya guna memastikan bahwa penambahan kode baru, perbaikan bug (*bug fixes*), atau perubahan konfigurasi infrastruktur tidak merusak fungsionalitas yang telah berjalan stabil.

---

## 1. Mengapa Regression Testing Sangat Krusial?

Dalam pengembangan perangkat lunak, modul-modul saling terhubung secara erat. Mengubah satu baris kode pada modul pembayaran berisiko merusak sistem pengiriman email atau perhitungan poin loyalitas. 

Tujuan utama regression testing:
- **Mencegah Kebocoran Bug Lama**: Menghindari fenomena "memperbaiki satu bug, memunculkan dua bug baru".
- **Memberikan Keamanan Refactoring**: Memungkinkan developer merapikan kode atau memperbarui dependensi usang tanpa ketakutan merusak alur aplikasi.
- **Menjaga Kepercayaan Pengguna**: Pengguna sangat frustrasi ketika fitur yang kemarin berjalan normal tiba-tiba macet setelah pembaruan aplikasi.
- **Mendukung Kecepatan Rilis**: Dengan automated regression suite, tim dapat merilis fitur setiap hari tanpa perlu melakukan sesi manual testing berhari-hari.

---

## 2. Kapan Harus Menjalankan Regression Testing?

1. **Setiap Pull Request (Otomatis di CI)**: Menjalankan suite regresi cepat (unit & integration tests).
2. **Pra-Rilis (Pre-Release Staging)**: Eksekusi menyeluruh sebelum build dirilis ke lingkungan produksi.
3. **Pasca-Refactoring Besar**: Setelah perombakan arsitektur database, migrasi framework, atau penggantian ORM.
4. **Hotfix di Produksi**: Sebelum merilis perbaikan darurat (*patch*) untuk memastikan penanganan instan tidak menimbulkan efek samping lain.
5. **Pembaruan Dependensi Mayor**: Saat memperbarui versi Node.js, database driver, atau library inti sistem.

---

## 3. Tingkatan Suite Pengujian Regresi

Menjalankan seluruh skenario pengujian di setiap perubahan kode adalah hal yang tidak realistis karena memakan waktu dan biaya server yang besar. Bagi suite regresi menjadi tiga lapisan:

```
+-------------------------------------------------------------+
| 1. Smoke Test Suite (~5 Menit)                              |
|    - Hanya alur login, health check endpoint, load home     |
|    - Dijalankan: Setiap commit dan post-deployment staging  |
+-------------------------------------------------------------+
                              |
                              v
+-------------------------------------------------------------+
| 2. Critical Path Suite (~15 - 20 Menit)                     |
|    - Transaksi utama: checkout, registrasi, pembayaran      |
|    - Dijalankan: Setiap Pull Request ke branch 'main'       |
+-------------------------------------------------------------+
                              |
                              v
+-------------------------------------------------------------+
| 3. Full Regression Suite (1 - 3 Jam / Overnight)            |
|    - Seluruh variasi edge cases, visual test, performa      |
|    - Dijalankan: Terjadwal setiap malam (Nightly Build)     |
+-------------------------------------------------------------+
```

### Matriks Pengelolaan Suite Regresi
| Lapisan Suite | Waktu Target | Pemicu Eksekusi | Tindakan Jika Gagal |
| :--- | :---: | :--- | :--- |
| **Smoke Suite** | $\le 5$ menit | Push git commit / Post-deploy | Deployment dibatalkan instan (*auto-rollback*) |
| **Critical Path** | $\le 15$ menit | Pull Request merge | PR diblokir dari branch utama |
| **Full Regression** | 1 - 3 jam | Terjadwal nightly (01:00 AM) | Notifikasi tiket ke tim on-call keesokan pagi |

---

## 4. Otomasi Selektif di CI/CD Menggunakan Tagging

Kelola eksekusi pengujian menggunakan tag agar pipeline CI tetap gesit:

```typescript
// Contoh penandaan tag pada Playwright / Jest
test('proses checkout pelanggan VIP @critical @smoke', async ({ page }) => {
  // Pengujian alur utama checkout
});

test('perhitungan diskon kupon bertingkat @regression', async ({ page }) => {
  // Pengujian kasus batas promosi diskon
});
```

Di pipeline CI:
```bash
# Jalankan smoke test cepat pada PR commit
npx playwright test --grep "@smoke"

# Jalankan critical test sebelum merge ke main
npx playwright test --grep "@critical"

# Jalankan full regression suite pada nightly cron job
npx playwright test
```

---

## 5. Manajemen Flaky Test & Protokol Karantina (Quarantine Process)

*Flaky test* adalah musuh nomor satu otomatisasi pengujian. Jika tim mulai terbiasa mengabaikan kegagalan tes dengan berkata "oh, itu biasa flaky, klik rerun saja", integritas pengujian telah hancur (*Broken Windows Theory*).

### Alur Karantina Flaky Test:
```
  [ Flaky Terdeteksi ]
           |
           v
  [ Pindahkan ke Quarantine Suite ] ---> (Hapus dari Blocking CI)
           |
           v
  [ Buat Tiket Bug: P1-TechDebt ]
           |
           v
  [ SLA Perbaikan: 5 Hari Kerja ]
     /                        \
  Berhasil Diperbaiki      Gagal / Tidak Bernilai
     |                                |
  Kembalikan ke CI                 Hapus Permanen
```

### Format Tiket Karantina Pengujian:
```markdown
### [FLAKY] TC-CHECKOUT-089: Timeout pada tombol verifikasi OTP
- **File Pengujian**: `tests/e2e/checkout-otp.spec.ts`
- **Frekuensi Kegagalan**: 3 dari 10 kali run di CI Ubuntu Runner
- **Dugaan Penyebab**: Race condition antara animasi modal dialog dan input fokus.
- **Tindakan Karantina**: Diberikan anotasi `test.fixme()` atau `@quarantine`.
- **Target Penyelesaian**: Sprint 42 (Maksimal 5 hari kerja).
```

1. **Isolasi Segera**: Berikan tag `@quarantine` agar tes tidak memblokir antrean merge developer lain.
2. **Buat Tiket Perbaikan**: Tetapkan SLA (Service Level Agreement) maksimal 1 minggu untuk memperbaikinya.
3. **Hapus Bila Tidak Relevan**: Jika tes tersebut rapuh dan nilainya kecil, lebih baik hapus daripada mempertahankan kode pengujian yang menyesatkan.

---

## 6. Mengurangi Biaya Pemeliharaan Pengujian (Test Maintenance)

Seiring bertambahnya usia aplikasi, jumlah berkas pengujian akan membengkak. Terapkan strategi perampingan:
- **Pruning (Pangkas Tes Mati)**: Hapus tes untuk fitur yang sudah dideprecate atau dihapus dari sistem.
- **Gunakan Reusable Fixtures**: Hindari menulis ulang logika persiapan data yang sama di puluhan file tes. Gunakan fixture bersama.
- **Pindahkan ke Layer Bawah**: Jika menemukan alur E2E yang lambat dan hanya menguji kombinasi if-else logika, pindahkan pengujian tersebut ke level unit test.
- **Deteksi Tes Duplikat**: Lakukan review berkala untuk menghilangkan tes yang menguji alur yang persis sama dengan parameter serupa.

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Menjalankan Full Suite di Setiap Git Push**: Membuat developer menunggu 45 menit untuk setiap perubahan kecil, memicu developer mencari cara bypass pengujian.
2. **Tidak Mengupdate Data Uji Regresi**: Membiarkan skenario regresi gagal hanya karena tanggal kalender atau data uji sudah kadaluwarsa.
3. **Memelihara Tes Tanpa Assert Bermakna**: Tes yang selalu hijau (*green*) hanya karena tidak memiliki verifikasi data sesungguhnya.
4. **Mengabaikan Tes Manual Eksploratif**: Berasumsi otomatisasi 100% menggantikan intuisi manusia dalam menemukan anomali visual atau alur kerja ganjil.
5. **Menumpuk Tes Karantina**: Membiarkan puluhan flaky test tertahan di karantina berbulan-bulan tanpa pernah diperbaiki.

---

## 8. Dokumen Terkait

- [Test Strategy](./test-strategy.md) — Alokasi piramida pengujian dan manajemen risiko rilis.
- [End-to-End Testing Guide](./end-to-end-testing.md) — Otomasi pengujian alur antarmuka pengguna.
- [CI/CD Pipeline Guide](../07-delivery-operations/ci-cd.md) — Integrasi pengujian regresi otomatis di pipeline deployment.
- [Release Management Guide](../07-delivery-operations/release-management.md) — Verifikasi regresi sebelum cut-off rilis.
- [Definition of Done](./definition-of-done.md) — Kriteria lolos pengujian regresi sebelum kode di-merge.

