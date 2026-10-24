# Product Vision and Strategy

## Pendahuluan

Product vision adalah gambaran jangka panjang tentang **dampak** yang ingin diciptakan produk kamu di dunia. Vision menjawab pertanyaan: "Jika produk ini berhasil sepenuhnya, dunia akan berubah seperti apa?"

Dokumen ini memandu kamu menyusun vision, strategy, dan metrik keberhasilan produk. Cocok untuk solo developer yang membangun side project maupun tim kecil yang sedang validasi ide.

---

## 1. Vision vs Mission — Apa Bedanya?

| Aspek | Vision | Mission |
|-------|--------|---------|
| Fokus | Masa depan ideal yang ingin dicapai | Apa yang dilakukan **sekarang** untuk menuju vision |
| Horizon | 3–10 tahun | 1–3 tahun |
| Sifat | Inspiratif, aspirasional | Konkret, actionable |
| Contoh | "Setiap UMKM di Indonesia bisa kelola keuangan semudah chat WhatsApp" | "Membangun aplikasi pembukuan sederhana berbasis mobile untuk pedagang pasar" |

**Tips:** Vision boleh terdengar ambisius. Mission harus bisa kamu kerjakan hari ini.

---

## 2. Elevator Pitch — Format Geoffrey Moore

Elevator pitch merangkum produk dalam satu paragraf yang bisa disampaikan dalam 30 detik. Gunakan template berikut:

```
Untuk       [target pengguna]
Yang        [mengalami masalah/kebutuhan]
Produk ini  [nama produk] adalah [kategori produk]
Yang        [manfaat utama / key benefit]
Berbeda     [alternatif yang ada saat ini]
Produk kami [pembeda unik yang sulit ditiru]
```

### Contoh: Aplikasi Pembukuan UMKM

```
Untuk       pedagang pasar dan warung kecil
Yang        kesulitan mencatat pemasukan dan pengeluaran harian secara konsisten
Produk ini  "CatatDuit" adalah aplikasi pembukuan mobile
Yang        memungkinkan pencatatan transaksi dalam 3 ketukan tanpa perlu paham akuntansi
Berbeda     dengan Excel atau buku tulis yang mudah hilang dan sulit direkap
Produk kami menggunakan input percakapan natural dan otomatis menghasilkan laporan laba rugi mingguan
```

**Kapan pakai:** Saat pitching ke investor, onboarding anggota tim baru, atau menuliskan deskripsi produk di README.

---

## 3. OKR Framework (Objectives & Key Results)

OKR adalah framework untuk menerjemahkan vision menjadi target terukur per kuartal.

### Komponen OKR

- **Objective (O):** Tujuan kualitatif yang inspiratif. Menjawab "Apa yang mau dicapai?"
- **Key Results (KR):** 2–5 hasil kuantitatif yang membuktikan objective tercapai. Menjawab "Bagaimana tahu kita berhasil?"

### Aturan Menulis OKR yang Baik

1. Objective harus **inspiratif** tapi **achievable** — bukan wishful thinking
2. Key Results harus **terukur** dengan angka spesifik — bukan "meningkatkan" tapi "naik dari X ke Y"
3. Satu kuartal, maksimal 3 Objectives
4. Setiap Objective punya 2–5 Key Results
5. Target KR sebaiknya ambisius (70% tercapai = good)

### Contoh OKR — Q1 2025 Aplikasi CatatDuit

```
Objective 1: Pedagang pasar merasa CatatDuit adalah bagian dari rutinitas harian mereka

  KR 1.1: DAU (Daily Active Users) naik dari 200 → 1.000
  KR 1.2: Rata-rata transaksi dicatat per user per hari naik dari 1,2 → 3,5
  KR 1.3: Retensi hari ke-7 naik dari 25% → 45%

Objective 2: Fitur laporan keuangan dipercaya untuk pengambilan keputusan

  KR 2.1: 60% pengguna aktif membuka laporan mingguan minimal 1x seminggu
  KR 2.2: NPS (Net Promoter Score) fitur laporan ≥ 40
  KR 2.3: Error rate pada kalkulasi laporan < 0,1%
```

---

## 4. North Star Metric

North Star Metric (NSM) adalah **satu metrik utama** yang paling mencerminkan nilai inti produk bagi pengguna. Semua tim bergerak ke arah metrik ini.

### Cara Memilih North Star Metric

Tanyakan: "Jika hanya boleh lihat satu angka untuk tahu produk ini sukses, angka apa?"

| Tipe Produk | Contoh NSM |
|-------------|-----------|
| Marketplace | Jumlah transaksi berhasil per minggu |
| SaaS productivity | Jumlah task yang diselesaikan per user per minggu |
| Media/content | Total waktu baca yang meaningful per user per hari |
| Fintech | Volume transaksi berhasil per bulan |

### NSM CatatDuit
**Jumlah transaksi yang dicatat per user per hari** — karena ini langsung mengukur apakah pengguna benar-benar memakai produk untuk kebutuhan inti.

**Peringatan:** NSM bukan satu-satunya metrik. Tetap pantau metrik lain. NSM hanya sebagai kompas utama.

---

## 5. Leading vs Lagging Indicators

