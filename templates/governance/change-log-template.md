---
title: "Project Change Log & Change Request (CR) Form"
project: "[Nama Proyek]"
project_lead: "[Nama Project Manager / Tech Lead]"
status: "Active"
last_updated: "[YYYY-MM-DD]"
---

# Project Change Log: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini mencatat setiap perubahan terhadap baseline proyek yang telah disepakati (ruang lingkup, jadwal, atau biaya).
> Gunakan Bagian 1 sebagai formulir pengajuan detail per perubahan (ID format: `CR-[NNN]`).
> Gunakan Bagian 2 sebagai tabel rekapitulasi cepat (*change register*) untuk pemantauan manajemen.
> Perubahan minor tanpa dampak jadwal/biaya cukup dicatat dalam tiket task harian, bukan CR.

---

## 1. Formulir Permintaan Perubahan (Change Request Detail) [Wajib]

> **Panduan Pengisian:**
> Salin blok ini setiap ada usulan perubahan baru. Isi analisis dampak secara objektif sebelum meminta persetujuan formal.

### `[CR-001]`: [Judul Singkat Permintaan Perubahan]

- **ID Perubahan [Wajib]:** `CR-001`
- **Tanggal Pengajuan [Wajib]:** `[YYYY-MM-DD]`
- **Pemohon (Requester) [Wajib]:** `[Nama Pemohon / Jabatan, misal: Product Manager]`
- **Deskripsi Perubahan [Wajib]:**
  - [Jelaskan apa yang ingin diubah dari baseline saat ini secara spesifik]
- **Alasan & Justifikasi Bisnis [Wajib]:**
  - [Mengapa perubahan ini diperlukan? Dampak bisnis jika permintaan ini tidak dijalankan]

#### Analisis Dampak (Impact Analysis) [Wajib]
| Dimensi Dampak | Tingkat Dampak (Low/Med/High) | Rincian Dampak |
|---|---|---|
| **Scope (Lingkup)** | High | Menambahkan alur integrasi payment gateway metode QRIS dinamis |
| **Schedule (Jadwal)** | Medium | Estimasi rilis M3 bergeser mundur 1 minggu (Sprint 6 -> Sprint 7) |
| **Cost (Biaya)** | Low | Biaya lisensi provider QRIS tambahan $15/bulan |
| **Quality (Kualitas)** | Low | Memerlukan automated test tambahan untuk skenario webhook timeout |
| **Risk (Risiko)** | Medium | Ketergantungan pada waktu persetujuan verifikasi akun merchant bank |

#### Keputusan & Otoritas Persetujuan (Governance & Sign-Off) [Wajib]
- **Hasil Review [Wajib]:** `[Approved | Rejected | Deferred]`
- **Otoritas Penyetuju [Wajib]:** `[Nama Project Sponsor / Steering Committee Lead]`
- **Tanggal Keputusan [Wajib]:** `[YYYY-MM-DD]`
- **Catatan / Syarat Persetujuan [Kondisional]:** [Disetujui dengan syarat fitur laporan PDF ditunda ke fase berikutnya]
- **Status Implementasi [Wajib]:** `[Backlog | In Progress | Deployed | Cancelled]`

---

## 2. Tabel Rekapitulasi Perubahan (Change Log Register) [Wajib]

> **Panduan Pengisian:**
> Perbarui tabel ringkasan ini setiap kali ada perubahan status pada Change Request.

| ID CR | Tgl Pengajuan | Pemohon | Ringkasan Perubahan | Dampak Jadwal | Dampak Biaya | Status Review | Status Implementasi |
|---|---|---|---|---|---|---|---|
| `CR-001` | `[YYYY-MM-DD]` | PM E-Commerce | Tambah QRIS dinamis | +1 Minggu | +$15/bln | Approved | In Progress |
| `CR-002` | `[YYYY-MM-DD]` | QA Lead | Tambah automated E2E load test | +3 Hari | $0 | Approved | Backlog |
| `CR-003` | `[YYYY-MM-DD]` | Marketing Lead | Custom loyalty points reward | +3 Minggu | +$500 dev | Rejected | Cancelled |

## 3. Rencana Komunikasi Perubahan [Kondisional] *(Boleh dihapus jika tim < 3 orang)*

> **Panduan Pengisian:**
> Tentukan pihak mana saja yang wajib diberitahu setelah perubahan disetujui.

- **Tim Pengembang & QA:** Diberitahu via kanal Slack/Discord segera setelah CR berstatus `Approved`.
- **Stakeholder Bisnis & Klien:** Disampaikan pada sesi Sprint Review mingguan oleh Project Lead.

## 4. Catatan Penyesuaian Baseline [Opsional] *(Boleh dihapus jika tidak mengubah kontrak formal)*

> **Panduan Pengisian:**
> Catat versi dokumen perencanaan yang direvisi akibat disetujuinya CR di atas.

- **Baseline Scope:** PRD v1.0 diperbarui menjadi PRD v1.1 mencakup CR-001.
- **Baseline Jadwal:** Rencana Peluncuran Umum digeser dari `[YYYY-MM-DD]` menjadi `[YYYY-MM-DD]`.
