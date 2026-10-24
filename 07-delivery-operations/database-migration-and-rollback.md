# Database Migration and Rollback

Panduan pelaksanaan migrasi skema database di lingkungan produksi tanpa downtime, penerapan pola Expand and Contract, serta strategi penanganan rollback yang aman.

---

## 1. Tantangan Migrasi Database di Produksi

Perubahan kode aplikasi dapat di-rollback dalam hitungan detik, tetapi perubahan skema dan data database bersifat persisten (*stateful*). Kesalahan eksekusi DDL (*Data Definition Language*) dapat mengunci tabel (*exclusive lock*), memicu *timeout* transaksi, hingga menyebabkan kehilangan data permanen (*data loss*).

Untuk mencapai stabilitas tinggi, migrasi skema harus selalu diperlakukan sebagai proses multi-fase yang kompatibel ke belakang (*backward-compatible*).

---

## 2. Pola Expand and Contract (Parallel Run)

Pola **Expand and Contract** membagi satu perubahan skema menjadi empat fase rilis terpisah agar versi aplikasi lama dan baru dapat berjalan berdampingan tanpa error.

```
Fase 1: Expand      Fase 2: Migrate      Fase 3: Contract Old    Fase 4: Clean Up
[Tambah kolom baru] -> [Dual-write data] -> [Aplikasi baca/tulis] -> [Hapus kolom lama]
(Nullable / Default)   (Sync data lama)     (ke kolom baru saja)     (Skema bersih)
```

### Studi Kasus: Mengubah Kolom `full_name` Menjadi `first_name` dan `last_name`

#### Fase 1: Expand (Rilis Skema Pertama)
Tambahkan kolom baru dengan status `NULL` agar tidak memutus query `INSERT` dari aplikasi versi lama.

```sql
-- DDL dieksekusi di database sebelum aplikasi baru aktif
ALTER TABLE users ADD COLUMN first_name VARCHAR(100) NULL;
ALTER TABLE users ADD COLUMN last_name VARCHAR(100) NULL;
```

#### Fase 2: Migrate (Rilis Kode Versi Baru)
Aplikasi baru mulai melakukan *dual-write* (menulis ke kolom lama dan kolom baru sekaligus). Jalankan script *backfill* di latar belakang secara bertahap untuk mengisi data pengguna lama.

```sql
-- Script backfill dijalankan per batch (misal 1000 baris per iterasi)
UPDATE users 
SET 
  first_name = split_part(full_name, ' ', 1),
  last_name = COALESCE(NULLIF(substring(full_name from position(' ' in full_name) + 1), ''), '')
WHERE first_name IS NULL
LIMIT 1000;
```

#### Fase 3: Contract Old (Alihkan Kode Penuh)
Setelah 100% data terisi dan terverifikasi, perbarui kode aplikasi untuk membaca dan menulis hanya ke kolom `first_name` dan `last_name`. Kolom `full_name` sudah tidak lagi disentuh oleh kode aplikasi.

#### Fase 4: Clean Up (Penghapusan Kolom Usang)
Pada siklus rilis berikutnya (setelah versi aplikasi lama dipastikan sudah tidak ada yang aktif), hapus kolom usang dari skema database.

```sql
-- Jalankan setelah kode lama benar-benar mati
ALTER TABLE users DROP COLUMN full_name;
```

---

## 3. Aturan Skema yang Kompatibel ke Belakang

Untuk menghindari crash pada instans aplikasi lama yang masih berjalan selama proses deployment:

1. **Menambah Kolom Wajib Nullable atau Memiliki Default:**
   - ❌ Hindari: `ALTER TABLE orders ADD COLUMN tax_rate NUMERIC NOT NULL;` (Aplikasi lama akan gagal saat INSERT).
   - ✅ Gunakan: `ALTER TABLE orders ADD COLUMN tax_rate NUMERIC DEFAULT 0 NOT NULL;` atau biarkan `NULL`.
2. **Mengubah Nama Kolom:** Jangan pernah menggunakan `RENAME COLUMN` langsung di database produksi. Gunakan pola Expand and Contract dengan menambah kolom baru, menyalin data, lalu menghapus kolom lama.
3. **Membuat Index Tanpa Mengunci Tabel:**
   - PostgreSQL: Gunakan `CREATE INDEX CONCURRENTLY idx_users_email ON users(email);`.
   - MySQL: Gunakan algoritma online DDL `ALGORITHM=INPLACE, LOCK=NONE;`.

---

