# Feature Prioritization Frameworks

## Mengapa Harus Memprioritaskan?

Tanpa prioritisasi yang jelas, tim menghadapi risiko:

- **Semua terasa urgent** — tidak ada yang benar-benar dikerjakan dengan fokus
- **Scope creep** — fitur terus bertambah tanpa ada yang selesai
- **Resource terbuang** — effort besar dihabiskan untuk fitur bernilai rendah
- **Stakeholder conflict** — setiap orang punya "fitur terpenting" versi sendiri

Prioritisasi bukan soal mengatakan "tidak", melainkan memutuskan **urutan pengerjaan** berdasarkan data dan kesepakatan. Fitur yang di-deprioritisasi bukan dibuang — hanya belum waktunya.

## 1. Metode MoSCoW

MoSCoW membagi fitur ke dalam 4 kategori berdasarkan urgensi dan dampak terhadap keberhasilan rilis.

### Kategori dan Panduan

| Kategori | Proporsi Ideal | Definisi | Pertanyaan Penguji |
|---|---|---|---|
| **Must Have** | ~60% effort | Tanpa ini, produk tidak bisa rilis atau melanggar regulasi | "Apakah produk *sama sekali* tidak berfungsi tanpa ini?" |
| **Should Have** | ~20% effort | Sangat penting, tapi ada workaround manual sementara | "Apakah user masih bisa mencapai tujuannya tanpa ini, meski lebih susah?" |
| **Could Have** | ~20% effort | Nice-to-have, meningkatkan UX atau efisiensi | "Apakah user akan *kecewa* tanpa ini, atau bahkan tidak sadar?" |
| **Won't Have** | 0% effort (rilis ini) | Disepakati tidak dikerjakan di rilis ini | "Bisa ditunda ke rilis berikutnya tanpa dampak bisnis signifikan?" |

### Contoh Penerapan: Aplikasi E-commerce MVP

| Fitur | Kategori | Alasan |
|---|---|---|
| Checkout dan pembayaran | **Must** | Tanpa ini, tidak ada transaksi sama sekali |
| Manajemen katalog produk | **Must** | Tidak ada yang bisa dijual tanpa katalog |
| Pencarian produk | **Should** | User bisa browse manual, tapi sangat tidak efisien di >50 produk |
| Review dan rating produk | **Could** | Meningkatkan trust, tapi MVP bisa jalan tanpa ini |
| Program loyalitas / poin | **Won't** | Fitur retensi, belum relevan di fase akuisisi awal |

### Red Flag: Semua "Must Have"

Jika lebih dari 60% backlog dikategorikan Must Have, ada masalah. Langkah perbaikan:

1. Tanyakan ulang: "Jika kita hanya bisa kirim 3 fitur minggu depan, yang mana?"
2. Pisahkan must-have *teknis* (legal, security) dari must-have *bisnis* (revenue)
3. Challenge setiap Must Have: apakah ada workaround, meski tidak ideal?

## 2. Kerangka Skor RICE

RICE memberikan skor numerik untuk membandingkan fitur secara objektif.

### Formula

```
Skor RICE = (Reach × Impact × Confidence) / Effort
```

### Komponen RICE

| Komponen | Definisi | Satuan / Skala | Cara Mendapatkan Data |
|---|---|---|---|
| **Reach** | Jumlah user/customer yang terdampak per periode | Angka absolut per kuartal (misal: 500 user/kuartal) | Data analytics, survey, jumlah request masuk |
| **Impact** | Seberapa besar dampak terhadap goal | 3 = Massive, 2 = High, 1 = Medium, 0.5 = Low, 0.25 = Minimal | Penilaian kualitatif tim, A/B test data jika ada |
| **Confidence** | Seberapa yakin kita dengan estimasi Reach dan Impact | 100% = data kuat, 80% = data cukup, 50% = tebakan teredukasi | Jumlah dan kualitas data pendukung |
| **Effort** | Beban kerja total untuk deliver fitur | Person-months (misal: 2 = 2 orang × 1 bulan) | Estimasi tim engineering |

