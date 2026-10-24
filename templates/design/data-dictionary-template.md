---
title: "Data Dictionary - [Nama Modul / Database]"
module: "[Nama Modul, misal: Order & Billing]"
version: "1.0.0"
data_owner: "[Nama Pemilik Data / Engineering Lead]"
last_updated: "[YYYY-MM-DD]"
status: "[Draft | In Review | Approved]"
---

# Data Dictionary: [Nama Modul / Database]

> **Panduan Pengisian:**
> Dokumen ini mendefinisikan kamus data tingkat fisik dan logis untuk basis data relasional.
> Gandakan blok metadata tabel, definisi kolom, dan definisi index untuk setiap tabel baru.
> Pastikan klasifikasi PII dan aturan validasi diisi secara disiplin demi kepatuhan regulasi data.

---

## 1. Metadata Tabel: `[nama_tabel_database]` [Wajib]

> **Panduan Pengisian:**
> Isi informasi umum tabel untuk memahami konteks bisnis sebelum meninjau detail kolom teknis.

- **Nama Fisik Tabel [Wajib]:** `[nama_tabel_snake_case]` (contoh: `orders`)
- **Nama Logis [Wajib]:** [Nama Entitas Bisnis, contoh: Pesanan Pelanggan]
- **Modul / Domain [Wajib]:** [Nama Domain, contoh: Checkout & Sales]
- **Deskripsi Bisnis [Wajib]:** [Menyimpan rekam jejak transaksi pesanan pengguna, mencakup status pembayaran dan pengiriman]
- **Estimasi Volume Awal [Opsional]:** [± 50.000 baris / bulan]
- **Tingkat Pertumbuhan Data [Opsional]:** [± 15% per bulan]

## 2. Definisi Kolom (Column Definitions) [Wajib]

> **Panduan Pengisian:**
> Cantumkan setiap atribut kolom. Klasifikasi PII: `Non-PII`, `PII-General` (nama/email), `PII-Sensitive` (NIK/kata sandi).

| Nama Kolom | Tipe Data | Length / Precision | Nullable | Default | Key Type | FK Reference | Aturan Validasi | Klasifikasi PII | Deskripsi Bisnis |
|---|---|---|---|---|---|---|---|---|---|
| `id` | UUID | - | [Tidak] | `gen_random_uuid()` | PK | - | Format UUIDv4 valid | Non-PII | Primary Key pengenal unik transaksi |
| `user_id` | UUID | - | [Tidak] | - | FK | `users(id)` ON DELETE RESTRICT | User wajib berstatus aktif | PII-General | Pengenal relasi pemilik pesanan |
| `order_number` | VARCHAR | 32 | [Tidak] | - | UK | - | Pola: `ORD-YYYYMMDD-XXXX` | Non-PII | Nomor referensi ramah pengguna |
| `total_amount` | DECIMAL | 14, 2 | [Tidak] | `0.00` | - | - | Nilai harus `>= 0.00` | Non-PII | Total tagihan akhir dalam rupiah |
| `shipping_address` | TEXT | - | [Kondisional] | - | - | - | Wajib jika tipe barang fisik | PII-General | Alamat lengkap tujuan pengiriman |
| `status` | VARCHAR | 20 | [Tidak] | `'pending'` | - | - | Enum: pending, paid, shipped, done, cancelled | Non-PII | Status alur proses pesanan |
| `created_at` | TIMESTAMPTZ | - | [Tidak] | `NOW()` | - | - | Zona waktu UTC | Non-PII | Timestamp pencatatan awal |
| `updated_at` | TIMESTAMPTZ | - | [Tidak] | `NOW()` | - | - | Terbarui otomatis via trigger | Non-PII | Timestamp pembaruan data terakhir |
| `deleted_at` | TIMESTAMPTZ | - | [Ya] | `NULL` | - | - | Soft-delete timestamp | Non-PII | Penanda baris dihapus secara logis |

## 3. Definisi Index (Indexes Table) [Wajib]

> **Panduan Pengisian:**
> Definisikan seluruh index sekunder, unique index, dan compound index untuk mengoptimasi pola query utama.

| Nama Index | Kolom Terlibat | Tipe Index | Unique | Tujuan / Query Pattern |
|---|---|---|---|---|
| `pk_orders` | `(id)` | BTREE | [Ya] | Primary Key enforcement |
| `uk_orders_order_number` | `(order_number)` | BTREE | [Ya] | Pencarian cepat via nomor pesanan |
| `idx_orders_user_created` | `(user_id, created_at DESC)` | BTREE | [Tidak] | Riwayat pesanan per user terurut terbaru |
| `idx_orders_status` | `(status)` | BTREE | [Tidak] | Filter status pada dashboard admin operasional |

## 4. Aturan Integritas & Foreign Key Constraints [Kondisional] *(Boleh dihapus jika tabel berdiri sendiri)*

> **Panduan Pengisian:**
> Jelaskan relasi antar-tabel serta tindakan yang diambil ketika data induk diperbarui atau dihapus.

- **Relasi Induk:** `users(id)` -> Saat user dihapus: `RESTRICT` (mencegah penghapusan akun jika ada pesanan aktif).
- **Relasi Anak:** `order_items(order_id)` -> Saat order dihapus logis (`deleted_at`), anak tabel tetap tersimpan untuk audit trail.

## 5. Kebijakan Retensi & Pengarsipan [Opsional] *(Boleh dihapus jika tidak ada aturan khusus)*

> **Panduan Pengisian:**
> Jelaskan masa simpan data aktif sebelum diarsipkan ke cold-storage atau dihapus permanen sesuai aturan kepatuhan.

- **Masa Simpan Data Operasional:** [Misal: 2 tahun di basis data operasional utama]
- **Strategi Pengarsipan:** [Misal: Data pesanan selesai > 2 tahun dipindahkan ke data lake / cold storage S3]
- **Hak Penghapusan (Right to Erasure):** [Data PII dianomisasi menjadi string acak jika user mengajukan hapus akun]
