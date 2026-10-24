---
title: "Template Code Review Checklist"
document_id: "CR-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
author: "[Nama Author]"
reviewer: "[Nama Reviewer]"
pr_number: "PR-[NNN]"
repository: "[owner/repo-name]"
---

# Code Review Checklist

> **Panduan Pengisian:** Gunakan checklist ini sebelum mengajukan Pull Request (Author) dan saat meninjau kode (Reviewer). Pastikan semua kriteria wajib terpenuhi sebelum status persetujuan diberikan.

---

## 1. Metadata Pull Request [Wajib]

> **Panduan Pengisian:** Isi informasi dasar tiket dan tautan referensi terkait.

- **Nomor PR [Wajib]:** `PR-[NNN]`
- **Tautan Tiket / Isu [Wajib]:** `[JIRA-NNN / GH-NNN]`
- **Tipe Perubahan [Wajib]:** `[Feature / Bugfix / Refactor / Hotfix / Docs]`
- **Tautan Dokumentasi [Opsional]:** `[URL Dokumentasi / ADR / Figma]`

---

## 2. Author Self-Review Checklist [Wajib]

> **Panduan Pengisian:** Tandai seluruh item di bawah ini sebelum memindahkan status PR menjadi "Ready for Review".

- [ ] [Wajib] Kode telah diuji mandiri di lingkungan lokal dan berjalan tanpa error.
- [ ] [Wajib] Branch telah disinkronkan dengan branch target terbaru (`main` atau `develop`).
- [ ] [Wajib] Seluruh automated unit test baru maupun eksisting lulus di lokal.
- [ ] [Wajib] Tidak meninggalkan komentar sementara, baris debugging, atau `console.log`.
- [ ] [Wajib] Deskripsi PR merangkum perubahan, konteks bisnis, dan langkah pengujian manual.
- [ ] [Opsional] Screenshot / video demonstrasi dilampirkan untuk perubahan UI.

---

## 3. Reviewer Checklist [Wajib]

> **Panduan Pengisian:** Reviewer wajib memeriksa aspek teknis pada 5 area evaluasi berikut.

### A. Kebenaran Fungsionalitas (Functionality) [Wajib]
- [ ] [Wajib] Logika kode memenuhi acceptance criteria pada tiket terkait.
- [ ] [Wajib] Seluruh edge cases dan skenario batas (boundary conditions) tertangani aman.
- [ ] [Wajib] Penanganan error (error handling) tidak menelan exception secara diam-diam.

### B. Arsitektur & Prinsip SOLID [Wajib]
- [ ] [Wajib] Struktur kode mematuhi Single Responsibility Principle (SRP).
- [ ] [Wajib] Tidak memperkenalkan coupling antarmodul yang tidak perlu (hindari circular dependency).
- [ ] [Kondisional] Abstraksi baru hanya dibuat jika benar-benar memiliki lebih dari satu implementasi.

### C. Kualitas Kode & Typing [Wajib]
- [ ] [Wajib] Naming convention jelas, konsisten, dan mencerminkan domain bisnis.
- [ ] [Wajib] Type definitions ketat (hindari penggunaan tipe implisit `any`).
- [ ] [Wajib] Tidak ada duplikasi logika yang melanggar prinsip DRY.

### D. Keamanan & Performa (Security & Performance) [Wajib]
- [ ] [Wajib] Tidak ada API keys, secrets, token, atau kredensial yang masuk commit.
- [ ] [Wajib] Input pengguna divalidasi dan disanitasi di sisi backend (proteksi injection / XSS).
- [ ] [Wajib] Query database tidak menimbulkan masalah N+1 queries atau full table scan tanpa indeks.

### E. Pengujian & Dokumentasi (Tests & Docs) [Wajib]
- [ ] [Wajib] Test coverage mencakup skenario happy path dan failure path.
- [ ] [Wajib] Dokumentasi API (OpenAPI/Swagger) atau README diperbarui jika kontrak berubah.

---

## 4. Standar Prefix Komentar Review [Wajib]

> **Panduan Pengisian:** Gunakan prefix berikut pada komentar inline agar author memahami urgensi masukan.

- `[BLOCKER] [Wajib]` : Isu kritis (keamanan, bug logika, data loss). PR tidak boleh dimerge sebelum diperbaiki.
- `[SUGGESTION] [Opsional]` : Rekomendasi perbaikan kualitas atau alternatif solusi yang lebih bersih.
- `[NITPICK] [Opsional]` : Masalah minor gaya penulisan atau typo yang tidak memengaruhi logika.
- `[QUESTION] [Kondisional]` : Pertanyaan klarifikasi niat implementasi kode yang membutuhkan jawaban author.

---

## 5. Keputusan Review (Approval Verdict) [Wajib]

> **Panduan Pengisian:** Tentukan verdict akhir berdasarkan kondisi temuan checklist di atas.

- **Status Putusan [Wajib]:** `[Approved / Request Changes / Comment]`
- **Alasan / Catatan Evaluasi [Wajib]:**
  - `[Jelaskan catatan ringkas atau tindak lanjut yang diperlukan sebelum merge]`
- **Reviewer Sign-off [Wajib]:** `[Nama Reviewer]`, `[YYYY-MM-DD HH:mm]`

*(Seksi catatan tambahan di bawah ini opsional: hapus jika tidak diperlukan)*
## 6. Catatan Khusus Modul [Opsional]
- `[Catatan teknis spesifik untuk dependensi eksternal, jadwal migrasi, atau rilis bertahap]`
