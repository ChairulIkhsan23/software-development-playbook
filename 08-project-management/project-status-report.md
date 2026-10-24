# Project Status Report

Panduan penyusunan laporan status berkala proyek perangkat lunak untuk menjaga transparansi dan menyelaraskan tim engineering dengan pemangku kepentingan bisnis.

---

## 1. Prinsip Utama Laporan Status Efektif

Status report bukan sekadar daftar absensi kerja atau kumpulan istilah teknis tak terpahami.

Empat pilar laporan status bernilai tinggi:
1. **Radical Transparency (Transparansi Penuh)**: Sampaikan fakta apa adanya. Menyembunyikan bug kritikal atau keterlambatan hanya akan memicu bencana saat rilis production.
2. **No Surprises (Nol Kejutan)**: Masalah genting harus segera dieskalasikan saat terdeteksi, bukan pertama kali diumumkan saat rapat status bulanan di depan C-level.
3. **Executive-Friendly (Ramah Eksekutif)**: Tempatkan ringkasan inti di bagian paling atas (TL;DR). Gunakan bahasa dampak bisnis, bukan log stack trace teknis.
4. **Forward-Looking (Fokus Masa Depan)**: Evaluasi masa lalu secukupnya untuk konteks; habiskan 70% laporan untuk fokus milestone berikutnya, mitigasi risiko, dan pembersihan blocker.

---

## 2. Kriteria & Definisi RAG Status (Red, Amber, Green)

Status kesehatan proyek wajib ditentukan berdasarkan kriteria objektif terukur, bukan perasaan (*gut feeling*):

```
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│   🟢 GREEN       │    │   🟡 AMBER       │    │    🔴 RED        │
│ On Track         │    │ At Risk          │    │ Off Track        │
│ Variansi < 5%    │    │ Variansi 5-15%   │    │ Variansi > 15%   │
│ Tidak ada blocker│    │ Butuh mitigasi   │    │ Butuh intervensi │
└──────────────────┘    └──────────────────┘    └──────────────────┘
```

### 🟢 Green (On Track)
- Proyek berjalan sesuai baseline jadwal, anggaran, dan ruang lingkup.
- Deviasi milestone < 5% dari jadwal rencana.
- Tidak ada risiko berkategori *Critical* yang belum memiliki mitigasi aktif.
- Tim memiliki jalur jelas (*clear path*) menuju penyelesaian sprint.

### 🟡 Amber (At Risk)
- Terdapat deviasi jadwal atau anggaran antara 5% hingga 15%.
- Muncul risiko *High* atau dependensi eksternal yang mulai tersendat (misal: review API perbankan terlambat 3 hari).
- Masih dapat dipulihkan (*recoverable*) oleh tim internal tanpa memundurkan tanggal rilis final atau tanpa menambah budget resmi, asalkan aksi mitigasi dieksekusi segera.

### 🔴 Red (Off Track)
- Deviasi jadwal atau anggaran > 15% dari baseline awal.
- Terdapat blocker kritikal yang menghentikan progres seluruh tim dev (misal: lisensi ditolak, sistem target down berkepanjangan).
- Tanggal rilis publik atau batas kontrak PASTI terlewati kecuali ada intervensi manajemen tingkat atas (seperti pemotongan scope fitur atau penambahan resource).

---

## 3. Ambang Batas Metrik Teknis RAG

| Metrik Kesehatan | 🟢 Green | 🟡 Amber | 🔴 Red |
|---|---|---|---|
| **Sprint Velocity Drift** | $\le 10\%$ dari target | $11\% - 25\%$ dari target | $> 25\%$ dari target |
| **Defect Severity 1 (Blocker)** | 0 open defects | 1 open defect (in-progress) | $> 1$ open defects aktif |
| **Milestone Variance** | $\le 2$ hari kerja | $3 - 7$ hari kerja | $> 7$ hari kerja |
| **Budget Burn Variance** | $\pm 5\%$ dari rencana | $\pm 6\% - 15\%$ dari rencana | $> 15\%$ over budget |

---

## 4. Komponen Standar Laporan Status

