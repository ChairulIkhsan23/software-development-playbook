# Code Review Guide

Panduan komprehensif pelaksanaan code review untuk menjaga kualitas perangkat lunak, mendorong pertukaran wawasan, dan membangun budaya kolaborasi tim yang sehat dan berkinerja tinggi.

---

## 1. Tujuan Code Review

Code review bukan ajang inspeksi atau kepolisian kode (*policing*), melainkan proses kolaboratif dengan tujuan utama:
- **Menjaga Kualitas Kode:** Menemukan bug logika, celah keamanan, dan inefisiensi arsitektur sebelum kode masuk ke branch produksi.
- **Berbagi Pengetahuan (Knowledge Sharing):** Menghilangkan *silo* pengetahuan. Seluruh anggota tim memahami perubahan fitur yang dibangun rekan kerjanya.
- **Konsistensi Basis Kode:** Memastikan kode baru mematuhi standar tim, pola arsitektur terpilih, dan konvensi penamaan yang telah disepakati.
- **Sarana Mentorship:** Membantu junior dan solo developer bertumbuh lewat feedback terarah, konstruktif, dan berbasis solusi nyata.
- **Kepemilikan Kolektif (Shared Ownership):** Setelah PR di-merge, kode menjadi tanggung jawab bersama tim, bukan hanya milik penulis awal.

---

## 2. Alur Siklus Hidup Pull Request

```text
[Author] Buat Branch & Commit Atomik
   │
   ▼
[Author] Self-Review & Isi Deskripsi PR Lengkap
   │
   ▼
[CI/CD] Automated Checks (Linter, Tests, Build, Security Scan)
   │ ──(Gagal)──► Author Memperbaiki
   ▼ (Lolos)
[Reviewer] Tinjau Kode & Berikan Feedback Berlabel
   │ ──(Perubahan Diminta)──► Author Revisi
   ▼ (Disetujui)
[Author / Lead] Squash & Merge ke main
```

---

## 3. Etika Code Review (Review Etiquette)

### A. Untuk Penulis PR (Author)
1. **Batasi Ukuran PR (< 400 Baris Perubahan):** PR berukuran raksasa sulit direview secara mendalam dan sering kali hanya disetujui tanpa ketelitian (*rubber-stamped*). Pecah fitur besar menjadi beberapa PR kecil yang berurutan.
2. **Lakukan Self-Review Sebelum Mengundang Tim:** Periksa kembali diff Anda di platform Git sebelum meminta review orang lain. Bersihkan sisa `console.log`, file temporary yang tidak sengaja terbawa, atau kesalahan ketik (typo).
3. **Sediakan Konteks Lengkap pada Deskripsi PR:** Ikuti templat standar PR tim agar reviewer dapat memahami latar belakang perubahan dalam 1 menit.

```markdown
### Ringkasan Perubahan
Memperbaiki bug race condition saat user menekan tombol checkout berkali-kali secara simultan.

### Relasi Tiket
Closes #142

### Pendekatan Solusi
Menggunakan database pessimistic lock dan idempotent request token pada endpoint mutasi pembayaran.

### Cara Pengujian (Manual Testing)
1. Buka halaman checkout di `/checkout`.
2. Buka DevTools Network, set throttling ke Slow 3G.
3. Klik tombol bayar berkali-kali secara cepat.
4. Pastikan hanya 1 request mutasi yang diproses server dan tidak terjadi double charge.
```

### B. Untuk Penilai PR (Reviewer)
1. **Berikan Feedback Konstruktif dan Bersolusi:** Jelaskan *mengapa* suatu perubahan diperlukan dan sertakan contoh kode pengganti. Hindari komentar satu arah yang bernada merendahkan seperti "ini jelek" atau "kode ini salah".
2. **Gunakan Label Konteks Komentar:**
   - `[Blocker]`: Celah keamanan nyata, bug fatal, hilangnya data, atau pelanggaran arsitektur berat yang wajib diperbaiki sebelum merge.
   - `[Nitpick]`: Preferensi minor atau saran formatting non-kritis yang tidak menghalangi proses merge.
   - `[Question]`: Pertanyaan untuk mengonfirmasi pemahaman atau konteks implementasi.
   - `[Suggestion]`: Ide optimasi alternatif yang sifatnya opsional untuk dipertimbangkan.
