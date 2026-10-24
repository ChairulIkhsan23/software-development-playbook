---
title: "Requirements Traceability Matrix (RTM)"
project: "[Nama Proyek]"
qa_lead: "[Nama QA Lead / Tech Lead]"
status: "Active"
last_updated: "[YYYY-MM-DD]"
---

# Requirements Traceability Matrix (RTM): [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini menghubungkan tujuan bisnis tingkat tinggi hingga ke kode program dan pengujian jaminan kualitas (*QA*).
> Berfungsi membuktikan bahwa tidak ada kebutuhan PRD yang terlewat atau kode yang dibuat tanpa tujuan bisnis yang jelas (*gold plating*).
> Gunakan format ID konsisten: `BG-[NNN]` -> `PRD-[CAT]-[NNN]` -> `US-[NNN]` -> `TC-[NNN]`.

---

## 1. Alur Rantai Penelusuran (Traceability Chain) [Wajib]

> **Panduan Pengisian:**
> Pahami hubungan berjenjang antar-artefak proyek berikut sebelum mengisi tabel matriks:

- **Business Goal (BG):** Sasaran strategis bisnis organisasi (contoh: `BG-01: Meningkatkan konversi pembayaran`).
- **PRD Requirement (PRD):** Kebutuhan fungsional formal dari dokumen PRD (contoh: `PRD-PAY-001`).
- **User Story (US):** Tiket pekerjaan pada backlog Sprint pengembang (contoh: `US-104`).
- **Design Link:** Tautan frame Figma atau komponen desain UI terkait.
- **Code / PR Reference:** Tautan Pull Request atau lokasi file modul backend/frontend.
- **Test Case (TC):** Skenario pengujian QA manual atau script automated test (contoh: `TC-PAY-021`).
- **Verification Status:** `[Passed | Failed | Pending | Blocked]`.

## 2. Tabel Matriks Penelusuran (Traceability Matrix Table) [Wajib]

> **Panduan Pengisian:**
> Perbarui status verifikasi secara berkala, terutama saat sprint review atau fase *User Acceptance Testing* (UAT).

| Business Goal ID | PRD Req ID | Deskripsi Kebutuhan Singkat | User Story ID | Tautan Desain UI | Referensi Kode / PR | Test Case ID | Status Verifikasi |
|---|---|---|---|---|---|---|---|
| `BG-01` | `PRD-PAY-001` | Integrasi QRIS dinamis real-time | `US-101` | [Figma Frame 12] | `PR #42 (payments/qris.ts)` | `TC-PAY-001` | [Passed] |
| `BG-01` | `PRD-PAY-002` | Notifikasi callback webhook sukses bayar | `US-102` | - *(N/A API)* | `PR #45 (webhooks/handler.go)` | `TC-PAY-002` | [Passed] |
| `BG-02` | `PRD-AUTH-001` | Registrasi akun baru dengan OTP WhatsApp | `US-205` | [Figma Frame 04] | `PR #51 (auth/otp-flow.tsx)` | `TC-AUTH-015`| [Pending] |
| `BG-02` | `PRD-AUTH-002` | Rate limiting kirim OTP maksimal 3x/jam | `US-206` | - *(N/A Backend)* | `PR #53 (middlewares/rate-limit.ts)` | `TC-AUTH-016`| [Passed] |
| `BG-03` | `PRD-ORD-001` | Ekspor rekapitulasi transaksi ke format CSV | `US-310` | [Figma Frame 28] | `PR #60 (reports/exporter.ts)` | `TC-REP-005` | [Failed] |

## 3. Ringkasan Cakupan Verifikasi (Coverage Summary) [Wajib]

> **Panduan Pengisian:**
> Hitung persentase kelengkapan verifikasi sebelum memutuskan kesiapan rilis fitur ke produksi.

- **Total PRD Requirements Terpetakan:** `5`
- **Total Test Cases Terkait:** `5`
- **Passed:** `3 (60%)` | **Pending:** `1 (20%)` | **Failed:** `1 (20%)` | **Blocked:** `0 (0%)`
- **Kesiapan Rilis (Ready for Release):** `Belum Memenuhi Syarat (Masih ada status Failed pada TC-REP-005)`

## 4. Panduan Pemeliharaan Efisien bagi Tim Kecil (Low Overhead Guide) [Wajib]

> **Panduan Pengisian:**
> RTM sering ditinggalkan jika perawatannya terlalu membebani. Terapkan strategi minimalis berikut:

- **Tautkan di PR Template:** Wajibkan developer mencantumkan `Fixes US-XXX` dan `Relates to PRD-XXX` pada setiap deskripsi Pull Request.
- **Pembaruan Satu Pintu oleh QA:** Jadwalkan QA memperbarui status tabel matriks hanya saat sesi Sprint Demo atau sebelum rilis mingguan.
- **Hanya untuk Alur Kritis:** Untuk tim solo atau tim < 3 orang, fokuskan RTM pada alur bernilai uang (*revenue critical*) dan otentikasi.
- **Otomatisasi Status via CI:** Jika menggunakan framework test (Jest/Playwright), beri tag `@TC-XXX` pada skrip tes agar status dapat di-grep otomatis.

## 5. Prosedur Penyesuaian saat Ada Change Request [Kondisional] *(Boleh dihapus jika tanpa CR)*

> **Panduan Pengisian:**
> Hubungkan perubahan lingkup dari Change Log ke dalam matriks penelusuran.

- Jika ada `CR-NNN` yang disetujui, tambahkan baris baru dengan anotasi `[CR-NNN]` pada kolom PRD Req ID.
- Test case lama yang terdampak wajib diberi status `Pending` hingga skrip pengujian disesuaikan.

## 6. Kebutuhan yang Ditunda / Dihapus (Deferred Requirements) [Opsional] *(Boleh dihapus jika tidak ada)*

> **Panduan Pengisian:**
> Catat kebutuhan PRD yang dibatalkan atau ditunda agar tidak membingungkan tim QA saat memverifikasi coverage.

- `PRD-ORD-003`: Integrasi pencetakan label thermal otomatis — *Ditunda ke Fase 2 berdasarkan keputusan DEC-005*.
