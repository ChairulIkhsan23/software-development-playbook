---
title: "Template Definition of Done (DoD)"
document_id: "DOD-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
owner: "[Nama Tech Lead / QA Lead]"
team_name: "[Nama Tim Rekayasa Perangkat Lunak]"
applicable_scope: "Semua Pull Request & User Story"
---

# Definition of Done (DoD) Standard

> **Panduan Pengisian:** Gunakan dokumen ini sebagai tolok ukur universal kelengkapan tugas teknis. Suatu User Story, bugfix, atau task teknis TIDAK BOLEH ditandai sebagai status "Done / Closed" sebelum setiap kriteria wajib di bawah ini terpenuhi secara penuh.

---

## 1. Metadata Standar DoD [Wajib]

> **Panduan Pengisian:** Tentukan identitas dan lingkup keberlakuan standar kualitas pengerjaan tim.

- **Nama Tim Rekayasa [Wajib]:** `[Nama Tim / Skuad]`
- **Penanggung Jawab Kepatuhan [Wajib]:** `[Nama Tech Lead & QA Lead]`
- **Lingkup Keberlakuan [Wajib]:** Seluruh repositori kode, service backend, dan aplikasi frontend di bawah tim.
- **Frekuensi Tinjauan [Wajib]:** Ditinjau ulang setiap kuartal atau saat terjadi perubahan arsitektur besar.

---

## 2. Checklist Kriteria Penyelesaian (DoD Checklist) [Wajib]

> **Panduan Pengisian:** Centang seluruh item wajib berikut saat memverifikasi pekerjaan sebelum memindahkan tiket ke kolom "Done".

### A. Penyelesaian Kode & Self-Review [Wajib]
- [ ] [Wajib] Kode sumber ditulis lengkap sesuai spesifikasi fungsional tanpa ada potongan logika tertunda.
- [ ] [Wajib] Author telah melakukan self-review di branch lokal dan membersihkan file sementara serta baris debug.
- [ ] [Wajib] Branch telah di-rebase atau dimerge dengan branch target utama (`main` / `develop`) tanpa konflik.

### B. Otomatisasi Pengujian (Automated Tests & Coverage) [Wajib]
- [ ] [Wajib] Unit test baru ditambahkan dengan target coverage minimal `[>= 80%]` pada business logic domain.
- [ ] [Wajib] Seluruh automated unit test dan integration test berjalan sukses dan berstatus hijau (*green*) di pipeline CI.
- [ ] [Kondisional] End-to-End (E2E) automated regression tests lulus tanpa flakiness jika alur user berubah.

### C. Peer Code Review & Persetujuan [Wajib]
- [ ] [Wajib] Pull Request telah ditinjau dan disetujui (*Approved*) oleh minimal `[1 atau 2]` peer engineer independen.
- [ ] [Wajib] Seluruh komentar peninjau berlabel `[BLOCKER]` telah diperbaiki dan di-*resolve*.

### D. Analisis Statis & Audit Keamanan (Static Analysis & Security) [Wajib]
- [ ] [Wajib] Linter dan formatter berjalan otomatis di CI tanpa galat (*zero error*) dan tanpa mematikan aturan paksa.
- [ ] [Wajib] Pemindaian keamanan ketergantungan (dependency scan via Snyk / Dependabot / npm audit) bersih dari kerentanan kritis.
- [ ] [Wajib] Tidak ada credential, token, atau secret mentah yang terkomit ke version control.

### E. Verifikasi Staging & QA Sign-off [Wajib]
- [ ] [Wajib] Fitur telah otomatis ter-deploy ke lingkungan staging tanpa kegagalan pipeline.
- [ ] [Wajib] Pengujian fungsional dan exploratory testing telah diverifikasi oleh tim QA di staging.
- [ ] [Wajib] Seluruh kriteria penerimaan khusus pada tiket (Acceptance Criteria) terbukti lulus 100%.

### F. Dokumentasi & Catatan Perubahan (Docs & Release Notes) [Wajib]
- [ ] [Wajib] Dokumentasi API (Swagger / OpenAPI / Postman collection) diperbarui jika terjadi modifikasi kontrak data.
- [ ] [Wajib] Catatan perubahan rilis (*Changelog / Release Notes*) ditulis ringkas pada tiket atau repositori.
- [ ] [Kondisional] Panduan instalasi dan environment variables baru ditambahkan ke `README.md` jika ada konfigurasi baru.

---

## 3. Tabel Perbedaan: DoD vs Acceptance Criteria vs Release Readiness [Wajib]

> **Panduan Pengisian:** Pahami perbedaan esensial antara tiga tingkat verifikasi berikut agar tim tidak salah kaprah.

| Dimensi Evaluasi | Definition of Done (DoD) | Acceptance Criteria (AC) | Release Readiness |
|---|---|---|---|
| **Definisi Inti** | Standar kualitas teknis universal yang berlaku untuk *semua* tiket | Kriteria fungsional unik spesifik untuk *satu* tiket cerita | Kelayakan menyeluruh rilis paket software ke lingkungan produksi |
| **Fokus Area** | Mutu kode, arsitektur, testing, keamanan, linting | Perilaku bisnis, ekspektasi pengguna, skenario input-output | Kesiapan operasi, performa load test, persetujuan bisnis akhir |
| **Pemilik Utama** | Tim Engineering & QA | Product Owner & Business Analyst | Release Manager & Operations Lead |
| **Waktu Evaluasi** | Setiap penyelesaian User Story / PR | Saat verifikasi fungsional story diuji | Menjelang cut-off deployment produksi |

---

*(Seksi kepatuhan regulasi di bawah ini opsional: hapus jika produk tidak terikat audit eksternal)*
## 4. Kepatuhan Audit Tambahan (Audit Compliance) [Opsional]
- [ ] Log audit jejak transaksi finansial telah ditambahkan untuk memenuhi kepatuhan regulasi.
- [ ] Skema enkripsi database at-rest dan in-transit telah divalidasi oleh Tim Kepatuhan Keamanan.
