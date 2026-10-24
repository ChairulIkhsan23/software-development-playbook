---
title: "Template Bug Report"
document_id: "BUG-TMPL-001"
bug_id: "BUG-[NNN]"
version: "1.0.0"
reported_date: "[YYYY-MM-DD HH:mm WIB]"
reporter: "[Nama Reporter / QA]"
assignee: "[Nama Developer / Tech Lead]"
status: "Open"
---

# Bug Report: `[Module] Deskripsi singkat kegagalan`

> **Panduan Pengisian:** Gunakan template ini untuk melaporkan kegagalan sistem, perilaku janggal, atau galat runtime. Berikan langkah reproduksi yang deterministik agar pengembang dapat meniru bug dengan cepat.

---

## 1. Identifikasi Cacat & Klasifikasi [Wajib]

> **Panduan Pengisian:** Berikan ID unik, format judul sesuai konvensi, serta tentukan tingkat keparahan dan urgensi penanganan.

- **Bug ID [Wajib]:** `BUG-[NNN]` (Contoh: `BUG-104`)
- **Format Judul [Wajib]:** `[Module] Deskripsi singkat kegagalan`  
  *(Contoh: `[Payment] Tombol bayar tidak merespons saat menggunakan kupon diskon 100%`)*
- **Tingkat Keparahan (Severity) [Wajib]:**
  - [ ] `SEV-1 (Blocker)` : Layanan down / data corrupt / alur transaksi terhenti total.
  - [ ] `SEV-2 (Critical)` : Fungsi utama rusak parah, tidak ada alur alternatif mandiri.
  - [ ] `SEV-3 (Major)` : Fitur sekunder gagal, ada solusi sementara (workaround).
  - [ ] `SEV-4 (Minor)` : Masalah tampilan UI/UX minor, teks typo, alignment.
- **Tingkat Prioritas (Priority) [Wajib]:**
  - `[P1 - Segera / P2 - Sprint Ini / P3 - Sprint Depan / P4 - Low / Backlog]`

---

## 2. Rincian Lingkungan Pengujian (Environment) [Wajib]

> **Panduan Pengisian:** Catat secara spesifik spesifikasi mesin, perangkat lunak, dan versi aplikasi saat bug ditemukan.

- **Environment [Wajib]:** `[Staging / Production / UAT / Local]`
- **Sistem Operasi [Wajib]:** `[macOS Sonoma 14.4 / Windows 11 / Ubuntu 22.04 / Android 14 / iOS 17]`
- **Browser & Versi [Wajib]:** `[Google Chrome 124.0.x / Safari 17.4 / Firefox 125]`
- **Versi Aplikasi / Commit Hash [Wajib]:** `v[1.2.0]` / `git-commit-[abcdef1]`
- **Perangkat / Device [Kondisional]:** `[Desktop / iPhone 15 Pro / Samsung Galaxy S23]`

---

## 3. Prasyarat Pengujian (Preconditions) [Wajib]

> **Panduan Pengisian:** Cantumkan status awal sistem, role akun, dan data prasyarat sebelum langkah dimulai.

1. Pengguna masuk dengan akun role `[Member Premium]`.
2. Akun memiliki item di keranjang dengan total transaksi minimal `Rp 100.000`.
3. Kupon promo `[DISKON100]` aktif di database promosi.

---

## 4. Langkah-langkah Reproduksi (Steps to Reproduce) [Wajib]

> **Panduan Pengisian:** Tulis langkah bernomor urut dan jelas. Jangan berasumsi developer mengetahui alur klik Anda.

1. Buka halaman checkout pada rute `/checkout`.
2. Pada form promo, masukkan kode voucher `DISKON100` dan tekan tombol "Terapkan".
3. Perhatikan subtotal berubah menjadi `Rp 0`.
4. Klik tombol utama "Konfirmasi & Bayar Sekarang".
5. Amati reaksi antarmuka dan respons pada konsol browser.

---

## 5. Hasil yang Diharapkan vs Hasil Aktual [Wajib]

> **Panduan Pengisian:** Kontraskan secara langsung apa yang seharusnya terjadi menurut spesifikasi vs apa yang benar-benar terjadi.

- **Hasil yang Diharapkan (Expected Result) [Wajib]:**  
  Sistem berhasil memproses transaksi senilai Rp 0, mengarahkan pengguna ke halaman invoice sukses, dan status pesanan menjadi "Lunas".
- **Hasil Aktual (Actual Result) [Wajib]:**  
  Tombol bayar mengalami freezing (*infinite spinner*), tidak terjadi pengalihan halaman, dan API endpoint `POST /api/v1/orders` mengembalikan status `500 Internal Server Error`.

---

## 6. Bukti Pendukung (Evidence Attachment) [Wajib]

> **Panduan Pengisian:** Lampirkan tangkapan layar, cuplikan log konsol, dan payload jaringan yang gagal.

### Cuplikan Log Konsol Browser / Terminal [Wajib]:
```text
Uncaught (in promise) TypeError: Cannot read properties of null (reading 'transactionId')
    at PaymentHandler.submit (payment-bundle.js:142:19)
    at HTMLButtonElement.dispatch (vendor.js:89:12)
```

### Network Payload & Response [Wajib]:
- **Endpoint:** `POST /api/v1/orders/checkout`
- **Response Code:** `500 Internal Server Error`
- **Response Body:** `{"error": "InvalidPaymentAmountException", "message": "Amount must be greater than zero"}`

### Tangkapan Layar / Tautan Rekaman Layar [Wajib]:
- `[Screenshot-UI-Freeze.png]` / `[Tautan video Loom / Google Drive]`

---

*(Seksi analisa akar masalah di bawah ini opsional: diisi oleh tim teknis saat investigasi)*
## 7. Analisis Akar Masalah (Root Cause Analysis - RCA) [Opsional]
- **Akar Masalah (Root Cause):** Backend service payment memiliki validasi batas bawah `amount > 0` tanpa penanganan khusus untuk voucher diskon 100%.
- **File Terdampak:** `src/modules/payments/services/payment-validator.ts:45`
- **Rencana Solusi:** Tambahkan pengecualian transaksi nol rupiah jika voucher jenis komplementer valid digunakan.
