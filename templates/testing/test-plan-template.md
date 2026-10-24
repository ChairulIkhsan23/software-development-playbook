---
title: "Template Master Test Plan"
document_id: "TP-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
qa_lead: "[Nama QA Lead / Tester]"
project_name: "[Nama Proyek / Modul]"
target_release: "[v1.0.0 / Sprint-XX]"
---

# Master Test Plan

> **Panduan Pengisian:** Gunakan dokumen ini untuk merencanakan cakupan, strategi, kriteria kelayakan, dan jadwal pengujian rilis aplikasi. Sesuaikan tingkat pengujian dengan skala fitur.

---

## 1. Metadata & Tujuan Pengujian [Wajib]

> **Panduan Pengisian:** Tentukan identitas rilis dan tujuan utama dari siklus pengujian ini.

- **ID Rencana Pengujian [Wajib]:** `TP-[MODUL]-[YYYYMMDD]`
- **Tujuan Pengujian [Wajib]:** Memastikan fitur baru stabil, tidak menimbulkan regresi pada alur transaksi inti, dan memenuhi standar performa serta keamanan sebelum rilis produksi.
- **Pemilik Pengujian [Wajib]:** `[Nama QA Lead / PIC Engineer]`
- **Target Tanggal Rilis [Wajib]:** `[YYYY-MM-DD]`

---

## 2. Ruang Lingkup Pengujian (Test Scope) [Wajib]

> **Panduan Pengisian:** Pisahkan dengan tegas batasan pengujian yang dikerjakan dan dikecualikan.

### Fitur Dalam Cakupan (In-Scope) [Wajib]
- [ ] Modul autentikasi pengguna baru (registrasi, login OTP, lupa password).
- [ ] Alur checkout dan integrasi payment gateway.
- [ ] Pembaruan skema data profil pengguna pada API v2.

### Fitur Di Luar Cakupan (Out-of-Scope) [Wajib]
- [ ] Integrasi sistem legacy pihak ketiga yang dijadwalkan decommission.
- [ ] Penetrasi keamanan infrastruktur tingkat hardware (di luar aplikasi web).

---

## 3. Matriks Strategi Pengujian [Wajib]

> **Panduan Pengisian:** Definisikan tingkat pengujian, cakupan minimum, serta perkakas otomatisasi.

| Tingkat Pengujian | Target Cakupan | Perkakas (Tool) | Penanggung Jawab | Status |
|---|---|---|---|---|
| Unit Testing | Coverage >= 80% logic | Vitest / Jest | Software Engineers | [Wajib] |
| Integration Testing | 100% Critical API Endpoints | Supertest / Postman | Backend Engineers | [Wajib] |
| End-to-End (E2E) | Core business flows | Playwright / Cypress | QA Engineers | [Wajib] |
| Performance Testing | P95 Response < 500ms (100 RPS) | k6 / Locust | DevOps / QA | [Kondisional] |
| Security Testing | OWASP Top 10 Scan & Dependency Audit | Snyk / OWASP ZAP | Security / DevOps | [Kondisional] |

---

## 4. Lingkungan & Manajemen Data Uji [Wajib]

> **Panduan Pengisian:** Tentukan URL server pengujian dan aturan sanitasi data yang digunakan.

- **Environment Uji [Wajib]:** `Staging (https://staging-app.example.com)`
- **Data Uji (Test Data) [Wajib]:**
  - Menggunakan akun uji terisolasi (contoh: `test-user-[01-10]@example.com`).
  - Dilarang keras memakai data pribadi riil (PII nasabah/pengguna) di lingkungan staging.
  - Data seeding direset otomatis setiap malam pukul 00:00 WIB via cron script.

---

## 5. Manajemen Cacat & SLA Keparahan (Defect Management) [Wajib]

> **Panduan Pengisian:** Terapkan klasifikasi tingkat keparahan defect dan batas waktu perbaikan (SLA).

| Severity Level | Definisi Dampak | Target SLA Resolusi | Status |
|---|---|---|---|
| **SEV-1 (Blocker)** | Sistem crash total, data hilang, alur inti terputus tanpa workaround | `< 4 Jam` | [Wajib] |
| **SEV-2 (Critical)** | Fitur utama gagal bekerja, namun ada workaround sementara | `< 24 Jam` | [Wajib] |
| **SEV-3 (Major)** | Fitur sekunder bermasalah, dampak terbatas pada beberapa user | `< 3 Hari Kerja` | [Wajib] |
| **SEV-4 (Minor)** | Masalah kosmetik UI, typo, alignment tata letak | Next Sprint / Backlog | [Opsional] |

---

## 6. Kriteria Masuk & Kriteria Keluar (Entry & Exit Criteria) [Wajib]

> **Panduan Pengisian:** Pastikan prasyarat terpenuhi sebelum memulai dan mengakhiri fase testing.

### Kriteria Masuk (Entry Criteria) [Wajib]
- [ ] Build sukses di CI pipeline dan ter-deploy tanpa galat di staging.
- [ ] Unit test pass rate 100% pada branch rilis.
- [ ] Seluruh story tiket telah berstatus "Ready for QA".

### Kriteria Keluar (Exit Criteria) [Wajib]
- [ ] Nol defect terbuka berkategori SEV-1 dan SEV-2.
- [ ] 100% test case prioritas P1 dan P2 telah dieksekusi dengan hasil Pass.
- [ ] Laporan uji final disetujui oleh QA Lead dan Product Owner.

---

## 7. Jadwal & Kepemilikan (Schedule & Ownership) [Wajib]

> **Panduan Pengisian:** Tentukan tahapan eksekusi dan penanggung jawab setiap fase pengujian.

| Fase Uji | Tanggal Mulai | Tanggal Selesai | Pemilik (Owner) | Status |
|---|---|---|---|---|
| Penyusunan Test Case | `[YYYY-MM-DD]` | `[YYYY-MM-DD]` | `[Nama QA]` | Selesai |
| Eksekusi Uji Fungsional & E2E | `[YYYY-MM-DD]` | `[YYYY-MM-DD]` | `[Nama QA]` | Berjalan |
| Regression Testing & Bug Re-test | `[YYYY-MM-DD]` | `[YYYY-MM-DD]` | `[Nama QA]` | Menunggu |
| UAT & Sign-off Bisnis | `[YYYY-MM-DD]` | `[YYYY-MM-DD]` | `[Nama PO]` | Menunggu |

*(Seksi pengujian perangkat keras di bawah ini opsional: hapus jika aplikasi berbasis web murni)*
## 8. Matriks Perangkat Fisik (Hardware Matrix) [Opsional]
- **Target OS:** `Android 13+, iOS 16+`
- **Device Pools:** `Samsung Galaxy S22, iPhone 14 Pro, Pixel 7`
