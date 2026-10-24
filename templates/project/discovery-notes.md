---
id: DOC-DSC-001
title: Discovery Session Notes
project: "[Nama Proyek]"
version: 1.0.0
author: "[Nama Penulis / Tim]"
date: "[YYYY-MM-DD]"
status: Draft # Pilihan: Draft / Review / Final
---

# Discovery Session Notes: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini mencatat ringkasan eksplorasi awal sebelum inisiasi proyek resmi. Fasilitator mengisi dokumen ini selama atau segera setelah workshop penemuan (discovery session). Isi placeholder bertanda `[...]`. Field bertanda `[Wajib]` harus diisi, `[Opsional]` atau `[Kondisional]` dapat diabaikan atau dihapus jika tidak berlaku.

---

## 1. Informasi Sesi Discovery [Wajib]

> **Panduan Pengisian:** Dokumentasikan jadwal, lokasi (fisik/virtual), dan seluruh peserta yang terlibat dalam discovery.

- **Tanggal & Waktu:** [YYYY-MM-DD] | [HH:MM - HH:MM WIB]
- **Fasilitator:** [Nama Fasilitator / Role]
- **Notulis:** [Nama Notulis]
- **Peserta:** [Nama (Jabatan/Peran), misal: Budi (Product Lead), Susi (Tech Lead), Rian (Business User)]
- **Tautan Papan Kerja Virtual:** [Opsional: Tautan Miro / FigJam / Mural]

---

## 2. Latar Belakang & Konteks Inisiatif [Wajib]

> **Panduan Pengisian:** Uraikan alasan mengapa discovery ini dijalankan sekarang dan apa pemicu bisnis di baliknya.

- **Pemicu Utama:** [Jelaskan pain point utama atau peluang bisnis baru yang mendesak]
- **Kondisi Saat Ini (Current State):** [Deskripsikan proses manual atau sistem lama yang sedang berjalan]
- **Dampak Ketiadaan Solusi (Cost of Inaction):** [Estimasi kerugian finansial, hilangnya jam kerja, atau risiko kompetisi jika inisiatif ditunda]

---

## 3. Problem Canvas [Wajib]

> **Panduan Pengisian:** Petakan domain masalah secara terstruktur agar tim fokus pada masalah riil, bukan langsung melompat ke solusi teknologi.

| Dimensi Masalah | Deskripsi Detail |
|---|---|
| **Masalah Inti** | [Deskripsi masalah dalam 1-2 kalimat lugas] |
| **Pihak Terdampak** | [Segmen pengguna akhir, unit operasional, atau mitra eksternal] |
| **Frekuensi Kejadian** | [Setiap hari / per batch bulanan / musiman saat peak traffic] |
| **Workaround Saat Ini** | [Cara kerja alternatif sementara yang dipakai pengguna saat ini] |
| **Kriteria Keberhasilan Solusi** | [Indikator bahwa masalah ini telah tuntas terpecahkan] |

---

## 4. Analisis Akar Masalah (5-Whys Root Cause Analysis) [Wajib]

> **Panduan Pengisian:** Lakukan iterasi pertanyaan 'Mengapa' minimal hingga 5 tingkat untuk menemukan akar penyebab, bukan sekadar gejala permukaan.

1. **Gejala Permukaan:** Mengapa [sebutkan gejala masalah utama]?  
   ↳ *Jawaban:* Karena [alasan level 1].
2. **Tingkat 2:** Mengapa [alasan level 1 terjadi]?  
   ↳ *Jawaban:* Karena [alasan level 2].
3. **Tingkat 3:** Mengapa [alasan level 2 terjadi]?  
   ↳ *Jawaban:* Karena [alasan level 3].
4. **Tingkat 4:** Mengapa [alasan level 3 terjadi]?  
   ↳ *Jawaban:* Karena [alasan level 4].
5. **Akar Masalah (Root Cause):** Mengapa [alasan level 4 terjadi]?  
   ↳ *Jawaban:* Karena [akar masalah fundamental struktural/proses/teknologi].

---

## 5. Rangkuman Wawancara Stakeholder [Kondisional]
*(Seksi ini kondisional: dapat dihapus jika discovery dilakukan mandiri oleh solo dev tanpa wawancara internal)*

> **Panduan Pengisian:** Rangkum kutipan langsung, ekspektasi, dan kekhawatiran dari stakeholder kunci.

| Stakeholder / Peran | Kebutuhan Utama | Kekhawatiran / Risiko yang Diantisipasi |
|---|---|---|
| [Nama / Unit Bisnis] | [Ekspektasi terhadap solusi baru] | [Kekhawatiran terhadap downtime/adaptasi pengguna] |
| [Nama / Tim Teknis/Ops] | [Kebutuhan kemudahan pemeliharaan & performa] | [Risiko migrasi data dari sistem lama] |

---

## 6. Catatan Persona Pengguna (User Persona Notes) [Wajib]

> **Panduan Pengisian:** Rangkum profil ringkas pengguna target yang paling krusial untuk fase MVP.

- **Persona Utama:** [Nama Fiktif / Peran, contoh: Andi - Kasir Cabang Lapangan]
  - **Tujuan Harian:** [Tugas kerja utama yang ingin diselesaikan cepat]
  - **Pain Points Utama:** [Hambatan terbesar pada alur saat ini]
  - **Lingkungan Penggunaan:** [Mobile outdoor jaringan 3G / Desktop kantor layar 14 inci]

---

## 7. Batasan Teknis & Operasional (Constraints) [Opsional]
*(Seksi ini opsional: dapat dihapus jika belum ada batasan spesifik yang mengikat)*

> **Panduan Pengisian:** Catat batasan arsitektur warisan, infrastruktur, regulasi, atau lisensi yang wajib dipatuhi.

- **Batasan Arsitektur / Stack:** [Contoh: Wajib terintegrasi dengan REST API Core Banking v1.2 lama]
- **Batasan Regulasi & Kepatuhan:** [Contoh: Data PII harus tersimpan di server lokal sesuai UU PDP]
- **Batasan Waktu & Anggaran:** [Contoh: Wajib rilis beta sebelum kuartal audit eksternal [YYYY-MM-DD]]

---

## 8. Langkah Selanjutnya & Action Items [Wajib]

> **Panduan Pengisian:** Tentukan tugas konkret pasca-discovery lengkap dengan PIC dan batas waktu penyelesaian. Format ID: `ACT-DSC-NNN`.

| ID Item | Tindakan Lanjutan | Penanggung Jawab (PIC) | Target Selesai | Status |
|---|---|---|---|---|
| ACT-DSC-001 | Menyusun draf Project Charter awal | [Nama PIC] | [YYYY-MM-DD] | Open |
| ACT-DSC-002 | Menyelidiki ketersediaan API sistem legasi | [Nama PIC] | [YYYY-MM-DD] | Open |
| ACT-DSC-003 | Validasi sketsa alur kerja ke perwakilan pengguna | [Nama PIC] | [YYYY-MM-DD] | Open |
