---
title: "Security and Privacy Architecture Specification"
project: "[Nama Proyek]"
security_lead: "[Nama Security Lead / Tech Lead]"
status: "[Draft | In Review | Approved]"
last_updated: "[YYYY-MM-DD]"
---

# Security & Privacy Specification: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini memetakan postur keamanan, klasifikasi data, mitigasi ancaman STRIDE, dan kepatuhan privasi (UU PDP / GDPR).
> Tinjau dokumen ini pada setiap perubahan arsitektur mayor atau penambahan integrasi pihak ketiga.

---

## 1. Skema Klasifikasi Data (Data Classification Scheme) [Wajib]

> **Panduan Pengisian:**
> Klasifikasikan seluruh jenis data yang disimpan atau diproses oleh aplikasi ke dalam empat tingkat sensitivitas.

| Klasifikasi | Definisi & Contoh Data | Kontrol Akses Minimal | Enkripsi Storage |
|---|---|---|---|
| **Public** | Data terbuka umum (katalog produk, artikel publik, harga promo) | Akses publik tanpa autentikasi | [Opsional] |
| **Internal** | Data operasional internal (ID transaksi non-PII, log status worker) | Autentikasi internal staf / sistem | [Wajib] AES-256 |
| **Confidential** | Data Pribadi Pengguna (PII: nama lengkap, nomor HP, email, alamat fisik) | Role-Based Access Control (RBAC) ketat | [Wajib] AES-256 + masking di UI |
| **Restricted** | Data sangat rahasia (hash kata sandi, API key, token rahasia, kartu kredit) | Akses mesin terbatas (Least Privilege) | [Wajib] Hashing Argon2id / HSM KMS |

## 2. Pemodelan Ancaman (Threat Modeling - STRIDE Matrix) [Wajib]

> **Panduan Pengisian:**
> Identifikasi skenario ancaman berdasarkan metodologi STRIDE dan tentukan mitigasi teknis konkretnya.

| Kategori Ancaman | Komponen Terdampak | Skenario Ancaman | Tindakan Mitigasi Terpilih | Status Mitigasi |
|---|---|---|---|---|
| **Spoofing** | Modul Autentikasi | Penyerang memalsukan JWT token pengguna lain | Tanda tangan token via RS256 dengan rotasi kunci berkala | [Wajib - Implemented] |
| **Tampering** | Endpoint Checkout | Manipulasi harga barang saat request payload dikirim | Validasi total harga wajib dihitung ulang di server side | [Wajib - Implemented] |
| **Repudiation** | Transaksi Finansial | Pengguna menyangkal telah melakukan transfer | Audit log append-only dengan timestamp kriptografis | [Wajib - Implemented] |
| **Information Disclosure** | Error Handler API | Stack trace teknis backend bocor ke client saat crash | Format error RFC 7807 tanpa membocorkan pesan exception internal | [Wajib - Implemented] |
| **Denial of Service** | API Gateway | Serangan flooding request melumpuhkan database | Rate limiting via Redis (100 req/mnt/IP) + Cloudflare DDoS protection | [Wajib - Implemented] |
| **Elevation of Privilege** | Layanan Pengguna | Akun reguler mengakses endpoint admin (`/api/admin`) | Middleware RBAC wajib memvalidasi claim role pada setiap route | [Wajib - Implemented] |

## 3. Checklist Kontrol Keamanan Aplikasi (Security Controls) [Wajib]

> **Panduan Pengisian:**
> Centang kontrol yang wajib aktif sebelum aplikasi dirilis ke lingkungan produksi.

### A. Autentikasi & Otorisasi (Auth & AuthZ)
- [ ] [Wajib] Password di-hash menggunakan `Argon2id` atau `bcrypt` (work factor minimum 12).
- [ ] [Wajib] Session token menggunakan JWT beralgoritma asimetris (RS256) dengan masa kedaluwarsa singkat (maks 15 menit).
- [ ] [Wajib] Refresh token tersimpan aman di database dengan status rotasi dan pembatalan (*revocation list*).

### B. Proteksi Data saat Transit & Diam (Data in Transit & at Rest)
- [ ] [Wajib] Enkripsi transit menggunakan HTTPS TLS 1.3 pada semua endpoint publik.
- [ ] [Wajib] Header HTTP keamanan aktif: `Strict-Transport-Security` (HSTS), `X-Content-Type-Options: nosniff`.
- [ ] [Wajib] Basis data produksi mengaktifkan Transparent Data Encryption (TDE / AES-256).

### C. Sanitasi Input & Penanganan Secret
- [ ] [Wajib] Parameterized queries atau ORM type-safe wajib digunakan untuk mencegah SQL Injection.
- [ ] [Wajib] Seluruh input HTML dibersihkan via sanitizer untuk mencegah Cross-Site Scripting (XSS).
- [ ] [Wajib] Dilarang meletakkan hardcoded secret di kode program; gunakan AWS Secrets Manager / Vault / `.env`.

### D. Logging, Audit & Monitoring
- [ ] [Wajib] Data rahasia (password, kartu kredit, auth token) otomatis disensor (*redacted*) dari logging.
- [ ] [Wajib] Log kegagalan login dan perubahan izin admin dicatat ke storage terpisah yang tidak dapat diubah (*immutable*).

## 4. Kepatuhan Privasi Data (UU PDP / GDPR Compliance) [Wajib]

> **Panduan Pengisian:**
> Uraikan implementasi hak-hak pemilik data pribadi sesuai Undang-Undang Perlindungan Data Pribadi (UU PDP).

- **Minimisasi Data (Data Minimization):** Aplikasi hanya meminta data yang mutlak diperlukan untuk transaksi operasional.
- **Persetujuan Pengguna (User Consent):** Checkbox persetujuan Terms of Service & Privacy Policy wajib aktif saat registrasi akun.
- **Hak Penghapusan (Right to Erasure / Right to Delete):**
  - Endpoint `DELETE /api/v1/users/me` menganonimkan data pribadi dalam database dalam waktu maksimal 3x24 jam.
  - Data transaksi historis tetap disimpan tanpa nama demi kewajiban pajak negara (selama 5 tahun).
- **Prosedur Kebocoran Data (Data Breach Notification):**
  - Tim on-call wajib mendeteksi dan mengisolasi insiden dalam waktu `< 2 jam`.
  - Notifikasi resmi dikirim ke otoritas pengawas dan pemilik data dalam waktu maksimal `72 jam` sejak insiden terverifikasi.

## 5. Pengujian Keamanan & Manajemen Kerentanan [Opsional] *(Boleh dihapus jika solo dev)*

> **Panduan Pengisian:**
> Rencanakan pengujian penetrasi berkala dan pembaruan dependensi pihak ketiga.

- **Automated Dependency Scan:** Tool `Dependabot` / `Snyk` memeriksa kerentanan CVE pada library setiap minggu.
- **Penetration Testing:** Audit penetrasi eksternal dilakukan minimal 1 kali per tahun sebelum audit kepatuhan tahunan.
