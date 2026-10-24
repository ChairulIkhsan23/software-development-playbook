# Developer Onboarding Guide

Panduan orientasi pengembang baru untuk memastikan proses setup lingkungan lokal berjalan mulus, cepat, dan mandiri dengan target sistem berjalan kurang dari 2 jam.

---

## 1. Target Hari Pertama (Day-1 Success Metric)

Tolak ukur keberhasilan onboarding hari pertama bagi setiap engineer baru:
- **Target Waktu: < 2 Jam:** Dari pertama kali menerima akses repositori hingga aplikasi utama berhasil dijalankan di mesin lokal dan seluruh test suite lulus 100%.
- **First PR pada Hari ke-2 atau ke-3:** Developer baru diharapkan sudah dapat menyelesaikan satu tiket perbaikan minor (*good first issue*) dan mengajukan Pull Request pertama mereka pada minggu pertama.

---

## 2. Milestone Minggu Pertama (Week 1 Milestones)

| Hari | Target Capaian | Hasil yang Diharapkan (*Deliverables*) |
| :--- | :--- | :--- |
| **Day 1** | Setup lingkungan lokal & verifikasi aplikasi berjalan. | Berhasil login di `localhost:3000` dengan data seed, test lulus 100%. |
| **Day 2** | Pemahaman domain bisnis & codebase walkthrough bersama Buddy. | Menyelesaikan sesi walk-through arsitektur dan memilih tiket pertama. |
| **Day 3** | Pengerjaan tiket minor (*good-first-issue*). | Membuat feature branch, menulis unit test, dan membuka PR pertama. |
| **Day 4** | Siklus code review & perbaikan feedback. | Merespon feedback review, lulus checks CI, dan merge PR pertama ke `main`. |
| **Day 5** | Retrospeksi onboarding & feedback loop. | Mengisi form feedback onboarding dan memperbaiki dokumentasi yang kurang jelas. |

---

## 3. Checklist Akses Akun & Tooling (Access Request Checklist)

Sebelum hari pertama dimulai, manajer tim atau onboarding buddy wajib memastikan akses berikut sudah aktif:

| Platform | Kebutuhan Akses | Penanggung Jawab |
| :--- | :--- | :--- |
| **GitHub / GitLab** | Anggota tim repositori (`Write` access) | Engineering Lead |
| **Linear / Jira** | Anggota project board tim | Scrum Master / Lead |
| **Slack / Discord** | Akun kerja & invite ke channel engineering | IT Support |
| **Cloud Staging Console** | Read-only access untuk debugging log staging | DevOps / SRE |
| **Sentry / Error Tracker** | Akses project monitoring aplikasi | Tech Lead |
| **Figma** | Viewer access ke UI Design System | Product Designer |

---

## 4. Checklist Setup Hari Pertama (Day-1 Checklist)

Ikuti langkah-langkah terurut berikut untuk menyiapkan lingkungan kerja Anda:

### Langkah 1: Pasang Prasyarat Sistem (Prerequisites)
Pastikan software dasar berikut terpasang dengan versi yang sesuai:
- **Git** (versi >= 2.38)
- **Node.js LTS** (via `nvm` atau `fnm`, gunakan versi sesuai `.nvmrc`)
- **Package Manager:** `pnpm` (pasang via `corepack enable`)
- **Docker Desktop / OrbStack / Rancher Desktop** (wajib aktif untuk database lokal)
- **Make** (Linux/macOS bawaan, Windows gunakan WSL2 atau `choco install make`)

### Langkah 2: Kloning Repositori
```bash
git clone git@github.com:my-org/my-project.git
cd my-project
```

### Langkah 3: Konfigurasi Variabel Lingkungan
Salin template konfigurasi lokal dan pastikan nilai default sudah siap pakai:
```bash
cp .env.example .env.local
```

### Langkah 4: Jalankan Service Dependensi Lokal (Docker Compose)
Nyalakan database Postgres, Redis, atau service mock lokal:
```bash
docker compose up -d
```

### Langkah 5: Instalasi Dependensi & Migrasi Database
Gunakan lockfile resmi dan jalankan migrasi beserta data awal (*seed data*):
```bash
pnpm install --frozen-lockfile
pnpm db:migrate
pnpm db:seed
```

### Langkah 6: Verifikasi Kesiapan Test Suite
Jalankan unit test untuk memastikan integritas environment lokal:
```bash
pnpm test
```

### Langkah 7: Jalankan Aplikasi
Nyalakan server development lokal:
```bash
pnpm dev
```
Buka browser di `http://localhost:3000` dan login menggunakan akun uji coba dari database seed:
- **Email:** `developer@example.com`
- **Password:** `password123`

---

## 5. Dokumentasi Wajib untuk Onboarding

Tim wajib menjaga ketersediaan 3 dokumen kunci berikut agar developer baru tidak bingung:

