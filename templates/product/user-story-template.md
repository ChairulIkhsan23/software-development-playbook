---
id: DOC-UST-001
title: User Story Specification Card
project: "[Nama Proyek / Produk]"
version: 1.0.0
author: "[Nama PM / Product Owner]"
date: "[YYYY-MM-DD]"
status: Ready # Pilihan: Draft / Backlog / Ready / In Progress / Done
---

# User Story: [US-MOD-001] [Judul Singkat Cerita Pengguna]

> **Panduan Pengisian:**
> Gunakan dokumen ini sebagai unit kerja utama (user story card) untuk perencanaan sprint. Format ID: `US-[MOD]-[NNN]`. Story harus memenuhi prinsip INVEST (Independent, Negotiable, Valuable, Estimable, Small, Testable). Bagian bertanda `[Wajib]` harus diisi.

---

## 1. Kartu Narasi User Story [Wajib]

> **Panduan Pengisian:** Rumuskan narasi dari perspektif persona pengguna target. Hindari bahasa teknis di dalam narasi ini.

- **Sebagai seorang** [peran persona, misal: Pembeli terdaftar di aplikasi mobile]
- **Saya ingin** [tindakan / aksi fungsional, misal: menyimpan alamat pengiriman default baru]
- **Agar** [alasan nilai manfaat / outcome bisnis, misal: tidak perlu mengetik ulang alamat saat checkout belanja berikutnya]

---

## 2. Konteks Nilai Bisnis & Dampak (Business Value) [Wajib]

> **Panduan Pengisian:** Jelaskan mengapa tiket ini penting dikerjakan sprint ini dan metrik apa yang dipengaruhi.

- **Kategori Nilai:** [Pilihan: Efisiensi Pengguna / Pertumbuhan Konversi / Kepatuhan Regulasi / Pengurangan Biaya]
- **Justifikasi Singkat:** [Menyimpan alamat mempercepat waktu checkout rata-rata sebesar 40%, mengurangi cart abandonment rate.]
- **Prioritas MoSCoW:** [Must Have / Should Have / Could Have / Won't Have]

---

## 3. Kriteria Penerimaan BDD (Acceptance Criteria - Gherkin) [Wajib]

> **Panduan Pengisian:** Tulis kriteria pengujian dalam sintaks Gherkin (Given-When-Then). Sertakan skenario positif (happy path) dan skenario negatif (edge cases).

### Skenario 1: Berhasil menyimpan alamat pengiriman baru yang valid (Happy Path)
```gherkin
Given pengguna telah login dan berada di halaman "Tambah Alamat"
When pengguna mengisi form dengan nama penerima "Budi", telepon "08123456789", dan alamat valid
And pengguna menekan tombol "Simpan Alamat"
Then sistem menyimpan alamat ke database akun pengguna
And sistem menampilkan pesan toast notifikasi "Alamat berhasil disimpan"
And daftar alamat pengguna memuat entri baru tersebut di urutan teratas
```

### Skenario 2: Gagal menyimpan karena nomor telepon tidak valid (Negative / Validation Flow)
```gherkin
Given pengguna berada di formulir "Tambah Alamat"
When pengguna memasukkan nomor telepon "123" (kurang dari 10 digit)
And pengguna menekan tombol "Simpan Alamat"
Then sistem tidak mengirim request penyimpanan ke server
And sistem menampilkan pesan error inline "Nomor telepon minimal 10 digit angka" di bawah input field
And tombol "Simpan Alamat" tetap aktif agar pengguna dapat memperbaiki data
```

---

## 4. Catatan Teknis & Edge Cases (Technical Implementation Notes) [Wajib]

> **Panduan Pengisian:** Catat pertimbangan arsitektur, batasan payload, atau kasus batas yang perlu diwaspadai developer.

- **Endpoint API Terkait:** `POST /api/v1/users/{id}/addresses` (Rujuk API Spec `DOC-API-001`)
- **Penanganan Batas Maksimal (Edge Case):** Maksimal 10 alamat tersimpan per user. Jika melebihi limit, kembalikan HTTP `422 Unprocessable Entity` dengan kode `MAX_ADDRESS_LIMIT_REACHED`.
- **Sanitasi Data:** Wajib sanitasi input teks untuk mencegah serangan Cross-Site Scripting (XSS).

---

## 5. Estimasi & Keterkaitan Aset Desain [Opsional]
*(Seksi ini opsional: tautan desain dapat dihapus jika fitur bersifat pure backend/API)*

> **Panduan Pengisian:** Isi estimasi usaha dan tautkan rancangan antarmuka Figma serta story/task dependen.

- **Estimasi Story Points:** [Contoh: 3 SP (Fibonacci scale: 1, 2, 3, 5, 8)]
- **Tautan Desain Figma:** [Tautan URL Frame Figma]
- **Dependensi User Story:** Membutuhkan `US-AUTH-001` (Modul Session Login Pengguna) berstatus Done.
