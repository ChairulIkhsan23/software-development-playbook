# UI Specification (Handoff Guide)

## 1. Apa Itu UI Specification?

UI Specification (UI spec) adalah **dokumen serah-terima dari designer ke developer** yang berisi semua detail teknis yang dibutuhkan untuk mengimplementasikan desain secara akurat.

UI spec menjawab pertanyaan developer:
- Berapa jarak antar elemen?
- Warna hex apa yang dipakai?
- Bagaimana perilaku tombol saat di-hover, disabled, loading?
- Seperti apa tampilan di layar kecil?
- Teks ini truncate atau wrap?

> **Tanpa UI spec yang jelas:** developer menebak → hasil tidak sesuai desain → revisi bolak-balik → timeline molor.

## 2. Apa yang Harus Ada di UI Spec

### 2.1 Layout Grid

| Properti | Mobile | Tablet | Desktop |
|---|---|---|---|
| Kolom | 4 | 8 | 12 |
| Gutter | 16px | 24px | 24px |
| Margin (kiri-kanan) | 16px | 32px | auto (max-width container) |
| Max content width | 100% | 100% | 1200px |

### 2.2 Spacing System

Gunakan skala berbasis **4px** untuk konsistensi:

```
4px   — spacing-1  (antar elemen sangat rapat, misal icon + label)
8px   — spacing-2  (antar elemen dalam satu grup)
12px  — spacing-3  (padding dalam komponen kecil)
16px  — spacing-4  (padding standar, gap antar field form)
24px  — spacing-6  (gap antar section dalam card)
32px  — spacing-8  (gap antar komponen/card)
48px  — spacing-12 (gap antar section halaman)
64px  — spacing-16 (gap besar antar section utama)
```

### 2.3 Responsive Breakpoints

```css
/* Mobile-first approach */
/* Default: Mobile (< 768px) */

@media (min-width: 768px)  { /* Tablet   */ }
@media (min-width: 1024px) { /* Desktop  */ }
@media (min-width: 1440px) { /* Wide     */ }
```

| Breakpoint | Nama | Target Device |
|---|---|---|
| < 768px | Mobile | Smartphone portrait |
| 768px – 1023px | Tablet | Tablet portrait, smartphone landscape |
| 1024px – 1439px | Desktop | Laptop, tablet landscape |
| ≥ 1440px | Wide | Monitor eksternal, desktop besar |

### 2.4 Typography Scale

```
Display    — 36px / 44px line-height / Bold
H1         — 30px / 38px / Bold
H2         — 24px / 32px / Semibold
H3         — 20px / 28px / Semibold
H4         — 18px / 26px / Medium
Body Large — 16px / 24px / Regular
Body       — 14px / 22px / Regular
Caption    — 12px / 18px / Regular
Overline   — 11px / 16px / Medium / UPPERCASE
```

Tentukan juga:
- **Font family:** Primer (untuk UI) dan sekunder (untuk heading jika beda)
- **Font weight** yang diizinkan (jangan pakai semua weight — pilih 3-4)
- **Letter-spacing** jika ada penyesuaian

### 2.5 Color Tokens

Dokumentasikan warna sebagai **token** bukan hex mentah, supaya bisa di-theme dan konsisten:

```
/* Brand */
--color-primary:       #2563EB   /* Tombol utama, link */
--color-primary-hover: #1D4ED8   /* Hover state */
--color-secondary:     #7C3AED   /* Aksen sekunder */

/* Neutral */
--color-text-primary:   #111827  /* Teks utama */
--color-text-secondary: #6B7280  /* Teks pendukung */
--color-border:         #E5E7EB  /* Garis pembatas */
--color-bg-primary:     #FFFFFF  /* Background utama */
--color-bg-secondary:   #F9FAFB  /* Background sekunder */

/* Semantic */
--color-success: #059669  /* Berhasil */
--color-warning: #D97706  /* Peringatan */
--color-error:   #DC2626  /* Error */
--color-info:    #2563EB  /* Informasi */
```

### 2.6 Component Behavior & Interaction States

Setiap komponen interaktif harus didokumentasikan state-nya:

| State | Keterangan | Yang Berubah |
|---|---|---|
| **Default** | Kondisi awal | — |
| **Hover** | Kursor di atas elemen | Warna, shadow, cursor |
| **Active / Pressed** | Sedang diklik/ditekan | Warna lebih gelap, scale down |
| **Focus** | Dipilih via keyboard (Tab) | Focus ring visible |
| **Disabled** | Tidak bisa diinteraksi | Opacity 50%, cursor not-allowed |
| **Loading** | Sedang proses | Spinner/shimmer, disabled |
| **Error** | Validasi gagal | Border merah, error message |
| **Success** | Aksi berhasil | Border hijau, success message |

### 2.7 Animasi dan Transisi

