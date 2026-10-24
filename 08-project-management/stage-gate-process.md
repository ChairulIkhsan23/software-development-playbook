# Stage-Gate Process

Kerangka kerja tata kelola bertahap untuk memastikan kualitas, kelayakan teknis, dan kesiapan operasional sebelum melangkah ke fase berikutnya.

---

## 1. Konsep Dasar & Nilai Inti

Stage-Gate Process adalah mekanisme pertahanan engineering untuk mencegah tim menghabiskan ratusan jam membangun produk yang salah, rapuh, atau tidak siap pakai.

Prinsip kerja utama:
- Pekerjaan dibagi menjadi **Stages** (tahapan eksekusi teknis).
- Setiap stage diakhiri oleh **Gate** (pintu audit kualitas biner).
- Pintu gate dijaga oleh kriteria pintu keluar (*Exit Criteria*). Jika kriteria belum tuntas 100%, proyek dilarang keras melangkah ke stage berikutnya.

```
[Stage 1: Discovery]   ──► [Gate 1] ──(Go)──► [Stage 2: Requirements] ──► [Gate 2] ──(Go)──┐
                                                                                            │
┌───────────────────────────────────────────────────────────────────────────────────────────┘
▼
[Stage 3: Architecture] ──► [Gate 3] ──(Go)──► [Stage 4: Development]  ──► [Gate 4] ──(Go)──┐
                                                                                            │
┌───────────────────────────────────────────────────────────────────────────────────────────┘
▼
[Stage 5: Operations]   ──► [Gate 5] ──(Go)──► [Production Release]
```

---

## 2. Empat Opsi Keputusan di Setiap Gate

Setiap evaluasi gate menghasilkan satu dari empat keputusan biner:

1. **Go (Lanjut)**:
   - Kriteria pintu keluar terpenuhi sepenuhnya tanpa kompromi fatal.
   - Mandat dan sumber daya dialokasikan secara resmi untuk memasuki stage berikutnya.
2. **Kill (Hentikan Permanen)**:
   - Asumsi nilai bisnis terbukti salah, teknologi terbukti tidak layak (*infeasible*), atau biaya membengkak melampaui potensi return.
   - Proyek dihentikan permanen tanpa keraguan untuk memotong kerugian (*cut sunk costs*). Tim dialihkan ke inisiatif bernilai lebih tinggi.
3. **Hold (Tunda Sementara)**:
   - Proyek secara fundamental valid dan layak, namun tertunda karena pergeseran prioritas korporat atau kendala sumber daya mendesak.
   - Tetapkan batas waktu penundaan maksimal (misal: 30 hari). Jangan biarkan proyek menggantung tanpa kejelasan menjadi proyek zombie.
4. **Recycle / Pivot (Revisi Ulang)**:
   - Kriteria belum lengkap atau arah pendekatan arsitektur/produk perlu disesuaikan.
   - Tim kembali ke stage yang sama untuk menuntaskan kekurangan sebelum menjadwalkan review gate ulang.

---

## 3. Matriks Ringkasan 5 Pintu Gerbang

| Pintu Gerbang | Tahapan Stage | Input Utama | Kriteria Pintu Keluar (Exit Criteria) | Pemilik Keputusan |
|---|---|---|---|---|
| **Gate 1** | Discovery & Feasibility | Discovery Notes | Masalah tervalidasi data riil, solusi feasible | Sponsor, PM |
| **Gate 2** | Requirements & PRD | Draf PRD & User Stories | PRD disetujui, MVP scope terkunci, acceptance criteria biner | Product Owner |
| **Gate 3** | Architecture & Design | ADR, ERD, OpenAPI, UI | Arsitektur disetujui, DoR backlog terpenuhi | Tech Lead |
| **Gate 4** | Development & Quality | Git PRs, CI Reports | Zero blocker bugs, tests pass, security scan clean, DoD met | Tech Lead, QA |
| **Gate 5** | Release & Operations | Runbook, Alerts, UAT | Staging deploy dry-run sukses, rollback siap, sign-off bisnis | DevOps, Sponsor |

---

## 4. Penyelarasan DoR (Gate 3) dan DoD (Gate 4)

- **Definition of Ready (DoR - Gate 3)**: Tiket dilarang masuk sprint development jika belum memiliki: (1) Acceptance criteria biner, (2) Desain UI final terhubung, (3) Skema database disetujui, (4) Kontrak payload API jelas.
- **Definition of Done (DoD - Gate 4)**: Tiket dilarang dianggap tuntas jika belum memiliki: (1) Code review disetujui 2 reviewer, (2) Unit test coverage > 80%, (3) Lolos uji staging environment, (4) Dokumentasi README/API diperbarui.

---

