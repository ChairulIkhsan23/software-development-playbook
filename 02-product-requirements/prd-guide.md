# Product Requirements Document (PRD) Guide

## Pendahuluan

PRD (Product Requirements Document) adalah dokumen yang menjelaskan **APA** yang harus dibangun dan **MENGAPA**, tanpa mendikte **BAGAIMANA** secara teknis. PRD menjadi kontrak pemahaman antara product, engineering, dan design.

Dokumen ini menjelaskan cara menulis PRD yang baik, struktur standar, dan kesalahan umum yang harus dihindari.

---

## 1. Apa Itu PRD

PRD adalah dokumen hidup yang mendefinisikan:
- **Masalah** yang ingin diselesaikan
- **Siapa** yang terdampak
- **Apa** solusi yang diusulkan (level fitur, bukan teknis)
- **Bagaimana mengukur** keberhasilan
- **Batasan** scope yang disepakati

### Siapa yang Menulis PRD?

- **Product Manager / Product Owner** — penulis utama
- **Solo developer** — ya, kamu tetap perlu PRD walau tim cuma satu orang. PRD memaksa kamu berpikir jernih sebelum coding
- **Tech Lead** — kontributor untuk section teknis (NFR, dependencies)
- **Designer** — kontributor untuk section UX/UI

### PRD Bukan...

| PRD Bukan | Penjelasan |
|-----------|-----------|
| Technical Spec | PRD bilang "user bisa login pakai Google". Tech spec bilang "pakai OAuth 2.0 PKCE flow dengan Google Identity API" |
| UI Spec / Mockup | PRD bilang "user bisa lihat riwayat transaksi 30 hari terakhir". UI spec menentukan layout, warna, posisi elemen |
| Project Plan | PRD bilang "fitur ini harus live Q1". Project plan detail sprint breakdown, resource allocation, timeline |
| User Manual | PRD untuk tim internal. User manual untuk end user |

---

## 2. Struktur Standar PRD

Berikut struktur lengkap PRD. Tidak semua section wajib diisi — sesuaikan dengan skala proyek. Section bertanda ★ adalah minimum yang harus ada bahkan untuk proyek kecil.

### 2.1 Metadata ★

```markdown
| Field         | Value                          |
|---------------|--------------------------------|
| Judul         | [Nama fitur / inisiatif]       |
| Versi         | v1.0                           |
| Penulis       | [Nama PM / Author]             |
| Status        | Draft / In Review / Approved   |
| Tanggal       | YYYY-MM-DD                     |
| Reviewer      | [Nama tech lead, designer]     |
| Related OKR   | [Link ke objective terkait]    |
```

**Tips:** Selalu update versi dan tanggal setiap ada perubahan substansial. Gunakan format `v1.0`, `v1.1`, `v2.0`.

### 2.2 Product Overview ★

Ringkasan 2–3 paragraf yang menjelaskan fitur secara high-level. Siapapun yang baca section ini harus langsung paham apa yang mau dibangun.

**Tips:** Tulis ini terakhir setelah semua section lain selesai — karena ini summary.

### 2.3 Problem Statement ★

Jelaskan masalah yang dialami user saat ini. Sertakan:
- **Data/evidence** yang mendukung (hasil riset, support ticket, analytics)
- **Dampak** jika masalah tidak diselesaikan
- **Siapa** yang terdampak paling besar

```markdown
### Masalah
Saat ini, 68% pedagang yang menggunakan CatatDuit berhenti mencatat transaksi
setelah hari ke-3 (data: cohort analysis Januari 2025). Alasan utama dari
survey: "terlalu banyak field yang harus diisi" (42% responden).

### Dampak
Retensi D7 stagnan di 25%. Jika tidak diperbaiki, target 1.000 DAU Q1
tidak tercapai.
```

### 2.4 Goals & Metrics ★

Hubungkan fitur ke OKR yang sudah ditentukan di [Product Vision & Strategy](./product-vision-and-strategy.md).

```markdown
| Goal                              | Metric                    | Target      |
|-----------------------------------|---------------------------|-------------|
| Mempermudah pencatatan transaksi  | Waktu catat 1 transaksi   | < 5 detik   |
| Meningkatkan retensi awal         | Retensi D7                | 25% → 45%   |
| Mengurangi friction onboarding    | Time-to-first-transaction | < 2 menit   |
```

### 2.5 Target Users ★

Deskripsikan persona utama. Tidak perlu dokumen persona yang panjang — cukup yang relevan.

```markdown
**Persona Utama: Ibu Sari — Pedagang Sayur Pasar Tradisional**
- Usia 35–50 tahun
- Smartphone Android entry-level (RAM 2–3 GB)
- Koneksi internet tidak stabil di area pasar
- Literasi digital: bisa WhatsApp dan Facebook, belum pernah pakai app keuangan
- Motivasi: ingin tahu untung atau rugi di akhir hari
```

### 2.6 Scope: In & Out ★

Section paling penting untuk mencegah scope creep.

