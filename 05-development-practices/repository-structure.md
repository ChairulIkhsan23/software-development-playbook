# Repository Structure Standard

Panduan standar penataan repositori kode untuk solo developer dan tim kecil agar basis kode tetap modular, mudah dinavigasi, dan siap diskalakan seiring pertumbuhan tim.

---

## 1. Monorepo vs Polyrepo (Multi-repo)

Pemilihan arsitektur repositori harus didasarkan pada skala tim, dependensi antar-layanan, dan kompleksitas alur deployment.

| Kriteria | Monorepo | Polyrepo (Multi-repo) |
| :--- | :--- | :--- |
| **Definisi** | Satu repositori Git yang menaungi beberapa aplikasi dan paket terkait. | Setiap service, frontend, atau modul pustaka berada di repositori terpisah. |
| **Kelebihan** | Refactoring lintas service mudah, atomic cross-service PR, dependensi bersama konsisten. | Hak akses granular, batas tanggung jawab tegas, CI/CD pipeline cepat dan terisolasi. |
| **Kekurangan** | Tooling build kompleks (butuh Turborepo/Nx), ukuran clone besar, konfigurasi cache CI rumit. | Sinkronisasi breaking changes sulit, duplikasi boilerplate config, overhead koordinasi. |
| **Tooling Populer** | Turborepo, Nx, pnpm workspaces, Cargo workspaces. | Git submodules, package registry privat (npm/GitHub Packages). |
| **Rekomendasi Tim** | Cocok untuk tim 1-8 orang yang mengelola 1 frontend web + 1 backend API + 1 paket shared types. | Cocok jika repositori dikelola tim berbeda dengan siklus rilis dan SLA yang independen. |

---

## 2. Pola Pengorganisasian Folder

### A. Layer-Based vs Feature-Based

- **Layer-Based (Arsitektur Teknis):** Mengelompokkan kode berdasarkan fungsi teknisnya (`controllers/`, `services/`, `models/`, `components/`).
  - *Kelemahan:* Untuk mengubah 1 alur bisnis, developer harus melompat ke 5 folder berbeda.
- **Feature-Based (Arsitektur Domain):** Mengelompokkan kode berdasarkan kapabilitas fungsional bisnis (`features/auth/`, `features/billing/`).
  - *Kelebihan:* Cohesion tinggi, batas domain jelas, mudah dihapus atau diekstrak menjadi microservice terpisah.

### B. Contoh Struktur: Node.js / TypeScript (Feature-Based Full-Stack)

```text
my-project/
├── .github/                  # CI/CD workflows, issue templates, PR template
├── docs/                     # Arsitektur sistem, ADR, API specs
├── scripts/                  # Script automasi seed, migrasi batch, CI runner
├── src/
│   ├── config/               # Validasi runtime environment variable (Zod)
│   ├── features/             # Modul domain fungsional
│   │   ├── auth/
│   │   │   ├── components/   # UI komponen khusus auth
│   │   │   ├── auth.service.ts
│   │   │   ├── auth.handler.ts
│   │   │   ├── auth.schema.ts
│   │   │   └── auth.service.test.ts
│   │   └── billing/
│   │       ├── billing.service.ts
│   │       └── billing.handler.ts
│   ├── shared/               # Kode yang dibagi lintas modul
│   │   ├── components/       # UI primitive reusable (Button, Modal, Input)
│   │   ├── middleware/       # Auth guard, rate limiter, logger middleware
│   │   ├── types/            # Contract global dan type definitions
│   │   └── utils/            # Helper fungsi murni (string, date, format)
│   └── main.ts               # Titik masuk utama aplikasi (entry point)
├── tests/                    # E2E test suites lintas fitur
├── .env.example              # Template variabel lingkungan publik
├── .gitignore                # Daftar file yang diabaikan Git
├── Dockerfile                # Kontainerisasi aplikasi utama
├── compose.yaml              # Service lokal (PostgreSQL, Redis)
├── Makefile                  # Task runner perintah standar
├── package.json
├── README.md                 # Dokumentasi orientasi proyek
└── tsconfig.json
```

### C. Contoh Struktur: Go (Standard Service Layout)

```text
my-go-service/
├── cmd/
│   └── api/
│       └── main.go           # Entrypoint binary API server
├── internal/                 # Kode privat tidak dapat di-import modul eksternal
│   ├── platform/             # Driver database, cache client, logger wrapper
│   │   ├── database/
│   │   └── cache/
│   └── user/                 # Domain package user
│       ├── handler.go        # HTTP transport handler
│       ├── service.go        # Business logic domain
│       ├── repository.go     # SQL query / persistence layer
│       └── entity.go         # Struct data domain
├── pkg/                      # Modul publik yang aman dikonsumsi repo lain
│   └── validator/
├── api/                      # Kontrak OpenAPI/Swagger, file proto gRPC
├── migrations/               # File migrasi SQL terurut
├── Makefile
├── go.mod
└── Dockerfile
```

### D. Contoh Struktur: Python (FastAPI / Modular Service)