3. **Prinsip "Approve with Minor Suggestions":** Jika PR hanya membutuhkan perbaikan kecil yang tidak berisiko, berikan status *Approve* disertai catatan perbaikan agar author dapat langsung me-merge setelah commit perbaikan tanpa menunggu siklus review baru.
4. **Berikan Apresiasi:** Jangan hanya mencari kesalahan; puji implementasi yang cerdas, efisien, atau struktur kode yang rapi.

---

## 4. Contoh Komentar Review: Baik vs Buruk

| Situasi | Komentar Buruk (Hindari) | Komentar Konstruktif (Gunakan) |
| :--- | :--- | :--- |
| **Pola Perulangan Tidak Efisien** | "Jangan pakai for-loop di sini, lambat dan jelek." | `[Suggestion]` "Kueri database di dalam loop ini berisiko menimbulkan masalah N+1. Bagaimana jika kita gunakan `WHERE id IN (...)` lalu dipetakan menggunakan `Map`?" |
| **Validasi Input Terlewat** | "Ini bahaya, bisa di-hack." | `[Blocker]` "Parameter `userId` diambil langsung dari URL tanpa validasi UUID dan otorisasi kepemilikan data. User bisa melihat data milik user lain. Mohon tambahkan guard middleware di sini." |
| **Penamaan Kurang Jelas** | "Ganti nama variabel ini, ambigu." | `[Nitpick]` "Nama variabel `d` mungkin lebih mudah dipahami rekan lain jika diubah menjadi `durationInDays` agar unit waktunya eksplisit." |
| **Arsitektur Modular** | "Kenapa komponen ini ditaruh di sini?" | `[Question]` "Saya melihat komponen `InvoiceCard` ditaruh di `/shared`. Apakah ada halaman selain modul billing yang membutuhkan komponen ini sekarang?" |

---

## 5. Checklist Evaluasi Reviewer

Reviewer harus memverifikasi lima pilar evaluasi berikut:

### 1. Kebenaran Logika (Logic & Correctness)
- Apakah kode memenuhi seluruh Acceptance Criteria dari Product Requirement?
- Apakah seluruh kondisi batas (*edge cases*) telah ditangani (misal: array kosong, data bernilai null/undefined, network error)?
- Apakah ada potensi race conditions pada alur asinkronus?

### 2. Keamanan (Security)
- Apakah seluruh input dari pengguna divalidasi dan disanitasi di boundary layer?
- Tidak ada rahasia, password, token API, atau private key yang di-hardcode ke dalam kode.
- Kueri database aman dari SQL Injection (menggunakan parameterized query atau ORM).
- Otorisasi dan role-based access control (RBAC) diperiksa sebelum mengeksekusi operasi data sensitif.

### 3. Performa & Skalabilitas (Performance)
- Tidak ada kueri berulang dalam perulangan (*N+1 database queries*).
- Indeks database yang relevan telah ditambahkan untuk filter atau join yang intensif.
- Menghindari alokasi memori berlebih atau kebocoran resource (misal: event listener dan streaming socket yang lupa ditutup).

### 4. Pengujian (Testing)
- Kode baru dilengkapi unit test atau integration test yang relevan.
- Test menguji skenario sukses (*happy path*) serta skenario kegagalan (*error path*).
- Test tidak bergantung pada state eksternal yang flaky atau urutan eksekusi acak.

