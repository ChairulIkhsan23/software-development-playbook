---
title: "Template Deployment Guide"
document_id: "DEP-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
author: "[Nama DevOps / SRE / Tech Lead]"
service_name: "[Nama Layanan / Aplikasi]"
target_environment: "Production"
---

# Standard Deployment Guide

> **Panduan Pengisian:** Gunakan panduan ini saat mempersiapkan, mengeksekusi, dan memverifikasi deployment rilis ke lingkungan staging maupun produksi. Simpan instruksi fallback darurat agar selalu siap dieksekusi saat CI/CD mengalami gangguan.

---

## 1. Lingkungan Sasaran & Metadata Rilis [Wajib]

> **Panduan Pengisian:** Tentukan target infrastruktur, versi rilis, dan jadwal waktu pemeliharaan.

- **Nama Layanan (Service) [Wajib]:** `[Nama Service / API Gateway / Frontend SPA]`
- **Lingkungan Sasaran (Target Environment) [Wajib]:** `[Production / Staging / Canary]`
- **Versi Rilis SemVer [Wajib]:** `v[X.Y.Z]`
- **Commit SHA / Image Tag [Wajib]:** `sha-[abcdef1]` / `registry.example.com/[repo]:v[X.Y.Z]`
- **Jadwal Jendela Pemeliharaan (Maintenance Window) [Opsional]:** `[YYYY-MM-DD HH:mm - HH:mm WIB]`

---

## 2. Checklist Verifikasi Pra-Deployment (Pre-Deployment) [Wajib]

> **Panduan Pengisian:** Wajib memastikan seluruh prasyarat siap sebelum memicu proses deployment.

- [ ] [Wajib] Semua commit rilis telah lolos build, linting, dan test suite di CI.
- [ ] [Wajib] Snapshot cadangan database produksi (full dump / RDS snapshot) telah selesai dibuat.
- [ ] [Wajib] Variabel lingkungan baru (jika ada) telah diinput ke Secret Manager / Vault dan diverifikasi.
- [ ] [Wajib] Tim operasional, customer support, dan stakeholder bisnis telah diberi notifikasi jadwal rilis.
- [ ] [Wajib] Rencana rollback dan kriteria pembatalan telah dipahami oleh seluruh tim release.

---

## 3. Prosedur Deployment Langkah Demi Langkah [Wajib]

> **Panduan Pengisian:** Prioritaskan pemicu otomatisasi CI/CD. Gunakan baris perintah manual HANYA jika pipeline otomatis gagal.

### Jalur A: Otomatisasi Pipeline CI/CD (Rekomendasi Utama) [Wajib]
1. Buat dan dorong Git Release Tag pada branch utama: `git tag -a v[X.Y.Z] -m "Release v[X.Y.Z]" && git push origin v[X.Y.Z]`.
2. Pantau workflow CI/CD pada URL pipeline rilis.
3. Pastikan job `build`, `test`, `security-scan`, dan `deploy-production` berstatus sukses hijau.

### Jalur B: Prosedur Manual Fallback Darurat [Kondisional]
```bash
# Login registry & tarik image [Kondisional]
docker login registry.example.com -u [username] && docker pull registry.example.com/[app]:v[X.Y.Z]

# Migrasi basis data & update deployment Kubernetes [Kondisional]
docker run --rm --env-file /etc/app.env registry.example.com/[app]:v[X.Y.Z] pnpm run db:migrate
kubectl set image deployment/[app]-deployment [app]=registry.example.com/[app]:v[X.Y.Z] -n prod
kubectl rollout status deployment/[app]-deployment -n prod --timeout=300s
```

---

## 4. Prosedur Migrasi Basis Data (Database Migration) [Wajib]

> **Panduan Pengisian:** Dokumentasikan skrip migrasi naik (Up) dan turun (Down) serta estimasi waktu eksekusi.

- **Perintah Migrasi Naik (Migrate Up) [Wajib]:** `pnpm run db:migrate` / `prisma migrate deploy`
- **Pemeriksaan Status Skema [Wajib]:** `pnpm run db:status`
- **Toleransi Waktu Migrasi [Wajib]:** Maksimal `< 5 Menit` (jika lebih lama, wajib mode maintenance).
- **Aturan Non-Breaking [Wajib]:** Perubahan skema wajib backward-compatible (metode expand-and-contract).

---

## 5. Verifikasi Pasca Deployment (Smoke Testing) [Wajib]

> **Panduan Pengisian:** Lakukan pemeriksaan cepat untuk memastikan traffic pengguna normal.

- [ ] [Wajib] Pemeriksaan status endpoint health check: `curl -f https://api.example.com/healthz` (ekspektasi: `HTTP 200 OK`).
- [ ] [Wajib] Uji otentikasi: Sesi login pengguna baru dan eksisting berhasil tanpa token error.
- [ ] [Wajib] Uji alur inti (Core flow): Transaksi pembuatan order berhasil tercatat di database.
- [ ] [Wajib] Pantau dashboard error rate di Datadog/Grafana: Tingkat galat HTTP 5xx tetap `< 0.1%`.

---

## 6. Prosedur Pembatalan Cepat (Rollback Procedure) [Wajib]

> **Panduan Pengisian:** Eksekusi bagian ini segera jika kriteria pemicu rollback terpenuhi.

- **Kondisi Pemicu Rollback [Wajib]:**
  - Error rate HTTP 5xx melebihi `> 2%` selama 5 menit berturut-turut setelah deployment.
  - Alur pembayaran gagal total (100% kegagalan transaksi).
  - Terjadi degradasi latency P99 melebihi target SLA (`> 3000ms`).
- **Langkah Rollback Aplikasi [Wajib]:**
  ```bash
  kubectl rollout undo deployment/[app]-deployment -n prod
  kubectl rollout status deployment/[app]-deployment -n prod
  ```
- **Langkah Rollback Database [Kondisional]:**
  ```bash
  # Hanya jalankan jika migrasi skema sebelumnya memiliki script down yang aman
  pnpm run db:migrate:rollback
  ```
- **Verifikasi Rollback [Wajib]:** Pastikan versi aplikasi kembali ke `v[Previous]` dan health check normal.

---

## 7. Kontak Komunikasi Tanggap Darurat [Wajib]

> **Panduan Pengisian:** Daftar nomor darurat dan kanal koordinasi insiden yang siaga selama deployment.

| Peran | Nama Personel | Kanal Komunikasi / No. HP | Status |
|---|---|---|---|
| Release Manager | `[Nama PIC]` | Slack: `@release-lead` / `+62-811-xxxx` | [Wajib] |
| Lead DevOps / SRE | `[Nama SRE]` | Slack: `@oncall-devops` / `+62-812-xxxx` | [Wajib] |
| Lead Database Admin | `[Nama DBA]` | Slack: `@oncall-dba` / `+62-813-xxxx` | [Kondisional] |
| War Room Incident | Tim Gabungan | `https://meet.google.com/[war-room-id]` | [Wajib] |

*(Seksi CDN di bawah ini opsional: hapus jika aplikasi backend murni)*
## 8. Invalidation Cache CDN [Opsional]
- **Perintah Purge:** `aws cloudfront create-invalidation --distribution-id [DIST_ID] --paths "/*"`
- **Verifikasi:** Cek header HTTP respons `x-cache: Miss` lalu `Hit from cloudfront`.
