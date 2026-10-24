# Estimation and Prioritization

Panduan praktis estimasi rekayasa perangkat lunak dan kerangka kerja penentuan prioritas backlog berbasis nilai bisnis.

---

## 1. Mengapa Estimasi Software Sulit

Estimasi bukan ramalan mistis; estimasi adalah komitmen probabilistik dalam kondisi ketidakpastian.

Tiga faktor utama deviasi estimasi:
1. **Cone of Uncertainty**: Di awal proyek, variasi estimasi bisa meleset 4x lebih cepat atau 0.25x lebih lambat. Ketidakpastian baru menyusut drastis setelah arsitektur dan spike teknis selesai diuji.
2. **Optimism Bias**: Developer cenderung mengasumsikan skenario ideal di mana kode langsung terkompilasi, API eksternal selalu responsif, dan tidak ada gangguan teknis mendadak.
3. **Unknown Unknowns**: Masalah yang bahkan tim tidak tahu bahwa mereka tidak tahu (misalnya bug tersembunyi pada library pihak ketiga atau limitasi undocumented dari platform hosting).

```
Ketidakpastian
  ▲
4x│ \                                 / (Estimasi terlalu pesimis)
2x│   \                             /
1x│─────[Discovery]──[Design]──[Build]──[Done] (Realitas Aktual)
.5│   /                             \
.25│ /                                 \ (Estimasi terlalu optimis)
  └─────────────────────────────────────► Waktu Siklus Proyek
```

---

## 2. Teknik Estimasi Perangkat Lunak

### 2.1 Story Points & Planning Poker
Story points mengukur tiga dimensi gabungan: **Kompleksitas Logika**, **Tingkat Ketidakpastian**, dan **Volume Usaha**.

Gunakan deret Fibonacci yang disederhanakan: `1, 2, 3, 5, 8, 13`.

| Nilai | Tingkat Kompleksitas | Contoh Nyata Pekerjaan |
|---|---|---|
| **1 Point** | Sangat Rendah / Trivial | Ubah label teks UI, tambah validasi regex format email sederhana. |
| **2 Points** | Rendah / Jelas | Buat endpoint CRUD tabel referensi sederhana tanpa relasi rumit. |
| **3 Points** | Sedang Standar | Tambah filter query parameter baru lengkap dengan unit test. |
| **5 Points** | Moderat Kompleks | Integrasi webhook callback dengan parsing signature & idempotent check. |
| **8 Points** | Sangat Kompleks | Refactor modul otentikasi JWT ke OAuth2 flow dengan multi-provider. |
| **13 Points** | Terlalu Besar / Riset | **Wajib dipecah!** Misal: migrasi database monolith ke multi-tenant. |

**Alur Planning Poker**:
1. Product Owner membacakan User Story beserta Acceptance Criteria.
2. Tim engineering mengajukan pertanyaan klarifikasi teknis (edge cases, validasi).
3. Setiap developer memilih skor secara rahasia (menggunakan kartu atau aplikasi web).
4. Buka skor secara simultan untuk menghindari *anchoring bias* (efek dominasi engineer senior).
5. Pemegang skor tertinggi dan terendah memaparkan argumen mereka.
6. Lakukan voting ulang sekali lagi hingga mencapai konsensus tim.

### 2.2 Pola Pemecahan Story Berukuran Raksasa (>13 Points)
Jangan masukkan story bernilai 13 ke dalam sprint. Terapkan pola *Vertical Slicing*:
- **Pola Spike**: Pisahkan riset/POC berdurasi 1 hari (misal: "Spike uji integrasi SDK Bank X: 2 pt") dari tiket implementasi produksi.
- **Pola Workflow Steps**: Pecah form registrasi multi-step menjadi langkah demi langkah (Step 1-2 vs Step 3-4).
- **Pola Happy Path vs Edge Cases**: Bangun alur normal transaksi terlebih dahulu (5 pt), buat tiket terpisah untuk handling retry dan failure fallback (3 pt).

