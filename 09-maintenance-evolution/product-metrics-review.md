# Product Metrics Review & Data-Driven Evolution

Panduan evaluasi berkala metrik produk di lingkungan produksi untuk memvalidasi dampak bisnis, perilaku pengguna, dan menentukan arah iterasi fitur.

---

## 1. Menghubungkan Kode dengan Nilai Bisnis

Sebagai engineer atau tim kecil, menulis kode yang lulus unit test dan berhasil di-deploy hanyalah separuh perjalanan. Perangkat lunak dinilai berhasil jika memberikan dampak nyata terhadap tujuan produk dan bisnis.

Evaluasi metrik berkala (bulanan atau per rilis besar) menjawab pertanyaan mendasar:
- Apakah pengguna benar-benar memakai fitur yang kita bangun dengan susah payah?
- Apakah fitur tersebut menyelesaikan masalah pengguna atau justru menambah kebingungan?
- Kapan kita harus mengulang iterasi (*iterate*), mengubah haluan (*pivot*), atau memensiunkan fitur (*sunset*)?

---

## 2. Kerangka Metrik Produk Kunci

```
                [North Star Metric: Indikator Nilai Inti Pelanggan]
                                         |
     +-----------------+-----------------+-----------------+
     |                 |                 |                 |
[Acquisition]     [Activation]      [Retention]       [Revenue & Referral]
(User Datang)     (Aha! Moment)     (User Bertahan)   (Monetisasi & Viralitas)
```

### A. North Star Metric (NSM)
Satu metrik utama yang paling mencerminkan pertukaran nilai inti antara produk dan pengguna:
- **E-Commerce:** Jumlah transaksi selesai per minggu (*Weekly Completed Orders*).
- **SaaS B2B:** Jumlah pengguna aktif yang menjalankan workflow inti mingguan (*Weekly Active Workflows*).
- **Media/Konten:** Total jam konsumsi konten berkualitas (*Total Reading/Viewing Hours*).

### B. Kerangka Pirate Metrics (AARRR)
1. **Acquisition (Akuisisi):** Bagaimana pengguna menemukan aplikasi (organic search, referral, ads). Metrik: *Signup conversion rate*, *Cost Per Acquisition (CPA)*.
2. **Activation (Aktivasi):** Pengalaman pertama pengguna mencapai *"Aha! Moment"* (contoh: menyelesaikan setup profil dalam 5 menit). Metrik: *Activation rate*, onboarding completion rate.
3. **Retention (Retensi):** Apakah pengguna kembali menggunakan aplikasi secara teratur. Metrik: D1, D7, D30 retention rate.
4. **Revenue (Pendapatan):** Bagaimana produk menghasilkan uang. Metrik: *Average Revenue Per User (ARPU)*, *Monthly Recurring Revenue (MRR)*, *Customer Lifetime Value (LTV)*.
5. **Referral (Rekomendasi):** Apakah pengguna mengajak rekan mereka. Metrik: *Net Promoter Score (NPS)*, rasio undangan tim, viral coefficient.

### C. Metrik Engagement
- **DAU / MAU Ratio (Stickiness):** Rasio Daily Active Users terhadap Monthly Active Users. Rasio `> 20%` menandakan produk memiliki daya lekat tinggi; `> 50%` merupakan kelas dunia (world-class stickiness).
- **Session Length & Depth:** Durasi rata-rata interaksi pengguna dan jumlah halaman/fitur yang diakses per sesi.
- **Feature Usage Frequency:** Persentase pengguna aktif yang membuka fitur tertentu minimal sekali per minggu.

---

## 3. Analisis Retensi dan Churn (Cohort Analysis)

Retensi adalah penentu keberlanjutan produk yang paling jujur. Produk dengan retensi buruk diibaratkan seperti "ember bocor" (*leaky bucket*).

Contoh Tabel Analisis Kohort Bulanan (Cohort Retention Table):

| Kohort Pendaftaran | Ukuran Kohort | Bulan 0 | Bulan 1 | Bulan 2 | Bulan 3 |
|---|---|---|---|---|---|
| **Januari** | 1.000 user | 100% | 42% | 35% | 31% |
| **Februari** | 1.200 user | 100% | 46% | 39% | 36% |
| **Maret** | 1.500 user | 100% | 51% | 44% | - |

