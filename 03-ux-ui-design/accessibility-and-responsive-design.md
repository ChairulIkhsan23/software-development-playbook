# Accessibility and Responsive Design

## 1. Mengapa Accessibility Penting?

### Alasan Etis
Sekitar 15% populasi dunia memiliki disabilitas (WHO). Produk digital yang tidak accessible mengecualikan jutaan orang dari layanan, informasi, dan peluang.

### Alasan Legal
Banyak negara mewajibkan accessibility digital:
- **Indonesia:** UU No. 8 Tahun 2016 tentang Penyandang Disabilitas
- **AS:** ADA (Americans with Disabilities Act), Section 508
- **EU:** European Accessibility Act (2025)

### Alasan Bisnis
- Pasar lebih besar — 15% populasi = potensi pengguna signifikan
- SEO lebih baik — semantic HTML dan alt text membantu search engines
- UX lebih baik untuk semua orang — captioning membantu di tempat bising, kontras tinggi membantu di bawah sinar matahari

## 2. WCAG 2.1 Level AA — Ringkasan

WCAG (Web Content Accessibility Guidelines) adalah standar internasional. Level AA adalah target minimum yang realistis.

### 2.1 Perceivable (Bisa Dipersepsi)

Konten harus bisa diakses melalui berbagai indera.

| Kriteria | Requirement | Contoh |
|---|---|---|
| **Teks alternatif** | Semua gambar informatif punya `alt` text | `<img alt="Grafik penjualan Q3 naik 15%">` |
| **Kontras warna** | Teks normal: minimal 4.5:1. Teks besar (≥18pt bold / ≥24pt): 3:1 | Teks abu-abu #6B7280 di atas putih #FFFFFF = 4.6:1 ✅ |
| **Resize teks** | Konten tetap berfungsi saat teks diperbesar 200% | Pakai `rem`/`em`, bukan `px` untuk font-size |
| **Tidak hanya warna** | Informasi tidak disampaikan hanya lewat warna | Error field: border merah + ikon ⚠️ + teks error |
| **Captions** | Video punya subtitle / captions | Sediakan `.vtt` file |

### 2.2 Operable (Bisa Dioperasikan)

Semua fungsi bisa diakses tanpa mouse.

| Kriteria | Requirement | Contoh |
|---|---|---|
| **Keyboard accessible** | Semua fungsi bisa dijangkau via keyboard | `Tab`, `Enter`, `Escape`, `Arrow keys` |
| **Focus indicator** | Elemen yang focused punya visual indicator jelas | `outline: 2px solid #2563EB; outline-offset: 2px` |
| **Skip links** | Link untuk skip ke konten utama | `<a href="#main" class="skip-link">Langsung ke konten</a>` |
| **Tidak ada keyboard trap** | Pengguna tidak stuck di satu elemen | Modal: `Escape` untuk close, focus kembali ke trigger |
| **Cukup waktu** | Jangan auto-timeout tanpa warning | Session timeout: beri warning 2 menit sebelum expire |

### 2.3 Understandable (Bisa Dipahami)

Konten dan UI bisa dipahami.

| Kriteria | Requirement | Contoh |
|---|---|---|
| **Bahasa halaman** | Deklarasikan bahasa | `<html lang="id">` |
| **Label form** | Setiap input punya label yang terhubung | `<label for="email">Email</label><input id="email">` |
| **Error identification** | Error teridentifikasi jelas, ada saran perbaikan | "Email harus dalam format nama@domain.com" |
| **Navigasi konsisten** | Menu di posisi yang sama di semua halaman | Navbar selalu di atas, sidebar selalu di kiri |
| **Predictable** | Tidak ada perubahan konteks yang tidak terduga | Jangan auto-submit saat focus keluar dari field |

### 2.4 Robust

Konten kompatibel dengan berbagai teknologi.

| Kriteria | Requirement | Contoh |
|---|---|---|
| **Valid HTML** | Markup valid, nesting benar | Heading berurutan: H1 → H2 → H3, bukan H1 → H4 |
| **ARIA landmarks** | Area halaman ditandai semantik | `<nav>`, `<main>`, `<aside>`, `<footer>` |
| **Name, role, value** | Custom components punya info accessible | `<button aria-expanded="false">Menu</button>` |

