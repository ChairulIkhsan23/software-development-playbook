---
title: "Template Release Plan"
document_id: "REL-TMPL-001"
release_version: "v[X.Y.Z]"
release_date: "[YYYY-MM-DD]"
release_manager: "[Nama Release Manager]"
system_tier: "Tier-1 Mission Critical"
overall_status: "Draft"
---

# Master Release Plan: `v[X.Y.Z]`

> **Panduan Pengisian:** Gunakan dokumen ini sebagai rencana operasional peluncuran rilis. Pastikan jadwal run-of-show, kriteria Go/No-Go, dan tenggat pembatalan (rollback deadline) disepakati sebelum hari pelaksanaan.

---

## 1. Metadata Rilis & Tim Pelaksana [Wajib]

> **Panduan Pengisian:** Tentukan versi rilis berstandar SemVer dan penanggung jawab utama tiap fungsi.

- **Versi Rilis SemVer [Wajib]:** `v[X.Y.Z]` (Contoh: `v1.4.0`)
- **Tanggal & Waktu Rilis [Wajib]:** `[YYYY-MM-DD]`, `[22:00 - 23:30 WIB]`
- **Release Manager [Wajib]:** `[Nama Release Manager]`
- **Lead Developer [Wajib]:** `[Nama Lead Dev]`
- **Lead QA [Wajib]:** `[Nama QA Lead]`
- **DevOps / Cloud Engineer [Wajib]:** `[Nama SRE/DevOps]`

---

## 2. Ringkasan Perubahan (Change Summary) [Wajib]

> **Panduan Pengisian:** Kategorikan seluruh perubahan kode yang disertakan dalam bundel rilis ini.

### A. Fitur Baru (New Features) [Wajib]
- `[FEAT-101]` : Integrasi metode pembayaran QRIS dinamis pada checkout.
- `[FEAT-105]` : Dashboard analitik performa penjualan harian bagi merchant.

### B. Perbaikan Bug (Bug Fixes) [Wajib]
- `[BUG-220]` : Perbaikan penghitungan pajak ganda pada transaksi mata uang asing.
- `[BUG-224]` : Perbaikan session timeout mendadak saat pengisian formulir KYC.

### C. Perubahan Merusak (Breaking Changes) [Kondisional]
- `[API-v2]` : Penghapusan field legacy `customer_phone` digantikan dengan format E.164 `phone_number`.

---

## 3. Linimasa Operasional Rilis (Run-of-Show Table) [Wajib]

> **Panduan Pengisian:** Rinci jadwal aktivitas menit demi menit beserta pemilik tugas dan status pelaksanaan real-time.

| Waktu (WIB) | Fase Rilis | Deskripsi Aktivitas Teknis | Pemilik (Owner) | Status |
|---|---|---|---|---|
| 21:30 - 21:45 | Pra-Rilis | Sinkronisasi War Room & freeze merge branch rilis | Release Mgr | Menunggu |
| 21:45 - 22:00 | Pra-Rilis | Snapshot backup database produksi & backup volume storage | DevOps / DBA | Menunggu |
| 22:00 - 22:15 | Migrasi | Eksekusi skrip migrasi skema database `pnpm run db:migrate` | DevOps | Menunggu |
| 22:15 - 22:30 | Deployment | Deploy container v[X.Y.Z] via rolling update / blue-green | DevOps | Menunggu |
| 22:30 - 22:50 | Verifikasi | Smoke testing end-to-end skenario kritis di produksi | QA Team | Menunggu |
| 22:50 - 23:00 | Keputusan | Evaluasi rapat Go / No-Go sign-off | Release Mgr | Menunggu |
| 23:00 - 23:30 | Observasi | Pemantauan lalu lintas transaksi dan metrik sistem | Seluruh Tim | Menunggu |

---

## 4. Kriteria Keputusan Go / No-Go [Wajib]

> **Panduan Pengisian:** Seluruh pihak kunci wajib menyetujui status sebelum rilis dinyatakan sah (Go) atau dibatalkan (No-Go).

- [ ] [Wajib] Nol cacat baru dengan tingkat keparahan SEV-1 atau SEV-2 ditemukan pada smoke test.
- [ ] [Wajib] Rata-rata response time API P95 berada di bawah ambang batas toleransi (`< 500ms`).
- [ ] [Wajib] Error rate transaksi pembayaran berada pada batas normal (`< 0.05%`).
- [ ] [Wajib] Seluruh log container tidak memicu continuous uncaught exceptions.
- **Keputusan Akhir [Wajib]:** `[ GO / NO-GO ]` pada pukul `[22:55 WIB]`

---

## 5. Rencana Pembatalan & Batas Waktu (Rollback Plan & Deadline) [Wajib]

> **Panduan Pengisian:** Tetapkan batas waktu final keputusan pembatalan rilis agar tidak mengganggu operasional esok hari.

- **Batas Waktu Keputusan Rollback (Hard Deadline) [Wajib]:** `[23:00 WIB]` (Maksimal 60 menit sejak deployment dimulai).
- **Kondisi Otomatis Rollback [Wajib]:**
  - Kegagalan migrasi basis data yang tidak dapat diperbaiki dalam 15 menit.
  - Alur otentikasi login pengguna gagal total di lingkungan produksi.
- **Langkah Cepat Pembatalan [Wajib]:**
  1. Kembalikan routing trafik load balancer ke versi sebelumnya `v[Prev]`.
  2. Jalankan rollback skema data dengan skrip `pnpm run db:migrate:rollback`.
  3. Verifikasi ketersediaan aplikasi lama dan kirimkan pengumuman insiden.

---

## 6. Rencana Pemantauan Pasca Rilis (Post-Release Monitoring) [Wajib]

> **Panduan Pengisian:** Tentukan metrik telemetri yang wajib dipantau selama periode observasi hiperkritis.

- **Periode Observasi Intensif [Wajib]:** `2 x 24 Jam` pasca deployment rilis.
- **Kanal Pemantauan (Monitoring Dashboard) [Wajib]:**
  - Metrik Kinerja Server: Grafana Dashboard (`https://grafana.example.com/d/prod-metrics`)
  - Log Galat: Sentry / Datadog APM (`https://sentry.example.com/projects/backend`)
- **Pemberitahuan Stakeholder [Wajib]:** Mengirimkan ringkasan status rilis sukses ke channel Slack `#announcement-product`.

*(Seksi pengumuman publik di bawah ini opsional: hapus jika rilis hanya berupa backend internal)*
## 7. Catatan Rilis Publik (Public Release Notes) [Opsional]
- `[Teks pengumuman fitur baru untuk pengguna publik atau newsletter pelanggan]`