### Contoh Perhitungan

**Konteks:** Tim e-commerce sedang memilih antara 3 fitur untuk kuartal depan.

| Fitur | Reach (user/kw) | Impact | Confidence | Effort (person-month) | Skor RICE |
|---|---|---|---|---|---|
| Notifikasi promo push | 5000 | 1 (Medium) | 80% | 1 | **(5000 × 1 × 0.8) / 1 = 4000** |
| Rekomendasi produk AI | 3000 | 2 (High) | 50% | 4 | **(3000 × 2 × 0.5) / 4 = 750** |
| Export laporan CSV | 200 | 2 (High) | 100% | 0.5 | **(200 × 2 × 1.0) / 0.5 = 800** |

**Keputusan:** Notifikasi promo push mendapat skor tertinggi (4000) → dikerjakan pertama. Export CSV (800) lebih tinggi dari rekomendasi AI (750) meski reach-nya kecil, karena confidence tinggi dan effort rendah.

### Tips Menggunakan RICE

1. **Konsisten dalam satuan** — sepakati apakah Reach dihitung per bulan atau per kuartal, lalu gunakan konsisten
2. **Jangan manipulasi Confidence** — kalau tidak punya data, akui dengan score 50%
3. **Review berkala** — skor RICE berubah seiring data baru masuk
4. **Bukan keputusan final** — RICE adalah input, bukan pengganti diskusi

## 3. Value vs Effort Matrix (2×2)

Matrix sederhana untuk visualisasi cepat dan diskusi tim.

```
                    EFFORT RENDAH          EFFORT TINGGI
                ┌─────────────────────┬─────────────────────┐
                │                     │                     │
  VALUE         │    ★ QUICK WINS     │   PROYEK BESAR      │
  TINGGI        │                     │                     │
                │  Kerjakan PERTAMA   │  Rencanakan dengan  │
                │  ROI terbaik        │  baik, bagi tahap   │
                │                     │                     │
                ├─────────────────────┼─────────────────────┤
                │                     │                     │
  VALUE         │    FILL-INS         │   ✗ HINDARI         │
  RENDAH        │                     │                     │
                │  Kerjakan jika ada  │  Jangan kerjakan    │
                │  waktu luang        │  Effort besar,      │
                │                     │  return kecil       │
                │                     │                     │
                └─────────────────────┴─────────────────────┘
```

### Strategi per Kuadran

| Kuadran | Strategi | Contoh |
|---|---|---|
| **Quick Wins** (value tinggi, effort rendah) | Eksekusi segera, sering kali bisa masuk sprint berjalan | Fix copy yang membingungkan di checkout, tambah loading indicator |
| **Proyek Besar** (value tinggi, effort tinggi) | Breakdown jadi milestone, masukkan ke roadmap, alokasi resource | Redesign sistem pencarian, migrasi payment gateway |
| **Fill-ins** (value rendah, effort rendah) | Kerjakan di waktu luang atau saat ada slot sprint tersisa | Perbaiki typo, update warna minor |
| **Hindari** (value rendah, effort tinggi) | Hapus dari backlog atau tantang asumsi value-nya | Fitur "nice-to-have" yang butuh integrasi kompleks |

### Cara Menggunakannya

1. Tulis setiap fitur di sticky note (atau card di tool digital)
2. Tim secara kolektif menentukan posisi relatif di matrix
3. Diskusikan item yang posisinya diperdebatkan — biasanya ini yang paling insightful
4. Hasilnya bukan ranking absolut, tapi kesepakatan tim tentang prioritas

## 4. Kano Model (Dasar)

Kano mengklasifikasikan fitur berdasarkan **kepuasan pengguna**, bukan hanya value bisnis.

### Tiga Kategori Utama

