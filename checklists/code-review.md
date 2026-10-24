# Code Review Quality Checklist

Checklist evaluasi Pull Request (PR) untuk author dan reviewer. Menjaga kualitas basis kode, stabilitas sistem, dan mencegah cacat lolos ke branch utama (`main`/`develop`).

---

## 1. Self-Review Author (Sebelum Buka PR)
- [ ] **Self-Diff Inspection**: Author telah meninjau diff perubahan secara mandiri baris demi baris di GitHub/GitLab.
- [ ] **Branch Up-to-Date**: Branch fitur sudah di-*rebase* atau di-*merge* dengan branch basis terbaru tanpa konflik.
- [ ] **Local Build & Tests Green**: Build proyek, typecheck (`tsc --noEmit`), dan automated tests lolos 100% di lokal.
- [ ] **Descriptive PR Description**: Deskripsi PR memuat latar belakang perubahan, tautan tiket, dan cara pengujian.
- [ ] **Visual Proof Attached**: Screenshot atau rekaman layar (GIF/Loom) disertakan untuk perubahan antarmuka (UI).
- [ ] **PR Size Checked**: Jumlah baris perubahan di bawah 400 baris; PR raksasa dipecah menjadi beberapa PR berurutan.

---

## 2. Functionality & Correctness
- [ ] **Acceptance Criteria Satisfied**: Implementasi kode memenuhi seluruh kriteria penerimaan pada tiket tanpa deviasi tersembunyi.
- [ ] **Edge Cases Handled**: Kondisi batas (array kosong, nilai null/undefined, string kosong, angka negatif) tertangani anggun.
- [ ] **No Regression**: Perubahan tidak merusak alur kerja eksisting atau fitur lain yang saling bergantung.
- [ ] **Idempotency & Concurrency**: Operasi pengubahan data (POST/PATCH) aman dari request ganda dan race condition.
- [ ] **Proper Error Propagation**: Kesalahan ditangkap dan diteruskan dengan konteks yang jelas, tidak ditelan mentah-mentah (`catch (e) {}` kosong dilarang).
- [ ] **Async / Await Hygiene**: Seluruh Promise di-await dengan benar; tidak ada unhandled promise rejections tersembunyi.

---

## 3. Architecture & Design
- [ ] **Project Structure Conformance**: File dan folder ditempatkan sesuai konvensi modular / layered architecture proyek.
- [ ] **SOLID Principles Respected**: Modul memiliki tanggung jawab tunggal (SRP) dan dependensi diabstraksi dengan wajar.
- [ ] **KISS & DRY Balanced**: Hindari duplikasi logika yang identik; jangan membuat abstraksi prematur untuk kode yang baru dipakai sekali.
- [ ] **No Unauthorized Abstractions**: Tidak ada *premature generalization* (jangan buat factory atau generic class jika implementasi cuma satu).
- [ ] **Separation of Concerns**: Logika bisnis murni terisolasi dari controller web, layer UI, atau kueri database langsung.
- [ ] **Minimal Dependency Footprint**: Tidak menambah dependensi NPM baru jika fungsionalitas dapat dicapai via stdlib atau 5 baris kode.

---

## 4. Code Quality & Readability
- [ ] **TypeScript Strict Conformance**: Bebas dari penggunaan tipe liar `any`; gunakan `unknown`, generics terukur, atau interface eksplisit.
- [ ] **Intuitive Naming Conventions**: Nama variabel, fungsi, dan class mencerminkan intensi bisnis dengan jelas (contoh: `isSubscriptionActive`, bukan `flag`).
- [ ] **Small & Focused Functions**: Panjang fungsi ideal di bawah 30 baris; fungsi kompleks dipecah menjadi helper fungsional yang teruji.
- [ ] **Dead Code & Debugger Removed**: Log `console.log`, debugger statements, dan kode yang di-comment out dibersihkan sebelum PR diajukan.
- [ ] **Consistent Formatting**: Kode lolos verifikasi linter dan formatter otomatis tanpa bypass flag (`eslint-disable`).
- [ ] **No Magic Numbers**: Angka atau string konstan diabstraksi ke dalam konstanta bernama deskriptif.

