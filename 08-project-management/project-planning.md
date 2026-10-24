# Project Planning

Panduan dekomposisi pekerjaan, pemetaan roadmap, dan manajemen dependensi untuk software engineer dan technical lead.

---

## 1. Work Breakdown Structure (WBS)

WBS adalah dekomposisi hierarkis ruang lingkup proyek menjadi unit-unit pekerjaan terkelola berorientasi deliverable.

```
                  [Level 1: Project Deliverable]
                                │
        ┌───────────────────────┴───────────────────────┐
 [Level 2: Sub-system A]                         [Level 2: Sub-system B]
        │                                               │
 ┌──────┴──────┐                                 ┌──────┴──────┐
[Work Package] [Work Package]                   [Work Package] [Work Package]
 (8-80 hours)   (8-80 hours)                     (8-80 hours)   (8-80 hours)
```

### Tiga Prinsip Utama WBS

1. **Aturan 100% (100% Rule)**:
   Total pekerjaan di level bawah harus mencakup 100% ruang lingkup di level atasnya. Tidak ada pekerjaan tersembunyi yang tertinggal; tidak ada pekerjaan ekstra di luar cakupan charter.
2. **Aturan Paket Kerja 8-80 Jam (8-80 Hour Rule)**:
   Setiap work package di level terbawah harus berukuran minimal 8 jam (1 hari kerja developer) dan maksimal 80 jam (2 minggu kerja). Jika lebih dari 80 jam, pecah lagi; jika di bawah 8 jam, gabungkan ke unit logis yang sama.
3. **Dekomposisi Berorientasi Deliverable**:
   Bagi berdasarkan hasil nyata (misalnya: *Authentication Service*, *Order Processing Worker*, *Admin Dashboard UI*), bukan berdasarkan aktivitas generik seperti *Coding* atau *Testing*.

### Contoh Dekomposisi WBS Nyata (Checkout Engine)

```
1.0 Payment Engine Platform
├── 1.1 Virtual Account Service
│   ├── 1.1.1 Integrasi API Bank Mandiri (16h)
│   ├── 1.1.2 Integrasi API Bank BCA (24h)
│   └── 1.1.3 Parser Inbound Webhook Callback (16h)
├── 1.2 QRIS Processing Engine
│   ├── 1.2.1 Dynamic QR Code Generator (20h)
│   └── 1.2.2 Worker Polling Status Settlement (32h)
└── 1.3 Observability & Security
    ├── 1.3.1 Setup Grafana Dashboard P95 Latency (12h)
    └── 1.3.2 Enkripsi Kredensial Database Vault (16h)
```

---

## 2. Visualisasi Roadmap: Now-Next-Later vs Gantt Chart

Pilih format roadmap sesuai tingkat ketidakpastian proyek:

| Aspek | Now-Next-Later Roadmap | Timeline-based Gantt Chart |
|---|---|---|
| **Konteks Ideal** | Produk SaaS internal, Agile sprint, startup | Proyek kontrak berdurasi tetap, integrasi enterprise |
| **Dimensi Waktu** | Fleksibel berbasis prioritas fase | Terikat tanggal kalender kaku |
| **Fokus Utama** | Outcomes dan value bisnis yang diselesaikan | Deadline output dan pemanfaatan sumber daya |
| **Resistensi Perubahan** | Sangat mudah beradaptasi saat prioritas berubah | Mahal dan rentan rapuh jika terjadi keterlambatan |

### Model Now-Next-Later
- **Now**: Fokus sprint saat ini (komitmen penuh, spesifikasi sudah matang, pekerjaan sedang aktif).
- **Next**: Rencana 1-2 sprint ke depan (kebutuhan sudah dipetakan, arsitektur sedang dimatangkan).
- **Later**: Inisiatif masa depan (masih berupa ide bernilai tinggi, belum dieksekusi secara teknis).

<!-- ponytail: Gunakan Now-Next-Later untuk tim engineering lincah; gunakan Gantt chart ringkas hanya saat berhadapan dengan deadline kontrak pihak ketiga. -->

---

## 3. Milestone Definition & Tracking

Milestone adalah penanda titik penyelesaian penting tanpa durasi kerja sendiri (durasi = 0 hari).

Kriteria penentuan milestone yang solid:
- Memiliki kriteria keberhasilan biner (Selesai atau Belum; tidak ada status 90% selesai).
- Menghasilkan deliverable yang dapat diuji atau ditinjau oleh pemangku kepentingan.

Contoh milestone terstruktur:
- **MS-01**: Arsitektur disetujui & schema database termigrasi di staging.
- **MS-02**: Integrasi core API pihak ketiga berhasil lolos uji happy-path dan error-path.
- **MS-03**: Seluruh acceptance criteria PRD terpenuhi dalam pengujian end-to-end.
- **MS-04**: Deployment production selesai dengan nol error kritikal dalam 24 jam pertama.

---

## 4. Dependency Management & Critical Path

Kegagalan proyek software umumnya berakar dari dependensi tersembunyi antar-tim atau antar-modul.

```
Task A: Setup Schema DB ──(Finish-to-Start)──> Task B: CRUD API Endpoints
Task C: Mock API Server ──(Start-to-Start)───> Task D: Frontend Integration
```

### Jenis Dependensi Teknis
1. **Finish-to-Start (FS)**: Task B tidak dapat dimulai sebelum Task A selesai.
   *Contoh*: Pembuatan migration table database (A) harus selesai sebelum menulis repository logic (B).
2. **Start-to-Start (SS)**: Task D dapat dimulai segera setelah Task C dimulai.
   *Contoh*: Pembuatan API mock server (C) dimulai bersamaan dengan integrasi UI frontend (D).
