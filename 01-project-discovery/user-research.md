# User Research

## 1. Mengapa Riset User?

Riset user adalah cara paling efektif untuk menghindari asumsi yang salah tentang siapa pengguna dan apa yang mereka butuhkan. Tanpa riset:

- Kamu membangun berdasarkan **asumsi** developer, bukan kebutuhan nyata.
- Fitur yang "logis" dari sudut pandang teknis bisa jadi **tidak berguna** bagi user.
- Masalah yang kamu anggap besar mungkin **bukan prioritas** user.
- UI yang kamu anggap intuitif mungkin **membingungkan** user.

> **Prinsip:** Kamu bukan user-mu. Bahkan jika kamu domain expert, perspektifmu berbeda dari orang yang menggunakan sistem setiap hari.

---

## 2. Metode Riset

### 2.1 User Interview (Wawancara 1-on-1)

Metode paling fundamental. Cocok untuk eksplorasi mendalam tentang workflow, motivasi, dan frustrasi user.

**Kapan digunakan:** Selalu — ini metode default untuk semua proyek.

#### Persiapan

1. **Tentukan tujuan wawancara** — pertanyaan apa yang ingin dijawab? Misal: "Bagaimana proses approval pembelian saat ini?"
2. **Siapkan interview guide** — 5-8 pertanyaan terbuka (open-ended). Hindari pertanyaan ya/tidak.
3. **Rekrut partisipan** — minimal 5 orang, idealnya 8. Pilih yang mewakili variasi peran dan pengalaman.
4. **Siapkan logistik** — jadwal 45-60 menit per sesi. Minta izin merekam jika perlu.

#### Pertanyaan yang Baik vs Buruk

| ❌ Hindari | ✅ Gunakan |
|-----------|-----------|
| "Apakah kamu suka fitur X?" | "Ceritakan bagaimana kamu menyelesaikan [task] hari ini." |
| "Apakah kamu akan pakai fitur ini?" | "Kapan terakhir kali kamu mengalami masalah dengan [proses]? Ceritakan." |
| "Bukankah lebih baik kalau ada fitur Y?" | "Apa bagian yang paling memakan waktu dari pekerjaan ini?" |
| "Fitur A atau B yang kamu pilih?" | "Jika kamu bisa mengubah satu hal dari proses saat ini, apa itu?" |

#### Cara Melakukan Wawancara

1. **Buka dengan ice-breaking** — perkenalkan diri, jelaskan tujuan, tegaskan tidak ada jawaban benar/salah.
2. **Mulai dari umum ke spesifik** — "Ceritakan tentang pekerjaan sehari-hari" → "Bagaimana proses X?"
3. **Dengarkan lebih banyak, bicara lebih sedikit** — rasio ideal: 80% mereka bicara, 20% kamu bertanya.
4. **Gali lebih dalam** — gunakan "Bisa ceritakan lebih detail?" atau "Mengapa itu menjadi masalah?"
5. **Catat observasi, bukan hanya kata-kata** — perhatikan emosi, bahasa tubuh, jeda.
6. **Tutup dengan pertanyaan terbuka** — "Ada hal lain yang ingin kamu sampaikan tentang topik ini?"

#### Analisis Hasil Wawancara

1. **Transkrip atau catatan** — tulis ulang poin-poin kunci dalam 24 jam setelah wawancara.
2. **Cari pola** — temukan tema yang muncul berulang dari beberapa partisipan.
3. **Buat affinity map** — kelompokkan temuan serupa ke dalam kategori (bisa pakai sticky notes atau tool digital).
4. **Prioritaskan** — temuan yang muncul dari 3+ partisipan = pola kuat. Temuan dari 1 orang = perlu validasi lebih lanjut.

### 2.2 Contextual Inquiry (Observasi Lapangan)

Mengamati user **di lingkungan kerja nyata** mereka saat melakukan task sebenarnya.

**Kapan digunakan:**

- Saat workflow melibatkan proses fisik atau multi-sistem.
- Saat user sulit menjelaskan prosesnya secara verbal (mereka "just do it").
- Saat kamu curiga ada gap antara apa yang user katakan dan apa yang sebenarnya terjadi.

**Cara melakukan:**

