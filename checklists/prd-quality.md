# PRD Quality Audit Checklist

Checklist audit kualitas, kelengkapan, dan kejelasan dokumen Product Requirements Document (PRD) sebelum tim engineering memulai fase estimasi dan perencanaan teknis.

---

## 1. Problem & Objectives
- [ ] **Problem Context (The "Why")**: Latar belakang bisnis dan akar masalah dijelaskan gamblang tanpa jargon kosong.
- [ ] **Measurable Business Goals**: Target bisnis dinyatakan via metrik konkret (contoh: konversi naik 15%, penurunan churn 5%).
- [ ] **Key Results / OKRs**: Metrik kesuksesan terikat langsung pada OKR kuartal atau KPI strategis organisasi.
- [ ] **Success Metrics Definition**: Baseline metrik saat ini tercatat bersama target angka spesifik pasca-rilis.
- [ ] **User Impact**: Dampak positif langsung terhadap efisiensi atau kepuasan pengguna dinyatakan jelas.

---

## 2. Target Audience & Scope
- [ ] **Target User Segments**: Segmen pengguna teridentifikasi spesifik (level akses, peran operasional, platform).
- [ ] **Explicit In-Scope**: Fitur dan kapabilitas yang wajib dibangun pada rilis ini terdaftar tuntas.
- [ ] **Explicit Out-of-Scope**: Fitur atau skenario yang sengaja ditunda / tidak dikerjakan tertulis tegas guna cegah *scope creep*.
- [ ] **Release Phasing / MVP Strategy**: Batasan perbedaan antara versi MVP (Phase 1) dan fase rilis lanjutan terdefinisi.
- [ ] **Platform Target**: Daftar peramban, resolusi layar, atau OS minimum dinyatakan jelas.

---

## 3. Functional Requirements (FR)
- [ ] **Hierarchical & Atomic Numbering**: Setiap kebutuhan fungsional memiliki kode unik (contoh: `FR-AUTH-001`, `FR-PAY-002`).
- [ ] **Single Responsibility**: Setiap nomor kebutuhan hanya mendeskripsikan satu kapabilitas tunggal.
- [ ] **Testable Statements**: Kebutuhan ditulis dengan kata kerja operasional terukur; dilarang pakai kata subjektif seperti "cepat" atau "mudah".
- [ ] **State Transitions**: Perubahan status entitas (contoh: `Pending` -> `Paid` -> `Shipped` -> `Completed`) dipetakan lengkap.
- [ ] **Input & Data Fields**: Spesifikasi tipe data, batas karakter, dan aturan validasi dasar tercantum.

---

## 4. Non-Functional Requirements (NFR)
- [ ] **Performance Benchmarks**: Latensi API ditarget terukur (contoh: p95 response time < 300ms di bawah 500 RPS).
- [ ] **Scalability Targets**: Proyeksi jumlah *concurrent users* dan volume data tahun pertama terdokumentasi.
- [ ] **Availability & Uptime**: Target SLA uptime disepakati (contoh: 99.9% uptime per bulan).
- [ ] **Security & Compliance Requirements**: Standar enkripsi, proteksi data sensitif, dan batas kepatuhan regulasi dinyatakan.
- [ ] **Accessibility (a11y)**: Standar kepatuhan aksesibilitas ditentukan (minimal level WCAG 2.1 AA).

---

## 5. UX/UI Design Links
- [ ] **User Flow Diagrams**: Diagram alur pengguna (*end-to-end user flow*) terlampir untuk seluruh skenario utama.
- [ ] **Design Artifacts Links**: Tautan ke file Figma / prototype terverifikasi aktif dan dapat diakses tim dev.
- [ ] **UI State Coverage**: Seluruh kondisi antarmuka terdesain (Normal, Loading, Empty State, Error State, Partial State).
- [ ] **Copy & Microcopy**: Teks tombol, notifikasi sistem, dan pesan kesalahan telah difinalisasi tim konten/produk.

---

## 6. Assumptions, Risks & Dependencies
- [ ] **Technical & Business Assumptions**: Asumsi dasar teknis dan pasar tercatat bersama dampaknya jika meleset.
- [ ] **Risk Register**: Potensi risiko teknis, regulasi, atau operasional dicatat beserta rencana mitigasinya.
- [ ] **External API & Service Dependencies**: Ketergantungan ke sistem eksternal atau vendor pihak ketiga dipetakan.
- [ ] **Legacy System Integration**: Kebutuhan integrasi terhadap database lama atau service lawas dijelaskan limitasinya.

---

## 7. Review & Approvals
- [ ] **Cross-Functional Walkthrough**: Sesi bedah PRD telah diselenggarakan bersama tim engineering, QA, dan design.
- [ ] **Engineering Feasibility Sign-off**: Tech Lead menyetujui batasan teknis dan kompleksitas kebutuhan.
- [ ] **QA Testability Sign-off**: QA Engineer menyetujui bahwa seluruh kebutuhan dapat diuji secara objektif.
- [ ] **Business Sponsor Sign-off**: Product Owner / Sponsor Bisnis mengesahkan versi final PRD.

---

## Scoring Guide & Quality Thresholds
Hitung persentase pemenuhan: `(Total Checked / 28) * 100%`

| Persentase | Level Kualitas | Keputusan Tindak Lanjut |
|---|---|---|
| **>= 90%** | **Audit Pass** | PRD berkualitas tinggi. Lanjut ke breakdown backlog dan estimasi sprint. |
| **75% - 89%** | **Conditional Pass** | Boleh mulai arsitektur teknis; revisi item yang kurang tuntas dalam 2 hari kerja. |
| **< 75%** | **Audit Fail** | PRD belum matang. Blokir proses estimasi sprint. Kembalikan ke Product Manager. |

*Critical Blocker Items:* Out-of-Scope eksplisit, NFR dengan target terukur, Link Figma aktif, dan Approval Engineering. Jika salah satu kosong, PRD otomatis berstatus **Audit Fail**.

---

## Remediation Guide
- **Requirement Ambigu**: Ganti kalimat pasif menjadi format deterministik: "Sistem [HARUS / WAPAT] melakukan [Aksi] ketika [Pemicu]".
- **Out-of-Scope Kosong**: Buat daftar tegas minimal 3 fitur yang ditolak atau ditunda demi menjaga timeline rilis.
- **NFR Subjektif**: Ubah pernyataan umum seperti "Sistem harus cepat" menjadi "Waktu muat halaman pertama < 1.5 detik pada koneksi 4G".
- **Empty & Error States Figma Belum Ada**: Tugaskan desainer UI melengkapi variasi komponen sebelum user story dipindahkan ke kolom *Ready for Development*.
- **Estimasi Tech Lead Membengkak**: Potong fitur sekunder ke *Phase 2* untuk mempertahankan scope rilis utama tetap ramping.