```markdown
### In-Scope (v1.0)
- Input transaksi single-field (ketik nominal saja)
- Auto-kategorisasi transaksi berdasarkan keyword
- Riwayat transaksi hari ini

### Out-of-Scope (dipertimbangkan di versi berikutnya)
- Multi-outlet management
- Export ke Excel/PDF
- Integrasi dengan bank
- Fitur utang-piutang
```

**Tips:** Eksplisit tulis out-of-scope. Ini mengurangi 80% diskusi "tapi harusnya bisa juga..." di tengah sprint.

### 2.7 User Needs

Daftar kebutuhan dari perspektif user, bukan teknis.

```markdown
| ID     | Kebutuhan User                                              | Prioritas  |
|--------|-------------------------------------------------------------|------------|
| UN-01  | User bisa mencatat penjualan hanya dengan ketik nominal     | Must Have  |
| UN-02  | User bisa lihat total penjualan hari ini secara real-time   | Must Have  |
| UN-03  | User bisa koreksi transaksi yang salah input                | Should Have|
| UN-04  | User bisa lihat kategori pengeluaran terbesar bulan ini     | Could Have |
```

### 2.8 Functional Requirements Summary

Rangkuman requirement level PRD. Detail lengkap ada di dokumen [Functional Requirements](./functional-requirements.md).

```markdown
| ID          | Requirement                                              | Priority  |
|-------------|----------------------------------------------------------|-----------|
| FR-TXN-01   | Sistem menerima input transaksi berupa nominal saja      | Must Have |
| FR-TXN-02   | Sistem auto-assign kategori berdasarkan keyword          | Must Have |
| FR-TXN-03   | Sistem menyimpan transaksi secara offline-first          | Must Have |
| FR-RPT-01   | Sistem menampilkan ringkasan penjualan hari ini          | Must Have |
```

### 2.9 Business Rules

Referensi ke aturan bisnis yang berlaku. Detail lengkap di [Business Rules](./business-rules.md).

```markdown
- BR-TXN-01: Transaksi dengan nominal > Rp 10.000.000 membutuhkan konfirmasi ulang
- BR-CAT-01: Jika keyword tidak dikenali, kategori default = "Lainnya"
- BR-TXN-02: Transaksi tidak bisa dihapus setelah 24 jam, hanya bisa di-void
```

### 2.10 Non-Functional Requirements Reference

Cantumkan ekspektasi NFR level produk. Detail teknis ada di technical spec.

```markdown
| Aspek         | Ekspektasi                                          |
|---------------|-----------------------------------------------------|
| Performance   | Input transaksi harus responsif < 300ms             |
| Availability  | App harus berfungsi offline untuk fitur pencatatan  |
| Data          | Sync ke server saat koneksi tersedia                |
| Security      | Data transaksi terenkripsi di device                |
| Compatibility | Android 8+ (API 26), RAM minimal 2 GB              |
```

### 2.11 UX/UI Considerations

Bukan mockup detail — cukup prinsip dan constraint UX.

```markdown
- One-handed operation: semua interaksi utama bisa dilakukan dengan satu tangan
- Font size minimal 16sp untuk readability di bawah sinar matahari
- Warna kontras tinggi (WCAG AA minimum)
- Tidak ada onboarding tutorial panjang — user langsung bisa catat di first screen
- Haptic feedback saat transaksi berhasil disimpan
```

### 2.12 Dependencies, Assumptions & Constraints

```markdown
**Dependencies:**
- Design: mockup final dari tim design (ETA: minggu ke-2)
- Backend: API kategorisasi harus ready sebelum integrasi (ETA: minggu ke-3)

**Assumptions:**
- User target sudah terbiasa mengetik angka di smartphone
- Koneksi internet tersedia minimal 1x sehari untuk sync

**Constraints:**
- Budget infrastruktur maksimal $50/bulan (early stage)
- Tim engineering: 2 orang (1 mobile, 1 backend)
- Timeline: 6 minggu sampai MVP
```

### 2.13 Risks

```markdown
| Risk                                        | Likelihood | Impact | Mitigation                          |
|---------------------------------------------|------------|--------|-------------------------------------|
| Auto-kategorisasi tidak akurat              | High       | Medium | Beri opsi user koreksi manual       |
| User tidak paham konsep "kategori"          | Medium     | High   | Gunakan istilah sehari-hari         |
| Offline storage penuh di HP low-end         | Low        | High   | Limit 1000 transaksi offline        |
```

### 2.14 Acceptance Criteria ★

Kapan fitur dianggap "selesai"? Detail ada di [Acceptance Criteria](./acceptance-criteria.md).

```markdown
- [ ] User bisa catat transaksi dalam < 5 detik (diukur dari tap sampai saved)
- [ ] Transaksi tersimpan saat offline dan sync saat online
- [ ] Auto-kategorisasi akurasi > 80% pada top 10 kategori
- [ ] Tidak ada data loss saat app di-kill oleh OS
```

### 2.15 MVP Scope

Dari semua requirement di atas, apa yang masuk MVP (Minimum Viable Product)?