1. Minta izin untuk mengamati user selama 1-2 jam kerja normal.
2. Jangan intervensi — biarkan mereka bekerja seperti biasa.
3. Catat: tools yang digunakan, workaround yang dilakukan, titik-titik jeda/frustrasi.
4. Setelah observasi, tanyakan klarifikasi: "Tadi saya lihat kamu copy data dari sistem A ke Excel — bisa ceritakan mengapa?"

**Kelebihan:** Menemukan hal yang tidak akan muncul dari wawancara — karena user sering lupa atau menganggap workaround mereka "normal".

### 2.3 Survei

Mengumpulkan data kuantitatif dari jumlah responden yang lebih besar.

**Kapan digunakan:**

- Setelah wawancara kualitatif — untuk validasi temuan pada skala yang lebih besar.
- Saat perlu data numerik untuk meyakinkan stakeholder ("73% user mengalami masalah X").
- Saat user tersebar di lokasi berbeda dan sulit diwawancara.

**Kapan TIDAK digunakan:**

- Sebagai pengganti wawancara di awal discovery — survei tidak bisa menggali "mengapa".
- Saat kamu belum tahu pertanyaan yang tepat untuk ditanyakan.

**Tips survei yang efektif:**

- Maksimal 10-15 pertanyaan — lebih dari itu, completion rate turun drastis.
- Campurkan pertanyaan pilihan ganda (kuantitatif) dengan 1-2 pertanyaan terbuka (kualitatif).
- Uji survei dengan 2-3 orang sebelum disebar — pastikan pertanyaan tidak ambigu.
- Sertakan opsi "Lainnya" dan "Tidak relevan".

---

## 3. Persona

Persona adalah representasi fiktif tapi berbasis data dari kelompok user utama.

### Template Persona

```
## Persona: [Nama Fiktif]

**Profil:**
- Role/Jabatan: [misal: Staff Administrasi Gudang]
- Usia: [range, misal: 25-35 tahun]
- Literasi digital: [Rendah / Sedang / Tinggi]
- Perangkat utama: [Desktop kantor, laptop pribadi, smartphone]

**Tujuan Utama:**
- [Apa yang ingin dicapai dalam pekerjaannya]

**Frustrasi (Pain Points):**
- [Hal yang memperlambat atau menghambat]
- [Sumber error atau kebingungan]

**Perilaku Saat Ini:**
- [Tools yang dipakai sekarang]
- [Workaround yang dilakukan]

**Kutipan Representatif:**
> "[Kalimat aktual atau parafrase dari wawancara]"

**Kebutuhan dari Sistem Baru:**
- [Apa yang harus ada agar persona ini terbantu]
```

### Contoh Persona

```
## Persona: Pak Hendra

**Profil:**
- Role: Supervisor Gudang
- Usia: 38 tahun
- Literasi digital: Sedang — bisa pakai Excel tapi belum familiar ERP
- Perangkat: Desktop Windows di kantor, WhatsApp di HP untuk koordinasi

**Tujuan Utama:**
- Memastikan stok barang akurat dan pengiriman tepat waktu.

**Frustrasi:**
- Data stok di sistem sering tidak cocok dengan fisik di gudang.
- Harus rekap manual setiap sore untuk laporan ke manager.
- Sering ditelepon sales untuk cek ketersediaan stok saat sedang sibuk.

**Perilaku Saat Ini:**
- Catat pergerakan barang di buku tulis, lalu input ke Excel malam hari.
- Kirim foto buku catatan via WhatsApp jika ada yang tanya stok.

**Kutipan:**
> "Saya sudah catat di buku, tapi kadang lupa input ke komputer.
> Jadi datanya sering beda."

**Kebutuhan:**
- Input stok yang bisa dilakukan langsung dari HP saat di gudang.
- Notifikasi otomatis jika stok mendekati batas minimum.
```

---

## 4. Empathy Map

Alat visual untuk merangkum pemahaman tentang user dari 4 perspektif:

| Kuadran | Pertanyaan Panduan | Contoh (Pak Hendra) |
|---------|-------------------|---------------------|
| **Says** (Apa yang dikatakan) | Kutipan langsung dari wawancara | "Data stok sering nggak cocok." |
| **Thinks** (Apa yang dipikirkan) | Kekhawatiran atau harapan yang mungkin tidak diucapkan | "Jangan-jangan nanti saya disalahkan kalau stok hilang." |
| **Does** (Apa yang dilakukan) | Tindakan dan perilaku yang teramati | Catat di buku → input ke Excel malam hari → kirim foto via WA. |
| **Feels** (Apa yang dirasakan) | Emosi — frustrasi, cemas, antusias | Frustrasi karena kerja dobel (manual + digital). Cemas soal akurasi. |