Laporan berkala harus ringkas dan memuat enam blok informasi wajib:

### 4.1 Overall Health Status (RAG)
Indikator visual warna (Green/Amber/Red) disertai tren pergerakan (Membaik ↗, Stabil →, Memburuk ↘).

### 4.2 Executive Summary (Ringkasan Eksekutif)
Satu paragraf padat (maksimal 3-4 kalimat):
> *"Proyek Checkout Revamp berstatus 🟡 AMBER minggu ini karena keterlambatan verifikasi sandbox bank mitra. Pembangunan core logic internal selesai 100%. Tim mengaktifkan mock server untuk menjaga jadwal sprint frontend. Diperlukan eskalasi dari Sponsor ke representatif bank mitra guna membuka akses staging."*

### 4.3 Key Achievements This Period (Pencapaian Periode Ini)
Daftar 3-5 poin deliverable nyata yang selesai diuji (*Done*):
- Selesai implementasi endpoint create QRIS dinamis dan webhook callback.
- Unit test coverage modul payment mencapai 88% di pipeline CI/CD.
- Lolos audit kepatuhan keamanan internal tahap awal (zero critical vulnerability).

### 4.4 Planned Milestones Next Period (Fokus Periode Berikutnya)
Komitmen deliverable untuk sprint mendatang:
- Menyelesaikan end-to-end testing integrasi Virtual Account BCA.
- Setup dashboard monitoring latency Grafana & alert Telegram.
- Menggelar sesi demo staging bersama tim Operations.

### 4.5 Critical Risks & Blockers Requiring Escalation (Blocker & Bantuan)
Hanya sebutkan isu yang membutuhkan intervensi manajemen atau pihak luar:
- **Blocker**: Akses kredensial sandbox staging Bank X belum diterbitkan (Tertunda 5 hari).
- **Ask to Sponsor**: Mengirimkan email resmi antar-pimpinan ke Direksi Vendor Pembayaran untuk percepatan perizinan.

### 4.6 Budget & Timeline Variance (Variansi Biaya & Waktu)
- **Timeline**: Baseline target 15 Nov 2026 | Proyeksi terkini: 18 Nov 2026 (+3 hari kalender).
- **Budget**: Anggaran $5,000 | Pengeluaran aktual: $3,200 (Sesuai alokasi).

---

## 5. Format Cepat: 1-Page Markdown Template

Salin template ringkas ini untuk update status mingguan:

```markdown
# [Project Name] Status Report - [YYYY-MM-DD]
**Health**: 🟢 Green / 🟡 Amber / 🔴 Red (Trend: →) | **Lead**: [Nama Lead]

### 1. Executive Summary
[Ringkasan 2-3 kalimat kondisi terkini dan dampak bisnis]

### 2. Completed This Period
- [Deliverable 1] (PR #123 merged, test pass)
- [Deliverable 2] (Deployed to staging)

### 3. Next Period Focus
- [Milestone A] (Target: YYYY-MM-DD)
- [Milestone B] (Target: YYYY-MM-DD)

### 4. Blockers & Help Needed (Asks)
- **Blocker**: [Deskripsi blocker]
- **Action Needed**: [Aksi yang dibutuhkan dari sponsor/manajemen]
```

### Pola Komunikasi Cepat (Slack/Chat):
- **Eskalasi Status 🟡 AMBER**:
  > "🚨 **Update Proyek Checkout (🟡 AMBER)**: Core API tuntas, sandbox Bank Mandiri telat 4 hari. Frontend pakai mock server. **Ask ke @VP-Eng**: Kontak Account Manager Bank Mandiri untuk approve tiket #4092."
- **Eskalasi Status 🔴 RED**:
  > "🛑 **CRITICAL ALERT (🔴 RED)**: Sertifikasi PCI-DSS audit menemukan celah pada enkripsi token logging. Rilis M4 tanggal 10 Nov terblokir. **Ask**: Jadwalkan emergency meeting 30 menit hari ini pukul 14:00."

---

## 6. Adaptasi Kadensi & Audiens Laporan

