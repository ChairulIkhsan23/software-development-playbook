---
title: "Project Domain Glossary & Ubiquitous Language"
project: "[Nama Proyek]"
curator: "[Nama Product Manager / Domain Expert]"
status: "Active"
last_updated: "[YYYY-MM-DD]"
---

# Domain Glossary: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini mendefinisikan *Ubiquitous Language* (bahasa bersama) untuk menyamakan persepsi seluruh stakeholder, developer, dan QA.
> Setiap istilah teknis domain bisnis wajib didefinisikan secara presisi agar tidak menimbulkan ambiguitas dalam kode maupun PRD.
> Perbarui glosarium setiap kali ada konsep baru yang disepakati dalam sesi penemuan produk (*discovery session*).

---

## 1. Aturan Penggunaan & Konsistensi Bahasa [Wajib]

> **Panduan Pengisian:**
> Patuhi pedoman berikut saat berkomunikasi lisan maupun menulis dokumen dan kode program:

- **Satu Istilah, Satu Arti:** Hindari menggunakan dua istilah berbeda untuk konsep yang sama (misal: jangan mencampur `Customer`, `Client`, dan `User` tanpa batasan jelas).
- **Cerminan di Kode Program:** Nama class, entitas tabel database, method, dan endpoint API wajib mengadopsi istilah resmi dari glosarium ini.
- **Konsultasi Domain Expert:** Jika terjadi keraguan makna dalam backlog refinement, konfirmasikan langsung kepada kurator glosarium.

## 2. Tabel Istilah Domain (Domain Glossary Table) [Wajib]

> **Panduan Pengisian:**
> Masukkan istilah domain ke dalam tabel berikut dengan format kolom yang lengkap. Kolom Disambiguasi wajib diisi untuk mencegah salah paham.

| Istilah (Term) | Akronim | Modul Domain | Definisi Formal | Contoh Penggunaan Kalimat | Disambiguasi (Bukan Merupakan) | Sinonim / Istilah Terkait |
|---|---|---|---|---|---|---|
| **Merchant** | - | Penjual | Badan usaha atau perorangan terdaftar yang menjual barang di platform | *"Merchant wajib menyelesaikan verifikasi KYC sebelum mencairkan dana."* | Bukan `Buyer` atau `Drop-shipper` non-terdaftar | Penjual, Mitra Usaha |
| **Gross Merchandise Value** | GMV | Keuangan & Sales | Total nilai uang dari seluruh transaksi penjualan yang tercatat sebelum dipotong diskon atau retur | *"GMV kampanye diskon gajian menembus target 2 milyar rupiah."* | Bukan `Net Revenue` atau keuntungan bersih perusahaan | Nilai Transaksi Bruto |
| **Escrow Account** | - | Pembayaran | Rekening penampungan bersama pihak ketiga yang menahan dana hingga pesanan diterima | *"Dana ditahan pada rekening Escrow hingga status pesanan menjadi Selesai."* | Bukan rekening tabungan pribadi milik merchant atau pembeli | Rekening Bersama (Rekber) |
| **Stock Keeping Unit** | SKU | Inventaris & Gudang | Kode alfanumerik spesifik pengenal satu varian fisik unik barang (ukuran, warna, tipe) | *"Produk kemeja ini memiliki 6 SKU berbeda berdasarkan warna dan ukuran."* | Bukan ID Produk induk (Parent Product ID) | Varian Barang |
| **Know Your Customer** | KYC | Kepatuhan & Regulasi | Prosedur identifikasi identitas legal pengguna sesuai ketentuan hukum perbankan | *"Pengguna harus mengunggah foto KTP pada proses KYC tingkat 2."* | Bukan sekadar proses verifikasi email atau OTP nomor telepon | Verifikasi Identitas |
| **Settlement** | - | Pembayaran | Proses rekonsiliasi dan pemindahan saldo bersih transaksi ke rekening bank merchant | *"Settlement dilakukan setiap hari kerja pada pukul 17:00 WIB."* | Bukan sekadar notifikasi pembayaran berhasil (payment captured) | Pencairan Dana Bersih |
| **Dispute / Chargeback** | - | CS & Resolusi | Klaim penolakan transaksi oleh pemegang kartu atau pembeli akibat dugaan penipuan | *"Tim finance wajib merespons bukti dispute dalam waktu maksimal 7 hari kerja."* | Bukan permintaan pembatalan sepihak (*cancellation*) reguler | Sanggahan Transaksi |

## 3. Pemetaan Istilah Domain ke Simbol Kode (Code Mapping) [Wajib]

> **Panduan Pengisian:**
> Cantumkan pemetaan terminologi domain bisnis ke nama entitas di basis data dan kelas pemrograman.

| Istilah Bisnis | Nama Entitas / Class Code | Nama Tabel Database | Format Representasi JSON |
|---|---|---|---|
| Merchant | `MerchantProfile` | `merchants` | `"merchant_id": "mrc_..."` |
| SKU | `ProductVariantSku` | `product_skus` | `"sku_code": "SKU-..."` |
| Settlement | `MerchantSettlement` | `settlements` | `"settlement_status": "settled"` |

## 4. Siklus Hidup & Prosedur Usulan Istilah Baru [Wajib]

> **Panduan Pengisian:**
> Jelaskan alur bagaimana anggota tim mengusulkan istilah baru saat sprint grooming atau perubahan regulasi.

1. **Pengajuan:** Buat Pull Request pada file ini atau ajukan di sesi refinement sprint.
2. **Review:** Product Manager bersama Tech Lead meninjau apakah istilah sudah ada atau bertabrakan dengan istilah lama.
3. **Persetujuan & Komunikasi:** Setelah disetujui, istilah diumumkan pada kanal komunikasi tim engineering.

## 5. Istilah Usang / Ditinggalkan (Deprecated Terms) [Opsional] *(Boleh dihapus jika proyek baru)*

> **Panduan Pengisian:**
> Catat istilah lama yang sudah tidak boleh digunakan lagi beserta alasan penggantiannya.

| Istilah Lama | Istilah Pengganti Resmi | Tanggal Usang | Alasan Perubahan |
|---|---|---|---|
| `Vendor` | `Merchant` | `[YYYY-MM-DD]` | Menyamakan penyebutan dengan model bisnis marketplace B2C |
| `Saldo Dompet` | `Deposit Account` | `[YYYY-MM-DD]` | Kepatuhan terminologi regulasi Bank Indonesia mengenai e-wallet |
| `Resi Pengiriman` | `Waybill / AWB` | `[YYYY-MM-DD]` | Penyesuaian standar integrasi API ekspedisi internasional |
