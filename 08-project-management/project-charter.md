# Project Charter

Dokumen otorisasi formal proyek. Beri mandat legal, batas scope, komitmen sumber daya dari sponsor sebelum tim tulis baris kode pertama.

---

## 1. Tujuan & Nilai Inti

Tanpa charter, proyek rentan menjadi zombie project: jalan tanpa arah jelas, scope membengkak liar, sponsor cuci tangan saat krisis datang.

Fungsi utama:
- Otorisasi resmi Project Manager / Tech Lead untuk alokasikan budget, server, dan developer.
- Menyamakan ekspektasi sponsor bisnis, stakeholder operasional, dan tim engineering.
- Menetapkan pagar pembatas scope: apa yang dikerjakan vs apa yang tegas ditolak.
- Menjadi baseline referensi saat evaluasi Change Request di kemudian hari.

---

## 2. Struktur Esensial Project Charter

Dokumen formal memuat sembilan elemen kunci yang saling terikat:

```
[Problem & Justification] -> [SMART Objectives] -> [Scope Boundaries]
           │                        │                     │
           ▼                        ▼                     ▼
[Key Deliverables]        -> [High-level Schedule] -> [Budget & Resource]
           │                        │                     │
           ▼                        ▼                     ▼
[Key Stakeholders]        -> [Sponsor Sign-off]    -> [Risks & Assumptions]
```

### 2.1 Project Purpose & Justification
Jelaskan masalah bisnis riil dan akar penyebab. Sertakan metrik kerugian saat ini atau peluang pasar yang hilang jika proyek tidak jalan.
- **Buruk**: "Membangun sistem pembayaran modern berbasis AI cloud."
- **Baik**: "Checkout drop-off rate mencapai 42% karena gateway lambat (P95 > 2.8s) dan sering timeout. Revamp payment gateway bertujuan menurunkan drop-off ke < 18% dan menaikkan conversion rate 15% pada Q4."

### 2.2 SMART Objectives
Target wajib spesifik, terukur, dan memiliki tenggat waktu tegas:
- **Specific**: Reduksi latency API payment gateway dan otomatisasi rekonsiliasi settlement bank.
- **Measurable**: P95 latency endpoint checkout < 350ms, error rate < 0.1%, throughput minimal 500 TPS.
- **Achievable**: Menggunakan arsitektur event-driven async queue (Redis Streams) dan integrasi direct bank API.
- **Relevant**: Terhubung langsung dengan OKR perusahaan: menaikkan gross revenue bulanan sebesar 20%.
- **Time-bound**: Go-live production dalam 10 minggu kalender sejak tanggal approval charter.

### 2.3 Scope Boundaries (In-Scope vs Out-of-Scope)
Batasan tegas mencegah perselisihan saat development berjalan.
- **In-Scope**:
  - Direct integration Virtual Account BCA, Mandiri, BRI.
  - Dynamic QRIS generation via Snap API.
  - Webhook callback handler dengan retry worker berjangka.
  - Admin dashboard untuk manual settlement dispute.
- **Out-of-Scope**:
  - Integrasi kartu kredit internasional dan PayLater (masuk Phase 2).
  - Multi-currency conversion (USD/SGD).
  - Fitur loyalti atau point reward split payment.

### 2.4 Key Deliverables
Output nyata yang dapat diuji dan diverifikasi:
- Repositori backend service pembayaran dengan unit test coverage > 80%.
- Dashboard observability (Grafana + Prometheus) untuk tracking TPS dan HTTP 5xx.
- OpenAPI / Swagger spec v3.0 lengkap dengan schema validasi request/response.
- Runbook deploy, migration scripts database, dan Disaster Recovery runbook.

### 2.5 Milestones & High-Level Schedule
Target titik periksa utama (checkpoint):
- **M1 (Minggu 2)**: Architecture review disahkan, PRD ditandatangani, repo scaffold siap.
- **M2 (Minggu 5)**: Modul core payment lolos integrasi sandbox perbankan.
- **M3 (Minggu 8)**: Internal UAT lolos, penetration test & load test memenuhi target TPS.
- **M4 (Minggu 10)**: Production canary release (10% traffic -> 100% traffic) & handover ke DevOps/Support.

### 2.6 High-Level Budget & Resources
- **Personel**: 1 Tech Lead (100%), 2 Backend Engineer (100%), 1 QA Engineer (50%), 1 DevOps (25%).
- **Infrastruktur Cloud**: Maksimal $900/bulan untuk cluster staging, production database managed, dan Redis.
- **Lisensi & Pihak Ketiga**: $2,500 one-time setup fee vendor agregator pembayaran.

### 2.7 Key Stakeholders & Roles
- **Project Sponsor**: VP of Engineering (penyedia dana & pemutus sengketa tertinggi).
- **Product Owner**: Lead PM Payments (pemilik user story & acceptance criteria).
- **Tech Lead**: Senior Backend Engineer (penanggung jawab arsitektur, SLA sistem, dan jadwal delivery).
- **Operations Lead**: Head of Customer Support (validasi alur refund dan penanganan kendala pengguna).

### 2.8 Project Sponsor & Authorization
Klausul mandat formal:
> "Dengan menandatangani dokumen ini, Sponsor memberikan mandat penuh kepada Tech Lead dan PM untuk menggunakan sumber daya, infrastruktur, dan anggaran yang tercantum guna mengeksekusi proyek sesuai ruang lingkup."

### 2.9 Initial Risks & Assumptions
- **Asumsi**: Bank mitra merespon permohonan kredensial sandbox maksimal 5 hari kerja setelah request diajukan.
- **Risiko Awal**: API sandbox bank sering maintenance mendadak. Mitigasi: buat in-memory mock server sejak hari pertama sprint.

