# Problem Discovery

## 1. Apa Itu Problem Discovery?

Problem discovery adalah proses sistematis untuk memahami, mendefinisikan, dan memvalidasi masalah **sebelum** membangun solusi. Tujuannya memastikan tim memecahkan masalah yang tepat (*building the right thing*) sebelum fokus pada cara menyelesaikannya (*building the thing right*).

### Mengapa Melewatkan Problem Discovery Berbahaya?

Tanpa problem discovery yang memadai, tim berisiko:

- **Membangun hal yang salah** — fitur selesai tapi tidak ada yang pakai.
- **Membuang waktu dan anggaran** — 6 bulan development untuk masalah yang bisa diselesaikan dengan spreadsheet.
- **Menyelesaikan gejala, bukan akar masalah** — bug terus muncul karena yang diperbaiki hanya permukaan.
- **Kehilangan buy-in stakeholder** — sulit meyakinkan sponsor jika masalah yang diselesaikan tidak jelas.

> **Prinsip:** Lebih murah menghabiskan 2 minggu memahami masalah daripada 6 bulan membangun solusi yang salah.

---

## 2. Teknik Problem Discovery

### 2.1 Teknik 5 Whys (Akar Masalah)

Teknik sederhana dari Toyota Production System untuk menggali akar penyebab masalah.

**Cara menggunakan:**

1. Mulai dengan pernyataan masalah yang teramati.
2. Tanyakan *"Mengapa ini terjadi?"* dan catat jawabannya.
3. Ulangi pertanyaan *"Mengapa?"* terhadap jawaban tersebut — biasanya 3–5 kali iterasi cukup untuk sampai ke akar masalah.
4. Berhenti ketika sampai pada penyebab yang bisa ditindaklanjuti (*actionable root cause*).

**Contoh:**

| Level | Pertanyaan | Jawaban |
|-------|-----------|---------|
| 1 | Mengapa laporan terlambat? | Karena data harus dikumpulkan manual dari 3 sistem berbeda. |
| 2 | Mengapa data dikumpulkan manual? | Karena tidak ada integrasi antar sistem. |
| 3 | Mengapa tidak ada integrasi? | Karena setiap sistem dibangun oleh vendor berbeda tanpa standar API. |
| 4 | Mengapa tanpa standar API? | Karena tidak ada kebijakan arsitektur integrasi saat procurement. |
| **Akar masalah** | | Tidak ada standar teknis integrasi dalam proses pengadaan sistem. |

**Tips penting:**

- Jangan berhenti di gejala permukaan (misal: "user malas input data").
- Jangan menyalahkan individu — fokus pada proses dan sistem.
- Satu masalah bisa punya beberapa cabang akar masalah. Eksplorasi semua cabang.

### 2.2 Jobs To Be Done (JTBD)

JTBD membantu memahami *mengapa* user melakukan sesuatu, bukan hanya *apa* yang mereka lakukan. Fokusnya pada "pekerjaan" yang ingin diselesaikan user.

**Format JTBD statement:**

> *"Ketika [situasi/konteks], saya ingin [tindakan/motivasi], agar saya dapat [hasil yang diharapkan]."*

**Contoh:**

> *"Ketika menerima order baru dari pelanggan, saya ingin langsung melihat stok gudang secara real-time, agar saya dapat memberikan konfirmasi ketersediaan tanpa harus menelepon bagian gudang."*

**Kapan pakai JTBD:**

- Saat ingin memahami motivasi di balik permintaan fitur.
- Saat user minta solusi spesifik tapi kamu curiga masalah sebenarnya berbeda.
- Saat merancang produk baru dari nol.

### 2.3 Problem Statement Canvas

Canvas visual untuk menstrukturkan pemahaman masalah secara menyeluruh.

| Komponen | Pertanyaan Panduan | Contoh |
|----------|-------------------|--------|
| **Masalah** | Apa masalah yang terjadi? | Proses rekonsiliasi data keuangan memakan waktu 3 hari kerja per bulan. |
| **Siapa yang Terdampak** | Siapa yang paling merasakan dampaknya? | Tim finance (3 orang) dan manajer operasional. |
| **Konteks** | Kapan dan di mana masalah ini muncul? | Setiap akhir bulan saat closing report. |
| **Dampak Negatif** | Apa konsekuensinya (waktu, biaya, peluang)? | Keterlambatan laporan → keputusan bisnis tertunda 3 hari. |
| **Solusi Saat Ini** | Bagaimana mereka mengatasi masalah ini sekarang? | Copy-paste manual dari 3 spreadsheet berbeda. |
| **Solusi Sukses** | Apa indikator jika masalah ini berhasil diselesaikan? | Rekonsiliasi selesai dalam 4 jam, bukan 3 hari. |

### 2.4 Pain/Gain Analysis

Pemetaan sederhana untuk memahami apa yang menyakitkan (pain) dan apa yang diinginkan (gain) oleh user.

**Pain (yang ingin dihilangkan):**

