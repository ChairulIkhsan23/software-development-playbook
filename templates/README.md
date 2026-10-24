# Direktori Dokumen Template

Kumpulan template standar siap pakai (*ready-to-copy*) untuk memfasilitasi pendokumentasian berkualitas tinggi dan konsisten di setiap fase siklus hidup pengembangan perangkat lunak (SDLC).

Setiap template dirancang dengan memisahkan instruksi pengisian dari konten sebenarnya, menyertakan placeholder terstandarisasi, dan menandai elemen wajib vs opsional agar mudah disesuaikan dengan skala proyek Anda.

---

## 1. Taksonomi Template Berdasarkan Fase

```
templates/
├── project/       → Inisiasi, discovery, piagam proyek, dan analisis risiko
├── product/       → PRD, kebutuhan fungsional, user story, use case, dan business rules
├── design/        → Arsitektur sistem, ADR, database, API, UX/UI, NFR, dan keamanan
├── development/   → Panduan setup developer, standar kode, dan checklist code review
├── testing/       → Test plan, test cases, bug reports, UAT checklist, DoR, dan DoD
├── delivery/      → Runbook operasi, release plan, panduan deployment, dan checklist backup
└── governance/    → Decision log, change log, matriks keterlacakan (RTM), dan glossary
```

---

## 2. Katalog Lengkap Template

### 2.1 Manajemen Proyek (`project/`)
- [`project/project-charter.md`](./project/project-charter.md) — Piagam proyek formal untuk otorisasi sponsor, batasan scope, milestone, dan alokasi sumber daya.
- [`project/discovery-notes.md`](./project/discovery-notes.md) — Format pencatatan sesi penemuan masalah, analisis 5-Whys, dan observasi persona.
- [`project/project-plan.md`](./project/project-plan.md) — Perencanaan dekomposisi WBS, estimasi jadwal sprint, dan pemetaan dependensi jalur kritis.
- [`project/risk-register.md`](./project/risk-register.md) — Log penilaian probabilitas & dampak risiko proyek beserta rencana mitigasi dan kontinjensi.
- [`project/stakeholder-analysis.md`](./project/stakeholder-analysis.md) — Pemetaan kuadran Power-Interest Grid, matriks RACI, dan rencana komunikasi berkala.

### 2.2 Kebutuhan Produk (`product/`)
- [`product/prd-template.md`](./product/prd-template.md) — *Product Requirements Document* lengkap mencakup problem statement, OKRs, persona, scope, dan kriteria rilis.
- [`product/functional-requirements-template.md`](./product/functional-requirements-template.md) — Katalog spesifikasi kebutuhan fungsional bernomor (`FR-MOD-NNN`) dengan skema prioritas MoSCoW.
- [`product/user-story-template.md`](./product/user-story-template.md) — Kartu user story standar INVEST dilengkapi skenario Acceptance Criteria berbasis Gherkin (Given-When-Then).
- [`product/use-case-template.md`](./product/use-case-template.md) — Rincian interaksi aktor dan sistem, alur utama keberhasilan (*happy path*), alur alternatif, dan alur eksepsi.
- [`product/business-rules-template.md`](./product/business-rules-template.md) — Katalog aturan bisnis formal (`BR-MOD-NNN`) dengan format logika IF-THEN-ELSE dan level penegakan.
- [`product/acceptance-criteria-template.md`](./product/acceptance-criteria-template.md) — Format penulisan kriteria penerimaan berbasis skenario BDD maupun checklist verifikasi.

### 2.3 Desain & Arsitektur Teknis (`design/`)
- [`design/adr-template.md`](./design/adr-template.md) — *Architecture Decision Record* (`ADR-NNN`) untuk merekam konteks, opsi alternatif, dan trade-off keputusan teknis.
- [`design/system-architecture-template.md`](./design/system-architecture-template.md) — Dokumen arsitektur sistem berbasis C4 model (Context & Container), matriks teknologi, dan pola komunikasi.
- [`design/database-specification-template.md`](./design/database-specification-template.md) — Spesifikasi skema database relasional/NoSQL, diagram ERD Mermaid, indeks, dan strategi partisi.
- [`design/data-dictionary-template.md`](./design/data-dictionary-template.md) — Kamus data terperinci per tabel/koleksi mencakup tipe data, batas null, relasi FK, dan klasifikasi privasi data.
- [`design/api-specification-template.md`](./design/api-specification-template.md) — Kontrak endpoint RESTful API lengkap dengan payload request, response sukses, dan format error RFC 7807.
- [`design/nfr-template.md`](./design/nfr-template.md) — Format spesifikasi persyaratan non-fungsional kuantitatif (latensi, uptime, keamanan, pemulihan bencana).
- [`design/security-privacy-template.md`](./design/security-privacy-template.md) — Penilaian ancaman keamanan (STRIDE), klasifikasi data, dan pemenuhan regulasi privasi (UU PDP / GDPR).
- [`design/ux-ui-specification-template.md`](./design/ux-ui-specification-template.md) — Dokumen serah-terima desain UI/UX mencakup design tokens, grid, breakpoints, status interaksi komponen, dan microcopy.