```
/* Standar transisi */
--transition-fast:   150ms ease-out  /* Hover, focus */
--transition-normal: 250ms ease-out  /* Modal open, dropdown */
--transition-slow:   350ms ease-out  /* Page transition, slide */

/* Easing */
ease-out:    masuk cepat, keluar pelan (untuk elemen muncul)
ease-in:     masuk pelan, keluar cepat (untuk elemen menghilang)
ease-in-out: untuk gerakan berulang
```

Dokumentasikan juga: apa yang beranimasi (opacity, transform, height) dan kapan animasi terjadi.

## 3. Annotation Best Practices

### Apa yang Perlu Di-annotate

- **Spacing** antar elemen yang tidak jelas dari visual saja
- **Behavior dinamis:** truncate vs wrap, scroll vs pagination, auto-resize
- **Conditional visibility:** "Section ini hanya muncul jika user sudah verifikasi email"
- **Content limits:** "Nama produk maksimal 80 karakter, lebih dari itu truncate dengan ellipsis"
- **Edge cases:** "Jika gambar gagal load, tampilkan placeholder dengan inisial"

### Format Anotasi

```
📐 Spacing: 16px antara judul dan deskripsi
📏 Max-width card: 320px, min-width: 280px
✂️ Truncate: judul max 2 baris, deskripsi max 3 baris
🔄 Hover: background berubah ke --color-bg-secondary
📱 Mobile: card stack vertikal, full-width
```

## 4. Design-Dev Handoff Workflow

### Step 1: Designer Menyiapkan
- File Figma terorganisir per halaman/flow
- Semua komponen pakai component library (bukan detach)
- Anotasi lengkap pada setiap halaman
- Semua state terdokumentasi (default, hover, error, loading, empty)
- Responsive variant tersedia (minimal mobile + desktop)

### Step 2: Handoff Meeting
- Designer walk-through desain ke developer (30-60 menit per fitur)
- Developer tanya klarifikasi **saat itu** — jangan tunda
- Catat semua pertanyaan dan jawaban di dokumen bersama

### Step 3: Developer Mengimplementasi
- Developer inspect Figma langsung untuk spacing, warna, typography
- Jika ada ambiguitas → tanya dulu, jangan asumsi
- Jika ada keterbatasan teknis → komunikasikan ke designer + PM

### Step 4: Design Review
- Designer review implementasi di staging/preview
- Feedback spesifik: "Spacing antara header dan content harusnya 24px, sekarang terlihat 16px"
- Bukan: "Ini kurang bagus" (tidak actionable)

## 5. Checklist UI Spec

- [ ] Grid system terdefinisi (kolom, gutter, margin per breakpoint)
- [ ] Spacing scale terdokumentasi dan konsisten
- [ ] Breakpoints responsif terdefinisi dengan behavior per breakpoint
- [ ] Typography scale lengkap (size, weight, line-height, letter-spacing)
- [ ] Color tokens terdokumentasi (bukan hex mentah di mana-mana)
- [ ] Setiap komponen interaktif punya dokumentasi semua state
- [ ] Behavior dinamis di-annotate (truncate, wrap, scroll, conditional)
- [ ] Content limits terdefinisi (max karakter, max baris)
- [ ] Animasi dan transisi terdokumentasi (durasi, easing, properti)
- [ ] Responsive behavior terdokumentasi per breakpoint
- [ ] Asset format terdefinisi (SVG untuk ikon, WebP/AVIF untuk foto)

## 6. Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|---|---|---|
| Tidak ada spacing system — semua jarak "by feel" | Implementasi inkonsisten, banyak revisi | Definisikan spacing scale di awal |
| Hanya desain desktop, mobile "nanti" | Mobile layout jadi afterthought, UX buruk | Mobile-first atau paralel desktop + mobile |
| State tidak lengkap — hanya desain happy state | Developer bikin state sendiri, inkonsisten | Desain semua 7 state (lihat UI States) |
| Anotasi tidak ada / minim | Developer menebak, hasil beda | Annotate setiap behavior non-obvious |
| Warna pakai hex langsung, bukan token | Tidak bisa ganti theme, inkonsisten | Pakai design tokens |
| Handoff tanpa meeting | Miskomunikasi, asumsi salah | Walk-through desain 30-60 menit |
| Feedback review tidak spesifik | Developer tidak tahu apa yang harus diubah | Berikan feedback dengan nilai spesifik |
| Tidak dokumentasi max content length | Teks panjang merusak layout | Definisikan max karakter + truncation behavior |

## 7. Dokumen Terkait

- [Design System](design-system.md) — Token dan komponen yang direferensikan di UI spec
- [Wireframes and Prototypes](wireframes-and-prototypes.md) — Prototype sebagai basis UI spec
- [UI States and Error Messages](ui-states-and-error-messages.md) — Detail state yang harus ada di spec
- [Accessibility and Responsive Design](accessibility-and-responsive-design.md) — Standar aksesibilitas dalam spec
- [User Flows](user-flows.md) — Alur yang menjadi konteks setiap halaman di spec
