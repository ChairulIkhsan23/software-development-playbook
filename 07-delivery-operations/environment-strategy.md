# Environment Strategy Guide

Panduan standarisasi tingkatan lingkungan (*environment tiering*), pengelolaan data, dan paritas infrastruktur untuk pengembang solo, tim kecil, dan junior engineer.

---

## 1. Environment Tiering Overview

Pemisahan *environment* mencegah bug pengujian merusak data produksi dan menjaga siklus rilis tetap terprediksi.

| Tier | Tujuan Utama | Akses Pengguna | Karakteristik Data | Infrastruktur |
|---|---|---|---|---|
| **Local** | Pengembangan fitur harian & unit test | Developer individual | Mock / Seed sintetis minimal | Laptop dev, Docker Compose |
| **Development (Dev)** | Integrasi merge PR harian | Internal dev team | Mock / Fixture terpusat | Cloud spec rendah / single node |
| **Staging / UAT** | Uji pra-rilis, verifikasi PO / QA | Dev, QA, Client / PO | Data anonim / sintetis skala penuh | Paritas identik dengan Production |
| **Production (Prod)** | Melayani end-user aktif | End-user (Dev dilarang write langsung) | Data riil (live customer data) | Multi-AZ, High Availability, Auto-scale |

---

## 2. Karakteristik Detail Setiap Tier

### Local Environment
- **Fokus:** Kecepatan *feedback loop* developer (`< 5 detik` reload/test).
- **Setup:** Gunakan `docker-compose.yml` untuk dependency lokal (database, Redis, local S3/MinIO).
- **Konfigurasi:** File `.env.local` (tidak di-commit ke Git; sediakan `.env.example`).
- **Data:** Database seed script dengan 10–50 record dummy.

### Development Environment (Dev)
- **Fokus:** Integrasi branch `dev` atau `develop`.
- **Deployment:** Otomatis via CI/CD setiap ada PR merge ke branch integrasi.
- **Toleransi Downtime:** Sedang. Boleh restart kapan saja saat deployment.

### Staging / UAT Environment
- **Fokus:** Validasi rilis final sebelum dikirim ke pengguna riil.
- **Prinsip Kunci:** Meniru arsitektur produksi (database engine, versi OS, caching layer, reverse proxy).
- **Akses:** Restricted VPN atau allowlist IP; lindungi dengan Basic Auth jika terekspos publik.

### Production Environment (Prod)
- **Fokus:** Stabilitas, keamanan, ketersediaan (*uptime 99.9%+*).
- **Akses:** Prinsip *least privilege* (RBAC). Developer tidak memiliki akses direct write ke database live.
- **Konfigurasi:** Injeksi environment variable via secret manager (AWS Secrets Manager, Vault, Doppler).

---

## 3. Environment Parity (Twelve-Factor App)

Prinsip ke-10 dari Twelve-Factor App menyatakan: **"Jaga development, staging, dan production semirip mungkin."**

Tiga celah paritas yang harus dihindari:
1. **Time gap:** Fitur ditulis hari ini tapi baru masuk staging 3 minggu kemudian. Solusi: deploy harian atau continuous delivery.
2. **Personnel gap:** Developer menulis kode, tim Ops terpisah yang mendeploy. Solusi: developer mengamati pipeline deployment sendiri hingga selesai.
3. **Tools gap:** Local menggunakan SQLite, Staging menggunakan PostgreSQL 14, Prod menggunakan PostgreSQL 16. Solusi: samakan database engine dan minor version di semua tier via Docker.

```
+-----------------------------------------------------------+
|                     Paritas Konfigurasi                   |
|  Local (Docker PG 16) -> Staging (PG 16) -> Prod (PG 16)  |
|                                                           |
|             Kode & Image Container Sama                   |
|       [Artifact Hash: sha256:abc123def456...]             |
+-----------------------------------------------------------+
```

---

## 4. Pengelolaan Data Antar-Tier

**Aturan Mutlak:** Jangan pernah meng-copy database produksi langsung ke Local atau Staging tanpa proses sanitasi data. Pelanggaran aturan ini melanggar regulasi privasi (GDPR, PDP Indonesia).

### Praktik Penanganan Data:
- **Local:** Buat seed data generatif menggunakan factory library (Faker, FactoryBoy, Prisma seed).
- **Staging:**
  - Gunakan **data sintetis** berskala setara prod untuk uji performa.
  - Jika wajib me-restore backup prod ke staging: jalankan script anonimisasi otomatis (masking nama, hapus email riil, acak password hash, enkripsi NIK/nomor kartu kredit).
- **Production:** Data riil hanya boleh dibaca lewat aplikasi berotoritas atau logging terenkripsi.

---

## 5. Infrastructure as Code (IaC) Dasar

Hindari konfigurasi server secara manual lewat dashboard GUI (*ClickOps*). Perubahan manual menciptakan *configuration drift*.

Gunakan IaC (Terraform, Pulumi, OpenTofu, atau Ansible) untuk menjamin reproduktibilitas:
- Definisikan VPC, subnet, database instance, dan cluster dalam modul kode.
- Gunakan *workspace* atau folder terpisah untuk `staging` dan `prod` dengan modul yang dapat digunakan ulang (*reusable modules*).
- Simpan state file di remote backend aman (S3 bucket dengan server-side encryption dan DynamoDB lock table).

```hcl
# Contoh struktur deklaratif Terraform
module "app_cluster" {
  source      = "./modules/ecs_cluster"
  environment = var.environment # "staging" atau "production"
  node_count  = var.node_count  # staging: 1, prod: 3
}
```

---

## 6. Ephemeral / Preview Environments

Ephemeral environments adalah lingkungan sementara yang dibuat otomatis per *Pull Request* dan dihancurkan setelah PR di-merge atau di-close.

### Manfaat:
- Review visual langsung oleh QA/PO tanpa menunggu slot staging kosong.
- Isolasi total antar pengembang; tidak ada antrean atau tabrakan pengujian (*test collision*).

### Implementasi Minimal:
- Gunakan platform seperti Vercel, Netlify, Cloudflare Pages untuk frontend.
- Gunakan Kubernetes namespace dinamis atau lightweight runner (Fly.io, Railway, AWS ECS task) untuk backend preview.
- Berikan label TTL (Time-To-Live, misal: 4 jam) untuk membersihkan container yatim (*orphaned resources*).

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Hardcoded URLs/Credentials di Kode:** Menyimpan `http://localhost:3000` atau token staging di source code alih-alih membaca environment variables.
2. **Configuration Drift:** Memodifikasi konfigurasi staging/prod langsung via SSH terminal tanpa mencatatnya di IaC atau Git repository.
3. **Menggunakan Engine Berbeda Antar Tier:** Menggunakan SQLite di laptop lokal tetapi PostgreSQL di produksi, memicu bug dialek query runtime.
4. **Staging Berisi Data Produksi Telanjang:** Membuka risiko kebocoran data sensitif customer ke laptop penguji atau staging log.
5. **Over-engineering di Awal:** Membangun multi-cluster Kubernetes untuk tim beranggotakan 2 developer padahal single PaaS (Heroku, Render, AWS App Runner) sudah mencukupi.

---

## 8. Dokumen Terkait

- [CI/CD Pipelines](ci-cd.md)
- [Deployment Guide](deployment-guide.md)
- [Release Management](release-management.md)
- [Database Migration and Rollback](database-migration-and-rollback.md)
- [Backup and Recovery](backup-and-recovery.md)
