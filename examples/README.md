# Studi Kasus & Contoh Proyek Nyata

Folder ini berisi contoh implementasi dokumentasi nyata dari Software Development Playbook. Contoh-contoh di sini bukan sekadar teori abstrak, melainkan berkas kerja konkret yang saling terhubung dan mendemonstrasikan bagaimana panduan serta template dalam playbook diterapkan pada dua skala proyek yang berbeda.

---

## 1. Perbandingan Dua Studi Kasus

| Aspek | Studi Kasus 1: [Personal Finance App](./personal-finance-app/) | Studi Kasus 2: [Organization Website](./organization-website/) |
|---|---|---|
| **Nama Proyek** | DompetKu | Yayasan Bina Karya Nusantara |
| **Domain Masalah** | FinTech Pribadi (Pencatatan Keuangan Harian) | Non-Profit / Lembaga Pelatihan Vokasi |
| **Tipe Sistem** | Mobile Application (Cross-Platform) + RESTful API | Web Statis & Dinamis Berbasis CMS (Jamstack) |
| **Kompleksitas** | Tinggi (Autentikasi JWT, Transaksi ACID, Indeks Relasional) | Rendah–Sedang (Aset Statis, Tanpa Database Relasional Publik) |
| **Arsitektur** | Modular Monolith Containerized (Fastify + PostgreSQL + Redis) | Static Site Generation (Next.js SSG + Headless CMS + Global CDN) |
| **Kebutuhan Kunci** | Integritas data, latensi API p95 < 200ms, isolasi data pengguna | Aksesibilitas WCAG 2.1 AA, Lighthouse > 95, SEO, biaya minim |
| **Fokus Dokumen** | PRD lengkap, skema ERD, kontrak REST API terinci | Discovery stakeholder, PRD konten web, panduan deployment edge |

---

## 2. Navigasi Dokumen Contoh

### Studi Kasus 1: Personal Finance App (DompetKu)
- [`personal-finance-app/README.md`](./personal-finance-app/README.md) — Ikhtisar proyek, baseline asumsi, dan matriks traceability.
- [`personal-finance-app/prd.md`](./personal-finance-app/prd.md) — Product Requirements Document lengkap dengan persona, FR, BR, dan AC.
- [`personal-finance-app/architecture.md`](./personal-finance-app/architecture.md) — Arsitektur teknis C4, keputusan ADR, dan skema relasional ERD.
- [`personal-finance-app/api-spec.md`](./personal-finance-app/api-spec.md) — Kontrak endpoint REST API lengkap dengan payload JSON dan format error RFC 7807.

### Studi Kasus 2: Organization Website (Yayasan Bina Karya)
- [`organization-website/README.md`](./organization-website/README.md) — Konteks yayasan, sasaran publik, dan baseline arsitektur Jamstack.
- [`organization-website/discovery.md`](./organization-website/discovery.md) — Wawancara stakeholder, identifikasi kendala, dan analisis kelayakan.
- [`organization-website/prd.md`](./organization-website/prd.md) — PRD ringkas berorientasi konten, SEO, dan aksesibilitas ramah pengguna.
- [`organization-website/deployment.md`](./organization-website/deployment.md) — Panduan integrasi CI/CD, deployment edge CDN, dan jadwal pemeliharaan rutin.

---

## 3. Prinsip Penting Penggunaan Contoh

1. **Konsistensi Identitas**: Kode entitas dan ID requirement (seperti `FR-TRANS-001`) konsisten dipertahankan maknanya mulai dari PRD hingga spesifikasi endpoint API.
2. **Kesesuaian Skala**: Jangan memaksakan seluruh kerumitan arsitektur Personal Finance App ke situs sederhana organisasi. Terapkan prinsip YAGNI (You Aren't Gonna Need It) dan pilihlah pendekatan yang paling proporsional.
3. **Data Fiktif Aman**: Seluruh data yang ditampilkan dalam contoh bersifat fiktif dan aman dari paparan informasi sensitif.