## 3. Checklist Praktis Accessibility

### HTML Semantik

```html
<!-- ✅ Benar -->
<nav aria-label="Menu utama">...</nav>
<main id="main">
  <h1>Judul Halaman</h1>
  <section aria-labelledby="section-title">
    <h2 id="section-title">Sub Judul</h2>
  </section>
</main>
<footer>...</footer>

<!-- ❌ Salah -->
<div class="nav">...</div>
<div class="main">
  <div class="title">Judul Halaman</div>
</div>
```

### Skip Link

```html
<body>
  <a href="#main" class="skip-link">Langsung ke konten utama</a>
  <nav>...</nav>
  <main id="main">...</main>
</body>

<style>
.skip-link {
  position: absolute;
  left: -9999px;
}
.skip-link:focus {
  position: fixed;
  top: 0; left: 0;
  background: #2563EB;
  color: #fff;
  padding: 8px 16px;
  z-index: 9999;
}
</style>
```

### Focus Management

```css
/* Jangan hilangkan focus ring — modify agar lebih bagus */

/* ❌ Jangan */
*:focus { outline: none; }

/* ✅ Custom focus ring */
:focus-visible {
  outline: 2px solid #2563EB;
  outline-offset: 2px;
}
```

### Alt Text Guidelines

| Situasi | Alt Text |
|---|---|
| Foto produk | `alt="Sepatu lari Nike Air Zoom warna hitam"` |
| Ikon dekoratif | `alt=""` (kosong, bukan dihilangkan) |
| Grafik / chart | `alt="Grafik penjualan: Januari 100jt, Februari 120jt, Maret 95jt"` |
| Logo link ke beranda | `alt="Nama Perusahaan - ke beranda"` |
| Background image | Pakai CSS `background-image`, bukan `<img>` |

### Form Labels

```html
<!-- ✅ Benar — label terhubung via for/id -->
<label for="email">Alamat Email</label>
<input type="email" id="email" name="email"
       aria-describedby="email-help email-error">
<span id="email-help">Contoh: nama@email.com</span>
<span id="email-error" role="alert">Format email belum sesuai</span>

<!-- ❌ Salah — placeholder bukan pengganti label -->
<input type="email" placeholder="Email">
```

## 4. Testing Tools

| Tool | Tipe | Kegunaan | Harga |
|---|---|---|---|
| **axe DevTools** | Browser extension | Audit accessibility otomatis | Gratis |
| **Lighthouse** | Built-in Chrome | Audit accessibility + performance | Gratis |
| **WAVE** | Browser extension | Visualisasi error accessibility | Gratis |
| **Contrast Checker** | Web tool (WebAIM) | Cek rasio kontras warna | Gratis |
| **NVDA** | Screen reader (Windows) | Test manual screen reader | Gratis |
| **VoiceOver** | Screen reader (Mac/iOS) | Test manual screen reader | Built-in |
| **Keyboard only** | Manual test | Navigasi tanpa mouse | Gratis |

### Testing Manual Minimum

1. **Keyboard test:** Navigasi seluruh halaman hanya dengan keyboard (`Tab`, `Shift+Tab`, `Enter`, `Escape`, `Arrow`). Semua elemen bisa dijangkau? Focus terlihat?
2. **Zoom test:** Zoom browser 200%. Layout masih berfungsi? Teks tidak terpotong?
3. **Screen reader test:** Nyalakan NVDA/VoiceOver. Baca halaman dari atas. Apakah masuk akal?
4. **Color test:** Grayscale screenshot. Apakah informasi masih terbaca tanpa warna?

## 5. Responsive Design

### 5.1 Mobile-First Approach

Rancang untuk layar kecil dulu, lalu **perluas** untuk layar besar. Bukan sebaliknya.

**Kenapa mobile-first:**
- Memaksa prioritisasi konten — layar kecil tidak muat semuanya
- Progressive enhancement — fitur bertambah di layar besar
- Mobile traffic ≥ 50% di kebanyakan produk