### 2.3 T-Shirt Sizing
Teknik estimasi tingkat tinggi untuk inisiatif besar atau pemetaan roadmap awal.
- **XS**: < 1 hari kerja (quick wins).
- **S**: 2-3 hari kerja.
- **M**: 1 sprint (1-2 minggu).
- **L**: 1 kuartal atau melibatkan beberapa sistem.
- **XL**: Inisiatif masif, perlu eksplorasi arsitektur multi-bulan.

### 2.4 Three-Point Estimation (PERT)
Gunakan pendekatan statistik tiga titik saat mengestimasi modul kritis yang sarat risiko:
- **O (Optimistic)**: Durasi jika semua berjalan mulus tanpa kendala.
- **M (Most Likely)**: Durasi normal berdasarkan pengalaman sebelumnya.
- **P (Pessimistic)**: Durasi jika semua asumsi buruk dan kendala terjadi.

Formula Nilai Harapan (PERT Expected Value):
$$\mu = \frac{O + 4M + P}{6}$$

Formula Deviasi Standar:
$$\sigma = \frac{P - O}{6}$$

Rentang komitmen realistis adalah $\mu \pm \sigma$ (tingkat keyakinan ~68%) atau $\mu \pm 2\sigma$ (keyakinan ~95%).

**Contoh Kasus Nyata (Migrasi Schema Database Payment)**:
- Optimis ($O$): 3 hari (skrip jalan sekali eksekusi tanpa lock table).
- Realistis ($M$): 6 hari (butuh perbaikan index dan sync staging).
- Pesimis ($P$): 15 hari (data corrupt, perlu rollback dan replay log).
- $\mu = \frac{3 + 4(6) + 15}{6} = \frac{42}{6} = 7 \text{ hari}$.
- $\sigma = \frac{15 - 3}{6} = 2 \text{ hari}$.
- Komitmen ke sponsor: **7 hingga 9 hari kerja** (bukan 3 hari!).

---

## 3. Matriks Pemilihan Teknik Estimasi

| Situasi | Teknik Terbaik | Alasan Teknis |
|---|---|---|
| Refinement sprint reguler | Story Points & Planning Poker | Membangun pemahaman bersama dan konsensus teknis |
| Roadmap Q3/Q4 jangka panjang | T-Shirt Sizing | Cepat, murah, tidak membuang waktu untuk detail yang belum matang |
| Modul migrasi database kritis | Three-Point PERT | Memperhitungkan skenario terburuk dan memberi rentang kepastian |
| Bug fixing operasional harian | Rule of Thumb (1-4 jam) | Overhead estimasi tidak boleh lebih lama dari penyelesaian bug itu sendiri |

<!-- ponytail: Gunakan Story Points untuk backlog sprint; gunakan T-Shirt sizing untuk ide kasar backlog produk sebelum PRD disahkan. -->

---

## 4. Relative Estimation vs Absolute Time Estimation

- **Absolute Time (Jam/Hari)**: Sangat bergantung pada individu yang mengerjakan (junior butuh 8 jam, senior butuh 2 jam). Menyebabkan friksi dan rasa tertekan.
- **Relative Estimation (Poin)**: Membandingkan ukuran tugas relatif terhadap tugas referensi baseline. Story bernilai 3 point memiliki kompleksitas yang sama siapa pun yang mengeksekusi, yang berbeda hanyalah kecepatan penyelesaian.

### Kalibrasi Ulang Backlog Berdasarkan Velocity
- Jangan ubah nilai poin sebuah story di tengah sprint hanya karena pengerjaannya lambat.
- Catat velocity aktual di akhir sprint (misal: 30 point selesai dari 40 point rencana).
- Kalibrasi komitmen sprint berikutnya menggunakan rata-rata velocity riil, bukan memaksakan target target fiktif.

---

## 5. Ringkasan Teknik Prioritisasi Backlog

| Kerangka Kerja | Formula / Logika | Kapan Digunakan |
|---|---|---|
| **MoSCoW** | Must have, Should have, Could have, Won't have | Menentukan ruang lingkup MVP secara tegas |
| **RICE** | $\frac{\text{Reach} \times \text{Impact} \times \text{Confidence}}{\text{Effort}}$ | Membandingkan fitur produk pada backlog yang padat ide |
| **Kano Model** | Basic Needs, Performance Needs, Delighters | Menjaga keseimbangan antara fitur inti dan fitur pemikat pasar |
| **WSJF (Cost of Delay)** | $\frac{\text{Cost of Delay}}{\text{Job Size / Duration}}$ | Skala tim besar untuk mengeksekusi tugas dengan nilai ekonomi tertinggi lebih dulu |

