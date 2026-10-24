# Architecture Decision Records (ADR)

## 1. Apa Itu ADR?

Architecture Decision Record (ADR) adalah dokumen pendek yang merekam **satu keputusan arsitektural** beserta konteks dan alasannya. ADR menjawab pertanyaan: *"Mengapa kita memilih X dan bukan Y?"*

ADR bukan dokumen desain yang panjang — satu ADR fokus pada satu keputusan saja.

---

## 2. Mengapa Merekam Keputusan?

- **Developer baru** bisa memahami alasan di balik desain tanpa harus bertanya ke developer senior
- **Mencegah perdebatan berulang** — keputusan yang sudah diambil dan alasannya tercatat jelas
- **Konteks historis** — 6 bulan dari sekarang, kamu sendiri lupa kenapa memilih PostgreSQL daripada MongoDB
- **Accountability** — jelas siapa yang memutuskan, kapan, dan berdasarkan informasi apa
- **Evolusi yang terkontrol** — saat konteks berubah, ADR bisa di-supersede dengan keputusan baru yang mereferensi ADR lama

---

## 3. Format Standar ADR

> 📄 Template lengkap: [ADR Template](../templates/design/adr-template.md)

### Struktur ADR

```markdown
# ADR-NNN: [Judul Keputusan]

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-XXX]

## Context
Masalah atau kebutuhan yang dihadapi. Jelaskan situasi bisnis dan teknis
yang memicu keputusan ini.

## Decision Drivers
- Faktor-faktor utama yang mempengaruhi keputusan
- Constraints yang harus dipenuhi
- Prioritas tim (performa? kecepatan development? cost?)

## Decision
Keputusan yang diambil dan alasan singkatnya.

## Alternatives Considered

### Alternatif A: [Nama]
- **Kelebihan:** ...
- **Kelemahan:** ...
- **Alasan ditolak:** ...

### Alternatif B: [Nama]
- **Kelebihan:** ...
- **Kelemahan:** ...
- **Alasan ditolak:** ...

## Consequences

### Positif
- Dampak baik dari keputusan ini

### Negatif
- Trade-off yang diterima
- Limitasi yang diketahui

### Risiko
- Risiko yang mungkin muncul dan mitigasinya

## Security Implications
Dampak terhadap keamanan sistem (jika ada).

## Operational Implications
Dampak terhadap deployment, monitoring, dan operasional (jika ada).

## Maintenance Implications
Dampak terhadap maintenance jangka panjang (jika ada).

## Related Decisions
- ADR-XXX: [Keputusan terkait]
```

---

## 4. Status Lifecycle

```
Proposed → Accepted → (berjalan) → Deprecated
                                  → Superseded by ADR-XXX
```

| Status | Artinya |
|---|---|
| **Proposed** | ADR dibuat, belum disetujui tim. Masih terbuka untuk diskusi |
| **Accepted** | Tim setuju, keputusan berlaku efektif |
| **Deprecated** | Keputusan tidak lagi relevan (misalnya fitur dihapus) |
| **Superseded** | Digantikan oleh ADR baru. Cantumkan nomor ADR pengganti |

> **Penting:** ADR yang sudah Accepted tidak diedit. Jika konteks berubah dan keputusan perlu diubah, buat ADR baru yang men-supersede ADR lama.

---

## 5. Penomoran dan Penyimpanan

### Penomoran

Gunakan format `ADR-NNN` dengan nomor berurutan:
- `ADR-001`, `ADR-002`, `ADR-003`, ...

Nomor tidak pernah di-reuse, bahkan jika ADR di-deprecated.

### Lokasi Penyimpanan

```
project-root/
├── docs/
│   └── adr/
│       ├── ADR-001-adopsi-postgresql.md
│       ├── ADR-002-pilihan-framework-backend.md
│       ├── ADR-003-strategi-authentication.md
│       └── README.md          ← index daftar semua ADR
```

**Kenapa di repository, bukan di Confluence/Notion?**
- ADR di-review bersama kode (pull request)
- Version control otomatis
- Tidak terpisah dari kode yang dipengaruhinya
- Bisa di-link dari kode (komentar: `// See ADR-003`)

---

## 6. Proses Review ADR

1. **Author** membuat ADR dengan status `Proposed` di branch baru
2. **Pull Request** dibuat — tim review ADR seperti review kode
3. **Diskusi** di PR: tim bisa suggest alternatif atau mempertanyakan asumsi
4. **Consensus** tercapai → status diubah ke `Accepted`, PR di-merge
5. **Implementasi** dilakukan sesuai keputusan ADR

Untuk tim kecil (1-3 orang), proses bisa disederhanakan — tulis ADR langsung di `main` branch setelah diskusi verbal, tapi tetap **tulis**.

---

## 7. Lightweight ADR untuk Tim Kecil / Solo Dev

Untuk solo developer atau tim sangat kecil, format lengkap bisa terasa berlebihan. Gunakan versi ringkas:

