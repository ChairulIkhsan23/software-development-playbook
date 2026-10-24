# Design System

## 1. Design System vs Style Guide vs Pattern Library

| Istilah | Isi | Contoh |
|---|---|---|
| **Style Guide** | Aturan visual: warna, tipografi, logo usage | Brand guideline PDF |
| **Pattern Library** | Koleksi komponen UI yang bisa dipakai ulang | Library komponen di Figma |
| **Design System** | Style guide + pattern library + **prinsip, guidelines, dokumentasi, governance** | Material Design (Google), Atlassian Design System |

Design system bukan hanya kumpulan komponen — tapi **sistem keputusan** yang membuat tim konsisten tanpa harus selalu bertanya.

> **Solo dev / tim kecil:** Tidak perlu design system seskala Material Design. Mulai dari **design tokens + 5-10 komponen inti** yang terdokumentasi. Grow organik sesuai kebutuhan.

## 2. Design Tokens

Design tokens adalah **nilai dasar** yang dibagikan antara desain (Figma) dan kode. Token adalah "single source of truth" untuk semua keputusan visual.

### 2.1 Color Tokens

```
/* Primitive tokens — warna mentah */
--blue-500:  #2563EB
--blue-600:  #1D4ED8
--red-500:   #DC2626
--green-600: #059669
--gray-50:   #F9FAFB
--gray-200:  #E5E7EB
--gray-500:  #6B7280
--gray-900:  #111827

/* Semantic tokens — berdasarkan fungsi */
--color-primary:        var(--blue-500)
--color-primary-hover:  var(--blue-600)
--color-text-primary:   var(--gray-900)
--color-text-secondary: var(--gray-500)
--color-border:         var(--gray-200)
--color-bg-primary:     #FFFFFF
--color-bg-secondary:   var(--gray-50)
--color-success:        var(--green-600)
--color-error:          var(--red-500)
```

**Kenapa dua layer?** Semantic token memungkinkan ganti theme (dark mode) tanpa ubah seluruh codebase — cukup remap semantic ke primitive yang berbeda.

### 2.2 Typography Tokens

```
--font-family-primary: 'Inter', sans-serif
--font-family-mono:    'JetBrains Mono', monospace

--font-size-xs:   12px   /* Caption */
--font-size-sm:   14px   /* Body */
--font-size-base: 16px   /* Body large */
--font-size-lg:   18px   /* H4 */
--font-size-xl:   20px   /* H3 */
--font-size-2xl:  24px   /* H2 */
--font-size-3xl:  30px   /* H1 */

--font-weight-regular:  400
--font-weight-medium:   500
--font-weight-semibold: 600
--font-weight-bold:     700

--line-height-tight:  1.25
--line-height-normal: 1.5
--line-height-loose:  1.75
```

### 2.3 Spacing Tokens

```
--space-1:  4px
--space-2:  8px
--space-3:  12px
--space-4:  16px
--space-6:  24px
--space-8:  32px
--space-12: 48px
--space-16: 64px
```

### 2.4 Elevation (Shadow) Tokens

```
--shadow-sm:  0 1px 2px rgba(0,0,0,0.05)                      /* Card ringan */
--shadow-md:  0 4px 6px rgba(0,0,0,0.07)                      /* Card, dropdown */
--shadow-lg:  0 10px 15px rgba(0,0,0,0.10)                    /* Modal, popover */
--shadow-xl:  0 20px 25px rgba(0,0,0,0.15)                    /* Dialog overlay */
```

### 2.5 Border Radius Tokens

```
--radius-sm:   4px    /* Badge, tag */
--radius-md:   8px    /* Card, input, tombol */
--radius-lg:   12px   /* Modal, panel besar */
--radius-xl:   16px   /* Card promosi */
--radius-full: 9999px /* Pill, avatar bulat */
```

## 3. Component Library: Atomic Design

### Atoms — Elemen terkecil, tidak bisa dipecah lagi
- Button (primary, secondary, ghost, danger)
- Input (text, number, password, textarea)
- Label
- Icon
- Badge / Tag
- Avatar
- Checkbox, Radio, Toggle

### Molecules — Kombinasi atoms yang punya fungsi
- Form field (label + input + error message)
- Search bar (input + icon + button)
- Card (image + title + description + CTA)
- Navigation item (icon + label + badge)
- Toast notification (icon + message + close button)

### Organisms — Kombinasi molecules yang jadi section UI
- Header / Navbar (logo + navigation items + user menu)
- Form section (multiple form fields + submit button)
- Data table (header row + data rows + pagination)
- Sidebar navigation (navigation items grouped)
- Modal / Dialog (header + content + action buttons)

## 4. Dokumentasi Komponen

Setiap komponen di library harus punya dokumentasi:

```markdown
## Button

### Props
| Prop     | Type    | Default   | Options                          |
|----------|---------|-----------|----------------------------------|
| variant  | string  | "primary" | "primary", "secondary", "ghost", "danger" |
| size     | string  | "md"      | "sm", "md", "lg"                 |
| disabled | boolean | false     | true, false                      |
| loading  | boolean | false     | true, false                      |
| icon     | string  | null      | nama icon (opsional)             |
| fullWidth| boolean | false     | true, false                      |

### States
- Default, Hover, Active, Focus, Disabled, Loading

### Usage Guidelines
- ✅ Satu primary button per section/form
- ✅ Gunakan "ghost" untuk aksi minor (cancel, close)
- ✅ Gunakan "danger" hanya untuk aksi destruktif (delete)
- ❌ Jangan pakai primary button untuk setiap aksi
- ❌ Jangan disable tanpa tooltip penjelasan kenapa

### Contoh
[Link ke Storybook / Figma component]
```

