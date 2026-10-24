# Product Requirements Document (PRD): Situs Web Yayasan Bina Karya

```yaml
document_id: DOC-PRD-002
title: Website Profil & Publikasi Transparansi Yayasan
version: 1.0.0
status: Approved
owner: "Head of Public Relations & Tech Lead"
created_at: 2026-03-12
target_release: 2026-04-15
```

---

## 1. Tujuan & Metrik Keberhasilan (OKRs)

### 1.1 Sasaran Proyek
Membangun situs web modern, berkecepatan tinggi, mudah diakses, dan mandiri secara operasional untuk mendukung transparansi serta perluasan jangkauan program sosial Yayasan Bina Karya Nusantara.

### 1.2 Metrik Kinerja (Target Kuantitatif)
| Indikator Kinerja | Standar Target | Metode Verifikasi |
|---|---|---|
| **Google Lighthouse Performance** | Nilai minimal >= 95 | Audit Google Lighthouse pada Mobile Profile |
| **Aksesibilitas (WCAG 2.1 AA)** | 100% lulus audit otomatis | Automated scan menggunakan axe-core & Lighthouse Accessibility |
| **Total Bobot Halaman Awal** | < 600 KB (termasuk gambar webp) | Network tab Chrome DevTools tanpa cache |
| **Waktu Publikasi Artikel** | < 10 menit dari draft ke live | Uji coba mandiri oleh staf humas non-teknis |
| **SEO Score & Indexing** | Nilai >= 95 | Validasi OpenGraph, sitemap.xml, robots.txt |

---

## 2. Struktur Informasi & Ruang Lingkup Halaman

### 2.1 Halaman Publik (In-Scope MVP)
1. **Beranda (Homepage)**: Hero section dengan video/foto dokumentasi, ringkasan dampak program (jumlah lulusan & penerima beasiswa), teaser kegiatan terkini, dan call-to-action (CTA) donasi.
2. **Profil & Transparansi (/tentang-kami)**: Sejarah, visi misi, susunan dewan pengurus, legalitas izin yayasan, dan arsip dokumen PDF laporan keuangan tahunan yang dapat diunduh.
3. **Program Vokasi (/program)**: Rincian kurikulum pelatihan (Otomotif, Desain Grafis, Pemrograman Dasar), jadwal gelombang pendaftaran, dan syarat calon siswa.
4. **Kabar & Kegiatan (/berita)**: Blog artikel kegiatan sosial, cerita sukses alumni, dan rilis pers resmi. Dilengkapi kategori dan pagination.
5. **Kontak & Donasi (/kontak)**: Lokasi kantor via peta interaktif, formulir kontak publik (terhubung ke email yayasan), nomor rekening resmi, dan QRIS yayasan.

### 2.2 Fitur Pengelolaan Konten (CMS)
- Antarmuka web ramah pengguna untuk membuat, menyunting, dan menghapus artikel berita.
- Optimasi gambar otomatis (konversi ke WebP dan kompresi responsif saat diunggah).
- Validasi metadata SEO (judul halaman, deskripsi meta, featured image).

---

## 3. Persyaratan Fungsional (FR)

| ID | Modul | Kebutuhan Fungsional | Prioritas |
|---|---|---|---|
| `FR-WEB-001` | Navigasi | Navigasi responsif dengan hamburger menu pada layar mobile (< 768px). | Must-Have |
| `FR-WEB-002` | Unduh Laporan | Pengguna dapat mengunduh file PDF laporan keuangan resmi dengan satu klik. | Must-Have |
| `FR-WEB-003` | Formulir Kontak | Pengunjung dapat mengirim pesan melalui form yang dilengkapi proteksi anti-spam (Cloudflare Turnstile). | Must-Have |
| `FR-WEB-004` | Pratinjau CMS | Admin humas dapat melihat pratinjau tampilan artikel secara real-time sebelum dipublikasikan. | Should-Have |
| `FR-WEB-005` | Filter Berita | Pembaca dapat menyaring artikel berdasarkan kategori (Pendidikan, Kegiatan Sosial, Pengumuman). | Should-Have |

---

## 4. Persyaratan Non-Fungsional (NFR)

- **Aksesibilitas**: Kontras rasio teks terhadap latar belakang minimal 4.5:1 untuk teks normal dan 3:1 untuk teks tebal/besar. Seluruh gambar memiliki atribut `alt` deskriptif. Seluruh elemen interaktif dapat dioperasikan penuh menggunakan keyboard (Tab, Enter, Escape).
- **SEO & Social Sharing**: Seluruh halaman menghasilkan meta tags Twitter Card dan OpenGraph secara dinamis, dilengkapi struktur schema markup `Organization` dari Schema.org.
- **Ketersediaan**: Karena berbasis static files di Global CDN, situs memiliki ketersediaan teoritis > 99,99% tanpa risiko server database down.

---

## 5. Dokumen Terkait

- Catatan Discovery: [`discovery.md`](./discovery.md)
- Panduan Deployment & Operasional: [`deployment.md`](./deployment.md)