| Aspek | Leading Indicators | Lagging Indicators |
|-------|-------------------|-------------------|
| Waktu | Bisa diukur **sekarang** | Terlihat **setelah beberapa waktu** |
| Sifat | Prediktif — menunjukkan arah | Konfirmatif — menunjukkan hasil |
| Actionable | Tinggi — bisa langsung diintervensi | Rendah — sudah terjadi |
| Contoh | Signup rate, onboarding completion, fitur pertama dipakai | Revenue, churn rate, LTV |

### Contoh untuk CatatDuit

**Leading:**
- Persentase user baru yang catat transaksi pertama dalam 24 jam
- Jumlah hari berturut-turut user aktif mencatat (streak)
- Waktu dari install sampai transaksi pertama dicatat

**Lagging:**
- Retensi 90 hari
- Monthly Recurring Revenue (jika berbayar)
- Churn rate bulanan

**Tips:** Fokus energi pada leading indicators karena kamu masih bisa mengubah hasilnya. Lagging indicators adalah "rapor" — berguna untuk evaluasi, tapi sudah terlambat untuk diintervensi.

---

## 6. Product Strategy Canvas

Product Strategy Canvas membantu memetakan strategi secara visual dalam satu halaman.

```
┌─────────────────────────────────────────────────┐
│                  PRODUCT VISION                 │
│  "Setiap UMKM kelola keuangan semudah chat"    │
├──────────────────┬──────────────────────────────┤
│  TARGET USER     │  MASALAH UTAMA              │
│  Pedagang pasar  │  Tidak konsisten mencatat   │
│  Warung kecil    │  Tidak bisa baca laporan    │
│  UMKM informal   │  Takut teknologi rumit      │
├──────────────────┼──────────────────────────────┤
│  SOLUSI INTI     │  UNFAIR ADVANTAGE           │
│  Catat dalam     │  Input natural language     │
│  3 ketukan       │  Offline-first              │
│  Laporan otomatis│  Integrasi bank lokal       │
├──────────────────┼──────────────────────────────┤
│  KEY METRICS     │  CHANNELS                   │
│  NSM: txn/user/  │  Komunitas pedagang pasar   │
│  day             │  Grup WhatsApp UMKM         │
│  Retensi D7: 45% │  Agen lapangan              │
├──────────────────┴──────────────────────────────┤
│  REVENUE MODEL                                  │
│  Freemium: gratis unlimited, premium = laporan  │
│  lanjutan + export + multi-outlet               │
└─────────────────────────────────────────────────┘
```

Isi canvas ini **sebelum** menulis PRD. Canvas ini menjadi "peta" yang dirujuk seluruh tim.

---

## 7. Vision-to-Feature Traceability

Setiap fitur yang dibangun harus bisa dilacak balik ke vision. Jika tidak bisa — pertanyakan apakah fitur itu perlu ada.

```
Vision
  └── Objective (OKR)
        └── Key Result
              └── Feature / Initiative
                    └── User Story
                          └── Task
```

### Contoh Traceability

| Level | Item |
|-------|------|
| Vision | Setiap UMKM kelola keuangan semudah chat |
| Objective | Pedagang merasa CatatDuit bagian rutinitas harian |
| Key Result | Rata-rata 3,5 transaksi dicatat per user per hari |
| Feature | Quick-entry: catat transaksi dalam 3 ketukan |
| User Story | Sebagai pedagang, saya ingin mencatat penjualan hanya dengan ketik nominal, agar tidak perlu isi form panjang |
| Task | Implementasi single-field input dengan auto-categorization |

**Aturan:** Jika feature tidak terhubung ke Key Result manapun, masukkan ke backlog "nice-to-have" atau buang.

---

## 8. Kesalahan Umum

| Kesalahan | Mengapa Berbahaya | Solusi |
|-----------|-------------------|--------|
| Vision terlalu vague ("membuat dunia lebih baik") | Tidak bisa dijadikan panduan keputusan | Spesifikkan: siapa, masalah apa, dampak apa |
| Vanity metrics sebagai NSM (total downloads, total users) | Angka besar tapi tidak mencerminkan value | Pilih metrik yang ukur **engagement** atau **value delivered** |
| Terlalu banyak OKR (>5 objectives) | Tim kehilangan fokus | Maksimal 3 Objectives per kuartal |
| Vision tidak pernah di-review | Vision jadi dokumen mati | Review setiap kuartal, update jika konteks berubah |
| Langsung loncat ke fitur tanpa strategy | Fitur yang dibangun tidak koheren | Isi strategy canvas dulu sebelum buat PRD |
| Bingung vision vs mission | Dokumen jadi rancu | Vision = future state, Mission = current action |

---

## 9. Checklist Sebelum Lanjut ke PRD

- [ ] Elevator pitch sudah ditulis dan bisa dijelaskan dalam 30 detik
- [ ] North Star Metric sudah dipilih dan disepakati tim
- [ ] OKR kuartal ini sudah ditentukan (max 3 objectives)
- [ ] Leading indicators sudah diidentifikasi
- [ ] Strategy canvas sudah diisi satu halaman
- [ ] Setiap planned feature bisa di-trace balik ke KR

---

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — langkah berikutnya setelah vision & strategy jelas
- [Prioritization](./prioritization.md) — cara prioritas fitur berdasarkan strategy
- [Requirements Traceability](./requirements-traceability.md) — detail tracking vision → implementation
- [Acceptance Criteria](./acceptance-criteria.md) — cara tulis kriteria keberhasilan yang testable
