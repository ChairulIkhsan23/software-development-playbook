# Contoh Proyek: Situs Web Organisasi (Yayasan Bina Karya)

Studi kasus ini mendemonstrasikan penerapan Software Development Playbook untuk proyek berbasis web publik dan pengelolaan konten (*content-driven website*). Berbeda dengan contoh Personal Finance App yang berbasis mobile/API kompleks, proyek ini menunjukkan bahwa aplikasi sederhana tidak memerlukan arsitektur microservices atau database relasional berskala besar.

---

## 1. Ringkasan Proyek

| Informasi | Keterangan |
|---|---|
| **Nama Organisasi** | Yayasan Bina Karya Nusantara (Lembaga Nirlaba Pelatihan Vokasi) |
| **Tipe Proyek** | Website Publik Statis & Dinamis (SSG/ISR) + Headless CMS |
| **Target Pengguna** | Calon donatur, calon peserta pelatihan vokasi, mitra industri, relawan |
| **Tujuan Utama** | Meningkatkan kredibilitas organisasi, mempublikasikan transparansi laporan keuangan/kegiatan, dan memfasilitasi formulir pendaftaran relawan serta informasi rekening donasi. |
| **Arsitektur Stack** | Next.js (Static Site Generation dengan Incremental Static Regeneration) + Decap CMS / Strapi + Cloudflare Pages & CDN |

---

## 2. Asumsi Baseline Proyek

1. **Minim Operasional**: Organisasi tidak memiliki divisi DevOps internal. Seluruh infrastruktur harus bersifat *serverless* atau *managed PaaS* dengan biaya operasional bulanan seminimal mungkin (menargetkan tier gratis/open-source).
2. **Kemandirian Editorial**: Tim humas/redaksi yayasan dapat menerbitkan artikel kegiatan baru tanpa ketergantungan pada tim teknis / developer.
3. **Inklusivitas & Aksesibilitas**: Website harus memenuhi standar WCAG 2.1 Level AA agar dapat diakses dengan mudah oleh pengguna dengan perangkat lama dan koneksi internet terbatas di pelosok daerah.
4. **Keamanan Tanpa Database Publik**: Konten artikel disimpan dalam repositori Git atau headless CMS terpisah. Tidak ada database SQL publik yang rentan serangan SQL injection.

---

## 3. Dokumen dalam Studi Kasus Ini

- [`discovery.md`](./discovery.md): Catatan penemuan masalah, analisis pemangku kepentingan (stakeholder), persona pengguna, dan kriteria kelayakan proyek.
- [`prd.md`](./prd.md): Product Requirements Document untuk website organisasi mencakup kebutuhan konten, SEO, aksesibilitas, dan metrik Lighthouse.
- [`deployment.md`](./deployment.md): Panduan build, integrasi CI/CD, deployment ke CDN edge, penataan domain, dan prosedur pemeliharaan berkala.