### 2.4 Praktik Pengembangan (`development/`)
- [`development/developer-guide-template.md`](./development/developer-guide-template.md) — Panduan onboarding setup lokal bagi developer baru (target setup < 2 jam).
- [`development/coding-standards-template.md`](./development/coding-standards-template.md) — Konvensi penulisan kode, aturan linting, casing identifier, dan batasan kompleksitas fungsi.
- [`development/code-review-checklist.md`](./development/code-review-checklist.md) — Checklist verifikasi Pull Request untuk author dan reviewer mencakup fungsionalitas, keamanan, dan pengujian.

### 2.5 Jaminan Kualitas & Pengujian (`testing/`)
- [`testing/test-plan-template.md`](./testing/test-plan-template.md) — Rencana induk pengujian piramida uji, manajemen lingkungan, data uji, dan jadwal QA.
- [`testing/test-case-template.md`](./testing/test-case-template.md) — Kartu uji terinci (`TC-MOD-NNN`) dengan prasyarat, data masukan, dan langkah eksekusi deterministik.
- [`testing/bug-report-template.md`](./testing/bug-report-template.md) — Format pelaporan cacat perangkat lunak (`BUG-NNN`) dengan matriks SEV1–SEV4, langkah reproduksi, dan log bukti.
- [`testing/acceptance-checklist.md`](./testing/acceptance-checklist.md) — Lembar verifikasi User Acceptance Testing (UAT) sebelum serah terima fitur ke stakeholder.
- [`testing/definition-of-ready.md`](./testing/definition-of-ready.md) — Syarat kelayakan tiket sebelum dapat dimasukkan ke dalam sprint backlog pengerjaan dev.
- [`testing/definition-of-done.md`](./testing/definition-of-done.md) — Kriteria ketuntasan absolut sebelum suatu fitur dinyatakan selesai dan siap dirilis.

### 2.6 Operasional & Pengiriman (`delivery/`)
- [`delivery/release-plan-template.md`](./delivery/release-plan-template.md) — Rencana eksekusi rilis SemVer mencakup *run-of-show* timeline menit demi menit dan kriteria Go/No-Go.
- [`delivery/deployment-guide-template.md`](./delivery/deployment-guide-template.md) — Panduan prosedur deployment otomatis via CI/CD maupun manual fallback beserta langkah verifikasi *smoke test*.
- [`delivery/operations-runbook-template.md`](./delivery/operations-runbook-template.md) — Prosedur standar operasional (SOP) on-call, penanganan insiden CPU/memory spike, dan pemulihan dependensi.
- [`delivery/backup-recovery-checklist.md`](./delivery/backup-recovery-checklist.md) — Checklist kepatuhan jadwal pencadangan data 3-2-1 dan log simulasi pemulihan bencana (*DR drill*).

### 2.7 Tata Kelola Proyek (`governance/`)
- [`governance/decision-log-template.md`](./governance/decision-log-template.md) — Buku catatan keputusan bisnis, ruang lingkup, dan proses untuk mencegah pembahasan ulang isu lama.
- [`governance/change-log-template.md`](./governance/change-log-template.md) — Log permohonan perubahan baseline proyek (*Change Request*) mencakup analisis dampak biaya dan jadwal.
- [`governance/traceability-matrix-template.md`](./governance/traceability-matrix-template.md) — Matriks keterlacakan dua arah dari tujuan bisnis hingga baris kode dan kasus uji.
- [`governance/glossary-template.md`](./governance/glossary-template.md) — Kamus istilah domain bisnis proyek untuk menyamakan persepsi antar tim.

---

## 3. Aturan Penggunaan Template

1. **Salin ke Repositori Proyek**: Jangan mengedit template asli di playbook ini. Salin berkas yang relevan ke dalam folder dokumentasi proyek Anda (misalnya ke folder `docs/` atau folder kerja Anda).
2. **Ganti Seluruh Placeholder**: Ganti seluruh teks di dalam kurung siku `[ ... ]` dengan data aktual proyek Anda.
3. **Perhatikan Penanda Status Field**:
   - `[Wajib]`: Harus diisi untuk semua jenis proyek demi menjaga kejelasan minimum.
   - `[Opsional]`: Dapat diisi jika relevan atau dihapus jika tidak dibutuhkan.
   - `[Kondisional]`: Wajib diisi hanya jika kondisi prasyarat tertentu terpenuhi (misal: ada komponen pihak ketiga).
4. **Hapus Blok Panduan**: Setelah dokumen selesai diisi, hapus blok kutipan `> **Panduan Pengisian:**` agar dokumen proyek menjadi bersih dan profesional.
5. **Sesuaikan dengan Skala**: Proyek berukuran kecil (solo developer / prototype) cukup menggunakan subset template minimum: *Project Charter ringkas, PRD ringkas, ADR penting, dan Developer Guide*.