| Kategori | Jika Ada | Jika Tidak Ada | Contoh |
|---|---|---|---|
| **Basic (Must-be)** | User tidak sadar, dianggap wajar | User sangat kecewa, tidak mau pakai | Keamanan login, validasi input, error message |
| **Performance (One-dimensional)** | Kepuasan naik proporsional | Kepuasan turun proporsional | Kecepatan loading, jumlah fitur filter, kapasitas storage |
| **Delighter (Attractive)** | User senang dan terkesan | User tidak kecewa (tidak tahu fiturnya bisa ada) | Auto-complete cerdas, dark mode, gesture shortcut |

### Implikasi untuk Prioritisasi

- **Basic** harus terpenuhi dulu — tanpa ini, fitur Performance dan Delighter tidak berarti
- **Performance** adalah arena kompetisi — lebih cepat, lebih lengkap, lebih murah
- **Delighter** menciptakan diferensiasi — tapi jangan bangun ini sebelum Basic solid
- **Pergeseran waktu** — Delighter hari ini bisa jadi Basic besok (contoh: dark mode dulu wow, sekarang expected)

## Prioritisasi dalam Praktik

### Siapa yang Memutuskan?

| Peran | Tanggung Jawab |
|---|---|
| **Product Owner / Product Manager** | Keputusan final prioritas berdasarkan value bisnis |
| **Engineering Lead** | Input tentang effort, risiko teknis, dan dependensi |
| **Stakeholder / Sponsor** | Input tentang tujuan bisnis dan timeline |
| **Tim Development** | Input tentang feasibility dan estimasi |

**Untuk solo dev / tim kecil:** Satu orang bisa pegang semua peran. Yang penting: tulis keputusan dan alasannya, bukan hanya di kepala.

### Kapan Re-prioritisasi?

- **Setiap sprint planning** — review apakah prioritas masih relevan
- **Saat data baru masuk** — hasil A/B test, feedback user, perubahan market
- **Saat scope berubah** — fitur baru masuk berarti fitur lain harus keluar atau turun
- **Saat timeline berubah** — deadline maju berarti scope harus mengecil

### Menangani Ketidaksepakatan Stakeholder

1. **Gunakan data, bukan opini** — tunjukkan RICE score, analytics, customer feedback
2. **Buat trade-off eksplisit** — "Jika A masuk, B harus keluar dari sprint ini"
3. **Timebox diskusi** — maksimal 30 menit per keputusan, jika belum sepakat → eskalasi ke decision maker
4. **Dokumentasikan keputusan** — tulis siapa memutuskan apa dan mengapa, di decision log
5. **Pisahkan volume dari urgency** — stakeholder yang paling vokal belum tentu paling benar

## Kesalahan Umum

| Kesalahan | Dampak | Cara Menghindari |
|---|---|---|
| **Semua "Must Have"** | Tidak ada yang benar-benar diprioritaskan | Batasi Must Have ≤ 60% total effort rilis |
| **Prioritas tanpa data** | Keputusan bias oleh siapa yang paling vokal | Minimal gunakan data kualitatif: jumlah request, feedback, support ticket |
| **Tidak pernah re-prioritisasi** | Backlog basi, fitur yang sudah tidak relevan tetap dikerjakan | Review prioritas setiap sprint planning |
| **HiPPO effect** | Highest Paid Person's Opinion mendominasi | Minta semua orang vote sebelum diskusi (menghindari anchoring bias) |
| **Sunk cost fallacy** | "Sudah setengah jadi, sayang kalau dihentikan" | Evaluasi berdasarkan value ke depan, bukan effort yang sudah keluar |
| **Membandingkan apel dan jeruk** | Fitur besar vs kecil dibandingkan tanpa normalisasi | Gunakan framework yang sama (RICE) untuk semua item |

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — konteks bisnis dan tujuan produk sebagai dasar prioritisasi
- [Acceptance Criteria](./acceptance-criteria.md) — kriteria yang harus dipenuhi oleh fitur yang diprioritaskan
- [User Stories and Use Cases](./user-stories-and-use-cases.md) — item backlog yang diprioritaskan
- [Requirements Change Management](./requirements-change-management.md) — proses saat prioritas berubah di tengah sprint
