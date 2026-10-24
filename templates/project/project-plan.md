---
id: DOC-PLN-001
title: Project Execution Plan
project: "[Nama Proyek]"
version: 1.0.0
author: "[Nama Project Lead / Scrum Master]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Active / Completed
---

# Project Execution Plan: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini menguraikan dekomposisi pekerjaan, jadwal sprint, jalur kritis, estimasi kapasitas, serta irama kerja tim. Format ID: WBS menggunakan `WBS-NN`, sprint menggunakan `SPR-NN`, dan item dependensi menggunakan `DEP-NN`. Bagian bertanda `[Wajib]` wajib diisi.

---

## 1. Work Breakdown Structure (WBS) & Paket Pekerjaan [Wajib]

> **Panduan Pengisian:** Pecah proyek besar menjadi paket kerja (work package) tingkat tinggi yang logis.

| ID WBS | Paket Pekerjaan (Work Package) | Deliverable Kunci | Estimasi Durasi | Pemilik Tugas |
|---|---|---|---|---|
| WBS-01 | Fondasi & Infrastruktur | CI/CD pipeline, staging environment, skema DB awal | 1 minggu | [Nama Dev / DevOps] |
| WBS-02 | Autentikasi & Akun Pengguna | Modul registrasi, login JWT, reset password | 1.5 minggu | [Nama Backend Dev] |
| WBS-03 | Katalog & Manajemen Inventori | API katalog, pencarian produk, halaman listing | 2 minggu | [Nama Fullstack Dev] |
| WBS-04 | Transaksi & Payment Gateway | Integrasi Webhook payment, alur checkout, faktur | 2 minggu | [Nama Backend Dev] |
| WBS-05 | UAT & Kesiapan Rilis | Script pengujian end-to-end, load testing, bug fixing | 1 minggu | [Nama QA & Lead] |

---

## 2. Jadwal Sprint & Deliverables [Wajib]

> **Panduan Pengisian:** Petakan alokasi sprint timebox (umumnya 2 minggu per sprint) lengkap dengan target story point (SP) dan deliverable fungsional.

| ID Sprint | Periode Tanggal | Target Story Points (SP) | Fokus Utama & Deliverable | Status |
|---|---|---|---|---|
| SPR-01 | [YYYY-MM-DD] s.d [YYYY-MM-DD] | 28 SP | Setup arsitektur, database migration, otentikasi dasar | Planned |
| SPR-02 | [YYYY-MM-DD] s.d [YYYY-MM-DD] | 32 SP | CRUD katalog produk, upload media, antarmuka admin | Planned |
| SPR-03 | [YYYY-MM-DD] s.d [YYYY-MM-DD] | 30 SP | Keranjang belanja, kalkulasi ongkos kirim, checkout | Planned |
| SPR-04 | [YYYY-MM-DD] s.d [YYYY-MM-DD] | 26 SP | Payment gateway callback, notifikasi email & push | Planned |
| SPR-05 | [YYYY-MM-DD] s.d [YYYY-MM-DD] | 20 SP | Hardening keamanan, optimasi kueri p95, UAT sign-off | Planned |

---

## 3. Peta Dependensi (Dependency Map) [Wajib]

> **Panduan Pengisian:** Identifikasi ketergantungan antar tugas internal maupun faktor eksternal yang berpotensi menghambat progres (blocking).

| ID Dep | Tugas yang Menunggu (Dependent) | Bergantung Pada (Dependency) | Jenis Dependensi | Mitigasi / Tindakan Pengamanan |
|---|---|---|---|---|
| DEP-01 | Integrasi Checkout (SPR-03) | Persetujuan akun merchant payment gateway | External | Pengajuan izin sandbox 2 minggu sebelum sprint mulai |
| DEP-02 | Frontend Halaman Transaksi | API Contract Backend difinalisasi (OpenAPI Spec) | Internal | Tim backend sediakan mock server JSON di hari ke-2 sprint |
| DEP-03 | Deployment Staging Server | Akses kredensial cloud dari tim infrastruktur pusat | External | Eskalasi via tiket helpdesk dengan prioritas Urgent |

---

## 4. Jalur Kritis (Critical Path Items) [Kondisional]
*(Seksi ini kondisional: dapat dihapus jika proyek berdurasi < 2 minggu atau dikerjakan oleh developer tunggal)*

> **Panduan Pengisian:** Tandai rangkaian pekerjaan berurutan yang tidak memiliki kelonggaran waktu (zero float/slack). Penundaan pada jalur ini langsung menunda tanggal go-live proyek.

1. **Jalur Kritis #1:** `WBS-01 (Setup Fondasi)` ➔ `WBS-02 (Autentikasi)` ➔ `WBS-04 (Transaksi Finansial)` ➔ `Go-Live Produksi`.
2. **Buffer Cadangan:** [Contoh: 3 hari kerja sebelum jadwal rilis dialokasikan sebagai buffer darurat tanpa komitmen fitur baru].

---

## 5. Estimasi Kapasitas & Velocity Tim [Wajib]

> **Panduan Pengisian:** Hitung kapasitas riil tim untuk menghindari over-committing saat sprint planning.

- **Jumlah Developer Efektif:** [Contoh: 3 engineer purna waktu]
- **Hari Kerja per Sprint:** [Contoh: 10 hari kerja per sprint per orang]
- **Faktor Fokus (Focus Factor):** [Contoh: 0.75 (memperhitungkan meeting, review PR, dan support operasional)]
- **Target Velocity Konsensus:** [Contoh: 28 - 32 Story Points per sprint]

---

## 6. Alat Kerja & Irama Komunikasi (Tools & Cadence) [Opsional]
*(Seksi ini opsional: dapat dihapus jika alur komunikasi informal solo developer sudah memadai)*

> **Panduan Pengisian:** Tetapkan kanal koordinasi resmi dan jadwal seremoni tangkas (agile ceremonies).

- **Manajemen Tugas & Kode:** GitHub Projects / Jira / GitLab | Repositori: `[URL Repo]`
- **Kanal Komunikasi Harian:** Slack channel `#[proyek-dev]` / Discord
- **Daily Standup:** Setiap hari kerja pukul 09:30 WIB (maksimal 15 menit, asinkron via chat bot atau call singkat)
- **Sprint Review & Demo:** Hari Jumat terakhir sprint pukul 15:00 WIB
- **Retrospective:** Dilaksanakan segera setelah Sprint Review (fokus perbaikan proses tim)