```text
my-python-service/
├── app/
│   ├── core/                 # Settings, security, database session
│   │   ├── config.py
│   │   └── database.py
│   ├── domains/              # Feature modules
│   │   └── billing/
│   │       ├── router.py     # Endpoint routing
│   │       ├── schemas.py    # Pydantic models
│   │       ├── service.py    # Business logic
│   │       └── models.py     # SQLAlchemy ORM models
│   ├── shared/               # Cross-cutting concerns, helpers
│   └── main.py               # FastAPI app declaration
├── tests/                    # Pytest suites
├── pyproject.toml            # Poetry / uv dependency manifest
└── Dockerfile
```

---

## 3. Standar File Root (Top-Level Files)

Setiap repositori wajib memiliki file dasar berikut pada level root:

1. **`README.md`**: Pintu gerbang proyek. Memuat deskripsi singkat, prasyarat sistem, perintah *quickstart* (< 3 langkah), dan daftar penanggung jawab.
2. **`.gitignore`**: Mengabaikan folder build (`dist/`, `build/`, `.next/`), dependensi lokal (`node_modules/`, `.venv/`), log, dan file kredensial (`.env`, `.env.local`).
3. **`.env.example`**: Template variabel lingkungan dengan nilai dummy yang aman dan komentar instruksi yang jelas.
4. **`CONTRIBUTING.md`**: Standar kontribusi, aturan branch, format pesan commit, dan checklist sebelum PR diajukan.
5. **`LICENSE`**: Penjelasan lisensi legal repositori (Proprietary, MIT, Apache 2.0).
6. **`Dockerfile` & `compose.yaml`**: Standarisasi eksekusi aplikasi dan dependensi runtime lokal (database, caching engine).
7. **`Makefile`**: Kumpulan alias task runner (`make setup`, `make test`, `make dev`, `make build`) agar instruksi seragam lintas developer.

---

## 4. Penempatan Configs, Scripts, Tests, dan Docs

- **Configs (`/config` atau root):** Konfigurasi tooling dev (`eslint.config.js`, `.prettierrc`, `tsconfig.json`) berada di root. Konfigurasi runtime yang membaca env vars divalidasi di dalam `src/config/`.
- **Scripts (`/scripts`):** Skrip automasi ad-hoc, database seeding massal, migrasi data lama, atau utility CI deployment.
- **Tests (Colocated vs `/tests`):**
  - *Colocated Tests:* Unit test diletakkan bersebelahan langsung dengan file sumber yang diuji (misal: `auth.service.test.ts` di samping `auth.service.ts`). Ini memudahkan pemeliharaan dan mencegah test tertinggal saat refactoring.
  - *Integration & E2E Tests:* Diletakkan di direktori `/tests` level teratas karena menguji interaksi alur kerja lintas banyak modul.
- **Docs (`/docs`):** Arsitektur sistem, diagram data flow, schema ERD, dan catatan keputusan teknis (*Architecture Decision Records* / ADR).

---

## 5. Strategi Skalabilitas Repositori

```text
Solo Developer                  Tim Kecil (2-6 Dev)             Tim Tumbuh (7-15+ Dev)
Modular Monolith           ->   Feature-Based Folders      ->   Workspaces / Multi-package
Layer sederhana                 Strict linting & hooks          CODEOWNERS & automated release
Minimal abstraction             Clear boundaries                Isolated deployment pipelines
```

1. **Fase Solo Dev:** Bangun modular monolith yang bersih. Utamakan kecepatan iterasi tanpa terbebani overhead microservices.
2. **Fase Tim Kecil:** Adopsi struktur **Feature-Based**. Pisahkan domain secara tegas untuk meminimalkan potensi konflik merge antar-developer. Pasang pre-commit hooks untuk standarisasi format.
3. **Fase Tim Tumbuh:** Terapkan workspace terpusat (seperti pnpm workspace atau Nx) jika ada kebutuhan berbagi paket komponen antarmuka atau kontrak data. Gunakan file `CODEOWNERS` untuk mengatur approval otomatis berdasarkan domain modul.

---

## 6. Anti-Patterns Struktur Repositori

- **The Kitchen Sink (`utils/` atau `helpers/` Raksasa):** Meletakkan ribuan baris kode utilitas acak tanpa batas domain yang jelas. *Solusi:* Buat utility sedekat mungkin dengan fitur yang memakainya.
- **Deep Nesting Hell:** Struktur folder lebih dari 4 level (`src/features/billing/components/cards/header/icons/svg/...`). Sulit dibaca dan dinavigasi di text editor.
- **Circular Imports:** Dua modul saling mengimpor dependensi satu sama lain secara bolak-balik. *Solusi:* Ekstrak kontrak data atau interface bersama ke direktori `shared/types`.
- **Committing Artifacts & Secrets:** Memasukkan folder build (`dist/`), file log, temporary cache, atau file `.env` ke riwayat Git.
- **Orphan Config Files:** Menaruh lusinan skrip konfigurasi ad-hoc di direktori root tanpa pengelompokan yang jelas.

---

## 7. Dokumen Terkait

- [Coding Standards](coding-standards.md)
- [Branching and Commits](branching-and-commits.md)
- [Environment Configuration](environment-configuration.md)
- [Developer Onboarding](developer-onboarding.md)