## 5. Rincian Komprehensif Lima Pintu Gerbang (The 5 Stage Gates)

### Gate 1: Problem Discovery & Feasibility
- **Objektif**: Memastikan masalah bisnis nyata, target pengguna terdefinisi konkret, dan pendekatan teknis awal layak dieksekusi (*technically feasible*).
- **Required Inputs**: Catatan discovery, hasil wawancara calon pengguna, analisis kompetitor, estimasi kasaran nilai ekonomi awal.
- **Verification Checklist**: Tinjau kelayakan menggunakan [Discovery Readiness Checklist](../checklists/discovery-readiness.md).
- **Exit Criteria**: Masalah terbukti valid secara data kuantitatif, batasan persona pengguna jelas, dan spike arsitektur awal membuktikan solusi memungkinkan.
- **Decision Outcomes**:
  - *Go*: Masalah bernilai tinggi dan solusi feasible -> Masuk ke penyusunan PRD.
  - *Kill*: Kebutuhan pasar tidak ada -> Hentikan eksplorasi segera.
- **Participants & Governance**: Product Manager, Tech Lead, Project Sponsor.

### Gate 2: Requirements & PRD Approval
- **Objektif**: Mengunci spesifikasi kebutuhan fungsional dan non-fungsional, mendefinisikan batasan ruang lingkup MVP, dan menyusun kriteria penerimaan terukur.
- **Required Inputs**: Product Requirement Document (PRD), user stories lengkap, matriks prioritas fitur (MoSCoW/RICE), batasan *Out-of-Scope*.
- **Verification Checklist**: Evaluasi mutu via [PRD Quality Checklist](../checklists/prd-quality.md) dan [Requirements Quality Checklist](../checklists/requirements-quality.md).
- **Exit Criteria**: PRD ditandatangani bersama oleh Sponsor dan Tech Lead; tidak ada ambiguitas logika pada acceptance criteria.
- **Decision Outcomes**:
  - *Go*: Cakupan MVP disepakati dan dikunci -> Masuk ke perancangan arsitektur & desain UI.
  - *Recycle*: PRD masih menyisakan banyak asumsi abu-abu -> Revisi user stories.
- **Participants & Governance**: Product Owner, Tech Lead, UI/UX Designer, QA Lead.

### Gate 3: Architecture & Design Readiness
- **Objektif**: Memvalidasi kesiapan teknis, rancangan skema database, kontrak API, desain antarmuka, dan pemenuhan Definition of Ready (DoR).
- **Required Inputs**: System Architecture Document, skema database (ERD), OpenAPI spec, clickable prototype Figma, Architectural Decision Records (ADR).
- **Verification Checklist**: Audit kesiapan pra-coding via [Pre-Development Readiness Checklist](../checklists/pre-development-readiness.md).
- **Exit Criteria**: Seluruh spike teknis lolos verifikasi, DoR terpenuhi untuk backlog sprint pertama, kontrak API disetujui frontend dan backend developer.
- **Decision Outcomes**:
  - *Go*: Fondasi desain & arsitektur matang -> Tim mulai menulis baris kode pertama.
  - *Hold*: Menunggu kesiapan infrastruktur sandbox pihak ketiga -> Tahan sprint dev.
- **Participants & Governance**: Software Architect, Tech Lead, Senior Engineers, UI/UX Lead.

### Gate 4: Development & Quality Verification
- **Objektif**: Memastikan seluruh kode selesai diimplementasikan, test suite lolos otomatis, celah keamanan bersih, dan Definition of Done (DoD) tercapai.
- **Required Inputs**: Repositori kode, test reports (unit, integration, E2E), security vulnerability scan report (SAST/DAST), staging environment URL.
- **Verification Checklist**: Jalankan [Code Review Checklist](../checklists/code-review.md), [Testing Readiness Checklist](../checklists/testing-readiness.md), dan [Security Review Checklist](../checklists/security-review.md).
- **Exit Criteria**: Seluruh acceptance criteria lolos verifikasi QA, zero critical/high security vulnerability, coverage target tercapai, DoD terpenuhi 100%.
- **Decision Outcomes**:
  - *Go*: Kualitas kode stabil dan aman -> Masuk fase persiapan deployment release.
  - *Recycle*: Ditemukan critical security flaw atau bug regresi -> Blokir rilis, buka sprint bugfix.
- **Participants & Governance**: Tech Lead, Backend & Frontend Devs, QA Engineers, Security Specialist.

