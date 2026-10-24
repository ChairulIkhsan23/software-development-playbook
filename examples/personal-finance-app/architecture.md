# Spesifikasi Arsitektur Sistem: DompetKu

```yaml
document_id: DOC-ARCH-001
title: System Architecture & Database Design - DompetKu
version: 1.0.0
status: Approved
author: "Lead Systems Architect"
date: 2026-03-20
related_prd: DOC-PRD-001
```

---

## 1. Ikhtisar Arsitektur

DompetKu menggunakan arsitektur **Modular Monolith** berbasis container pada fase MVP. Pilihan ini diambil untuk meminimalkan beban operasional (*operational overhead*) pada tim pengembang kecil, sembari mempertahankan batasan modul domain yang bersih agar mudah diekstrak menjadi microservices jika skala traffic menuntut di masa mendatang.

---

## 2. Diagram C4 Container

Diagram berikut mengilustrasikan batas sistem dan komponen pendukung:

```mermaid
graph TD
    User([Pengguna Mobile]) -->|HTTPS / TLS 1.3| CDN[Cloudflare CDN & WAF]
    CDN -->|Reverse Proxy| Gateway[API Gateway / Fastify REST API]
    
    subgraph Core Backend Services [Docker Containerized Node.js Environment]
        Gateway --> AuthMod[Auth Module]
        Gateway --> TransMod[Transaction Module]
        Gateway --> BudgetMod[Budget Module]
    end
    
    TransMod -->|Read / Write SQL| DB[(PostgreSQL 16 Primary)]
    AuthMod -->|Read / Write SQL| DB
    BudgetMod -->|Read / Write SQL| DB
    
    Gateway -->|Rate Limiting & Token Blacklist| Redis[(Redis 7 Cache)]
    
    style User fill:#f9f,stroke:#333,stroke-width:2px
    style DB fill:#4a90d9,stroke:#333,stroke-width:2px,color:#fff
    style Redis fill:#e57373,stroke:#333,stroke-width:2px,color:#fff
    style Gateway fill:#7cb342,stroke:#333,stroke-width:2px,color:#fff
```

---

## 3. Keputusan Teknologi (ADR Summary)

### 3.1 Framework Backend: Fastify (Node.js/TypeScript)
- **Keputusan**: Menggunakan Fastify daripada Express.js.
- **Rasional**: Fastify memiliki performa throughput 2x lebih tinggi dibanding Express, dilengkapi schema compilation berbasis Ajv yang terintegrasi secara bawaan, serta dukungan first-class untuk TypeScript dan async/await.

### 3.2 Database Utama: PostgreSQL 16
- **Keputusan**: Menggunakan PostgreSQL 16 Relational Database daripada NoSQL (MongoDB).
- **Rasional**: Data finansial membutuhkan integritas data tingkat tinggi, transaksi ACID, foreign key constraints untuk relasi akun-transaksi, dan dukungan indeks B-Tree majemuk untuk penyaringan tanggal.

### 3.3 Cache & Session Layer: Redis 7
- **Keputusan**: Menggunakan Redis standalone untuk rate limiting dan revocation token JWT.
- **Rasional**: Latensi baca in-memory sub-milidetik memastikan pemeriksaan laju request (rate limit) tidak menambah beban latensi pada request API harian.

---

## 4. Desain Database & Skema Relasional (ERD)

```mermaid
erDiagram
    USERS ||--o{ ACCOUNTS : owns
    USERS ||--o{ CATEGORIES : configures
    USERS ||--o{ BUDGETS : sets
    ACCOUNTS ||--o{ TRANSACTIONS : records
    CATEGORIES ||--o{ TRANSACTIONS : classifies
    CATEGORIES ||--o{ BUDGETS : targets

    USERS {
        uuid id PK
        varchar email UK
        varchar password_hash
        varchar full_name
        timestamp created_at
        timestamp updated_at
    }

    ACCOUNTS {
        uuid id PK
        uuid user_id FK
        varchar name
        varchar type "CASH, BANK, EWALLET"
        numeric current_balance
        timestamp created_at
        timestamp updated_at
    }

    CATEGORIES {
        uuid id PK
        uuid user_id FK "nullable for system default"
        varchar name
        varchar type "EXPENSE, INCOME"
        varchar icon
        varchar color_hex
    }

    TRANSACTIONS {
        uuid id PK
        uuid user_id FK
        uuid account_id FK
        uuid category_id FK
        numeric amount "amount > 0"
        varchar type "EXPENSE, INCOME"
        date transaction_date
        text notes
        timestamp created_at
    }

    BUDGETS {
        uuid id PK
        uuid user_id FK
        uuid category_id FK
        numeric monthly_limit
        varchar month_year "YYYY-MM"
        timestamp created_at
    }
```

---

## 5. Strategi Indexing & Optimasi Query

Untuk menjamin waktu respon `p95 < 200ms` saat tabel transaksi membesar:

1. **`idx_transactions_user_date`**: Indeks majemuk `(user_id, transaction_date DESC, created_at DESC)` pada tabel `TRANSACTIONS`. Mengakselerasi pemanggilan riwayat transaksi berpaginasi.
2. **`idx_transactions_user_category_date`**: Indeks majemuk `(user_id, category_id, transaction_date)` untuk mempercepat perhitungan agregasi pengeluaran per kategori pada fitur anggaran.
3. **`idx_budgets_user_month`**: Indeks unik `(user_id, category_id, month_year)` pada tabel `BUDGETS` untuk mencegah duplikasi batas anggaran pada kategori yang sama di bulan kalender yang sama.

---

## 6. Keamanan & Batas Kepercayaan (Security Architecture)

- **Isolasi Data**: Setiap query transaksi dan akun menyertakan klausa eksplisit `WHERE user_id = :authenticated_user_id` yang diekstrak dari klaim token JWT yang telah diverifikasi di middleware.
- **Enkripsi Kredensial**: Password pengguna di-hash menggunakan algoritma **Argon2id** (memory cost 64MB, time cost 3 iterations, parallelism 1 thread).
- **Rate Limiting**: Diterapkan pada level reverse proxy dan aplikasi (Redis Token Bucket):
  - Endpoint publik / login: Maksimal 5 percobaan per menit per IP address.
  - Endpoint privat / transaksi: Maksimal 120 request per menit per User ID.
- **Audit Logging**: Setiap transaksi mencatat stempel waktu UTC presisi tinggi untuk merekonstruksi mutasi saldo historis jika terjadi rekonsiliasi.