### Contoh Komparasi Skor RICE Fitur Baru:

| Fitur Kandidat | Reach (User/bln) | Impact (0.25-3) | Confidence (50-100%) | Effort (Person-wks) | Skor RICE | Prioritas |
|---|---|---|---|---|---|---|
| **Dynamic QRIS** | 5,000 | 2.0 (High) | 80% (0.8) | 2 | **4,000** | Rank 1 |
| **Export Excel** | 800 | 0.5 (Low) | 100% (1.0) | 1 | **400** | Rank 3 |
| **One-Click Checkout** | 3,000 | 3.0 (Massive) | 50% (0.5) | 3 | **1,500** | Rank 2 |

### Rincian Kategori Kano Model
- **Must-be (Dasar)**: Fitur wajib (misal: user login aman). Tidak membuat user puas jika ada, tapi memicu kemarahan jika rusak.
- **Performance (Linear)**: Semakin cepat/baik, semakin puas user (misal: response time transaksi dari 2s ke 300ms).
- **Delighters (Kejutan Menyenangkan)**: Fitur tak terduga yang memikat user (misal: visualisasi receipt belanja interaktif).

---

## 6. Protokol Kalibrasi Ulang (Re-estimation Triggers)

Jangan lakukan re-estimation secara sembarangan di tengah sprint. Kalibrasi ulang diizinkan HANYA jika:
1. Spike teknis mengungkap arsitektur dependensi pihak ketiga yang sama sekali berbeda dari dokumentasi vendor.
2. Acceptance criteria dari Product Owner bertambah atau berubah di tengah sprint (wajib ubah scope atau potong story).
3. Kode legacy yang disentuh memiliki tingkat technical debt ekstrem yang memerlukan refactoring arsitektur pra-syarat.

---

## 7. Mengelola Ekspektasi Stakeholder: Rentang, Bukan Tanggal Tunggal

Jangan pernah memberikan satu tanggal pasti untuk pekerjaan yang belum dieksplorasi.

Terapkan strategi komunikasi berbasis rentang:
- **Buruk**: "Fitur payment baru akan live tanggal 14 November."
- **Baik**: "Berdasarkan estimasi tiga titik dan kapasitas velocity saat ini, kami memproyeksikan rilis antara 10 November hingga 24 November dengan tingkat keyakinan 85%."

Sediakan *trade-off slider* saat stakeholder meminta penambahan fitur di tengah jalan:
> "Kita bisa menambahkan fitur Export Excel, namun jadwal rilis bergeser 1 sprint, atau kita menurunkan prioritas modul analitik grafik ke rilis berikutnya."

---

## 8. Common Mistakes

- **Mengonversi 1 Story Point = 1 Hari Kerja**: Menghancurkan filosofi relative sizing dan mengembalikan jebakan micromanagement berbasis jam.
- **Estimasi Tanpa Acceptance Criteria**: Mencoba mengukur story yang deskripsinya hanya satu baris tanpa batasan yang jelas.
- **Planning Poker Dikuasai Tech Lead**: Engineer junior takut berpendapat berbeda dari tech lead. Selalu buka skor bersamaan.
- **Membiarkan Story Berukuran Raksasa (>13 pt)**: Tugas besar selalu menyembunyikan risiko fatal. Segera dekomposisi menjadi sub-tugas kecil.
- **Re-estimating Task yang Belum Selesai**: Jangan mengubah poin tugas sisa. Ambil kredit hanya untuk tugas yang selesai 100% (*zero points for 99% done*).

---

## 9. Related Docs & Templates

- Perencanaan proyek: [Project Planning](./project-planning.md)
- Template rencana proyek: [Project Plan Template](../templates/project/project-plan.md)
- Pengambilan keputusan ruang lingkup: [Decision Log](./decision-log.md)
- Pengendalian perubahan estimasi: [Change Log](./change-log.md)

---
*skipped: Monte Carlo probabilistic forecasting; add when historical repository issues exceed 500 completed work items.*