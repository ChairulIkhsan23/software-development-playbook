# Buku Rekomendasi Software Engineering

Daftar buku pilihan yang menjadi referensi klasik dan modern dalam industri rekayasa perangkat lunak. Buku-buku ini direkomendasikan untuk memperdalam prinsip desain, arsitektur sistem, pengujian, dan budaya delivery.

---

## 1. Prinsip Desain Kode & Craftsmanship

### 1.1 *Clean Code: A Handbook of Agile Software Craftsmanship*
- **Penulis**: Robert C. Martin ("Uncle Bob")
- **Penerbit**: Prentice Hall (2008)
- **Topik Kunci**: Penamaan variabel bermakna, fungsi kecil dengan satu level abstraksi, penanganan error, dan unit testing.
- **Relevansi di Playbook**: Bab penamaan dan fungsi sejalan dengan [`05-development-practices/coding-standards.md`](../05-development-practices/coding-standards.md).
- **Catatan Kritis**: Beberapa saran berorientasi OOP murni perlu disesuaikan saat menggunakan paradigma fungsional modern.

### 1.2 *The Pragmatic Programmer: Your Journey to Mastery (20th Anniversary Edition)*
- **Penulis**: David Thomas, Andrew Hunt
- **Penerbit**: Addison-Wesley Professional (2019)
- **Topik Kunci**: Mindset kepemilikan kode, prinsip DRY, orthogonal design, prototyping, dan otomatisasi tooling.
- **Relevansi di Playbook**: Memperkuat fondasi mindset developer di [`00-foundations/software-engineering-principles.md`](../00-foundations/software-engineering-principles.md).

### 1.3 *Refactoring: Improving the Design of Existing Code (2nd Edition)*
- **Penulis**: Martin Fowler
- **Penerbit**: Addison-Wesley Professional (2018, contoh kode dalam JavaScript)
- **Topik Kunci**: Mengidentifikasi "code smells", teknik perombakan kode bertahap tanpa mengubah perilaku eksternal, dan test-driven refactoring.
- **Relevansi di Playbook**: Menjadi dasar penanganan technical debt di [`09-maintenance-evolution/technical-debt.md`](../09-maintenance-evolution/technical-debt.md).

---

## 2. Arsitektur Sistem & Data

### 2.1 *Designing Data-Intensive Applications: The Big Ideas Behind Reliable, Scalable, and Maintainable Systems*
- **Penulis**: Martin Kleppmann
- **Penerbit**: O'Reilly Media (2017)
- **Topik Kunci**: Model data, indexing (B-Tree vs LSM), transaksi ACID, replikasi, partisi, konsistensi data, dan arsitektur event-driven.
- **Relevansi di Playbook**: Rujukan utama untuk bab database dan integrasi di [`04-technical-design/database-design.md`](../04-technical-design/database-design.md).
- **Tingkat Kesulitan**: Menengah ke Lanjutan. Sangat direkomendasikan untuk backend dan system architect.

### 2.2 *Building Microservices: Designing Fine-Grained Systems (2nd Edition)*
- **Penulis**: Sam Newman
- **Penerbit**: O'Reilly Media (2021)
- **Topik Kunci**: Batasan layanan (*service boundaries*), komunikasi sinkron vs asinkron, evolusi skema database, dan migrasi bertahap dari monolit.
- **Relevansi di Playbook**: Menjadi dasar perbandingan arsitektur di [`04-technical-design/architecture-patterns.md`](../04-technical-design/architecture-patterns.md).

### 2.3 *Software Architecture: The Hard Parts*
- **Penulis**: Neal Ford, Mark Richards, Pramod Sadalage, Zhamak Dehghani
- **Penerbit**: O'Reilly Media (2021)
- **Topik Kunci**: Trade-off analisis keputusan arsitektur, granularitas data dan kode, serta dokumentasi Architecture Decision Records (ADR).
- **Relevansi di Playbook**: Panduan penulisan ADR di [`04-technical-design/architecture-decision-records.md`](../04-technical-design/architecture-decision-records.md).

---

## 3. DevOps, Pengiriman, & Budaya Operasi

### 3.1 *Accelerate: The Science of Lean Software and DevOps*
- **Penulis**: Nicole Forsgren, Jez Humble, Gene Kim
- **Penerbit**: IT Revolution Press (2018)
- **Topik Kunci**: Penelitian empiris DORA metrics (Deployment Frequency, Lead Time for Changes, Change Failure Rate, Time to Restore Service).
- **Relevansi di Playbook**: Panduan pipeline dan kecepatan rilis di [`07-delivery-operations/ci-cd.md`](../07-delivery-operations/ci-cd.md).

### 3.2 *Site Reliability Engineering: How Google Runs Production Systems*
- **Penulis**: Betsy Beyer, Chris Jones, Jennifer Petoff, Niall Richard Murphy
- **Penerbit**: O'Reilly Media (2016)
- **Topik Kunci**: Service Level Objectives (SLO), Error Budgets, manajemen insiden tanpa menyalahkan (*blameless post-mortem*), dan otomatisasi operasional.
- **Relevansi di Playbook**: Menjadi rujukan utama manajemen insiden di [`07-delivery-operations/incident-response.md`](../07-delivery-operations/incident-response.md) dan monitoring.

---

## 4. Jalur Rekomendasi Membaca Sesuai Peran

| Peran Developer | Urutan Buku Prioritas |
|---|---|
| **Junior / Fresh Graduate** | 1. *The Pragmatic Programmer* → 2. *Clean Code* → 3. *Refactoring* |
| **Mid-Level Backend Dev** | 1. *Refactoring* → 2. *Designing Data-Intensive Applications* → 3. *Building Microservices* |
| **Tech Lead / Solo Architect** | 1. *Designing Data-Intensive Applications* → 2. *Software Architecture: The Hard Parts* → 3. *Accelerate* |
| **DevOps / Platform Engineer** | 1. *Accelerate* → 2. *Site Reliability Engineering* → 3. *The Phoenix Project* |