## 5. Governance: Mengelola Design System

### Cara Menambah Komponen Baru

1. **Identifikasi kebutuhan** — Komponen dipakai di ≥ 3 tempat? Jika hanya 1-2 tempat, bikin custom dulu
2. **Proposal** — Tulis kebutuhan: apa, kenapa, variasi apa yang dibutuhkan
3. **Design** — Buat di Figma dengan semua state dan variasi
4. **Review** — Tim review (cukup 1 reviewer untuk tim kecil)
5. **Implement** — Buat di kode + Storybook
6. **Document** — Tulis props, states, usage guidelines
7. **Publish** — Merge ke main, update versi

### Cara Memodifikasi Komponen Existing

1. **Cek impact** — Komponen ini dipakai di mana saja? Breaking change?
2. **Jika non-breaking:** Tambah props/variant baru, backward compatible
3. **Jika breaking:** Komunikasikan ke tim, migration plan, bump major version

### Aturan Tim Kecil

- Satu orang "owner" design system (bisa part-time)
- Review PR design system lebih ketat dari fitur biasa
- Jangan modify component untuk satu use case — jika unik, bikin custom

## 6. Versioning

Gunakan **semantic versioning** untuk design system:

```
MAJOR.MINOR.PATCH

1.0.0 → 1.1.0  (tambah komponen baru — backward compatible)
1.1.0 → 1.2.0  (tambah variant baru pada komponen existing)
1.2.0 → 2.0.0  (breaking change — props berubah, komponen dihapus)
1.2.0 → 1.2.1  (bug fix — spacing salah, warna typo)
```

Changelog wajib ditulis setiap release. Format:

```markdown
## [1.2.0] - 2024-03-15
### Added
- Button: variant "ghost" baru
- Toast: auto-dismiss setelah 5 detik
### Changed
- Card: padding default dari 16px ke 24px
### Fixed
- Input: focus ring tidak muncul di Safari
```

## 7. Tools

| Tool | Kegunaan | Harga |
|---|---|---|
| **Figma** | Desain komponen, prototyping, variable/token | Gratis (3 file) |
| **Storybook** | Dokumentasi + playground komponen di kode | Open source |
| **Chromatic** | Visual regression testing untuk Storybook | Gratis (5K snapshots/bulan) |
| **Style Dictionary** | Generate token dari JSON ke CSS/JS/iOS/Android | Open source |

### Workflow Minimal (Tim Kecil)

```
Figma (desain tokens + komponen)
    ↓ export tokens as JSON
Style Dictionary (generate CSS variables)
    ↓
Code (komponen React/Vue pakai CSS variables)
    ↓
Storybook (dokumentasi + testing)
```

## 8. Mulai Kecil — Jangan Build Semua di Awal

### Fase 1: Foundation (Minggu 1-2)
- Color tokens (primary, neutral, semantic)
- Typography scale
- Spacing scale
- 3 komponen: Button, Input, Card

### Fase 2: Core Components (Minggu 3-4)
- Form fields (label + input + error)
- Modal/Dialog
- Toast notification
- Table (basic)

### Fase 3: Expand On Demand
- Tambah komponen saat **benar-benar dibutuhkan** di fitur
- Jangan bikin komponen "untuk jaga-jaga"
- Setiap komponen baru harus dipakai di ≥ 3 tempat

## 9. Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|---|---|---|
| Build design system besar di awal sebelum produk jelas | Banyak komponen tidak terpakai, maintenance berat | Mulai kecil, grow organik |
| Token tidak sinkron antara Figma dan kode | Warna / spacing beda, revisi bolak-balik | Single source of truth — export dari satu tempat |
| Komponen tanpa dokumentasi | Developer pakai salah, inkonsisten | Tulis props + usage guidelines minimal |
| Modifikasi komponen untuk satu use case | Komponen jadi bloated, sulit maintain | Bikin custom jika unik, masukkan ke system jika ≥3x pakai |
| Tidak ada governance — semua orang ubah sesuka hati | Inkonsistensi, regresi visual | Satu owner, PR review ketat |
| Skip versioning | Tidak tahu kapan breaking change terjadi | Semantic versioning + changelog |
| Pakai hex hardcoded di mana-mana, bukan token | Tidak bisa ganti theme, inkonsisten | Selalu pakai token/variable |

## 10. Dokumen Terkait

- [UI Specification](ui-specification.md) — Tokens dan komponen direferensikan di spec
- [UI States and Error Messages](ui-states-and-error-messages.md) — State yang harus ada di setiap komponen
- [Accessibility and Responsive Design](accessibility-and-responsive-design.md) — Standar aksesibilitas komponen
- [Wireframes and Prototypes](wireframes-and-prototypes.md) — Komponen dipakai di prototype hi-fi