---

## 3. Scaling Charter: Solo Dev vs Small Team vs Enterprise

| Dimensi | Solo Dev / Side Project | Small Team (3-8 Orang) | Client / Enterprise |
|---|---|---|---|
| **Format** | 1 Halaman Markdown (`CHARTER.md`) | 2-3 Halaman Markdown/Notion | 5-15 Halaman Dokumen Legal PDF |
| **Penyusun** | Developer sendiri | Tech Lead + PM | PM + Solution Architect + Legal |
| **Sponsor** | Diri sendiri / Co-founder | Head of Engineering / CPO | VP / Client Executive Sponsor |
| **Approval** | Self-commitment checklist | Review 30 menit + approval PR | Tanda tangan basah / DocuSign resmi |
| **Kekakuan Scope** | Fleksibel sesuai iterasi | Terkendali lewat Change Request ringkas | Sangat ketat, perubahan wajib addendum kontrak |
| **Review Cycle** | Sekali di awal proyek | Awal sprint / evaluasi milestone | Formal Stage-Gate steering committee |

<!-- ponytail: Gunakan format markdown 1-page untuk tim internal < 8 orang; naikkan ke dokumen legal formal bila melibatkan pihak ketiga atau budget > $50k. -->

---

## 4. Contoh Ringkas 1-Page Project Charter

Template kilat untuk tim kecil atau solo engineer:

```markdown
# CHARTER: Payment Engine v2
- **Sponsor**: VP Engineering | **Lead**: Lead Backend Engineer
- **Problem**: 42% transaksi drop akibat timeout gateway lama (>2.8s).
- **Target**: P95 latency < 350ms, success rate > 99.8%, rilis 10 minggu.
- **In-Scope**: VA BCA/Mandiri, Snap QRIS, webhook retry queue.
- **Out-of-Scope**: PayLater, kartu kredit internasional, loyalty points.
- **Milestones**: M1 (W2: Arch), M2 (W5: Core Sandbox), M3 (W8: UAT), M4 (W10: Prod).
- **Budget**: $900/bulan cloud + $2,500 vendor setup.
- **Risiko Utama**: Sandbox bank lambat; mitigasi: mock server dev lokal.
- **Approval**: [Approved by Sponsor - 2026-10-25]
```

---

## 5. Sign-Off & Approval Process

Jangan izinkan sprint development berjalan sebelum proses otorisasi tuntas:

1. **Drafting**: Tech Lead dan PM menyusun draf charter awal berdasarkan business requirements dan discovery notes.
2. **Asynchronous Review**: Draf dibagikan ke stakeholder via Git Pull Request atau shared workspace. Beri batas waktu komentar 48 jam kerja.
3. **Alignment Meeting**: Rapat fokus 30 menit hanya untuk menyelesaikan konflik (khususnya batasan out-of-scope dan alokasi orang).
4. **Formal Sign-off**: Sponsor menyetujui via komentar tertulis "Approved", e-signature, atau PR merge approval.
5. **Baseline Freeze**: File charter disimpan di repositori proyek (`docs/project-charter.md`) dan dikunci sebagai baseline resmi.

---

## 6. Pre-Sign-Off Verification Checklist

Pastikan seluruh pertanyaan ini terjawab sebelum charter disahkan:
- [ ] Apakah masalah bisnis didukung data kuantitatif, bukan sekadar opini subjektif?
- [ ] Apakah daftar Out-of-Scope sudah memuat fitur-fitur berisiko yang sering diminta stakeholder?
- [ ] Apakah target SMART realistis terhadap jumlah developer dan timeline yang tersedia?
- [ ] Apakah Tech Lead dan tim engineering sudah mereview estimasi teknis awal?
- [ ] Apakah sponsor memiliki otoritas finansial riil atas budget yang diajukan?
- [ ] Apakah seluruh asumsi eksternal memiliki penanggung jawab tindak lanjut?

---

## 7. Pemicu Terminasi & Eskalasi Charter (Kill Triggers)

Proyek dilarang diteruskan tanpa evaluasi ulang bila terjadi kondisi batas:
- Sponsor menarik alokasi engineer inti lebih dari 50%.
- Regulasi perbankan melarang arsitektur direct API yang dirancang.
- Vendor pihak ketiga menaikkan biaya lisensi setup melebihi 2x total anggaran proyek.
- Deviasi milestone M1 atau M2 terlambat lebih dari 3 minggu berturut-turut.

---

## 8. Common Mistakes

- **Dokumen Terlalu Tebal**: Menyusun charter 40 halaman yang tidak pernah dibaca. Pertahankan keringkasannya.
- **Out-of-Scope Kosong**: Menyerahkan batasan pada asumsi verbal. Hasilnya adalah scope creep instan dari tim bisnis.
- **Target Tanpa Metrik**: Menulis tujuan kabur seperti "membuat sistem cepat". Selalu definisikan angka P95/P99 latency.
- **Sponsor Bayangan**: Menjalankan proyek tanpa sponsor yang punya kuasa anggaran. Tim akan mandek saat butuh persetujuan server.
- **Dokumen Sekali Pakai**: Menyimpan charter di folder lokal lalu melupakannya. Jadikan charter acuan saat menolak permintaan fitur baru di tengah sprint.

---

## 9. Related Docs & Templates

- Template panduan: [Project Charter Template](../templates/project/project-charter.md)
- Perencanaan detail: [Project Planning](./project-planning.md)
- Log keputusan arsitektur/bisnis: [Decision Log](./decision-log.md)
- Tata kelola perubahan scope: [Change Log](./change-log.md)

---
*skipped: multi-tiered steering committee governance; add when enterprise procurement mandates multi-department approval.*