---
id: DOC-UCS-001
title: Use Case Specification
project: "[Nama Proyek / Produk]"
version: 1.0.0
author: "[Nama System Analyst / Technical Writer]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Review / Approved
---

# Use Case: [UC-MOD-001] [Judul Use Case, misal: Proses Checkout Pesanan]

> **Panduan Pengisian:**
> Gunakan template ini untuk mendokumentasikan interaksi langkah-demi-langkah antara aktor (pengguna/sistem eksternal) dan sistem yang dibangun. Konvensi ID: `UC-[MOD]-[NNN]`. Bagian bertanda `[Wajib]` harus diisi.

---

## 1. Metadata Use Case [Wajib]

> **Panduan Pengisian:** Definisikan identitas pemangku skenario, pemicu peristiwa, dan jaminan keamanan transaksi.

- **ID Use Case:** `UC-MOD-001`
- **Nama Use Case:** [Contoh: Melakukan Checkout dan Pembayaran Pesanan]
- **Aktor Utama (Primary Actor):** [Pelanggan Terotentikasi]
- **Aktor Sekunder (Secondary Actors):** [Payment Gateway Provider, Sistem Manajemen Inventori]
- **Pemicu (Trigger):** Pelanggan menekan tombol "Bayar Sekarang" di halaman keranjang belanja.
- **Prasyarat (Preconditions):**
  1. Pelanggan telah berhasil login ke akun.
  2. Keranjang belanja berisi minimal 1 item aktif yang berstok > 0.
- **Jaminan Minimal (Minimal Guarantee):** Jika proses transaksi gagal, saldo pelanggan tidak terpotong dan kuantitas stok reservasi dikembalikan utuh.
- **Jaminan Keberhasilan (Success Guarantee):** Tagihan pembayaran resmi dibuat, stok item terkunci, dan invoice dikirimkan ke email pelanggan.

---

## 2. Alur Sukses Utama (Main Success Scenario) [Wajib]

> **Panduan Pengisian:** Tuliskan urutan aksi aktor dan respons sistem langkah demi langkah untuk skenario normal (Happy Path).

1. Pengguna membuka halaman checkout dan meninjau ringkasan item belanja.
2. Sistem menampilkan rincian subtotal, estimasi ongkos kirim, dan opsi kanal pembayaran yang tersedia.
3. Pengguna memilih alamat tujuan pengiriman dan memilih metode pembayaran [misal: Transfer Virtual Account].
4. Pengguna menekan tombol konfirmasi "Buat Pesanan".
5. Sistem mengunci kuantitas stok item di basis data selama 15 menit.
6. Sistem memanggil API pihak ketiga payment gateway untuk menerbitkan nomor invoice dan nomor VA.
7. Sistem mengembalikan status pesanan `PENDING_PAYMENT` dan menampilkan nomor VA serta batas waktu pembayaran di layar pengguna.
8. Sistem mengirimkan salinan instruksi pembayaran ke alamat email pengguna.

---

## 3. Alur Alternatif (Alternative Flows) [Opsional]
*(Seksi ini opsional: dapat dihapus jika alur sistem sangat linier tanpa cabang pilihan)*

> **Panduan Pengisian:** Deskripsikan variasi alur yang tetap berujung pada penyelesaian use case secara sukses.

- **3a. Pengguna menggunakan kode voucher diskon promosi:**
  - 3a1. Pengguna memasukkan kode promo pada kolom voucher dan menekan "Terapkan".
  - 3a2. Sistem memvalidasi kode promo sesuai Business Rule `BR-PRM-001`.
  - 3a3. Sistem mengkalkulasi ulang total tagihan dengan potongan diskon dan memperbarui tampilan rincian harga.
  - 3a4. Alur kembali ke langkah 4 pada Alur Sukses Utama.

---

## 4. Alur Eksepsi / Penanganan Error (Exception Flows) [Wajib]

> **Panduan Pengisian:** Uraikan percabangan ketika terjadi kesalahan validasi, kegagalan jaringan, atau error sistem.

- **5a. Stok item habis saat proses penguncian (Race Condition):**
  - 5a1. Sistem mendeteksi stok item tidak mencukupi saat proses kueri lock database.
  - 5a2. Sistem membatalkan transaksi tanpa membuat tagihan.
  - 5a3. Sistem menampilkan dialog error: "Maaf, stok item [Nama Produk] baru saja habis terbeli pengguna lain".
  - 5a4. Use case berakhir dengan kegagalan transaksi aman.

- **6a. API Payment Gateway mengalami timeout (> 5 detik):**
  - 6a1. Sistem mencoba pengiriman ulang (retry) sebanyak 1 kali.
  - 6a2. Jika masih timeout, sistem membatalkan kunci reservasi stok.
  - 6a3. Sistem menampilkan pesan: "Layanan pembayaran sedang sibuk, silakan coba beberapa saat lagi".
  - 6a4. Use case berakhir dalam kondisi transaksi aman (*rollback*).

---

## 5. Kondisi Akhir (Postconditions) [Wajib]

> **Panduan Pengisian:** Catat kondisi final sistem setelah use case selesai, baik berhasil maupun gagal.

- **Kondisi Sukses:** Catatan pesanan berstatus `PENDING_PAYMENT` tersimpan di database, stok terkunci, log audit checkout tercatat.
- **Kondisi Gagal:** Tidak ada data tagihan tertunda, saldo pengguna utuh, stok barang kembali tersedia bebas.

---

## 6. Kebutuhan Khusus & Non-Fungsional (Special Requirements) [Opsional]
*(Seksi ini opsional: dapat dihapus jika telah dicakup penuh di dokumen PRD)*

> **Panduan Pengisian:** Sebutkan aspek performa, aksesibilitas, atau kepatuhan khusus pada use case ini.

- Waktu respon dari langkah 4 ke langkah 7 tidak boleh melebihi 2.5 detik pada koneksi internet seluler normal (4G).
- Seluruh data nomor rekening atau kartu kredit yang ditampilkan harus di-masking sesuai standar keamanan.