### Gate 5: Release Readiness & Operations
- **Objektif**: Memverifikasi kesiapan operasional infrastruktur, konfigurasi monitoring production, prosedur mitigasi rollback darurat, dan izin rilis akhir.
- **Required Inputs**: Deployment runbook, monitoring alert triggers (Prometheus/Grafana/Datadog), rollback plan teruji, persetujuan UAT stakeholder bisnis.
- **Verification Checklist**: Evaluasi kesiapan akhir via [Release Readiness Checklist](../checklists/release-readiness.md) dan kesiapan pemeliharaan [Maintenance Review Checklist](../checklists/maintenance-review.md).
- **Exit Criteria**: Uji dry-run deploy sukses di staging, dashboard alert aktif, tim support terlatih menangani tiket kendala, persetujuan rilis ditandatangani.
- **Decision Outcomes**:
  - *Go*: Rollout production (canary 10% -> 100% traffic).
  - *Hold*: Alert monitoring belum merespon anomali -> Perbaiki konfigurasi telemetry sebelum go-live.
- **Participants & Governance**: DevOps / SRE Lead, Tech Lead, Product Manager, Head of Operations, Project Sponsor.

---

## 6. Scaling Stage-Gate: Solo Dev vs Small Team vs Enterprise

| Dimensi | Solo Developer | Small Team (3-8 Orang) | Enterprise Organization |
|---|---|---|---|
| **Mekanisme Audit** | Self-audit checklist terstandar di Git PR | Pertemuan review 30 menit per gate | Formal Stage-Gate Steering Review |
| **Dokumentasi** | File markdown ringkas di repo (`docs/`) | Pull Request review + link Figma & PRD | Dokumen kepatuhan formal + tiket Jira terikat |
| **Pengambil Keputusan** | Developer mandiri / Klien langsung | Tech Lead + Product Owner bersama-sama | Komite lintas divisi (Engineering, Sec, Ops, C-Level) |
| **Toleransi Formalitas** | Ringan: fokus pada kualitas checklist | Seimbang: audit cepat tanpa birokrasi berbelit | Sangat ketat: audit trail hukum & kepatuhan regulasi |
| **Cadence Review** | Mandiri saat deliverable tuntas | Di akhir sprint perencanaan/eksekusi | Sesuai kalender tata kelola korporat bulanan |

<!-- ponytail: Untuk tim < 5 orang, gabungkan Gate 1 dan Gate 2 dalam satu sesi evaluasi PRD ringkas; jangan pernah kompromi atau melompati Gate 4 dan Gate 5. -->

---

## 7. Panduan Self-Audit Mandiri untuk Solo Developer

Bagi solo developer, Stage-Gate adalah jangkar disiplin diri agar tidak langsung coding secara serampangan:
1. **Buat Checkpoints di Repositori**: Buat issue template untuk setiap Gate (Gate 1 hingga Gate 5).
2. **Kunci Kriteria Keluar Sebelum Coding**: Tuliskan acceptance criteria di issue tracker sebelum membuat branch fitur baru.
3. **Mandatory Staging Smoke Test**: Jangan pernah deploy langsung ke production server tanpa melewati pengujian dry-run di staging env.

---

## 8. Common Mistakes

- **Gerbang Formalitas Kosong (Rubber Stamping)**: Melewatkan proyek ke tahap coding padahal PRD belum jelas hanya demi mengejar ilusi kecepatan semu.
- **Melewati Gate Tanpa Checklist**: Menilai kelayakan berbasis perasaan (*vibes*), bukan data checklist yang dapat diverifikasi secara objektif.
- **Takut Mengambil Keputusan "Kill"**: Memaksakan proyek bermasalah terus berjalan karena sudah terlanjur keluar biaya (*sunk cost fallacy*).
- **Mengabaikan Operasional di Gate 5**: Rilis software ke production tanpa runbook rollback dan tanpa dashboard pemantau error rate.
- **Birokrasi Berlebihan**: Memperlakukan tim kecil seperti instansi militer dengan 20 tanda tangan untuk perubahan kecil. Skalakan gerbang sesuai risiko.

---

## 9. Related Checklists & Templates

- Validasi awal masalah: [Discovery Readiness Checklist](../checklists/discovery-readiness.md)
- Kualitas kebutuhan spesifikasi: [PRD Quality](../checklists/prd-quality.md) & [Requirements Quality](../checklists/requirements-quality.md)
- Kesiapan pra-development: [Pre-Development Readiness Checklist](../checklists/pre-development-readiness.md)
- Verifikasi kode & testing: [Code Review](../checklists/code-review.md), [Testing Readiness](../checklists/testing-readiness.md), & [Security Review](../checklists/security-review.md)
- Kesiapan rilis & operasional: [Release Readiness](../checklists/release-readiness.md) & [Maintenance Review](../checklists/maintenance-review.md)
- Otorisasi dan perencanaan: [Project Charter](./project-charter.md) & [Project Planning](./project-planning.md)

---
*skipped: multi-committee physical voting assemblies; add when governmental aerospace or medical device regulatory approvals required.*