**Cara menggunakan:** Isi setelah wawancara atau observasi. Satu empathy map per persona. Gunakan sebagai referensi saat membuat keputusan desain.

---

## 5. Competitive Analysis Dasar

Sebelum membangun, pelajari apa yang sudah ada di pasaran:

| Yang Perlu Dicek | Pertanyaan |
|-----------------|------------|
| **Kompetitor langsung** | Siapa yang sudah menyediakan solusi serupa? |
| **Fitur utama** | Fitur apa yang mereka punya? Mana yang relevan untuk user kita? |
| **Kekurangan** | Apa keluhan user terhadap solusi yang ada? (Cek review, forum, atau tanyakan langsung) |
| **Harga** | Berapa biayanya? Apakah user kita mampu/mau bayar? |
| **Diferensiasi** | Apa yang bisa kita lakukan berbeda/lebih baik? |

**Untuk solo dev:** Cukup 30-60 menit Googling, cek Product Hunt, G2, atau forum terkait. Jangan over-analyze — tujuannya memastikan kamu tidak reinvent the wheel.

---

## 6. Riset User untuk Solo Developer / Budget Terbatas

Tidak punya budget riset besar? Ini cara melakukan riset dengan effort minimal:

| Metode | Effort | Hasil |
|--------|--------|-------|
| **Wawancara informal 5 orang** — bisa lewat telepon, video call, atau ngobrol saat makan siang | 3-5 jam total | Pemahaman kualitatif tentang masalah dan kebutuhan |
| **Guerrilla testing** — minta 3 orang coba prototype/sketch dan amati | 2-3 jam total | Validasi awal flow dan UI |
| **Baca forum/community** — Reddit, StackOverflow, grup Telegram/Discord, review produk kompetitor | 1-2 jam | Temuan pain point dari user yang sudah vokal |
| **Analisis support ticket/email** — jika ada sistem existing, baca keluhan user | 1-2 jam | Pattern masalah yang paling sering muncul |
| **Google Forms survey** — 5-10 pertanyaan, sebar di channel yang relevan | 1 jam setup + tunggu respons | Data kuantitatif dasar |

> **Minimum viable research:** 5 wawancara 30 menit + baca 20 support tickets/forum posts. Total effort: ~5 jam. Ini sudah jauh lebih baik daripada nol riset.

---

## 7. Kesalahan Umum

| Kesalahan | Dampak | Pencegahan |
|-----------|--------|------------|
| **Tidak melakukan riset sama sekali** | Membangun berdasarkan asumsi yang mungkin salah | Minimal wawancara 5 orang — ini non-negotiable |
| **Bertanya "apakah kamu akan pakai fitur ini?"** | User hampir selalu bilang "ya" — ini data yang tidak berguna | Tanyakan pengalaman masa lalu dan perilaku saat ini, bukan prediksi masa depan |
| **Hanya riset user yang mudah dijangkau** | Bias seleksi — user yang easy-to-reach bukan representasi semua user | Usahakan variasi role, lokasi, dan tingkat pengalaman |
| **Membuat persona dari imajinasi** | Persona tanpa data = fiksi yang menyesatkan | Persona harus berdasarkan temuan wawancara/observasi nyata |
| **Terlalu banyak persona** | Kehilangan fokus — tidak bisa melayani semua orang | Maksimal 3-4 persona untuk proyek kecil-menengah |
| **Riset sekali lalu tidak pernah lagi** | Kebutuhan user berubah seiring waktu | Jadwalkan riset singkat setiap quarter atau per major release |

---

## 8. Dokumen Terkait

- [Problem Discovery](problem-discovery.md) — konteks masalah yang menjadi dasar riset user.
- [Stakeholder Analysis](stakeholder-analysis.md) — identifikasi siapa saja user yang perlu diriset.
- [Assumptions and Constraints](assumptions-and-constraints.md) — temuan riset memvalidasi atau membantah asumsi.
- [Discovery Exit Criteria](discovery-exit-criteria.md) — persona dan temuan riset adalah exit criteria discovery.
