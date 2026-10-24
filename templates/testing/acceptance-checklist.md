---
title: "Template User Acceptance Testing (UAT) Checklist"
document_id: "UAT-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
feature_name: "[Nama Fitur / Modul Bisnis]"
sprint: "Sprint-[XX]"
tester_name: "[Nama Penguji Bisnis / Stakeholder]"
target_signoff_date: "[YYYY-MM-DD]"
---

# User Acceptance Testing (UAT) Checklist

> **Panduan Pengisian:** Gunakan dokumen ini untuk memfasilitasi pengujian validasi bisnis oleh Product Owner, stakeholder bisnis, atau perwakilan pengguna akhir. Skenario berfokus pada kecocokan alur kerja nyata (*business workflow*), bukan pengujian unit teknis.

---

## 1. Metadata Sesi UAT [Wajib]

> **Panduan Pengisian:** Catat ruang lingkup fitur yang divalidasi dan penanggung jawab persetujuan.

- **Nama Fitur / Modul Bisnis [Wajib]:** `[Nama Fitur, misal: Portal Pembayaran Multi-Vendor]`
- **Nomor Sprint / Rilis [Wajib]:** `[Sprint-24 / Release v1.3.0]`
- **Penguji Bisnis (Business Tester) [Wajib]:** `[Nama Pemilik Bisnis / Stakeholder / PIC Operasional]`
- **Fasilitator QA [Wajib]:** `[Nama QA Engineer]`
- **Target Tanggal Sign-off [Wajib]:** `[YYYY-MM-DD]`
- **Lingkungan UAT [Wajib]:** `https://uat.example.com`

---

## 2. Checklist Eksekusi Skenario Bisnis [Wajib]

> **Panduan Pengisian:** Masukkan skenario alur kerja dari sudut pandang pengguna akhir. Tandai hasil uji dan centang kotak persetujuan jika hasil telah memuaskan kebutuhan bisnis.

| ID Skenario | Deskripsi Skenario Bisnis | Hasil Ekspektasi Bisnis (Expected Outcome) | Hasil Uji (Pass / Fail / Blocked) | Sign-off | Catatan Masukan (Feedback) |
|---|---|---|---|:---:|---|
| `UAT-SCN-001` | Pendaftaran akun merchant baru hingga approval KYC | Merchant menerima email verifikasi dan status akun berubah aktif dalam 5 menit | Pass | [x] | Notifikasi SMS dan email masuk cepat |
| `UAT-SCN-002` | Merchant menambahkan produk baru dengan varian warna dan stok | Produk langsung muncul di etalase pembeli dengan varian akurat | Pass | [x] | Tampilan preview foto produk jelas |
| `UAT-SCN-003` | Pembeli melakukan checkout menggunakan split payment (Wallet + CC) | Transaksi sukses terdebet proporsional dan invoice terbit otomatis | Fail | [ ] | Muncul pesan error jika saldo wallet pas-pasan |
| `UAT-SCN-004` | Merchant mengajukan pencairan dana penjualan ke rekening bank | Mutasi penarikan tercatat dan notifikasi transfer terkirim ke finance | Pass | [x] | Alur persetujuan admin finance berjalan mulus |

---

## 3. Pencatatan Cacat Selama UAT (Defect Logging) [Kondisional]

> **Panduan Pengisian:** Dokumentasikan setiap bug, ketidaksesuaian fungsional, atau kendala yang ditemukan selama sesi pengujian penerimaan berlangsung.

| ID Defect | ID Skenario Terkait | Deskripsi Masalah yang Ditemukan | Tingkat Keparahan (Blocker / Major / Minor) | Tindak Lanjut Tim Dev | Status |
|---|---|---|---|---|---|
| `BUG-UAT-01` | `UAT-SCN-003` | Validasi saldo wallet gagal saat nilai saldo sama persis dengan potongan transaksi | Blocker | Patch hotfix di PR #204 | In Progress |
| `BUG-UAT-02` | `UAT-SCN-002` | Label mata uang di halaman invoice tertulis `IDR` bukan `Rp` | Minor | Ditunda ke rilis berikutnya | Backlog |

---

## 4. Blok Tanda Tangan Persetujuan Akhir (Final Sign-off Block) [Wajib]

> **Panduan Pengisian:** Seluruh pihak berwenang wajib menandatangani blok ini untuk mengizinkan fitur dirilis ke lingkungan produksi.

### Ringkasan Hasil Pengujian:
- Total Skenario Diuji: `[4]` | Lulus (Pass): `[3]` | Gagal (Fail): `[1]` | Tertahan (Blocked): `[0]`
- Status Rekomendasi Rilis: `[GO / NO-GO / CONDITIONAL GO]`

### Pernyataan Persetujuan (Sign-off Acceptance):
Dengan menandatangani dokumen ini, kami menyatakan bahwa fitur yang diuji telah memenuhi kriteria penerimaan bisnis yang disepakati, dengan rincian pengecualian cacat yang telah disetujui untuk ditangani pada iterasi berikutnya.

| Peran (Role) | Nama Penandatangan | Tanggal Tanda Tangan | Keputusan (Approve / Reject) |
|---|---|---|---|
| Product Owner / Manager | `[Nama Product Owner]` | `[YYYY-MM-DD]` | `[Approve]` |
| Business Stakeholder | `[Nama Stakeholder Bisnis]` | `[YYYY-MM-DD]` | `[Approve]` |
| Quality Assurance Lead | `[Nama QA Lead]` | `[YYYY-MM-DD]` | `[Approve]` |

---

*(Seksi feedback UX di bawah ini opsional: hapus jika tidak ada masukan non-fungsional)*
## 5. Masukan Kualitatif Pengalaman Pengguna (UX Feedback) [Opsional]
- `[Catatan kenyamanan pengguna: penempatan tombol, kemudahan navigasi, kejelasan istilah bisnis]`