### 5. Keterbacaan & Pemeliharaan (Readability & Maintainability)
- Penamaan variabel, fungsi, dan class mencerminkan terminologi domain bisnis secara jelas.
- Tidak ada duplikasi logika bisnis (*DRY - Don't Repeat Yourself*).
- Kode terorganisasi rapi sesuai batas modul yang telah ditetapkan.

---

## 6. Checklist Khusus Perubahan UI / Frontend

Untuk PR yang melibatkan antarmuka pengguna:
- **Responsif:** Tampilan berfungsi baik pada resolusi mobile (360px), tablet (768px), dan desktop (1280px+).
- **Aksesibilitas (A11y):** Form input memiliki label terkait, tombol memiliki teks atau `aria-label`, dan navigasi keyboard berfungsi tanpa mouse trap.
- **Loading & Empty State:** Komponen memiliki penanganan visual saat data sedang dimuat (skeleton/spinner) dan saat data kosong (*empty state*).

---

## 7. Waktu Respon Review (Turnaround SLA) & Eskalasi

| Kategori PR | Target SLA Review Pertama | Jalur Eskalasi jika Tertunda |
| :--- | :--- | :--- |
| **Hotfix / P0 Insiden** | < 2 Jam | Colek langsung via telepon / channel insiden di Slack. |
| **PR Fitur Reguler (< 400 baris)** | < 24 Jam Kerja | Mention reviewer di channel tim setelah 24 jam. |
| **PR Arsitektur Besar (> 400 baris)** | < 48 Jam Kerja | Jadwalkan sesi live walkthrough review 30 menit. |

---

## 8. Menangani Perbedaan Pendapat (Handling Disagreements)

1. **Aturan 5 Komentar:** Jika satu thread diskusi telah melampaui 5 komentar bolak-balik tanpa ada titik temu, hentikan perdebatan di teks. Segera jadwalkan panggilan suara atau tatap muka selama 5-10 menit.
2. **Utamakan Bukti Objektif:** Jadikan dokumentasi resmi, benchmark performa nyata, atau buku standar industri sebagai rujukan perbandingan, bukan opini pribadi.
3. **Eskalasi ke Tech Lead:** Jika kata sepakat tetap tidak tercapai, bawa masalah ke Tech Lead atau Principal Engineer untuk keputusan final. Begitu keputusan diambil, kedua pihak berkomitmen mendukung hasil (*disagree and commit*).

---

## 9. Otomasi dalam Pipeline Code Review

Hemat energi tim manusia dengan menyerahkan pengecekan mekanis kepada sistem otomatis CI/CD:
- **Linters & Formatters (ESLint, Prettier, Ruff, Golangci-lint):** Memastikan konsistensi penulisan dan sintaksis sebelum review manusia dimulai.
- **Static Application Security Testing (SAST):** Alat seperti SonarQube, CodeQL, atau Snyk otomatis memindai kerentanan keamanan dan *code smells* pada setiap PR baru.
- **Branch Protection Rules:** Lindungi branch utama (`main` / `develop`) agar tidak dapat di-merge jika build CI gagal atau belum memperoleh approval minimal 1 reviewer.

---

## 10. Metrik Kesehatan Code Review Tim

Gunakan metrik berikut saat evaluasi sprint untuk mengukur efektivitas proses review:
- **Review Turnaround Time:** Durasi rata-rata dari pembukaan PR hingga review pertama diberikan.
- **PR Size Distribution:** Persentase PR yang berukuran di bawah 400 baris.
- **Review Depth:** Rata-rata jumlah komentar substansial per PR (menghindari rubber-stamping).

---

## 11. Kesalahan Umum dalam Code Review

1. **Rubber Stamping ("LGTM" Tanpa Membaca):** Menyetujui PR dalam beberapa detik tanpa memeriksa perubahan logika secara kritis.
2. **Review Adu Ego:** Menyerang kemampuan personal penulis kode dibanding mengevaluasi implementasi teknis secara objektif.
3. **Scope Creep saat Review:** Menuntut perbaikan kode lama di luar cakupan pekerjaan PR yang sedang dibahas.
4. **Mendebatkan Formatting Manual:** Menghabiskan waktu memperdebatkan spasi, kurung kurawal, atau titik koma yang semestinya dikerjakan otomatis oleh formatter.

---

## 12. Dokumen & Template Terkait

- [Code Review Checklist Detail](../checklists/code-review.md)
- [Template PR Review Checklist](../templates/development/code-review-checklist.md)
- [Coding Standards](coding-standards.md)
- [Branching and Commits](branching-and-commits.md)
- [Repository Structure](repository-structure.md)
