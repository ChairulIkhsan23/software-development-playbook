---
id: DOC-ACC-001
title: Acceptance Criteria Specification
project: "[Nama Proyek / Produk]"
version: 1.0.0
author: "[Nama QA Lead / Product Owner]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Review / Approved
---

# Acceptance Criteria Specification: [Fitur / Modul]

> **Panduan Pengisian:**
> Dokumen ini mendefinisikan kriteria kelulusan penerimaan (Acceptance Criteria) yang tidak ambigu sebelum fitur mulai dikerjakan tim rekayasa. Konvensi ID: `AC-[MOD]-[NNN]`. Dokumen ini menjadi kontrak validasi antara Product Owner, Engineer, dan QA Tester. Bagian bertanda `[Wajib]` harus diisi.

---

## 1. Pedoman Format: Kapan Menggunakan Gherkin vs Rule-Based [Wajib]

> **Panduan Pengisian:** Pilih gaya penulisan yang paling efektif berdasarkan jenis kompleksitas kebutuhan fitur.

- **Format Skenario BDD (Gherkin: Given-When-Then):**
  - *Gunakan saat:* Menguji alur perilaku pengguna secara interaktif, navigasi antarmuka, transaksi multi-langkah, atau integrasi end-to-end yang melibatkan transisi state.
  - *Kelebihan:* Dapat diotomatisasi langsung ke dalam framework pengujian (Cucumber, Playwright, Cypress).
- **Format Berbasis Aturan (Rule-Based Checklist):**
  - *Gunakan saat:* Menguji batasan data statis, formula kalkulasi angka, aturan format input, atau kepatuhan non-fungsional sederhana.
  - *Kelebihan:* Ringkas, cepat ditulis, dan mudah dibaca oleh developer solo atau tim kecil.

---

## 2. Template Kriteria Berbasis Aturan (Rule-Based Checklist) [Wajib]

> **Panduan Pengisian:** Gunakan daftar checklist berformat ID untuk verifikasi validasi masukan data dan perilaku UI statis.

- [ ] **AC-CHK-001 [Validasi Nominal]:** Bidang input nominal transfer hanya menerima angka bulat positif (bilangan bulat > 0) tanpa desimal.
- [ ] **AC-CHK-002 [Pencegahan Double-Click]:** Tombol "Kirim Pembayaran" otomatis beralih ke state *disabled* dan menampilkan *spinner loading* segera setelah ditekan pertama kali.
- [ ] **AC-CHK-003 [Batas Karakter]:** Kolom catatan transaksi dibatasi maksimal 140 karakter; sistem menampilkan counter karakter sisa secara realtime.
- [ ] **AC-CHK-004 [Responsif Mobile]:** Pada layar viewport 360px hingga 428px, tidak boleh ada scrolling horizontal (*zero horizontal overflow*).

---

## 3. Template Skenario BDD Gherkin (Happy Path & Negative Flows) [Wajib]

> **Panduan Pengisian:** Tuliskan skenario alur sukses dan alur penanganan kegagalan secara eksplisit. Format penamaan skenario harus menggambarkan kondisi pengujian.

### Skenario 1: [AC-CHK-005] Sukses Melakukan Pembayaran dengan Saldo Cukup (Happy Path)
```gherkin
Scenario: Pengguna menyelesaikan pembayaran menggunakan saldo dompet
  Given pengguna memiliki saldo aktif dompet digital sebesar "Rp 100.000"
  And pengguna berada di halaman checkout dengan tagihan pesanan "Rp 45.000"
  When pengguna memilih metode bayar "Dompet Digital"
  And pengguna menekan tombol "Bayar Sekarang"
  And pengguna memasukkan 6-digit PIN keamanan yang benar "123456"
  Then sistem memotong saldo dompet digital sebesar "Rp 45.000"
  And sisa saldo dompet pengguna menjadi "Rp 55.000"
  And sistem menampilkan halaman konfirmasi "Pembayaran Berhasil"
  And sistem mengirimkan notifikasi push receipt transaksi
```

### Skenario 2: [AC-CHK-006] Gagal Pembayaran karena Saldo Tidak Cukup (Negative Flow)
```gherkin
Scenario: Pengguna mencoba membayar dengan saldo dompet di bawah nilai tagihan
  Given pengguna memiliki saldo dompet digital sebesar "Rp 20.000"
  And pengguna memiliki tagihan pesanan sebesar "Rp 50.000"
  When pengguna memilih metode bayar "Dompet Digital"
  Then sistem menampilkan label peringatan "Saldo tidak mencukupi (Kurang Rp 30.000)"
  And tombol "Bayar Sekarang" dalam kondisi dinonaktifkan (disabled)
  And sistem menampilkan tombol alternatif "Top Up Saldo"
```

---

## 4. Checklist Kesiapan Serah-Terima (Definition of Ready - Dev Handoff) [Wajib]

> **Panduan Pengisian:** Sebelum engineer menarik tiket ke status "In Progress", seluruh checklist di bawah wajib tercentang (Definition of Ready).

- [ ] Seluruh kriteria penerimaan (Happy path, edge cases, error state) telah disepakati oleh PM, Tech Lead, dan QA.
- [ ] Aset desain UI/UX (Figma screens, responsive states, dan icon SVG) siap pakai dan terhubung di tiket.
- [ ] Kontrak API (request/response schema) telah ditentukan dan disetujui tim frontend & backend.
- [ ] Estimasi story points telah ditetapkan melalui konsensus sprint planning.
- [ ] Dependensi eksternal (API sandbox, environment credentials) telah tersedia dan dapat diakses tim.