| Level Laporan | Frekuensi | Audiens Target | Format & Saluran Ideal |
|---|---|---|---|
| **Weekly Team Pulse** | Setiap akhir pekan (Jumat) | Developer, QA, Designer, PO | Pesan ringkas Slack/Discord/Teams atau PR description |
| **Bi-Weekly Stakeholder Summary** | Dua mingguan (per Sprint) | Head of Engineering, Product Lead, Klien | Dokumen Markdown di repo atau Notion page (1-2 halaman) |
| **Monthly Executive Dashboard** | Bulanan | C-Level, VP, Project Sponsor | Presentasi 3 slide visual atau ringkasan email satu layar |

<!-- ponytail: Gunakan pesan markdown asinkron di chat atau repositori; hindari membuat slide presentasi PDF kecuali diminta khusus oleh komite audit eksekutif. -->

---

## 7. Asynchronous Reporting Tools & Best Practices

Hindari rapat tatap muka 1 jam hanya untuk membacakan slide laporan status:
- **Automated Git Pulse**: Gunakan GitHub Project Insights atau Linear Cycle Dashboard untuk visualisasi burndown otomatis.
- **Async Standup Bot**: Manfaatkan Slack bot (Geekbot, Standuply) untuk mengumpulkan progres mingguan tanpa interupsi fokus coding.
- **Single Source of Truth**: Publikasikan status report di repositori kode bersama dokumen arsitektur (`docs/status-reports/YYYY-WW.md`).

---

## 8. Panduan Pengumpulan Data Metrik (Telemetry)

Otomatiskan pengambilan metrik laporan dari pipeline CI/CD dan tracker:
- **Velocity & Burndown**: Ekspor otomatis dari GitHub Projects API atau Linear GraphQL webhook.
- **Defects & Bug Count**: Filter tiket dengan label `bug` dan `severity:p1` di issue tracker.
- **Deployment Frequency & Change Failure Rate**: Ambil langsung dari metrik DORA di workflow GitHub Actions.

---

## 9. Checklist Verifikasi Sebelum Mengirim Laporan

Sebelum mempublikasikan laporan status ke saluran manajemen:
- [ ] Apakah status RAG didukung angka metrik konkret, bukan asumsi pribadi?
- [ ] Apakah kalimat pertama Executive Summary sudah menjawab: "Apakah rilis tepat waktu?"
- [ ] Apakah setiap Blocker yang ditulis mencantumkan nama orang yang diminta bertindak?
- [ ] Apakah istilah teknis mikro sudah diterjemahkan ke implikasi fungsional bisnis?
- [ ] Apakah durasi membaca laporan ini tidak melebihi 2 menit?

---

## 10. Common Mistakes

- **Watermelon Status Reporting**: Status tampak hijau di luar (laporan selalu diklaim aman), namun merah padam di dalam (tim panik dan sistem penuh bug). Saat rilis tiba, proyek mendadak gagal.
- **Jargon Dump**: Memenuhi laporan eksekutif dengan detail teknis mikro seperti "Optimasi index B-tree pada field `trx_uuid`". Ubah menjadi dampak bisnis: "Kecepatan loading halaman naik 40%".
- **Laporan Tanpa "Call to Action" (Ask)**: Menyebutkan blocker fatal tetapi tidak meminta bantuan spesifik kepada siapa pun.
- **Terlalu Panjang (Novel Report)**: Laporan 10 halaman tidak akan dibaca oleh pimpinan. Batasi maksimal 1 halaman layar monitor.
- **Hanya Menatap Masa Lalu**: Terlalu fokus mendokumentasikan apa yang dikerjakan minggu lalu tanpa memberikan proyeksi realistis apakah deadline bulan depan masih terkejar.

---

## 11. Related Docs & Templates

- Otorisasi baseline awal: [Project Charter](./project-charter.md)
- Perencanaan jadwal & milestone: [Project Planning](./project-planning.md)
- Log risiko proyek: [Risk Register](./risk-register.md)
- Pengendalian perubahan baseline: [Change Log](./change-log.md)

---
*skipped: automated enterprise ERP data sync pipelines; add when multiple concurrent cross-org enterprise business units require centralized financial rollups.*