```markdown
# ADR-001: Pilih PostgreSQL untuk Database

**Status:** Accepted
**Tanggal:** 2024-03-15

**Konteks:** Butuh database untuk aplikasi e-commerce. Data relasional
(users, orders, products) dengan kebutuhan transaksi ACID.

**Keputusan:** PostgreSQL.

**Alasan:** Open source, ACID compliant, JSON support untuk data
semi-structured, ekosistem tooling mature, tim sudah familiar.

**Alternatif ditolak:**
- MySQL: fitur lebih terbatas (window functions, JSON operations)
- MongoDB: data relasional, butuh ACID transactions lintas collection

**Trade-off:** Harus manage database server sendiri (atau pakai managed
service yang lebih mahal dari SQLite).
```

Yang penting bukan formatnya sempurna — yang penting **keputusan tercatat**.

---

## 8. Contoh ADR Lengkap

```markdown
# ADR-001: Adopsi PostgreSQL sebagai Database Utama

## Status
Accepted

## Context
Tim sedang membangun aplikasi e-commerce yang mengelola data users,
products, orders, dan payments. Data bersifat relasional dengan
kebutuhan transaksi lintas tabel (misal: membuat order sekaligus
mengurangi stock). Estimasi data awal: 10.000 users, 50.000 products,
100.000 orders/bulan.

## Decision Drivers
- Kebutuhan ACID transactions untuk konsistensi data finansial
- Dukungan JSON untuk menyimpan metadata produk yang bervariasi
- Tim familiar dengan SQL-based database
- Budget terbatas — preferensi open source
- Butuh ekosistem migration tool yang mature

## Decision
Menggunakan PostgreSQL 16 sebagai database utama untuk seluruh
data aplikasi.

## Alternatives Considered

### MySQL 8.x
- **Kelebihan:** Populer, banyak hosting murah, cukup untuk CRUD
- **Kelemahan:** Window functions terbatas, JSON operations kurang
  powerful dibanding PostgreSQL, partial index tidak ada
- **Alasan ditolak:** Fitur advanced query yang dibutuhkan untuk
  reporting lebih lengkap di PostgreSQL

### MongoDB 7.x
- **Kelebihan:** Schema flexible, horizontal scaling native, JSON-native
- **Kelemahan:** Multi-document ACID transactions baru dan kurang mature,
  data model kita sangat relasional, butuh denormalization yang
  menyulitkan update konsisten
- **Alasan ditolak:** Data e-commerce sangat relasional, ACID transactions
  lintas collection tidak se-robust RDBMS

## Consequences

### Positif
- ACID compliance menjamin konsistensi data transaksi keuangan
- JSONB column bisa dipakai untuk metadata produk tanpa schema migration
- Ekosistem migration tool (Prisma, Knex, Flyway) sangat mature

### Negatif
- Horizontal scaling lebih sulit dibanding MongoDB (butuh read replicas
  atau sharding dengan Citus untuk skala sangat besar)
- Butuh manage server atau bayar managed service (Supabase, Neon, RDS)

### Risiko
- Jika data tumbuh sangat besar (>100GB), mungkin perlu evaluasi
  read replicas atau caching layer
- Mitigasi: monitor query performance sejak awal, setup slow query log

## Security Implications
- Enforce SSL connections ke database
- Gunakan role-based access (bukan superuser untuk aplikasi)
- Encrypt sensitive columns (password sudah di-hash, tapi PII
  pertimbangkan column-level encryption)

## Operational Implications
- Setup automated backup (pg_dump daily + WAL archiving)
- Monitor connection pool usage
- Plan maintenance window untuk major version upgrade

## Related Decisions
- ADR-002 akan membahas pemilihan ORM/query builder
```

---

## 9. Kesalahan Umum

### ❌ Tidak Merekam Keputusan Sama Sekali
Alasan keputusan hanya ada di kepala developer senior. Ketika dia resign, konteks hilang.

### ❌ ADR Terlalu Panjang
ADR 10 halaman yang tidak ada yang mau baca. ADR harus bisa dibaca dalam 5-10 menit.

### ❌ Tidak Mencatat Alternatif
Hanya menulis "kita pilih X" tanpa menjelaskan kenapa Y dan Z ditolak. Ini menyebabkan tim di masa depan mempertanyakan ulang keputusan yang sama.

### ❌ Mengedit ADR yang Sudah Accepted
Mengubah ADR lama membuat history keputusan tidak akurat. Buat ADR baru yang men-supersede jika konteks berubah.

### ❌ ADR untuk Hal Terlalu Kecil
Tidak perlu ADR untuk memilih library date formatting. ADR untuk keputusan yang **signifikan dan sulit dibalik**: database, framework, communication pattern, authentication strategy.

---

## 10. Kapan Membuat ADR?

Buat ADR ketika keputusan memenuhi salah satu kriteria:

- Sulit atau mahal untuk diubah nanti
- Mempengaruhi banyak bagian sistem
- Tim punya pendapat berbeda (perlu alignment)
- Melibatkan trade-off yang perlu tercatat
- Menggunakan teknologi baru yang belum pernah dipakai tim

---

## 11. Dokumen Terkait

- [System Architecture](./system-architecture.md) — dokumentasi arsitektur keseluruhan
- [Architecture Patterns](./architecture-patterns.md) — pola arsitektur yang tersedia
- **Template:** [ADR Template](../templates/design/adr-template.md)
