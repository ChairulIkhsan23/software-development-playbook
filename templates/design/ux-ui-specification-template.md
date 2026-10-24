---
title: "UX/UI Specification & Handoff Document"
screen_id: "SCR-[MODUL]-[NNN]"
screen_name: "[Nama Layanan / Halaman Layar]"
designer: "[Nama UI/UX Designer]"
status: "[Ready for Dev | In Review | Iteration]"
figma_url: "https://www.figma.com/file/[KEY]/[Project-Name]?node-id=[NODE_ID]"
last_updated: "[YYYY-MM-DD]"
---

# UX/UI Specification: [Nama Layanan / Halaman]

> **Panduan Pengisian:**
> Dokumen ini adalah kontrak serah terima (*handoff*) dari tim desain ke tim frontend developer.
> Memuat struktur layout, token desain, state interaktif setiap komponen, mikro-kopi validasi, dan spesifikasi gerak.

---

## 1. Metadata Layar & Konteks Alur (User Flow Context) [Wajib]

> **Panduan Pengisian:**
> Definisikan konteks penempatan layar ini dalam perjalanan pengguna (*user journey*).

- **Screen ID [Wajib]:** `SCR-[MODUL]-[NNN]` (contoh: `SCR-CHECKOUT-001`)
- **Nama Layar [Wajib]:** [Halaman Ringkasan & Pembayaran Pesanan]
- **Route / URL Path [Wajib]:** `/checkout/payment-summary`
- **Persona Target [Wajib]:** [Pelanggan terdaftar yang memiliki minimal 1 item di keranjang belanja]
- **Kondisi Awal (Pre-condition):** [Pengguna sudah login dan memilih alamat pengiriman]
- **Pemicu (Trigger):** [Klik tombol "Lanjut ke Pembayaran" dari halaman keranjang]
- **Kondisi Akhir (Post-condition):** [Faktur pesanan terbit dan pengguna diarahkan ke gateway pembayaran]

## 2. Grid Tata Letak & Breakpoint Responsif [Wajib]

> **Panduan Pengisian:**
> Cantumkan spesifikasi tata letak untuk setiap ukuran layar perangkat target.

| Breakpoint | Rentang Lebar Layar | Jumlah Kolom | Margin Kontainer | Lebar Gutter | Target Perangkat |
|---|---|---|---|---|---|
| **Mobile (`sm`)** | `< 640px` | 4 kolom | 16px | 12px | Smartphone (iPhone / Android) |
| **Tablet (`md`)** | `640px - 1023px` | 8 kolom | 24px | 16px | iPad / Tablet vertikal |
| **Desktop (`lg`)** | `1024px - 1439px` | 12 kolom | 32px | 24px | Laptop / Layar standar |
| **Wide Desktop (`xl`)** | `≥ 1440px` | 12 kolom | Auto (Max 1280px) | 24px | Monitor desktop lebar |

## 3. Token Desain Terpilih (Design Tokens Reference) [Wajib]

> **Panduan Pengisian:**
> Rujuk token warna dan tipografi global tanpa menggunakan hardcoded *hex-code* sembarangan di komponen.

- **Color Tokens:**
  - `color-primary`: `#0D6EFD` (Brand blue - aksi utama)
  - `color-neutral-dark`: `#1E293B` (Teks judul utama)
  - `color-neutral-body`: `#475569` (Teks paragraf isi)
  - `color-surface`: `#FFFFFF` / `color-surface-subtle`: `#F8FAFC` (Latar kartu dan form)
  - `color-danger`: `#DC3545` (Pesan galat / tombol destruktif)
- **Typography Tokens:**
  - `text-h1`: 32px / Bold / Line-height 1.25 (Judul utama halaman)
  - `text-body-md`: 16px / Regular / Line-height 1.5 (Teks instruksi form)
  - `text-caption`: 12px / Medium / Line-height 1.33 (Teks bantuan helper & mikro-kopi error)

## 4. Status Interaksi Komponen UI (Interaction States) [Wajib]

> **Panduan Pengisian:**
> Definisikan wujud visual komponen saat berinteraksi dengan pengguna (hindari state yang tidak didefinisikan).

| Nama Komponen | Default State | Hover State | Active / Focus State | Disabled State | Loading State | Error State |
|---|---|---|---|---|---|---|
| Tombol Bayar Sekarang | Background biru pekat, teks putih | Opacity 90%, kursor pointer | Border outline 2px biru muda | Background abu-abu muda, kursor prohibited | Spinner memutar di sisi kiri teks, label non-aktif | Border merah bergetar 1 siklus |
| Input Nomor Kartu | Border abu-abu 1px, placeholder abu-abu | Border abu-abu 2px gelap | Ring biru 3px (aksesibilitas fokus) | Background abu-abu netral, teks abu redup | Ikon loader di kanan dalam input | Border merah 1.5px solid |

## 5. Mikro-kopi Validasi Formulir (Validation Microcopy) [Kondisional] *(Boleh dihapus jika halaman tanpa form)*

> **Panduan Pengisian:**
> Pastikan pesan kesalahan jelas, manusiawi, dan memberitahu cara memperbaikinya.

| Input Field | Aturan Validasi | Teks Bantuan (Helper Text) | Pesan Error (Bahasa Indonesia) |
|---|---|---|---|
| Nomor Telepon | Wajib diisi, format angka Indo (`08...` atau `+628...`) | Format: 08123456789 | "Nomor handphone belum lengkap atau format tidak sesuai." |
| Kode Promo | Opsional, alfanumerik 5-10 karakter huruf kapital | Masukkan kode jika ada | "Kode promo tidak ditemukan atau masa berlaku telah habis." |

## 6. Spesifikasi Animasi & Transisi (Motion Specs) [Opsional] *(Boleh dihapus jika tanpa animasi khusus)*

> **Panduan Pengisian:**
> Tentukan durasi gerak dan dukung preferensi aksesibilitas `prefers-reduced-motion`.

- **Modal Popup:** Masuk via `fade-in-up` (Durasi: `200ms`, Easing: `ease-out cubic-bezier(0, 0, 0.2, 1)`).
- **Reduced Motion Support:** Jika sistem pengguna mengaktifkan mode reduced-motion, matikan efek translate gerak dan gunakan transisi instan `opacity: 100%`.
