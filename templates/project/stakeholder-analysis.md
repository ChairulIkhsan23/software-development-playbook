---
id: DOC-STK-001
title: Stakeholder Analysis & Communication Plan
project: "[Nama Proyek]"
version: 1.0.0
author: "[Nama Project Lead / PM]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Active / Approved
---

# Stakeholder Analysis & RACI Matrix: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini memetakan seluruh pemangku kepentingan (stakeholder), tingkat pengaruh dan minat mereka terhadap proyek, matriks akuntabilitas RACI, serta rencana komunikasi resmi. Format ID stakeholder: `STK-NNN`. Bagian bertanda `[Wajib]` harus diisi.

---

## 1. Stakeholder Identification Register [Wajib]

> **Panduan Pengisian:** Daftarkan semua individu atau kelompok yang mempengaruhi atau terpengaruh oleh proyek.

| ID STK | Nama Pemangku Kepentingan | Peran / Departemen | Tipe (Internal / Eksternal) | Minat / Sasaran Kunci | Potensi Hambatan / Kekhawatiran |
|---|---|---|:---:|---|---|
| STK-001 | [Nama Sponsor] | VP of Product & Tech | Internal | Peluncuran MVP tepat waktu sesuai roadmap Q3 | Keterlambatan jadwal memicu komitmen pasar terlewat |
| STK-002 | [Nama Lead Ops] | Head of Operations | Internal | Automasi pencatatan order & minim kesalahan data | Sistem baru membingungkan staf cabang non-teknis |
| STK-003 | [Nama Lead Legal] | Legal & Compliance Officer | Internal | Kepatuhan mutlak regulasi UU PDP & keamanan PII | Denda regulasi akibat penyimpanan log audit tidak rapi |
| STK-004 | [Nama Rep Merchant] | Partner Merchant Representative | Eksternal | Integrasi API checkout mudah & dokumentasi jelas | Downtime transaksi harian saat proses cutover migrasi |

---

## 2. Pemetaan Power-Interest Grid [Wajib]

> **Panduan Pengisian:** Petakan posisi stakeholder berdasarkan matriks Kekuasaan (Power) vs Ketertarikan (Interest) untuk menentukan strategi perlakuan.

```
Tinggi ▲
       │  [Keep Satisfied / Puaskan]       │  [Manage Closely / Kelola Dekat]
       │  • STK-003 (Legal & Compliance)   │  • STK-001 (VP of Product & Tech)
P      │                                   │  • STK-002 (Head of Operations)
O      │───────────────────────────────────┼─────────────────────────────────
W      │  [Monitor / Pantau Minimal]       │  [Keep Informed / Beri Informasi]
E      │  • Pihak Vendor Hosting Pihak ke-3│  • STK-004 (Partner Merchant Rep)
R      │                                   │  • Tim Layanan Pelanggan (CS Team)
Rendah ▼───────────────────────────────────┴─────────────────────────────────►
       Rendah                         INTEREST                       Tinggi
```

### Strategi Penanganan Kuadran:
- **Manage Closely (High Power, High Interest):** Kolaborasi aktif, libatkan dalam keputusan arsitektur & scope trade-off.
- **Keep Satisfied (High Power, Low Interest):** Penuhi kriteria regulasi/anggaran mereka tanpa membebani rapat harian.
- **Keep Informed (Low Power, High Interest):** Kirim pembaruan status berkala dan petunjuk panduan rilis.
- **Monitor (Low Power, Low Interest):** Komunikasi searah melalui log perubahan (*changelog*) berkala saja.

---

## 3. Matriks RACI Aktivitas Kunci [Wajib]

> **Panduan Pengisian:** Tentukan peran: **R** (Responsible - Pelaksana), **A** (Accountable - Penanggung jawab tunggal), **C** (Consulted - Dimintai masukan dua arah), **I** (Informed - Diberi informasi satu arah). *Catatan: Setiap baris aktivitas hanya boleh memiliki TEPAT SATU huruf 'A'*.

| Aktivitas Kunci / Fase Proyek | Project Sponsor | Product Lead | Tech Lead | Senior Engineer | QA Tester | Ops Lead |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Finalisasi Dokumen PRD (DOC-PRD-001) | I | **A** | C | C | C | C |
| Persetujuan Desain Solusi Arsitektur | C | C | **A** | R | I | I |
| Implementasi Kode Fitur Sprint | I | I | C | **R** / **A** | I | I |
| Pelaksanaan & Sign-Off Pengujian UAT | I | C | C | I | **R** | **A** |
| Keputusan Go-Live Deployment Produksi | C | **A** | C | R | I | I |

---

## 4. Rencana Saluran & Irama Komunikasi [Wajib]

> **Panduan Pengisian:** Tetapkan jadwal dan sarana komunikasi untuk menghindari miskomunikasi tim dan sponsor.

| Media / Kanal | Tujuan & Format | Frekuensi | Sasaran Audiens | Penanggung Jawab (Owner) |
|---|---|---|---|---|
| Slack `#proj-announcements` | Update singkat progres & pengumuman rilis | Setiap 2 hari | Seluruh tim & stakeholder terkait | [Nama Scrum Master / Lead] |
| Email Executive Status Report | Ringkasan metrik, anggaran, & blocker kritis | 2 minggu sekali (akhir sprint) | STK-001 (Sponsor), Eksekutif | [Nama PM] |
| Sprint Demo / Show & Tell | Demonstrasi langsung fitur jadi di staging | Setiap akhir sprint | Tim Pengembang & Tim Operasional | [Nama Product Lead] |

---

## 5. Protokol Eskalasi Masalah [Opsional]
*(Seksi ini opsional: dapat dihapus jika tim beranggotakan < 3 orang)*

> **Panduan Pengisian:** Tetapkan jalur eskalasi jika ditemukan isu blocker yang tidak dapat diselesaikan internal tim dev.

- **Level 1 (Tim Internal, < 24 jam):** Diselesaikan bersama Tech Lead dan PM via diskusi ad-hoc.
- **Level 2 (Llintas Divisi, 24 - 48 jam):** Tech Lead eskalasi ke VP/Head divisi terkait untuk resolusi dependensi.
- **Level 3 (Kritis / Sponsor, > 48 jam):** Pemanggilan rapat darurat dengan Project Sponsor untuk penyesuaian scope/timeline.