- Proses yang memakan waktu terlalu lama
- Error yang sering terjadi dan butuh perbaikan manual
- Informasi yang sulit diakses atau tersebar di banyak tempat
- Ketergantungan pada orang tertentu untuk menyelesaikan tugas

**Gain (yang ingin dicapai):**

- Proses yang lebih cepat dan otomatis
- Data yang akurat dan konsisten
- Visibilitas real-time terhadap status pekerjaan
- Kemandirian dalam menyelesaikan tugas tanpa bottleneck

**Cara mengumpulkan:** Wawancara user, observasi langsung, atau survei sederhana. Prioritaskan pain/gain berdasarkan frekuensi dan intensitas.

---

## 3. Validasi: Apakah Masalah Ini Nyata?

Jangan hanya percaya asumsi sendiri. Validasi bahwa masalah benar-benar ada:

| Metode Validasi | Kapan Digunakan | Effort |
|----------------|-----------------|--------|
| **Wawancara 5–8 user** | Selalu — ini minimum | Rendah |
| **Data usage/analytics** | Jika ada sistem existing | Rendah |
| **Observasi langsung** | Jika prosesnya melibatkan workflow fisik/manual | Sedang |
| **Survei skala besar** | Jika perlu data kuantitatif untuk meyakinkan stakeholder | Sedang |
| **Prototype/smoke test** | Jika masalah terkait produk baru | Tinggi |

**Tanda masalah sudah tervalidasi:**

- Minimal 3–5 user independen menyebutkan masalah yang sama.
- Ada data kuantitatif yang mendukung (waktu terbuang, error rate, biaya).
- Stakeholder bisnis mengonfirmasi dampak terhadap tujuan organisasi.

---

## 4. Template Problem Statement

Gunakan template ini sebagai output utama dari problem discovery:

```
## Problem Statement

**Masalah:**
[Deskripsi singkat masalah — 1-2 kalimat.]

**Siapa yang Terdampak:**
[Persona atau role yang terkena dampak.]

**Dampak Negatif:**
[Konsekuensi terukur — waktu, biaya, error rate, peluang yang hilang.]

**Konteks:**
[Kapan dan di mana masalah ini muncul.]

**Bukti/Validasi:**
[Data atau temuan wawancara yang mendukung.]

**Kriteria Sukses:**
[Indikator terukur bahwa masalah telah terselesaikan.]
```

---

## 5. Kapan Masalah TIDAK Layak Diselesaikan?

Tidak semua masalah harus dipecahkan dengan software. Pertimbangkan untuk **tidak melanjutkan** jika:

- **Frekuensi rendah** — masalah terjadi 1-2 kali setahun dan bisa ditangani manual.
- **Dampak minimal** — waktu yang terbuang hanya beberapa menit per kejadian.
- **Sudah ada solusi cukup baik** — spreadsheet atau proses manual yang berjalan lancar.
- **Biaya solusi > biaya masalah** — development cost Rp 500 juta untuk menghemat Rp 50 juta/tahun.
- **Jumlah user terdampak sangat kecil** — hanya 1-2 orang, dan bisa diselesaikan dengan training.
- **Masalah akan hilang sendiri** — misalnya karena sistem legacy akan di-sunset dalam 6 bulan.

> **Rule of thumb:** Jika ROI payback period lebih dari 3 tahun untuk proyek internal, pertanyakan kembali.

---

## 6. Kesalahan Umum

| Kesalahan | Dampak | Pencegahan |
|-----------|--------|------------|
| Langsung loncat ke solusi | Membangun fitur yang tidak menyelesaikan masalah inti | Pastikan problem statement disetujui sebelum solutioning |
| Hanya bicara dengan 1 user | Bias — masalah yang ditemukan mungkin hanya milik 1 orang | Wawancara minimal 5 user dari role berbeda |
| Menerima "permintaan fitur" sebagai masalah | Solusi prematur — user bilang "saya butuh dashboard" padahal masalahnya visibilitas | Selalu tanyakan "mengapa?" di balik permintaan |
| Tidak melibatkan stakeholder bisnis | Problem statement tidak align dengan tujuan organisasi | Validasi problem statement dengan sponsor bisnis |
| Mengabaikan solusi yang sudah ada | Reinvent the wheel — membangun sesuatu yang sebenarnya sudah tersedia | Peta solusi existing sebelum propose solusi baru |
| Terlalu lama di fase discovery | Analysis paralysis — tidak pernah mulai membangun | Tetapkan timebox (1-2 minggu untuk proyek kecil, 4-6 minggu untuk besar) |

---

## 7. Dokumen Terkait

- [User Research](user-research.md) — teknik wawancara dan riset pengguna lebih mendalam.
- [Stakeholder Analysis](stakeholder-analysis.md) — identifikasi siapa yang terdampak dan siapa pengambil keputusan.
- [Assumptions and Constraints](assumptions-and-constraints.md) — dokumentasikan asumsi yang muncul dari discovery.
- [Feasibility Analysis](feasibility-analysis.md) — evaluasi apakah solusi yang diusulkan layak dibangun.
- [Discovery Exit Criteria](discovery-exit-criteria.md) — kapan discovery cukup dan siap lanjut ke requirements.