```markdown
**MVP = In-Scope v1.0 items dengan prioritas Must Have**

Specifically:
1. Single-field transaction input
2. Auto-kategorisasi (top 10 kategori)
3. Ringkasan penjualan hari ini
4. Offline storage + background sync

**Bukan MVP:**
- Koreksi transaksi (Should Have — v1.1)
- Laporan bulanan (Could Have — v1.2)
```

### 2.16 Open Questions

Daftar hal yang belum diputuskan. Setiap item harus ada owner dan deadline.

```markdown
| # | Question                                              | Owner  | Deadline   |
|---|-------------------------------------------------------|--------|------------|
| 1 | Apakah perlu login atau anonymous-first?              | PM     | 2025-01-15 |
| 2 | Berapa lama data offline disimpan sebelum force sync?  | Tech   | 2025-01-20 |
| 3 | Apakah void transaksi perlu approval?                 | PM     | 2025-01-15 |
```

---

## 3. Tips Menulis PRD

1. **Tulis untuk orang yang tidak ikut meeting** — PRD harus self-explanatory
2. **Gunakan bahasa user, bukan bahasa teknis** — "user bisa catat transaksi", bukan "sistem meng-invoke endpoint POST /transactions"
3. **Satu requirement = satu kalimat** — jangan gabung 2 hal dalam 1 kalimat
4. **Hindari kata ambigu** — "cepat" → "< 300ms". "Mudah" → "dalam 3 langkah". "Banyak" → "minimal 100 item"
5. **Screenshot atau diagram > paragraf panjang** — jika bisa digambar, gambar
6. **Tulis out-of-scope setegas in-scope** — ini menyelamatkan kamu dari scope creep

---

## 4. PRD Review Process

```
Draft → Self-Review → Peer Review → Stakeholder Review → Approved
```

### Checklist Review

- [ ] Problem statement didukung data/evidence
- [ ] Scope in/out sudah eksplisit
- [ ] Setiap requirement punya prioritas (MoSCoW)
- [ ] Acceptance criteria testable dan spesifik
- [ ] Dependencies dan risks sudah diidentifikasi
- [ ] Open questions ada owner dan deadline
- [ ] Tidak ada requirement yang ambigu ("cepat", "mudah", "banyak")
- [ ] Terhubung ke OKR / North Star Metric

### Siapa yang Review?

| Reviewer | Fokus Review |
|----------|-------------|
| Tech Lead | Feasibility, dependencies, NFR realistis |
| Designer | UX flow, user needs terwakili |
| QA Lead | Acceptance criteria testable |
| Stakeholder / Founder | Alignment dengan vision & business |

---

## 5. Versioning PRD

| Perubahan | Versi | Contoh |
|-----------|-------|--------|
| Typo, formatting | Tidak perlu naik versi | Fix typo |
| Klarifikasi tanpa ubah scope | v1.0 → v1.1 | Tambah detail acceptance criteria |
| Ubah scope atau requirement | v1.1 → v2.0 | Tambah fitur baru ke in-scope |
| Pivot atau rewrite | v2.0 → v3.0 | Ubah target user atau problem statement |

Simpan changelog di akhir dokumen:

```markdown
## Changelog
| Versi | Tanggal    | Perubahan                          | Author |
|-------|------------|------------------------------------|--------|
| v1.0  | 2025-01-10 | Initial draft                      | Andi   |
| v1.1  | 2025-01-18 | Klarifikasi acceptance criteria    | Andi   |
| v2.0  | 2025-02-01 | Tambah fitur void transaksi        | Andi   |
```

---

## 6. Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|-----------|--------|--------|
| PRD terlalu teknis | Engineer bingung mana requirement mana implementasi | Pisahkan PRD dan Technical Spec |
| Tidak ada out-of-scope | Scope creep, timeline molor | Selalu tulis out-of-scope secara eksplisit |
| Acceptance criteria vague ("berjalan dengan baik") | QA tidak bisa test, definisi "done" berbeda tiap orang | Gunakan format Given-When-Then atau checklist spesifik |
| PRD ditulis setelah development mulai | PRD jadi dokumentasi retroaktif, bukan panduan | PRD harus approved sebelum sprint planning |
| Tidak ada open questions | Asumsi tersembunyi muncul saat development | Lebih baik tulis "belum tahu" daripada asumsi diam-diam |
| Satu PRD untuk seluruh produk | Dokumen terlalu besar, tidak pernah selesai | Satu PRD per fitur / initiative |

---

## 7. Template

Gunakan template PRD siap pakai di:

📄 [`../templates/product/prd-template.md`](../templates/product/prd-template.md)

---

## Dokumen Terkait

- [Product Vision & Strategy](./product-vision-and-strategy.md) — harus jelas sebelum tulis PRD
- [Functional Requirements](./functional-requirements.md) — detail requirement dari PRD
- [User Stories & Use Cases](./user-stories-and-use-cases.md) — breakdown requirement jadi stories
- [Business Rules](./business-rules.md) — katalog aturan bisnis yang direferensi PRD
- [Acceptance Criteria](./acceptance-criteria.md) — detail cara tulis acceptance criteria
- [Prioritization](./prioritization.md) — framework prioritas (MoSCoW, RICE, dll)
