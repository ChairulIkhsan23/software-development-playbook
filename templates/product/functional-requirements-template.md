---
id: DOC-FRS-001
title: Functional Requirements Specification (FRS)
project: "[Nama Proyek / Produk]"
version: 1.0.0
author: "[Nama System Analyst / PM / Tech Lead]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Review / Approved
---

# Functional Requirements Specification (FRS): [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini merinci secara granular perilaku sistem, masukan (input), keluaran (output), validasi, dan prioritas implementasi. Konvensi penamaan ID: `FR-[MODULE]-[NNN]` (contoh: `FR-AUTH-001`, `FR-PAY-002`). Prioritas menggunakan metode MoSCoW (Must have, Should have, Could have, Won't have). Bagian bertanda `[Wajib]` harus diisi.

---

## 1. Modul: [Nama Modul Utama, misal: Manajemen Checkout & Pembayaran] [Wajib]

> **Panduan Pengisian:** Kelompokkan kebutuhan berdasarkan modul fungsional. Lengkapi tabel matriks kebutuhan berikut.

| ID Kebutuhan | Nama Kebutuhan | Deskripsi Terperinci | Input | Output / Perubahan Status | Aturan Validasi | Prioritas (MoSCoW) | User Story Terkait | Status |
|---|---|---|---|---|---|:---:|---|:---:|
| FR-CHK-001 | Validasi Stok Keranjang | Memeriksa ketersediaan kuantitas item keranjang sebelum proses bayar | `cart_id`, `items[]` | Status stok valid (200 OK) atau error stok habis (409 Conflict) | Kuantitas item <= stok aktif di tabel inventori | Must | US-CHK-01 | Approved |
| FR-CHK-002 | Generate Tagihan Pembayaran | Membuat referensi pembayaran transaksi baru ke payment gateway | `order_id`, `payment_method_id` | `payment_url`, `va_number`, status order berubah `PENDING_PAYMENT` | Nominal transaksi > 0 dan kanal pembayaran aktif | Must | US-CHK-02 | In Progress |
| FR-CHK-003 | Penerimaan Webhook Notifikasi | Menangkap payload status callback dari server payment gateway | JSON payload event gateway, `signature_header` | Update status order menjadi `PAID`, kirim event bus | Verifikasi HMAC SHA256 signature sesuai secret key | Must | US-PAY-01 | Draft |
| FR-CHK-004 | Batalkan Pesanan Kedaluwarsa | Membatalkan order jika melebihi batas waktu bayar (timeout) | Background cron job worker | Status order berubah `EXPIRED`, kembalikan reservasi stok | `order.created_at + 24 jam < NOW()` dan status masih `PENDING` | Should | US-ORD-05 | Draft |

---

## 2. Kartu Spesifikasi Detail Kebutuhan Kompleks (Detailed Spec Card) [Wajib]

> **Panduan Pengisian:** Gunakan format kartu di bawah untuk kebutuhan kompleks yang membutuhkan kejelasan alur logika, payload skema, atau penanganan state machine. Format ID: `FR-[MOD]-[NNN]`.

### Kartu Spesifikasi: FR-CHK-002 (Generate Tagihan Pembayaran)
- **Modul:** Checkout / Pembayaran
- **Prioritas:** Must Have
- **Aktor:** Pengguna Terotentikasi & Sistem Checkout Service
- **Deskripsi Operasi:**
  Sistem menerima permintaan pembuatan invoice pembayaran, mengunci stok barang (inventory hold) selama 15 menit, lalu melakukan pemanggilan API gateway mitra untuk mendapatkan nomor Virtual Account atau QRIS dinamis.
- **Payload Input (JSON Request):**
  ```json
  {
    "order_id": "ORD-20261025-001",
    "payment_channel": "BCA_VA",
    "amount": 250000
  }
  ```
- **Keluaran Sukses (HTTP 201 Created):**
  ```json
  {
    "payment_ref": "PAY-889912",
    "va_number": "1234508123456789",
    "expires_at": "2026-10-25T15:30:00Z",
    "status": "PENDING_PAYMENT"
  }
  ```
- **Penanganan Kondisi Error (Exception Flow):**
  - Jika payment channel sedang maintenance di pihak vendor: Kembalikan `503 Service Unavailable`, batalkan reservasi stok sementara, berikan pesan alternatif metode bayar kepada pengguna.
  - Jika `order_id` sudah pernah dibayar: Kembalikan `400 Bad Request` dengan error code `ORDER_ALREADY_PAID`.

---

## 3. Matriks Traceability Kebutuhan Fungsional [Opsional]
*(Seksi ini opsional: dapat dihapus jika pelacakan dikelola terpusat di issue tracker GitHub/Jira)*

> **Panduan Pengisian:** Hubungkan ID fungsional ke komponen arsitektur backend, endpoint API, dan skenario tes.

| ID Kebutuhan | Komponen Service | Endpoint API / Worker | Test Case ID |
|---|---|---|---|
| FR-CHK-001 | Cart Service | `POST /api/v1/cart/validate` | TC-CHK-001 |
| FR-CHK-002 | Payment Gateway Service | `POST /api/v1/payments/invoices` | TC-CHK-002 |
| FR-CHK-003 | Webhook Consumer Worker | `POST /api/v1/webhooks/payments` | TC-PAY-003 |

---

## 4. Konvensi Status Siklus Hidup Kebutuhan [Wajib]

> **Panduan Pengisian:** Perbarui status kebutuhan sesuai tahapan siklus hidup rekayasa perangkat lunak.

- **Draft:** Kebutuhan baru diajukan, masih dalam tahap penulisan dan klarifikasi logika.
- **In Review:** Sedang ditinjau oleh Tech Lead dan QA terkait kelayakan teknis dan keterujian (*testability*).
- **Approved:** Disetujui bersama dan siap dimasukkan ke dalam backlog sprint.
- **Implemented:** Kode telah digabungkan (*merged*) ke cabang pengembangan utama dan lulus unit test.
- **Verified:** Telah divalidasi oleh QA di lingkungan staging melalui pengujian fungsional end-to-end.
