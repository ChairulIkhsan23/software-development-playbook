---
id: ADR-[NNN]
title: "[Judul Keputusan Singkat dan Spesifik]"
status: "[Proposed | Accepted | Deprecated | Superseded]"
date: "[YYYY-MM-DD]"
author: "[Nama Pengusul]"
deciders: "[Nama Tim / Stakeholder Pengambil Keputusan]"
consulted: "[Nama Pihak yang Dikonsultasikan]"
informed: "[Nama Pihak yang Diinformasikan]"
supersedes: "[ADR-XXX - Kondisional: isi jika menggantikan ADR lama]"
superseded_by: "[ADR-YYY - Kondisional: isi jika sudah digantikan]"
---

# ADR-[NNN]: [Judul Keputusan Singkat]

> **Panduan Pengisian:**
> Isi template ini untuk mendokumentasikan keputusan arsitektur signifikan yang berdampak jangka panjang.
> Target audiens: tim pengembang saat ini dan di masa depan.
> Gunakan kalimat lugas, objektif, dan sertakan trade-off teknis yang nyata.

---

## 1. Konteks & Problem Statement [Wajib]

> **Panduan Pengisian:**
> Jelaskan latar belakang masalah teknis atau kebutuhan bisnis yang memerlukan keputusan arsitektur.
> Hindari menyebutkan solusi di seksi ini; fokuslah pada masalah, batasan, dan urgensi.

[Jelaskan konteks sistem saat ini, kebutuhan spesifik yang belum terpenuhi, dan dampak jika masalah tidak diselesaikan. Contoh: "Layanan notifikasi saat ini bersifat sinkron dan memblokir request checkout pelanggan hingga 4 detik saat provider SMS mengalami latensi tinggi."]

## 2. Decision Drivers [Wajib]

> **Panduan Pengisian:**
> Tuliskan faktor-faktor kunci pendorong keputusan (3-5 poin). Urutkan dari prioritas tertinggi.

- **[Driver 1]:** [Faktor teknis atau bisnis, misal: Latensi p95 transaksi checkout harus < 500ms]
- **[Driver 2]:** [Kemudahan pemeliharaan oleh tim kecil tanpa staf DevOps khusus]
- **[Driver 3]:** [Biaya operasional infrastruktur bulanan < $50 USD]
- **[Driver 4]:** [Opsional - Kepatuhan regulasi penyimpanan data transaksi]

## 3. Pilihan yang Dipertimbangkan [Wajib]

> **Panduan Pengisian:**
> Evaluasi minimal 2 atau 3 alternatif realistis. Uraikan deskripsi singkat beserta pro dan kontra tiap opsi.

### Opsi 1: [Nama Alternatif 1 - Opsi Terpilih]
- **Deskripsi:** [Penjelasan singkat arsitektur atau teknologi yang diajukan]
- **Kelebihan (+):** [Kelebihan utama terkait decision drivers]
- **Kekurangan (-):** [Trade-off atau beban tambahan yang harus ditanggung]

### Opsi 2: [Nama Alternatif 2]
- **Deskripsi:** [Penjelasan alternatif kedua]
- **Kelebihan (+):** [Kelebihan opsi ini]
- **Kekurangan (-):** [Alasan mengapa opsi ini kalah dibanding opsi terpilih]

### Opsi 3: [Nama Alternatif 3 - Opsional] *(Boleh dihapus jika hanya ada 2 opsi)*
- **Deskripsi:** [Penjelasan alternatif ketiga]
- **Kelebihan (+):** [Kelebihan opsi ini]
- **Kekurangan (-):** [Kekurangan opsi ini]

## 4. Keputusan yang Diambil (Decision Outcome) [Wajib]

> **Panduan Pengisian:**
> Nyatakan opsi terpilih dengan tegas dalam 1-2 kalimat pembuka, diikuti alasan penentu (justifikasi utama).

Keputusan yang diambil adalah **[Opsi 1: Nama Solusi]**, karena [berikan alasan ringkas mengapa opsi ini paling seimbang memenuhi decision drivers di atas].

## 5. Konsekuensi [Wajib]

> **Panduan Pengisian:**
> Uraikan dampak setelah keputusan ini diterapkan secara jujur, baik sisi positif maupun negatif.

- **Dampak Positif (+):** [Manfaat langsung, misal: Latensi checkout turun menjadi < 200ms]
- **Dampak Negatif (-):** [Kompleksitas baru, misal: Perlu mengelola message queue baru dan dead-letter retry]
- **Risiko & Mitigasi:**
  - *Risiko:* [Potensi kegagalan atau hambatan teknis]
  - *Mitigasi:* [Langkah preventif atau penanganan yang disepakati]

## 6. Implikasi Operasional, Keamanan & Kepatuhan [Kondisional] *(Boleh dihapus jika tidak ada dampak khusus)*

> **Panduan Pengisian:**
> Isi jika keputusan ini mempengaruhi infrastruktur, audit log, keamanan data, atau beban monitoring tim.

- **Keamanan:** [Misal: Kredensial queue disimpan di Secret Manager; autentikasi mTLS antar service]
- **Operasional & Monitoring:** [Misal: Alarm CloudWatch pada metric queue backlog > 1000 pesan]
- **Kepatuhan:** [Misal: Pesan tidak mengandung data PII mentah sesuai UU PDP]

## 7. Keputusan Terkait (Related Decisions) [Opsional] *(Boleh dihapus jika tidak ada)*

> **Panduan Pengisian:**
> Cantumkan tautan atau ID ke ADR lain yang melatarbelakangi, terpengaruh, atau menggantikan keputusan ini.

- Bergantung pada: `[ADR-001: Pemilihan Database Utama]`
- Mempengaruhi: `[ADR-005: Strategi Caching Gateway]`
