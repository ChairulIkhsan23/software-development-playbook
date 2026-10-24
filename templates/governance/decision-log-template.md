---
title: "Project Decision Log (Business, Scope & Process)"
project: "[Nama Proyek]"
owner: "[Nama Project Manager / Product Manager]"
status: "Active"
last_updated: "[YYYY-MM-DD]"
---

# Decision Log: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini mencatat keputusan-keputusan non-teknis atau lintas disiplin (bisnis, produk, ruang lingkup, dan proses kerja tim).
> Untuk keputusan arsitektur piranti lunak murni, gunakan dokumen `ADR` (Architecture Decision Record).
> ID format: `DEC-[NNN]`. Gunakan model peran DACI agar akuntabilitas keputusan jelas.

---

## 1. Definisi Peran DACI [Wajib]

> **Panduan Pengisian:**
> Pahami peran berikut sebelum mengisi pihak yang bertanggung jawab pada setiap keputusan:

- **Driver (D):** Individu yang bertanggung jawab memfasilitasi diskusi, mengumpulkan data, dan mengejar kesepakatan.
- **Approver (A):** Satu orang pengambil keputusan final yang memegang hak persetujuan atau veto.
- **Contributors (C):** Tenaga ahli yang dimintai pendapat teknis atau perspektif operasional.
- **Informed (I):** Anggota tim atau stakeholder yang diberitahu setelah keputusan disahkan.

## 2. Detail Keputusan (Decision Entry Form) [Wajib]

> **Panduan Pengisian:**
> Salin blok berikut setiap kali tim menghadapi dilema signifikan dan menyepakati jalan keluar.

### `[DEC-001]`: [Topik atau Dilema Keputusan Singkat]

- **ID Keputusan [Wajib]:** `DEC-001`
- **Tanggal Keputusan [Wajib]:** `[YYYY-MM-DD]`
- **Kategori [Wajib]:** `[Business | Product Scope | Team Process | Vendor Selection]`

#### Penugasan Peran DACI [Wajib]
- **Driver [Wajib]:** `[Nama - yang memfasilitasi riset dan pembahasan]`
- **Approver [Wajib]:** `[Nama - pengambil keputusan tunggal / PM / Sponsor]`
- **Contributors [Wajib]:** `[Nama Tech Lead, QA Lead, UI/UX Designer]`
- **Informed [Wajib]:** `[Seluruh Anggota Tim Proyek, Client Stakeholder]`

#### Konteks & Alternatif yang Dievaluasi [Wajib]
- **Masalah / Dilema:** [Jelaskan situasi yang memerlukan keputusan, misal: Pemilihan vendor payment gateway untuk pasar Indonesia]
- **Opsi yang Dievaluasi:**
  - *Opsi A:* [Vendor X - Integrasi cepat, fee 1.5% per transaksi, dokumentasi lengkap]
  - *Opsi B:* [Vendor Y - Fee 1.2% per transaksi, namun proses onboarding memakan waktu 4 minggu]
  - *Opsi C [Opsional]:* [Membangun koneksi direct transfer manual]

#### Keputusan yang Diambil & Rasional (Outcome & Rationale) [Wajib]
- **Pilihan Terpilih:** **[Opsi A: Menggunakan Vendor X]**
- **Alasan Penentu (Rationale):** [Kecepatan go-to-market lebih diprioritaskan untuk fase beta kuartal ini dibandingkan selisih fee 0.3%]

#### Peninjauan Ulang & Dokumen Terkait [Kondisional]
- **Review Trigger Date [Kondisional]:** `[YYYY-MM-DD - misal: 6 bulan setelah go-live untuk evaluasi ulang volume transaksi]`
- **Tautan Terkait [Opsional]:** `[Tautan ke PRD-002 / Tautan Dokumen Kontrak Vendor / Tautan Notulen Rapat]`

---

## 3. Tabel Rekapitulasi Keputusan (Decision Register) [Wajib]

> **Panduan Pengisian:**
> Catat intisari setiap keputusan pada tabel di bawah untuk penelusuran kilat oleh stakeholder atau anggota tim baru.

| ID | Tanggal | Topik Keputusan | Keputusan Terpilih | Approver (DACI) | Status | Tautan Dokumen |
|---|---|---|---|---|---|---|
| `DEC-001` | `[YYYY-MM-DD]` | Vendor Payment Gateway | Vendor X | [Nama PM] | Final | `PRD-SEC-03` |
| `DEC-002` | `[YYYY-MM-DD]` | Bahasa Standar Dokumentasi | Bahasa Indonesia + Tech English | [Nama Tech Lead] | Final | `PLAYBOOK-01` |
| `DEC-003` | `[YYYY-MM-DD]` | Batas Minimum Browser Support | Chrome, Edge, Safari versi 2 tahun terakhir | [Nama Frontend Lead] | Final | `SCR-ALL` |
| `DEC-004` | `[YYYY-MM-DD]` | Durasi Siklus Sprint Tim | 2 Minggu (Dimulai setiap hari Senin) | [Nama Scrum Master] | Review 3 Bln | `PROCESS-01` |

## 4. Catatan Keputusan yang Dibatalkan / Kedaluwarsa [Opsional] *(Boleh dihapus jika belum ada)*

> **Panduan Pengisian:**
> Jika ada keputusan terdahulu yang tidak lagi relevan akibat perubahan strategi pasar atau regulasi, catat di sini beserta alasannya.

- `DEC-000`: [Keputusan lama yang digantikan oleh DEC-XXX karena alasan restrukturisasi bisnis].
