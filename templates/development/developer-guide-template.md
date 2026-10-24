---
title: "Template Developer Setup Guide"
document_id: "DEV-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
maintainer: "[Nama Tech Lead / Tim Core]"
project_name: "[Nama Proyek]"
repository_url: "https://github.com/[owner]/[repo]"
---

# Developer Onboarding & Local Setup Guide

> **Panduan Pengisian:** Gunakan panduan ini untuk mengonfigurasi lingkungan kerja lokal developer baru. Perbarui versi minimal dependensi dan perintah berkala saat terjadi pembaruan teknologi.

---

## 1. Prasyarat Lingkungan Lokal [Wajib]

> **Panduan Pengisian:** Pastikan seluruh perkakas berikut telah terpasang di mesin pengembang sebelum memulai langkah berikutnya.

| Perkakas (Tool) | Versi Minimal | Tautan Instalasi Resmi | Status |
|---|---|---|---|
| Git | `v2.40+` | `https://git-scm.com/downloads` | [Wajib] |
| Node.js | `v20 LTS` / `v22 LTS` | `https://nodejs.org/` | [Wajib] |
| pnpm / npm | `pnpm v9+` | `https://pnpm.io/installation` | [Wajib] |
| Docker & Compose | `v25+` / Compose `v2.24+` | `https://docs.docker.com/get-docker/` | [Wajib] |
| PostgreSQL Client | `v16+` | `https://www.postgresql.org/download/` | [Kondisional] |

---

## 2. 6 Langkah Mulai Cepat (Quick Start) [Wajib]

> **Panduan Pengisian:** Ikuti urutan 6 langkah di bawah ini secara presisi dari terminal root proyek.

```bash
# Langkah 1: Clone repositori proyek [Wajib]
git clone https://github.com/[owner]/[repo].git
cd [repo]

# Langkah 2: Konfigurasi file environment lokal [Wajib]
cp .env.example .env.local

# Langkah 3: Install seluruh dependensi proyek [Wajib]
pnpm install

# Langkah 4: Jalankan container pendukung, migrasi basis data, dan seed data dummy [Wajib]
docker compose up -d
pnpm run db:migrate
pnpm run db:seed

# Langkah 5: Jalankan development server lokal [Wajib]
pnpm run dev

# Langkah 6: Jalankan test suite untuk memvalidasi setup lingkungan [Wajib]
pnpm run test
```

---

## 3. Referensi Perintah Harian (Daily Dev Commands) [Wajib]

> **Panduan Pengisian:** Daftar perintah CLI yang paling sering digunakan selama siklus pengembangan aktif.

| Perintah | Deskripsi Tindakan | Status |
|---|---|---|
| `pnpm run dev` | Menjalankan server lokal dengan hot reload di `http://localhost:3000` | [Wajib] |
| `pnpm run test` | Menjalankan seluruh unit test dengan Vitest / Jest | [Wajib] |
| `pnpm run test:watch` | Menjalankan test runner dalam mode interaktif watch | [Opsional] |
| `pnpm run lint` | Menjalankan pemeriksaan linter dan style guide | [Wajib] |
| `pnpm run build` | Menjalankan build produksi dan validasi type-check | [Wajib] |
| `pnpm run db:reset` | Mereset database lokal dan mengisi ulang data awal | [Kondisional] |

---

## 4. Variabel Lingkungan (Environment Variables) [Wajib]

> **Panduan Pengisian:** Dokumentasikan semua kunci environment yang wajib didefinisikan pada file `.env.local`.

| Nama Variabel | Deskripsi Nilai | Contoh Nilai Lokal | Status |
|---|---|---|---|
| `DATABASE_URL` | String koneksi basis data PostgreSQL lokal | `postgresql://user:pass@localhost:5432/app_dev` | [Wajib] |
| `APP_SECRET` | Kunci enkripsi sesi minimal 32 karakter | `local-super-secret-key-32-chars-min` | [Wajib] |
| `API_PORT` | Port HTTP server lokal | `3000` | [Wajib] |
| `REDIS_URL` | URL instance Redis untuk cache dan queue | `redis://localhost:6379` | [Kondisional] |
| `LOG_LEVEL` | Tingkat keparahan log aplikasi | `debug` | [Opsional] |

---

## 5. FAQ Pemecahan Masalah (Troubleshooting) [Wajib]

> **Panduan Pengisian:** Tuliskan solusi atas galat umum yang sering dialami oleh developer baru.

### Q1: `Error: connect ECONNREFUSED 127.0.0.1:5432`
- **Penyebab:** Container database Docker belum berjalan atau port 5432 bentrok dengan PostgreSQL lokal.
- **Solusi:** Jalankan `docker compose ps` untuk mengecek status kontainer, atau jalankan `docker compose up -d db`.

### Q2: `Error: Cannot find module or type declarations`
- **Penyebab:** Dependensi belum terpasang atau TypeScript engine belum me-refresh cache.
- **Solusi:** Jalankan `pnpm install` ulang, lalu lakukan reload window pada editor (VS Code: `Ctrl+Shift+P` -> `Reload Window`).

### Q3: `Error: PrismaClientInitializationError / Migration checksum mismatch`
- **Penyebab:** Skema lokal tertinggal dibanding migrasi terbaru branch utama.
- **Solusi:** Eksekusi `pnpm run db:migrate:reset` (Peringatan: menghapus data lokal).

*(Seksi layanan eksternal di bawah ini opsional: hapus jika proyek tidak memakai cloud emulator)*
## 6. Emulator Layanan Cloud Lokal [Opsional]
- **AWS LocalStack / MinIO:** `http://localhost:4566` (Mock S3 Storage lokal)
- **MailHog:** `http://localhost:8025` (Web UI penangkap email testing lokal)