```css
/* Mobile-first: default style = mobile */
.card-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 16px;
}

/* Tablet ke atas */
@media (min-width: 768px) {
  .card-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }
}

/* Desktop ke atas */
@media (min-width: 1024px) {
  .card-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

### 5.2 Breakpoint Strategy

| Breakpoint | Nama | Kolom | Gutter | Margin |
|---|---|---|---|---|
| Default (< 768px) | Mobile | 4 | 16px | 16px |
| ≥ 768px | Tablet | 8 | 24px | 32px |
| ≥ 1024px | Desktop | 12 | 24px | auto |
| ≥ 1440px | Wide | 12 | 24px | auto |

### 5.3 Touch Targets

**Minimum touch target: 44 × 44 pixel** (WCAG) atau **48 × 48 pixel** (Material Design).

```css
/* ✅ Touch-friendly button */
.button {
  min-height: 44px;
  min-width: 44px;
  padding: 12px 24px;
}

/* ✅ Touch-friendly link dalam list */
.list-item a {
  display: block;
  padding: 12px 16px; /* area tap cukup besar */
}
```

**Jarak antar touch target:** minimal 8px supaya tidak salah tap.

### 5.4 Viewport Units dan Safe Areas

```css
/* Full-height layout yang benar di mobile */
.app-container {
  min-height: 100dvh; /* dynamic viewport height — handles mobile browser chrome */
}

/* Handle notch / safe area (iPhone) */
.bottom-bar {
  padding-bottom: env(safe-area-inset-bottom);
}
```

## 6. Responsive Patterns

### Stack Pattern
Elemen yang horizontal di desktop → vertikal di mobile.

**Kapan:** Grid card, form fields berdampingan, footer multi-kolom.

### Reveal Pattern
Konten tambahan tersembunyi di mobile → terlihat di desktop.

**Kapan:** Sidebar filter (collapse jadi button di mobile), detail panel, secondary navigation.

### Off-Canvas Pattern
Panel geser dari samping, tersembunyi secara default di mobile.

**Kapan:** Navigation menu, filter panel, shopping cart panel.

```
Desktop: sidebar selalu terlihat di kiri
Mobile:  hamburger menu → sidebar slide dari kiri
```

## 7. Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|---|---|---|
| `outline: none` tanpa pengganti | Keyboard users tidak tahu elemen mana yang focused | Pakai `:focus-visible` custom ring |
| Placeholder sebagai label | Screen reader tidak baca, hilang saat mengetik | Selalu pakai `<label>` terpisah |
| Kontras warna kurang | Teks tidak terbaca oleh low vision users | Cek kontras 4.5:1 sebelum ship |
| Touch target terlalu kecil | Salah tap di mobile | Minimum 44×44px |
| Heading tidak berurutan (H1 → H4) | Screen reader kehilangan struktur | H1 → H2 → H3 berurutan |
| Gambar tanpa alt text | Screen reader bilang "image" tanpa konteks | Tulis alt text deskriptif |
| Horizontal scroll di mobile | Konten terpotong, UX frustrasi | Test di viewport kecil, pakai `max-width: 100%` |
| Fixed position element menutupi konten | Teks tertutup header/footer sticky | Tambah padding/margin sesuai tinggi fixed element |
| Desktop-first lalu "responsive-kan" | Layout mobile berantakan, banyak override | Mulai mobile-first |
| Abaikan `prefers-reduced-motion` | Pengguna motion-sensitive pusing | `@media (prefers-reduced-motion: reduce) { ... }` |

## 8. Dokumen Terkait

- [UI Specification](ui-specification.md) — Breakpoints, grid, dan spacing yang harus accessible
- [Design System](design-system.md) — Komponen harus accessible by default
- [UI States and Error Messages](ui-states-and-error-messages.md) — Error messages harus accessible (aria-live, role="alert")
- [Wireframes and Prototypes](wireframes-and-prototypes.md) — Responsive wireframe per breakpoint
- [Information Architecture](information-architecture.md) — Navigasi harus keyboard accessible
