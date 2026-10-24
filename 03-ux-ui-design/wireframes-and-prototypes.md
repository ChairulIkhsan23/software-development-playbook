# Wireframes and Prototypes

## 1. Fidelity Spectrum

Fidelity menunjukkan seberapa dekat artefak desain dengan produk akhir. Pilih level yang sesuai dengan fase proyek.

### Low-Fidelity (Lo-Fi) — Sketsa

**Apa:** Gambar tangan di kertas atau whiteboard. Kotak-kotak kasar, garis, teks placeholder.

**Kapan pakai:**
- Brainstorming awal — eksplorasi banyak ide cepat
- Diskusi internal tim sebelum komitmen desain
- Budget/waktu terbatas, perlu validasi konsep dulu

**Waktu pembuatan:** 5-15 menit per halaman.

**Tools:** Pen + paper, whiteboard, tablet + stylus.

**Aturan:** Tidak ada warna, tidak ada font spesifik, tidak ada gambar real. Fokus pada **layout dan hierarki konten**.

### Mid-Fidelity (Mid-Fi) — Wireframe Digital

**Apa:** Wireframe digital dengan grid, spacing proporsional, dan komponen dasar (tombol, input, card). Monokrom atau grayscale.

**Kapan pakai:**
- Sudah ada konsensus arah desain dari lo-fi
- Perlu komunikasi layout ke developer atau stakeholder
- Basis untuk usability testing awal

**Waktu pembuatan:** 30-60 menit per halaman.

**Tools:** Figma (gratis untuk 3 proyek), Excalidraw (open source), Balsamiq.

**Aturan:**
- Gunakan kotak abu-abu untuk placeholder gambar
- Teks menggunakan konten realistis (bukan "Lorem ipsum" — tulis teks yang mirip konten asli)
- Anotasi tertulis untuk menjelaskan behavior yang tidak terlihat (misal: "scroll horizontal di mobile")

### High-Fidelity (Hi-Fi) — Prototype Interaktif

**Apa:** Desain visual lengkap dengan warna, tipografi, ikon, dan gambar sesuai design system. Dapat diklik dan dinavigasi.

**Kapan pakai:**
- Design system sudah tersedia
- Perlu user testing realistis sebelum development
- Handoff ke developer — ini yang jadi acuan implementasi

**Waktu pembuatan:** 2-4 jam per halaman (termasuk interaksi).

**Tools:** Figma (prototyping built-in), Framer (untuk interaksi kompleks).

## 2. Kapan Pakai Fidelity Mana?

| Fase Proyek | Fidelity | Output |
|---|---|---|
| Ideation / brainstorm | Lo-fi | Sketsa 5-10 variasi layout |
| Validasi konsep + layout | Mid-fi | Wireframe digital, flow antar halaman |
| User testing + handoff | Hi-fi | Prototype interaktif + UI spec |
| Quick fix / iterasi kecil | Mid-fi / Hi-fi | Langsung di file desain existing |

> **Jangan loncat ke hi-fi terlalu cepat.** Lo-fi dan mid-fi lebih murah dibuang. Satu wireframe lo-fi yang salah = 10 menit terbuang. Satu prototype hi-fi yang salah = 2 hari terbuang.

## 3. Wireframe Conventions

### Elemen Visual

| Elemen | Representasi di Wireframe |
|---|---|
| Gambar / foto | Kotak abu-abu dengan ikon silang (X) diagonal |
| Teks heading | Garis tebal atau teks besar placeholder |
| Body text | Garis-garis horizontal tipis atau teks realistis |
| Tombol / CTA | Rounded rectangle dengan label teks |
| Input field | Rectangle dengan placeholder text |
| Icon | Lingkaran kecil atau simbol sederhana |

### Anotasi

Tambahkan anotasi untuk behavior yang tidak bisa ditunjukkan secara visual:

```
[Anotasi #1] Klik tombol "Simpan" → loading state 
selama proses → success toast muncul → redirect 
ke halaman daftar setelah 2 detik.

[Anotasi #2] Jika form validation gagal → inline 
error muncul di bawah field yang bermasalah → 
tombol submit tetap enabled → scroll ke error pertama.
```

### Penomoran Halaman

Beri nomor unik setiap halaman wireframe:
- `P-01` Beranda
- `P-02` Daftar Produk
- `P-03` Detail Produk
- `P-04a` Checkout — Step 1: Alamat
- `P-04b` Checkout — Step 2: Pembayaran

## 4. Tipe Prototype

### Clickable Prototype
- Halaman statis yang terhubung dengan link/hotspot
- Klik area tertentu → pindah ke halaman berikutnya
- Cukup untuk validasi flow dan navigasi
- Waktu pembuatan cepat