3. **Finish-to-Finish (FF)**: Task F baru bisa diselesaikan saat Task E tuntas.
4. **Start-to-Finish (SF)**: Jarang digunakan dalam rekayasa perangkat lunak modern.

### Perhitungan Critical Path & Float (Slack)
Critical path adalah urutan tugas berantai terpanjang dari awal hingga akhir proyek yang menentukan tanggal rilis tercepat.
- **Total Float**: Toleransi waktu keterlambatan sebuah tugas tanpa memundurkan tanggal akhir proyek:
  $$\text{Total Float} = \text{Late Start (LS)} - \text{Early Start (ES)}$$
- Jika Float = 0, tugas berada tepat di Critical Path. Penundaan 1 hari pada tugas ini memundurkan rilis proyek 1 hari.
- Tugas dengan Float > 0 dapat digeser jadwalnya jika engineer dialihkan untuk membantu tugas di critical path.

---

## 5. Capacity Planning & Team Velocity

Kapasitas tim bukan 40 jam kerja per orang per minggu. Angka riil jauh lebih rendah.

### Formula Perhitungan Kapasitas Riil
```
Kapasitas Efektif = Total Anggota x Hari Kerja x Jam Produktif Harian x Focus Factor
```
- **Jam Produktif Harian**: Rata-rata 5-6 jam (sisa 2-3 jam habis untuk standup, review PR, email, koordinasi).
- **Focus Factor**: Nilai standar 0.70 hingga 0.80 untuk memperhitungkan disrupsi darurat, bug live, atau cuti.
- Contoh: Tim berisi 3 developer, sprint 10 hari kerja:
  `3 orang x 10 hari x 6 jam x 0.75 = 135 jam waktu fokus efektif`.

### Mengukur Velocity
Velocity adalah rata-rata Story Points yang diselesaikan tim per sprint (hanya tugas dengan status *Done* 100%).
- Jangan gunakan velocity untuk membandingkan produktivitas antar-tim.
- Gunakan rolling average dari 3 sprint terakhir untuk memprediksi komitmen sprint berikutnya.

---

## 6. Buffer Management (Sprint Buffer vs Project Buffer)

- **Sprint Buffer**: Sisihkan 15-20% kapasitas per sprint tanpa alokasi story baru. Gunakan ruang ini untuk bug triage, PR review, dan spike teknis tak terduga.
- **Project Buffer**: Tempatkan cadangan waktu 2 minggu sebelum target tanggal rilis publik. Jangan beri tahu tanggal buffer ini ke sales untuk mencegah scope creeping.

---

## 7. Agile Sprint Planning vs Traditional Scheduling

| Parameter | Agile Sprint Planning | Traditional Project Scheduling (Waterfall) |
|---|---|---|
| **Siklus Perencanaan** | 1-2 minggu berulang | Seluruh siklus proyek di depan |
| **Estimasi Unit** | Relatif (Story Points / T-shirt) | Absolut (Hari kerja / Man-months) |
| **Kendali Ruang Lingkup** | Scope variabel, waktu tetap | Scope tetap, waktu variabel/fleksibel |
| **Respon Perubahan** | Diakomodasi pada backlog sprint berikutnya | Wajib melalui formal Change Control Board |
| **Deliverable** | Working software setiap akhir sprint | Software utuh di akhir fase testing |

---

## 8. Tooling Matrix

| Tool | Cocok Untuk | Keunggulan Utama | Limitasi |
|---|---|---|---|
| **GitHub Projects** | Solo dev, tim open-source, tech startup | Terintegrasi langsung dengan PR, issue, branch | Fitur pelaporan biaya dan resource management terbatas |
| **Linear** | Engineering team modern, fast-paced SaaS | UI sangat cepat, navigasi keyboard, siklus otomatis | Opsi kustomisasi workflow enterprise tidak sebanyak Jira |
| **Jira** | Organisasi menengah hingga enterprise | Pelaporan kepatuhan lengkap, konfigurasi permission ketat | Setup kompleks, performa terasa lambat jika salah konfigurasi |
| **Spreadsheets** | Proyek kecil, budgeting sederhana | Fleksibilitas format tabel tanpa biaya lisensi | Rawan human-error formula, tidak ada audit trail otomatis |

---

## 9. Common Mistakes

- **Merencanakan Waktu 100% Kapasitas**: Menganggap tim bekerja produktif 8 jam penuh tanpa meeting dan distraksi.
- **WBS Berbasis Fase, Bukan Deliverable**: Membagi pekerjaan menjadi "Analysis -> Coding -> Testing" yang membuat progres tidak terukur jelas.
- **Mengabaikan Dependensi Pihak Ketiga**: Menjadwalkan integrasi vendor di critical path tanpa memperhitungkan birokrasi persetujuan kredensial API.
- **Memperlakukan Gantt Chart Sebagai Fakta**: Mengunci jadwal 6 bulan ke depan tanpa ruang penyusutan atau pengembangan arsitektur.
- **Tidak Memperhitungkan Libur & Cuti**: Menghitung 1 sprint penuh padahal terdapat libur nasional atau cuti bersama di tengah jadwal.

---

## 10. Related Docs & Templates

- Template acuan kerja: [Project Plan Template](../templates/project/project-plan.md)
- Dokumen inisiasi awal: [Project Charter](./project-charter.md)
- Estimasi dan prioritas: [Estimation and Prioritization](./estimation-and-prioritization.md)
- Pelacakan risiko teknis: [Risk Register](./risk-register.md)

---
*skipped: automated earned value management (EVM); add when government or defense contract compliance requires financial audit.*