1. **`README.md` Terkini:**
   - Deskripsi satu paragraf tentang apa fungsi repositori ini.
   - Quickstart commands (3 langkah: install, migrate, dev).
   - Port default yang digunakan (`3000` frontend, `8080` backend, `5432` postgres).
2. **Architecture Overview (`docs/architecture.md`):**
   - Diagram arsitektur sistem level tinggi (C4 model / flowchart).
   - Interaksi antar-komponen (database, cache, payment gateway, background queue).
3. **Onboarding Guide Spesifik Proyek:**
   - Berisi daftar hak akses yang perlu diminta (GitHub Team, Cloud Console, Jira/Linear, Figma, Sentry).

---

## 6. Standardisasi Lingkungan: Docker Compose & Devcontainers

Hilangkan friksi beda sistem operasi (macOS vs Windows vs Linux) dengan standardisasi infrastruktur:
- **`compose.yaml` (Docker Compose):** Menjamin service database, cache Redis, dan message broker berjalan dengan konfigurasi identik di semua laptop tim.
- **VS Code Dev Containers (`.devcontainer/`):**
  - Menyediakan file `devcontainer.json` dan Dockerfile terisolasi.
  - Saat developer baru membuka project di VS Code, container langsung otomatis memasang ekstensi linter, formatter, runtime compiler, dan CLI tools tanpa perlu instalasi manual di OS induk.

---

## 7. Pertanyaan Umum & Troubleshooting FAQ

Berikut kendala yang paling sering dihadapi developer baru saat setup lokal:

- **Error: `Port 5432 already in use`**
  - *Penyebab:* Ada instance PostgreSQL lokal lain yang sedang berjalan di OS host.
  - *Solusi:* Hentikan service PostgreSQL lokal (`sudo service postgresql stop` atau matikan via Services di Windows) sebelum menyalakan Docker Compose.
- **Error: `Database migration connection refused`**
  - *Penyebab:* Container database masih dalam status inisialisasi boot.
  - *Solusi:* Tunggu 10-15 detik hingga log database siap menerima koneksi, lalu jalankan kembali `pnpm db:migrate`.
- **Error: `Node version mismatch`**
  - *Penyebab:* Versi Node.js di sistem host berbeda dari spesifikasi tim.
  - *Solusi:* Jalankan `nvm use` di root folder proyek untuk menyelaraskan versi Node secara otomatis.

---

## 8. Sistem Mentorship & Buddy Minggu Pertama

Jangan biarkan engineer baru merasa tersesat atau terisolasi:
- **Penunjukan Buddy Resmi:** Setiap developer baru dipasangkan dengan 1 senior/peer engineer sebagai *Onboarding Buddy*.
- **Daily 1-on-1 Check-in (15 Menit):** Buddy mengadakan obrolan singkat harian di 5 hari pertama untuk menanyakan kendala teknis dan memvalidasi progres.
- **Pair Programming:** Alokasikan minimal 2 jam sesi pair programming di minggu pertama untuk menunjukkan workflow tim: mulai dari membuat branch, commit, running test, hingga mengajukan PR.

---

## 9. Loop Evaluasi & Peningkatan Dokumen Onboarding

Dokumentasi onboarding adalah dokumen hidup (*living document*):
- **Aturan Developer Baru:** Jika seorang developer baru menemukan langkah instalasi yang error atau dokumentasi yang usang, **tugas pertamanya adalah memperbaiki dokumentasi tersebut.**
- **Retrospeksi Hari ke-14:** Setelah 2 minggu bergabung, minta developer baru mengisi formulir feedback singkat: bagian apa yang membingungkan, berapa lama waktu setup yang dihabiskan, dan informasi apa yang kurang.

---

## 10. Kesalahan Umum dalam Onboarding

1. **Dokumentasi Usang yang Menyesatkan:** Membiarkan `README.md` tidak terurus selama berbulan-bulan dengan perintah instalasi yang sudah tidak berfungsi.
2. **Akses Diberikan Sepotong-sepotong:** Developer baru harus menunggu berhari-hari hanya untuk mendapatkan akses ke repositori atau tiket board.
3. **Tanpa Seed Data Lokal:** Developer dipaksa memasukkan ratusan baris data transaksi manual hanya untuk menguji satu tombol checkout.
4. **Pendekatan "Sink or Swim":** Melepaskan developer baru tanpa bimbingan dan mengharapkan mereka memahami arsitektur kompleks secara mandiri.
5. **Setup Manual Lebih dari 20 Langkah:** Mengharuskan instalasi puluhan tool CLI manual tanpa adanya Docker Compose atau Makefile pembantu.

---

## 11. Dokumen & Template Terkait

- [Template Developer Guide](../templates/development/developer-guide-template.md)
- [Repository Structure](repository-structure.md)
- [Environment Configuration](environment-configuration.md)
- [Dependency Management](dependency-management.md)
- [Coding Standards](coding-standards.md)
