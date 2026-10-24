# Pre-Development Readiness Checklist (Gate 3)

Checklist pintu gerbang Gate 3 sebelum penulisan kode dimulai. Memastikan seluruh dependensi desain, arsitektur, lingkungan kerja, dan keselarasan tim tuntas agar sprint berjalan tanpa interupsi.

---

## 1. Requirements Readiness
- [ ] **Definition of Ready (DoR) Satisfied**: Seluruh tiket sprint memenuhi standar DoR tanpa pengecualian.
- [ ] **Stories Estimated by Consensus**: Tim pengembang telah menyepakati estimasi usaha via *Planning Poker* atau *T-shirt Sizing*.
- [ ] **Acceptance Criteria Finalized**: Skenario uji biner telah disetujui bersama oleh developer dan QA.
- [ ] **Business Priority Defined**: Urutan pengerjaan item sprint diurutkan tegas dari prioritas tertinggi.
- [ ] **No Unresolved Requirements Questions**: Tidak ada pertanyaan terbuka atau keraguan spesifikasi pada tiket.
- [ ] **Scope Freeze Confirmed**: Scope sprint terkunci; penambahan tiket baru wajib lewat persetujuan formal Tech Lead.

---

## 2. Technical Design Complete
- [ ] **Architecture Overview Documented**: Diagram arsitektur sistem (C4 Model Context & Container) terdokumentasi jelas.
- [ ] **Architecture Decision Records (ADR)**: Keputusan teknis krusial (pilihan pustaka, database, protokol komunikasi) disahkan.
- [ ] **Database Schema Finalized**: ERD, tipe data kolom, constraint relasi, dan strategi indexing telah disahkan.
- [ ] **API Contract Defined**: Spesifikasi API (OpenAPI/Swagger atau schema GraphQL) disepakati bersama frontend dan backend.
- [ ] **Error Code Convention**: Struktur payload error JSON dan konvensi status code HTTP telah diseragamkan.
- [ ] **Security Threat Model Reviewed**: Celah permukaan serangan (*attack surface*) dasar telah dievaluasi.

---

## 3. UX/UI Design Finalized
- [ ] **High-Fidelity Mockups Approved**: Seluruh screen alur utama disetujui Product Owner dan stakeholder bisnis.
- [ ] **Design Tokens & Component Specs**: Spacing, warna, tipografi, dan varian tombol terdokumentasi di Figma.
- [ ] **Responsive Breakpoints**: Tampilan pada ukuran mobile (375px), tablet (768px), dan desktop (1440px) terdefinisi.
- [ ] **Interactive & UI States Complete**: Seluruh kondisi komponen (Default, Hover, Active, Disabled, Loading, Error, Empty) siap pakai.
- [ ] **Asset Export Ready**: Ikon vektor SVG, ilustrasi, dan font telah dioptimasi dan siap diunduh developer.
- [ ] **Copy & Error Messaging Approved**: Redaksi teks peringatan dan pesan kesalahan tervalidasi final.

---

## 4. Environment & Tooling
- [ ] **Repository Configured**: Proteksi branch aktif pada branch `main`/`develop` (wajib PR dan minimal 1 reviewer).
- [ ] **Linter & Formatter Enforced**: Aturan ESLint, Prettier, dan pre-commit hooks (Husky) aktif otomatis.
- [ ] **CI Pipeline Initialized**: Pipeline CI tervalidasi berjalan otomatis saat PR dibuka (uji compile, lint, dan test).
- [ ] **Local Setup Documented**: Petunjuk instalasi dependensi lokal dan seeding database terdokumentasi jelas di `README.md`.
- [ ] **Environment Variables Template**: Berkas `.env.example` mutakhir dan bebas dari kredensial sensitif nyata.
- [ ] **Database Migrations Baseline**: Skrip migrasi awal siap dieksekusi tanpa tabrakan versi.

---

## 5. Team Alignment
- [ ] **Sprint Backlog Selected**: Beban total story points tidak melampaui rata-rata kapasitas historis tim.
- [ ] **External Dependencies Cleared**: Kunci API pihak ketiga, akun sandbox pembayaran, dan kredensial uji aktif tersedia.
- [ ] **Role Assignment Clear**: Setiap anggota tim mengetahui tugas awal mereka tanpa tumpang tindih.
- [ ] **Sprint Goal Agreed**: Tim menyepakati 1 kalimat sasaran utama sprint yang wajib tercapai.
- [ ] **Definition of Done (DoD) Shared**: Seluruh engineer menyepakati kriteria penyelesaian kode yang berlaku.

---

## Kriteria Keputusan Go / No-Go
Evaluasi formal Gate 3 dilakukan pada akhir sesi *Sprint Planning*:

| Hasil Evaluasi | Status Keputusan | Konsekuensi & Langkah Lanjutan |
|---|---|---|
| **Semua 5 Bagian 100% Terpenuhi** | **GO** | Sprint dimulai sesuai jadwal; developer mulai pull tiket. |
| **Hambatan Minor (1-2 item non-kritis)** | **CONDITIONAL GO** | Sprint jalan; item yang tertunda wajib beres dalam waktu < 24 jam. |
| **Hambatan Kritis (Desain / API / Infra belum siap)** | **NO-GO** | Sprint ditunda. Tim kembali ke sesi perancangan teknis. |

### Aturan Mutlak NO-GO (Automatic Blockers)
Sprint dilarang dimulai jika salah satu kondisi berikut terjadi:
1. Kontrak API antara frontend dan backend belum disepakati.
2. Mockup UI kondisi Error dan Loading belum tersedia.
3. Kredensial lingkungan sandbox atau akses repositori belum aktif untuk seluruh engineer.
4. Tiket backlog belum memiliki Acceptance Criteria terstruktur.

---

## Rencana Mitigasi Cepat (Fast Remediation)
- **Kontrak API Belum Selesai**:
  Luangkan sesi sync 60 menit frontend-backend. Sepakati file schema JSON mentah sebelum mulai menulis kode controller.
- **Desain UI Belum Selesai**:
  Hapus tiket UI tersebut dari sprint berjalan. Ganti dengan tiket backend atau tugas refactoring yang siap dikerjakan.
- **Setup Lokal Gagal di Mesin Developer**:
  Gunakan Docker Compose untuk membungkus database dan dependency server agar lingkungan terisolasi seragam.
- **Sprint Goal Terlalu Ambisius**:
  Keluarkan story berprioritas rendah hingga kapasitas tim kembali ke batas aman 80% dari total kecepatan normal.