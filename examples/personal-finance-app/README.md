# Contoh Proyek: Personal Finance App (DompetKu)

Dokumen ini adalah studi kasus utama (*flagship example*) dalam Software Development Playbook. Studi kasus ini mendemonstrasikan implementasi end-to-end yang konsisten antar dokumen: mulai dari Discovery, PRD, Arsitektur Sistem, Desain Database, hingga Kontrak API.

---

## 1. Ringkasan Proyek

| Informasi | Keterangan |
|---|---|
| **Nama Produk** | DompetKu |
| **Tipe Aplikasi** | Mobile Application (iOS & Android) + REST API Backend |
| **Target Pengguna** | Dewasa muda, mahasiswa, dan first-jobber (usia 18–30 tahun) |
| **Masalah Utama** | 68% pengguna berhenti mencatat keuangan dalam 2 minggu karena proses pencatatan di aplikasi yang ada terlalu rumit dan memakan waktu (> 30 detik per transaksi). |
| **Solusi MVP** | Pencatatan transaksi super-cepat (< 10 detik), visualisasi alokasi anggaran kategori, dan peringatan batas pengeluaran otomatis. |
| **Model Deployment** | Cloud-native containerized API + PostgreSQL terkelola + Redis cache |

---

## 2. Asumsi Baseline Proyek

Seluruh dokumen turunan dalam studi kasus ini mengacu pada batasan dan asumsi baseline berikut:

1. **Mata Uang Tunggal**: Fase MVP hanya mendukung mata uang Rupiah Indonesia (IDR). Konversi multi-mata uang masuk ke roadmap v2.
2. **Model Akun Sederhana**: Setiap transaksi terikat pada satu akun sumber dana (`Cash`, `Bank Transfer`, atau `E-Wallet`).
3. **Pencatatan Manual**: MVP fokus pada pencatatan manual yang cepat. Integrasi Open Banking / mutasi otomatis bank di-defer ke versi lanjutan karena regulasi dan biaya agregator.
4. **Keamanan Data Finansial**: Data transaksi bersifat konfidensial. Password wajib di-hash menggunakan Argon2id atau bcrypt (cost factor >= 12), dan komunikasi client-server wajib menggunakan TLS 1.3.
5. **Autentikasi**: Token-based menggunakan JSON Web Token (JWT) dengan access token berumur pendek (15 menit) dan refresh token (7 hari) yang disimpan di secure storage perangkat.

---

## 3. Matriks Traceability Antar Dokumen

| Entitas Requirement | PRD | Arsitektur & Database | Spesifikasi API |
|---|---|---|---|
| Autentikasi Pengguna | `FR-AUTH-001` | Tabel `users`, JWT Service | `POST /api/v1/auth/login` |
| Pencatatan Pengeluaran/Pemasukan | `FR-TRANS-001` | Tabel `transactions`, `accounts` | `POST /api/v1/transactions` |
| Riwayat & Filter Transaksi | `FR-TRANS-002` | Index `idx_transactions_user_date` | `GET /api/v1/transactions` |
| Batas Anggaran Kategori | `FR-BUDGET-001` | Tabel `budgets`, `categories` | `GET /api/v1/budgets/summary` |
| Proteksi Nilai Negatif | `BR-TRANS-001` | Constraint `amount > 0` | HTTP 422 Unprocessable Entity |
| Peringatan Overbudget | `BR-BUDGET-001` | Budget Calculation Service | Payload response `budget_status` |

---

## 4. Struktur Dokumen Studi Kasus

- [`prd.md`](./prd.md): *Product Requirements Document* lengkap mencakup problem statement, personas, functional requirements, business rules, dan acceptance criteria.
- [`architecture.md`](./architecture.md): Arsitektur teknis C4 (Context & Container), diagram ERD skema relasional, dan catatan keputusan teknologi (ADR summary).
- [`api-spec.md`](./api-spec.md): Kontrak endpoint RESTful API dengan skema JSON request/response lengkap dan format error standar RFC 7807.

---

## 5. Cara Menggunakan Contoh Ini

1. **Sebagai Referensi Format**: Bandingkan dokumen di folder ini dengan template terkait di `templates/product/` dan `templates/design/` untuk melihat bagaimana placeholder template diisi secara konkret.
2. **Sebagai Uji Konsistensi**: Perhatikan bagaimana ID requirement (`FR-TRANS-001`) konsisten dirujuk pada model database dan penamaan endpoint API.
3. **Sebagai Baseline Modifikasi**: Salin pola dokumen ini saat memulai perancangan aplikasi SaaS atau mobile baru Anda.