---

## 5. Security & Performance
- [ ] **No Hardcoded Secrets**: Tidak ada kunci API, password database, atau token privat tertanam di file kode.
- [ ] **Boundary Input Validation**: Seluruh data yang masuk dari user atau webhook divalidasi skemanya sebelum diproses.
- [ ] **No N+1 Query Problem**: Kueri relasi database dimuat menggunakan batch fetching, eager loading, atau `JOIN` yang tepat.
- [ ] **Index Coverage for Queries**: Kueri pencarian, pengurutan, atau filter `WHERE` memanfaatkan index database yang sudah ada.
- [ ] **Resource Cleanup**: Koneksi database, file streams, event listener, atau subscription React dibersihkan (*cleanup/unsubscribe*) cegah memory leak.
- [ ] **Payload Sanitization**: Cegah over-fetching; kembalikan field respons seperlunya tanpa mengekspos hash sandi internal.

---

## 6. Testing & Documentation
- [ ] **Automated Tests Included**: Unit test atau integration test baru disertakan mencakup skenario sukses dan skenario batas utama.
- [ ] **Meaningful Assertions**: Test melakukan verifikasi kondisi riil, bukan sekadar tes formalitas demi mengejar persentase coverage.
- [ ] **Comments Explain "Why", Not "What"**: Komentar kode hanya dipakai untuk menjelaskan keputusan teknis yang tidak lazim atau workarounds kompleks.
- [ ] **API & Schema Documentation Updated**: Dokumentasi OpenAPI, model database, atau file README diperbarui jika kontrak sistem berubah.
- [ ] **Migration Scripts Tested**: Skrip migrasi database sudah diuji eksekusi maju (*up*) dan mundur (*down*) di lokal.

---

## Panduan Peninjau Kode (Approver Guidelines)
Reviewer wajib menerapkan etika dan standar objektif saat memberikan ulasan:

### Konvensi Prefiks Komentar PR
Gunakan label prefiks untuk memisahkan instruksi wajib dari saran opsional:
- `[BLOCKER]`: Cacat logika, celah keamanan, atau pelanggaran arsitektur berat. PR dilarang di-merge sebelum diperbaiki.
- `[SUGGESTION]`: Saran perbaikan keterbacaan atau alternatif syntax lebih bersih. Author bebas memilih untuk menerapkan atau menolak beralasan.
- `[QUESTION]`: Pertanyaan klarifikasi intensi kode; tidak mengharuskan perubahan jika penjelasan memadai.
- `[NITPICK]`: Masalah gaya penulisan minor (e.g., typo di komentar). Boleh diabaikan jika tidak berdampak fungsi.

### Standar Persetujuan (Approval Policy)
1. **Ukuran PR Ideal**: PR dibatasi maksimal 300-400 baris perubahan agar review tetap cermat. PR berukuran > 800 baris wajib dipecah.
2. **Review Responsif**: Reviewer wajib memberikan umpan balik pertama dalam waktu maksimal 4 jam kerja.
3. **Syarat Merge**: Minimal 1 approval dari Tech Lead atau Senior Dev, seluruh komentar bertanda `[BLOCKER]` tuntas ter-resolve, dan status CI hijau.
4. **Resolusi Perbedaan Pendapat**: Jika terjadi perdebatan arsitektur lebih dari 5 komentar, pindahkan diskusi ke panggilan suara 10 menit lalu tulis ringkasan keputusan di PR.
5. **Kebijakan Re-review**: Author wajib me-request re-review hanya setelah semua komentar ditindaklanjuti dan komit baru di-push.
6. **Squash and Merge**: Gunakan opsi Squash Merge pada GitHub agar histori branch utama tetap rapi dan linear.