## 4. Evaluasi Tooling Migrasi Skema

| Tool | Ekosistem / Bahasa | Keunggulan Utama | Karakteristik |
|---|---|---|---|
| **Prisma Migrate** | TypeScript / Node.js | Integrasi ORM kuat, deklaratif via schema file | Sangat cocok untuk startup TS; perlu review manual saat SQL generated |
| **golang-migrate** | Go / CLI serbaguna | Ringan, binary tunggal, mendukung file raw SQL murni | Menggunakan pasangan file `.up.sql` dan `.down.sql` |
| **Flyway** | Java / JVM / Multi-stack | Standar enterprise industri, versioning berbasis nama file | Sangat stabil dan ketat dalam validasi checksum |
| **Liquibase** | Multi-stack / Polyglot | Mendukung XML, YAML, JSON, dan SQL; rollback granular | Kuat untuk arsitektur database enterprise yang kompleks |

---

## 5. Pemeriksaan Pra-Migrasi (Pre-Migration Checks)

Sebelum mengeksekusi migrasi di staging atau produksi, lakukan validasi berikut:

- [ ] **Locking Analysis:** Apakah DDL ini membutuhkan `ACCESS EXCLUSIVE LOCK` pada tabel besar (> 1 juta baris)?
- [ ] **Transaction Timeout:** Apakah batas waktu statement timeout sudah dikonfigurasi agar migrasi macet tidak melumpuhkan database?
- [ ] **Dry-Run di Staging:** Uji coba skrip migrasi terhadap database staging yang memiliki volume data serupa dengan produksi.
- [ ] **Backup Snapshot:** Pastikan snapshot backup terverifikasi tersedia sebelum DDL dieksekusi.
- [ ] **Disk Space Verification:** Pastikan kapasitas penyimpanan database memiliki ruang bebas minimal 2x dari ukuran tabel yang sedang diubah.

---

## 6. Strategi Rollback: Mengapa Roll-Forward Lebih Aman

Banyak developer menganggap file `down.sql` adalah penyelamat utama ketika rilis gagal. Namun pada kenyataannya, **Down-Migration di Produksi Sangat Berbahaya**:

- Jika skrip `down.sql` mengeksekusi `DROP COLUMN`, data transaksi baru yang telah masuk selama beberapa menit rilis akan hilang permanen.
- Menjalankan migrasi mundur sering kali memicu konflik integritas referensial (*foreign key*).

### Standar Penanganan: Roll-Forward
- **Pertahankan Skema Tetap Kompatibel:** Karena skema Fase 1 (Expand) bersifat aditif dan nullable, aplikasi versi lama tetap dapat berjalan normal meskipun skema baru sudah terpasang.
- **Rollback Kode Aplikasi Saja:** Jika aplikasi baru bermasalah, segera rollback kode aplikasi ke image versi sebelumnya. Skema database dibiarkan tetap seperti semula.
- **Perbaiki via Migrasi Baru (Roll-Forward):** Jika skema baru bermasalah, buat skrip migrasi perbaikan baru (`up_fix.sql`) lalu terapkan melalui pipeline CI/CD resmi.

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Mengubah Tipe Data Kolom Langsung:** Menjalankan `ALTER TABLE transactions ALTER COLUMN amount TYPE BIGINT;` yang memaksa penulisan ulang seluruh tabel (*table rewrite*) dan mengunci database selama berjam-jam.
2. **Migrasi dan Seed Berjalan di Pod Startup:** Menjalankan perintah migrasi database otomatis di dalam entrypoint container aplikasi pada saat autoscaling memicu *race condition* dan kebuntuan (*deadlock*).
3. **Mengabaikan Checksum File Migrasi:** Mengedit isi file migrasi lama yang sudah pernah dieksekusi di produksi alih-alih membuat file migrasi baru.
4. **Menghapus Kolom yang Masih Dibaca Aplikasi:** Menghapus kolom di database sebelum memverifikasi bahwa seluruh instance aplikasi lama sudah benar-benar mati.
5. **Menjalankan DDL di Jam Sibuk:** Mengeksekusi perubahan indeks atau tabel tanpa jadwal *maintenance window* atau tanpa monitoring lock query aktif.

---

## 8. Dokumen Terkait

- [Deployment Guide](deployment-guide.md)
- [Backup and Recovery](backup-and-recovery.md)
- [Environment Strategy](environment-strategy.md)
- [Release Readiness Checklist](release-readiness.md)
- [Operations Runbook Template](../templates/delivery/operations-runbook-template.md)