### Interactive Prototype
- Termasuk micro-interaction: hover state, animasi transisi, drag & drop, form validation visual
- Lebih realistis, lebih mahal dibuat
- Pakai untuk fitur kritis yang behavior-nya sulit dijelaskan dengan wireframe statis

### Kapan Pakai Mana?

| Kebutuhan | Tipe |
|---|---|
| Validasi alur navigasi | Clickable |
| Testing form kompleks | Interactive |
| Demo ke stakeholder | Clickable (cukup) |
| Testing micro-interaction | Interactive |
| Handoff ke developer | Clickable + anotasi detail |

## 5. Usability Testing dengan Prototype

### Kapan Testing?

- **Sebelum development** — mencegah rework mahal
- Minimal **5 partisipan** sudah bisa menemukan ~85% masalah usability (Nielsen Norman Group)

### Metode Testing

#### Think-Aloud Protocol
Partisipan diminta **berbicara keras** menceritakan apa yang dipikirkan saat menggunakan prototype.

Cara menjalankan:
1. Berikan skenario tugas: "Anda ingin membeli sepatu ukuran 42 dan bayar dengan transfer bank."
2. Minta partisipan bernarasi: "Saya lihat ada tombol kategori di atas... saya klik Sepatu..."
3. **Jangan membantu** — catat di mana partisipan ragu, bingung, atau salah klik
4. Rekam layar + audio (minta izin dulu)

#### Metrik yang Diukur

| Metrik | Cara Ukur | Target |
|---|---|---|
| **Task Success Rate** | % partisipan yang berhasil menyelesaikan tugas | ≥ 80% |
| **Time on Task** | Waktu rata-rata menyelesaikan tugas | Sesuai benchmark |
| **Error Rate** | Jumlah kesalahan / salah klik per tugas | Menurun tiap iterasi |
| **SUS Score** | System Usability Scale — kuesioner 10 pertanyaan, skor 0-100 | ≥ 68 (above average) |

#### SUS (System Usability Scale)
Kuesioner standar 10 pertanyaan dengan skala 1-5. Diberikan **setelah** partisipan menyelesaikan semua tugas. Skor rata-rata global: 68. Di atas 68 = above average. Di atas 80 = excellent.

## 6. Checklist Validasi Wireframe/Prototype

- [ ] Setiap halaman punya tujuan jelas — tidak ada halaman "sekedar ada"
- [ ] Navigasi konsisten di semua halaman (posisi menu, breadcrumb)
- [ ] Setiap form punya clear CTA (tombol utama jelas, tombol sekunder dibedakan)
- [ ] Empty state, loading state, dan error state sudah dipertimbangkan
- [ ] Konten menggunakan teks realistis, bukan Lorem ipsum
- [ ] Responsive layout sudah dipikirkan (minimal mobile + desktop)
- [ ] Aksesibilitas dasar: hierarki heading logis, kontras teks cukup
- [ ] Alur bisa diselesaikan tanpa dead end
- [ ] Anotasi lengkap untuk behavior dinamis (hover, scroll, animasi)
- [ ] Penomoran halaman konsisten dan terdokumentasi

## 7. Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|---|---|---|
| Langsung hi-fi tanpa lo-fi/mid-fi | Buang waktu polish desain yang konsepnya salah | Mulai lo-fi, iterasi, baru hi-fi |
| Lorem ipsum di mana-mana | Tidak ketahuan kalau teks asli terlalu panjang/pendek | Pakai konten realistis |
| Tidak anotasi behavior dinamis | Developer asumsi sendiri, hasil beda ekspektasi | Anotasi setiap interaction |
| Prototype terlalu interaktif untuk testing awal | Waktu pembuatan lama, feedback terlambat | Clickable prototype cukup untuk validasi awal |
| Tidak test dengan pengguna nyata | Bias tim — "menurut kita sudah jelas" | Minimal 5 partisipan think-aloud |
| Wireframe tanpa grid/spacing system | Layout tidak konsisten, developer improvise | Pakai grid system dari awal |
| Skip mobile wireframe | Tampilan mobile jadi afterthought | Wireframe mobile-first atau paralel |

## 8. Dokumen Terkait

- [User Flows](user-flows.md) — Alur yang divisualisasikan di wireframe
- [UI Specification](ui-specification.md) — Spesifikasi detail dari hi-fi prototype
- [Design System](design-system.md) — Komponen yang dipakai di hi-fi prototype
- [UI States and Error Messages](ui-states-and-error-messages.md) — State yang harus ada di setiap halaman
- [Information Architecture](information-architecture.md) — Struktur navigasi yang jadi kerangka wireframe
