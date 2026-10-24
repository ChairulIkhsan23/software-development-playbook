---
id: DOC-CHA-001
title: Project Charter
project: "[Nama Proyek]"
version: 1.0.0
author: "[Nama Project Lead]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Under Review / Approved
---

# Project Charter: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini adalah kontrak formal otorisasi proyek antara sponsor bisnis dan tim pengembang. Dokumen ini mendefinisikan batas ruang lingkup, target terukur, dan alokasi sumber daya. Bagian bertanda `[Wajib]` harus diisi lengkap. Bagian `[Opsional]` atau `[Kondisional]` dapat disesuaikan dengan skala tim.

---

## 1. Otorisasi Proyek (Project Authorization) [Wajib]

> **Panduan Pengisian:** Sebutkan sponsor proyek yang berwenang memberikan mandat serta tanggal mulai dan target akhir proyek.

- **Project Sponsor:** [Nama Sponsor / Jabatan Eksekutif]
- **Project Lead / Manager:** [Nama Project Lead / Tech Lead]
- **Tanggal Efektif Mandat:** [YYYY-MM-DD]
- **Target Tanggal Selesai:** [YYYY-MM-DD]
- **Pernyataan Mandat:** Proyek ini resmi disetujui untuk mengeksekusi pengembangan [Nama Proyek] sesuai lingkup dan alokasi yang tercantum.

---

## 2. Tujuan Bisnis & Sasaran Kuantitatif (SMART Objectives) [Wajib]

> **Panduan Pengisian:** Tuliskan justifikasi bisnis dan target keberhasilan yang terukur (Specific, Measurable, Achievable, Relevant, Time-bound).

- **Latar Belakang Bisnis:** [Jelaskan masalah pasar atau efisiensi internal yang memicu proyek ini]
- **Target Kuantitatif (SMART):**
  - **Specific:** [Contoh: Membangun modul checkout mandiri terintegrasi payment gateway QRIS]
  - **Measurable:** [Contoh: Menurunkan waktu checkout dari 4 menit menjadi < 90 detik, conversion rate naik 15%]
  - **Achievable:** [Contoh: Menggunakan modul pembayaran siap pakai dan infrastruktur cloud yang sudah ada]
  - **Relevant:** [Contoh: Mendukung target tahunan perusahaan untuk ekspansi transaksi kanal digital]
  - **Time-bound:** [Contoh: Go-live produksi selambat-lambatnya pada [YYYY-MM-DD]]

---

## 3. Ruang Lingkup Proyek (Scope Boundary) [Wajib]

> **Panduan Pengisian:** Tegaskan batasan fungsionalitas agar mencegah scope creep sejak fase inisiasi.

| Kategori | Batasan Ruang Lingkup |
|---|---|
| **In-Scope (Dikerjakan)** | • [Fitur A: Autentikasi pengguna via OTP WhatsApp dan Email]<br>• [Fitur B: Katalog produk dengan sinkronisasi stok realtime]<br>• [Fitur C: Dashboard ringkasan transaksi kasir cabang] |
| **Out-of-Scope (Tidak Dikerjakan)** | • [Integrasi marketplace pihak ketiga (ditunda ke Fase 2)]<br>• [Aplikasi native iOS/Android (fase ini fokus ke responsif PWA Web)]<br>• [Modul penggajian karyawan cabang] |

---

## 4. Deliverables Utama & Milestone [Wajib]

> **Panduan Pengisian:** Tetapkan tahapan pencapaian kunci (milestone gates) beserta output dokumen/kode yang harus diserahkan. Format ID: `MLS-001`.

| ID Milestone | Nama Milestone & Deliverable | Kriteria Selesai (Definition of Done) | Target Tanggal |
|---|---|---|---|
| MLS-001 | Approval PRD & Arsitektur Sistem | PRD (DOC-PRD-001) dan ADR disetujui tim | [YYYY-MM-DD] |
| MLS-002 | Rilis Versi Internal Alpha | Modul auth dan katalog siap di staging | [YYYY-MM-DD] |
| MLS-003 | Feature Freeze & UAT Sign-off | Seluruh skenario testing lolos tanpa bug blocker | [YYYY-MM-DD] |
| MLS-004 | Peluncuran Produksi (Go-Live) | Deployment sukses di server live & verifikasi asap (smoke test) | [YYYY-MM-DD] |

---

## 5. Alokasi Anggaran & Sumber Daya [Opsional]
*(Seksi ini opsional: dapat dihapus jika proyek internal solo dev atau tanpa anggaran finansial terpisah)*

> **Panduan Pengisian:** Catat estimasi biaya infrastruktur, lisensi, serta alokasi persentase waktu tim kerja.

- **Anggaran Infrastruktur Cloud:** [IDR / USD per bulan, contoh: IDR 5.000.000 / bulan]
- **Biaya Lisensi / Pihak Ketiga:** [Contoh: Layanan SMS gateway IDR 2.000.000]
- **Komposisi Tim:** 1 Tech Lead ([Nama]), 2 Software Engineer ([Nama]), 1 QA Tester ([Nama]).

---

## 6. Matriks Stakeholder (DACI / RACI) [Wajib]

> **Panduan Pengisian:** Petakan peran keputusan kunci. Driver (D), Approver (A), Contributor (C), Informed (I).

| Peran Proyek | Nama / Jabatan | Peran DACI | Tanggung Jawab Utama |
|---|---|---|---|
| Project Sponsor | [Nama Sponsor] | Approver (A) | Pengambil keputusan akhir & persetujuan anggaran |
| Project Lead | [Nama Lead] | Driver (D) | Mengemudikan eksekusi harian dan penghilang blocker |
| Software Engineer | [Nama Dev] | Contributor (C) | Membangun kode dan unit test |
| Operasional Lapangan | [Nama User Rep] | Informed (I) | Menerima update status rilis dan jadwal pelatihan |

---

## 7. Asumsi Utama & Risiko Awal [Wajib]

> **Panduan Pengisian:** Dokumentasikan asumsi dasar sebelum pengerjaan dan risiko teknis/bisnis awal yang teridentifikasi.

- **Asumsi Utama:** [Contoh: API pihak ketiga memiliki SLA ketersediaan 99.5% dan dokumentasi sandbox lengkap]
- **Risiko Awal #1:** [Keterlambatan izin integrasi payment vendor -> Mitigasi: Ajukan uji sandbox di minggu pertama]
- **Risiko Awal #2:** [Perubahan regulasi privasi data -> Mitigasi: Konsultasikan arsitektur data dengan tim legal]

---

## 8. Persetujuan Formal (Sign-Off) [Kondisional]
*(Seksi ini kondisional: dapat dihapus jika proyek hobi atau proyek personal solo developer)*

> **Panduan Pengisian:** Tanda tangan atau persetujuan tertulis dari pimpinan proyek dan sponsor.

| Peran | Nama Lengkap | Status / Keputusan | Tanggal |
|---|---|---|---|
| Project Sponsor | [Nama Sponsor] | [ ] Approved / [ ] Rejected | [YYYY-MM-DD] |
| Technical Lead | [Nama Tech Lead] | [ ] Approved / [ ] Rejected | [YYYY-MM-DD] |
| Product Manager | [Nama PM] | [ ] Approved / [ ] Rejected | [YYYY-MM-DD] |
