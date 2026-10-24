---
title: "Template Test Case Specification"
document_id: "TC-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
author: "[Nama QA Engineer]"
module: "[Nama Modul]"
target_release: "[v1.0.0 / Sprint-XX]"
---

# Test Case Specification

> **Panduan Pengisian:** Buat satu berkas per skenario pengujian utama atau kelompokkan beberapa test case dalam modul yang sama. Gunakan format ID standar `TC-[MODUL]-[NNN]` untuk memudahkan penelusuran balik ke tiket kebutuhan.

---

## 1. Kartu Informasi Test Case (Test Case Card) [Wajib]

> **Panduan Pengisian:** Isi informasi metadata, tipe, tingkat prioritas, dan dependensi kebutuhan bisnis.

- **ID Test Case [Wajib]:** `TC-[MODUL]-[NNN]` (Contoh: `TC-AUTH-001`, `TC-PAY-042`)
- **Judul Test Case [Wajib]:** `[Tindakan Pengujian Singkat dan Jelas]`
- **Modul / Fitur [Wajib]:** `[Nama Modul / Komponen]`
- **Tipe Pengujian [Wajib]:** `[Functional / Negative / Boundary / Regression]`
- **Tingkat Prioritas [Wajib]:** `[P1 - Blocker / P2 - High / P3 - Medium / P4 - Low]`
- **Kebutuhan Terkait [Wajib]:** `[FR-NNN / US-NNN]`
- **Prasyarat Sistem (Preconditions) [Wajib]:**
  1. Pengguna memiliki akun aktif berstatus `verified`.
  2. Sesi login telah aktif di lingkungan `staging`.
  3. Keranjang belanja berisi minimal 1 barang fisik aktif.
- **Data Uji (Test Data) [Wajib]:**
  - Akun: `user-tester@example.com` / `Password123!`
  - Nilai Transaksi: `Rp 50.000`
  - Kupon: `DISKONHEMAT`

---

## 2. Tabel Eksekusi Langkah Pengujian [Wajib]

> **Panduan Pengisian:** Tuliskan urutan aksi pengujian secara deterministik agar siapapun penguji dapat mereproduksinya secara identik.

| No | Tindakan Penguji (Action) | Data Masukan (Test Input) | Hasil yang Diharapkan (Expected Result) | Hasil Aktual (Actual Result) | Status |
|---|---|---|---|---|---|
| 1 | Klik tombol "Proses Pembayaran" pada halaman keranjang | Klik tombol primer | Halaman checkout terbuka, rincian produk sesuai isi keranjang | Sesuai ekspektasi | Pass |
| 2 | Masukkan kode kupon promo ke input kupon | `DISKONHEMAT` | Pesan sukses muncul, diskon 10% teraplikasi pada subtotal | Sesuai ekspektasi | Pass |
| 3 | Pilih metode transfer bank dan tekan "Bayar Sekarang" | Bank BCA | Nomor rekening virtual (VA) muncul, status order "Menunggu Pembayaran" | Sesuai ekspektasi | Pass |
| 4 | [Uji Batas] Masukkan nomor VA kadaluarsa pada simulator | VA Kedaluwarsa | Sistem menolak pembayaran dengan pesan "VA telah kadaluarsa" | Sesuai ekspektasi | Pass |

---

## 3. Catatan Eksekusi & Tautan Cacat (Defect Link) [Kondisional]

> **Panduan Pengisian:** Bagian ini wajib diisi jika saat pengujian ditemukan status `Fail` atau hambatan eksekusi `Blocked`.

- **Tanggal Eksekusi [Wajib]:** `[YYYY-MM-DD HH:mm WIB]`
- **Nama Penguji (Tester) [Wajib]:** `[Nama Penguji]`
- **Hasil Akhir [Wajib]:** `[PASS / FAIL / BLOCKED]`
- **Tautan Cacat (Defect / Bug ID) [Kondisional]:** `[BUG-NNN / Tautan Jira / None]`
- **Catatan Teknis / Log Tambahan [Opsional]:**
  - `[Catatan respons server, waktu respons jaringan, atau keanehan perilaku visual]`

---

## 4. Kondisi Pasca Pengujian (Post-Conditions) [Wajib]

> **Panduan Pengisian:** Pastikan data uji dibersihkan atau dikembalikan ke status awal agar tidak mengotori sesi uji berikutnya.

- [ ] Keranjang belanja dibersihkan.
- [ ] Pesanan uji dibatalkan atau ditandai sebagai data sampah (*garbage data*).
- [ ] Kupon uji direset limit penggunaannya pada simulator promosi.

*(Seksi script otomatisasi di bawah ini opsional: hapus jika pengujian dilakukan manual)*
## 5. Tautan Otomatisasi (Automation Script) [Opsional]
- **Framework:** `Playwright`
- **File Test Spec:** `tests/e2e/checkout/tc-pay-042.spec.ts`
- **Command:** `npx playwright test tc-pay-042`