- **Kurva Retensi Mendatar (Flattening Curve):** Menandakan produk telah menemukan Product-Market Fit untuk segmen tertentu.
- **Kurva Menuju Nol:** Menandakan fitur baru tidak memberikan retensi jangka panjang, membutuhkan evaluasi akar masalah sebelum menambah anggaran akuisisi.

---

## 4. Integrasi Umpan Balik Kualitatif

Angka analitik menunjukkan *apa* yang terjadi (*what*), sedangkan umpan balik kualitatif menjelaskan *mengapa* hal itu terjadi (*why*).

1. **Support Tickets Tagging:** Beri tag kategori pada tiket bantuan (misal: `#checkout-confusing`, `#report-slow`). Tren kenaikan tag spesifik adalah sinyal kegagalan UX.
2. **Micro-Surveys (In-App CSAT):** Tanyakan kepuasan 1-klik (*rating 1-5 bintang*) tepat setelah user mengeksekusi fitur baru.
3. **User Interview Sederhana:** Luangkan waktu 15 menit per bulan mewawancarai 3-5 pengguna aktif dan pengguna yang baru saja berhenti berlangganan (*churned users*).

---

## 5. Keputusan Berbasis Data: Iterate, Pivot, atau Sunset?

Gunakan matriks pengambilan keputusan berbasis data dan biaya perawatan:

```
                          Adopsi Pengguna Tinggi
                                    ^
                                    |
          [OPTIMIZE / AUTOMATE]     |      [CORE FEATURE: EXPAND]
          - Penggunaan tinggi       |      - Nilai tinggi, engagement kuat
          - Butuh efisiensi infra   |      - Lanjutkan investasi roadmap
Biaya Pemeliharaan -----------------+-----------------> Nilai Bisnis
Rendah                              |                   Tinggi
          [SUNSET CANDIDATE]        |      [ITERATE / PIVOT]
          - Penggunaan mendekati 0  |      - Konsep disukai tapi ada friksi UX
          - Beban technical debt    |      - Redesain alur / perbaiki edukasi
                                    v
                          Adopsi Pengguna Rendah
```

- **Iterate:** Jika aktivasi rendah tetapi pengguna yang berhasil melewati onboarding memiliki retensi sangat tinggi (masalahnya ada pada UX/edukasi, bukan nilainya).
- **Pivot:** Jika pengguna menggunakan fitur dengan cara yang sama sekali berbeda dari tujuan perancangan awal.
- **Sunset:** Jika adopsi `< 2%` selama 3 kuartal berturut-turut meskipun telah dilakukan promosi dan edukasi berkala.

---

## 6. Audit Instrumentasi Analitik (Instrumentation Hygiene)

Dashboard metrik sering kali menyajikan data palsu akibat event tracking yang rusak (*event rot*):
- **Naming Convention yang Disiplin:** Gunakan pola konsisten, seperti `object_action` (contoh: `invoice_created`, `payment_submitted`, `checkout_cancelled`).
- **Verifikasi di CI Pipeline:** Pastikan payload analytics event masuk dalam pengujian otomatis agar developer tidak menghapus event secara tak sengaja saat refactoring.
- **Audit Skema Berkala:** Hapus event duplikat atau event usang yang sudah tidak lagi dibaca oleh tim produk.

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Memuja Vanity Metrics:** Terpukau oleh total download atau total akun terdaftar, padahal 90% di antaranya adalah akun mati (*dormant users*).
2. **Event Tracking Berlebihan (Data Hoarding):** Melacak setiap gerakan mouse pengguna tanpa tahu apa pertanyaan bisnis yang ingin dijawab.
3. **Mengabaikan Data Negatif:** Menolak hasil data retensi yang anjlok dan bersikeras mempertahankan fitur rumit hanya karena merasa telah menghabiskan waktu berbulan-bulan untuk kodenya (*sunk cost fallacy*).
4. **Analitik Terisolasi dari Engineering:** Tim engineer tidak pernah melihat dashboard produk, hanya mengerjakan tiket backlog tanpa konteks apakah kodenya berdampak atau sia-sia.

---

## 8. Dokumen Terkait

- [Post-Release Review](post-release-review.md) — Menggunakan metrik produk sebagai bahan evaluasi retrospektif pasca rilis.
- [Deprecation and Migration](deprecation-and-migration.md) — Prosedur memensiunkan fitur yang diputuskan untuk di-sunset.
- [Technical Debt](technical-debt.md) — Membersihkan kode pelacakan dan logika fitur yang tidak terpakai.
