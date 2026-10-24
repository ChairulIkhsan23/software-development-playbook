---
id: DOC-BRC-001
title: Business Rules Catalog
project: "[Nama Proyek / Produk]"
version: 1.0.0
author: "[Nama System Analyst / PM]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Active / Deprecated
---

# Business Rules Catalog: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini mengkatalogkan seluruh logika bisnis, rumus matematis, batasan otorisasi, dan kebijakan operasional secara terpusat agar tidak tersebar tak terkontrol di dalam kode program. Konvensi ID: `BR-[MODULE]-[NNN]` (contoh: `BR-ORD-001`, `BR-PAY-002`). Bagian bertanda `[Wajib]` harus diisi.

---

## 1. Taksonomi Kategori Aturan Bisnis [Wajib]

> **Panduan Pengisian:** Setiap aturan harus diklasifikasikan ke dalam salah satu dari lima kategori baku berikut:

- **Calculation:** Rumus matematika, perhitungan pajak, potongan harga, bunga, atau komisi.
- **Constraint:** Batasan nilai data, integritas domain, limit transaksi minimum/maksimum.
- **Authorization:** Batasan hak akses berdasarkan role pengguna, batasan persetujuan (*approval threshold*).
- **Workflow:** Alur status state machine, urutan prasyarat tindakan (*precondition state*).
- **Temporal:** Aturan berbasis waktu, durasi batas kedaluwarsa (*timeout*), penjadwalan cron job.

---

## 2. Tabel Katalog Aturan Bisnis [Wajib]

> **Panduan Pengisian:** Isi tabel di bawah untuk seluruh aturan bisnis. Tingkat Penegakan (*Enforcement Level*): **Strict** (sistem menolak keras/block), **Overrideable** (bisa diabaikan dengan otorisasi manajer), **Warning** (hanya notifikasi peringatan).

| ID Aturan | Nama Aturan Bisnis | Kategori | Logika Kondisi (IF - THEN - ELSE) | Pengecualian (Exception) | Sumber Kebijakan (Source Policy) | Modul Terkait | Tingkat Penegakan |
|---|---|---|---|---|---|---|:---:|
| BR-ORD-001 | Batas Minimal Transaksi | Constraint | **IF** subtotal pesanan < Rp 10.000 **THEN** tolak transaksi dengan pesan error "Minimal transaksi Rp 10.000" **ELSE** izinkan proses checkout | Transaksi item produk digital promosi gratis (harga Rp 0) | SOP Operasional E-Commerce Bagian 4.1 | Checkout, Cart | Strict |
| BR-ORD-002 | Waktu Kedaluwarsa Invoice | Temporal | **IF** order berstatus `PENDING` dan `NOW() > created_at + 24 jam` **THEN** ubah status menjadi `EXPIRED` dan lepas hold stok **ELSE** pertahankan status | Metode pembayaran PayLater memiliki toleransi hingga 7 hari | Kebijakan Manajemen Risiko Finansial v2 | Payment, Order | Strict |
| BR-PRM-003 | Batas Maksimum Diskon Promo | Calculation | **IF** `persentase_diskon * subtotal > Rp 50.000` **THEN** terapkan nilai diskon tepat Rp 50.000 **ELSE** terapkan `persentase_diskon * subtotal` | Akun berstatus VIP Whitelist tidak memiliki cap nominal | Memo Direksi Promosi Akhir Tahun 2026 | Promo, Cart | Strict |
| BR-USR-004 | Otorisasi Persetujuan Refund | Authorization | **IF** nominal pengembalian dana > Rp 2.000.000 **THEN** wajib meminta approval dari minimal Finance Manager **ELSE** Customer Care Lead dapat menyetujui langsung | Pengembalian dana akibat kesalahan teknis duplikasi tagihan sistem | Matriks Otorisasi Keuangan Internal | Refund, Admin | Overrideable |
| BR-INV-005 | Peringatan Stok Kritis | Workflow | **IF** kuantitas stok produk <= 5 unit **THEN** kirim email alert ke tim logistik dan beri badge "Stok Terakhir" di UI **ELSE** tampilkan stok normal | Produk dengan kategori *Pre-Order* / *Made-to-Order* | SOP Manajemen Gudang 2026 | Inventory, Catalog | Warning |

---

## 3. Catatan Implementasi Teknis & Lokasi Enforce [Wajib]

> **Panduan Pengisian:** Sebutkan lapisan arsitektur tempat aturan bisnis ditegakkan (misal: Database Constraint, Domain Service Layer, atau Frontend UI Validation).

- **Prinsip Validasi Berlapis:** Aturan bertingkat `Strict` wajib ditegakkan di sisi **Backend Domain Service** atau **Database Constraint**, tidak boleh hanya mengandalkan validasi di sisi Frontend client.
- **Audit Logging:** Setiap terjadinya `Exception` atau `Override` terhadap aturan bisnis wajib dicatat dalam tabel audit log sistem lengkap dengan User ID, Timestamp, dan Alasan Override.

---

## 4. Riwayat Perubahan Aturan (Change History) [Wajib]

> **Panduan Pengisian:** Catat setiap penambahan, pembaruan rumus, atau penghapusan aturan bisnis.

| Versi | Tanggal | ID Aturan | Jenis Perubahan | Alasan Bisnis Perubahan | Disetujui Oleh |
|:---:|:---:|---|---|---|---|
| 1.0.0 | [YYYY-MM-DD] | BR-ORD-001 s.d 005 | Initial release | Baseline aturan bisnis sistem baru | [Nama Product Lead] |
| 1.1.0 | [YYYY-MM-DD] | BR-PRM-003 | Update cap diskon | Kenaikan batasan promosi dari 30rb ke 50rb | [Nama Finance Lead] |

---

## 5. Lembar Kerja Perumusan Aturan Baru (Drafting Worksheet) [Opsional]
*(Seksi ini opsional: dapat dihapus jika tidak ada usulan aturan baru yang sedang dirumuskan)*

> **Panduan Pengisian:** Gunakan seksi ini untuk menguji dan memvalidasi aturan bisnis baru bersama stakeholder sebelum dimasukkan ke tabel katalog resmi.

- **Usulan ID Aturan:** `BR-[MOD]-[NNN]`
- **Masalah Bisnis yang Ingin Diselesaikan:** [Deskripsikan perilaku menyimpang atau potensi celah kerugian finansial yang ingin dicegah]
- **Logika Rancangan:**
  - **IF (Kondisi Prasyarat):** [Parameter input dan nilai ambang batas]
  - **THEN (Perilaku yang Diharuskan):** [Tindakan otomatis sistem]
  - **ELSE (Kondisi Default):** [Tindakan normal jika kondisi tidak terpenuhi]
- **Potensi Dampak Pengguna:** [Apakah aturan ini dapat menimbulkan friksi atau komplain pada pengalaman pengguna?]

---

## 6. Panduan Pengujian & Verifikasi Aturan (Testing & Verification) [Wajib]

> **Panduan Pengisian:** Pastikan setiap aturan bisnis memiliki cakupan pengujian otomatis (automated unit/integration test).

- **Unit Testing:** Setiap logika bersyarat (IF-THEN) wajib diuji dengan batas nilai minimum (*lower boundary*), batas nilai maksimum (*upper boundary*), dan nilai di luar batas (*off-by-one errors*).
- **Regresi Logika:** Perubahan pada satu aturan bisnis (misal: diskon promo) tidak boleh merusak perhitungan pada modul lain (misal: faktur pajak atau pencatatan jurnal akuntansi).
