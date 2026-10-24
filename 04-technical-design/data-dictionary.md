# Data Dictionary

## 1. Apa Itu Data Dictionary?

Data dictionary adalah **katalog** yang mendokumentasikan setiap tabel, kolom, tipe data, constraint, dan deskripsi bisnis dari database. Ini bukan ERD (yang menunjukkan relasi visual) — data dictionary menjelaskan **detail spesifik** setiap field.

Analoginya: ERD adalah peta jalan, data dictionary adalah buku panduan yang menjelaskan setiap bangunan di peta itu.

> 📄 Template: [Data Dictionary Template](../templates/design/data-dictionary-template.md)

---

## 2. Mengapa Perlu Data Dictionary?

- **Developer baru** langsung tahu arti setiap kolom tanpa menebak dari nama atau membaca kode
- **Konsistensi** — semua orang punya pemahaman yang sama tentang field `status` (apa saja nilainya? apa artinya?)
- **Koordinasi frontend-backend** — frontend tahu tipe data dan constraint tanpa trial-and-error
- **Compliance** — tahu di mana data sensitif (PII, data keuangan) disimpan
- **Debugging** — saat data aneh muncul, bisa cek constraint dan validasi yang seharusnya berlaku
- **Onboarding** — dokumentasi yang selalu up-to-date lebih baik dari penjelasan lisan

---

## 3. Format Data Dictionary

Setiap tabel didokumentasikan dengan format berikut:

| Field | Penjelasan |
|---|---|
| **Table Name** | Nama tabel di database |
| **Description** | Deskripsi bisnis singkat — tabel ini menyimpan apa? |
| **Column Name** | Nama kolom |
| **Data Type** | Tipe data SQL (VARCHAR, BIGINT, TIMESTAMPTZ, dll.) |
| **Nullable** | Apakah kolom boleh NULL? |
| **Default** | Nilai default jika tidak diisi |
| **Constraints** | PK, FK, UNIQUE, CHECK, validasi format |
| **FK Reference** | Tabel dan kolom yang direferensi (untuk foreign key) |
| **Business Description** | Penjelasan bisnis — apa arti kolom ini dalam konteks domain? |

---

## 4. Contoh Data Dictionary

### Tabel: `users`

**Deskripsi:** Menyimpan data akun pengguna terdaftar.

| Column | Type | Nullable | Default | Constraints | FK | Business Description |
|---|---|---|---|---|---|---|
| `id` | BIGSERIAL | NOT NULL | auto-increment | PK | - | Identifier unik user |
| `email` | VARCHAR(255) | NOT NULL | - | UNIQUE, format email valid | - | Alamat email untuk login dan komunikasi |
| `password_hash` | VARCHAR(255) | NOT NULL | - | - | - | Hash password (bcrypt/argon2). Tidak pernah simpan plaintext |
| `name` | VARCHAR(100) | NOT NULL | - | - | - | Nama lengkap user untuk display |
| `role` | VARCHAR(20) | NOT NULL | `'member'` | CHECK: `admin`, `member`, `guest` | - | Peran user yang menentukan access level |
| `is_active` | BOOLEAN | NOT NULL | `true` | - | - | `false` jika akun dinonaktifkan oleh admin |
| `last_login_at` | TIMESTAMPTZ | NULL | - | - | - | Waktu login terakhir. NULL jika belum pernah login |
| `created_at` | TIMESTAMPTZ | NOT NULL | `NOW()` | - | - | Waktu akun dibuat |
| `updated_at` | TIMESTAMPTZ | NOT NULL | `NOW()` | - | - | Waktu terakhir data akun diubah |

---

### Tabel: `orders`

**Deskripsi:** Menyimpan data pesanan yang dibuat oleh user.

| Column | Type | Nullable | Default | Constraints | FK | Business Description |
|---|---|---|---|---|---|---|
| `id` | BIGSERIAL | NOT NULL | auto-increment | PK | - | Identifier unik order |
| `user_id` | BIGINT | NOT NULL | - | INDEX | `users(id)` | User yang membuat order |
| `order_number` | VARCHAR(20) | NOT NULL | - | UNIQUE | - | Nomor order yang ditampilkan ke user (format: `ORD-YYYYMMDD-XXXX`) |
| `status` | VARCHAR(20) | NOT NULL | `'pending'` | CHECK: `pending`, `paid`, `shipped`, `delivered`, `cancelled` | - | Status lifecycle order |
| `total_amount` | DECIMAL(12,2) | NOT NULL | - | CHECK: `>= 0` | - | Total harga order (snapshot, termasuk diskon). Dalam Rupiah |
| `shipping_address` | TEXT | NOT NULL | - | - | - | Alamat pengiriman lengkap (snapshot saat order dibuat) |
| `notes` | TEXT | NULL | - | - | - | Catatan opsional dari buyer |
| `paid_at` | TIMESTAMPTZ | NULL | - | - | - | Waktu pembayaran berhasil dikonfirmasi |
| `created_at` | TIMESTAMPTZ | NOT NULL | `NOW()` | - | - | Waktu order dibuat |
| `updated_at` | TIMESTAMPTZ | NOT NULL | `NOW()` | - | - | Waktu terakhir order diubah |

---

### Tabel: `order_items`

**Deskripsi:** Menyimpan detail item dalam setiap order. Relasi many-to-many antara orders dan products.

| Column | Type | Nullable | Default | Constraints | FK | Business Description |
|---|---|---|---|---|---|---|
| `id` | BIGSERIAL | NOT NULL | auto-increment | PK | - | Identifier unik order item |
| `order_id` | BIGINT | NOT NULL | - | INDEX | `orders(id)` ON DELETE CASCADE | Order yang memiliki item ini |
| `product_id` | BIGINT | NOT NULL | - | INDEX | `products(id)` ON DELETE RESTRICT | Produk yang dipesan |
| `quantity` | INTEGER | NOT NULL | - | CHECK: `> 0` | - | Jumlah item yang dipesan |
| `unit_price` | DECIMAL(12,2) | NOT NULL | - | CHECK: `>= 0` | - | Harga satuan saat order dibuat (snapshot, bukan harga produk saat ini) |

---

## 5. Klasifikasi Data

Klasifikasi data membantu menentukan **bagaimana data harus dilindungi** dan siapa yang boleh mengaksesnya.

| Level | Deskripsi | Contoh | Perlakuan |
|---|---|---|---|
| **Public** | Boleh diakses siapa saja | Nama produk, deskripsi, harga | Tidak perlu enkripsi khusus |
| **Internal** | Hanya untuk penggunaan internal tim | Order statistics, aggregate data | Access control, tidak diekspos ke public API |
| **Confidential** | Data sensitif bisnis atau personal | Email user, alamat, nomor telepon | Enkripsi at rest, access log, masking di log |
| **Restricted** | Data sangat sensitif | Password hash, payment token, KTP | Enkripsi at rest + in transit, audit log, minimal access, retention policy |

### Menandai Klasifikasi di Data Dictionary

Tambahkan kolom klasifikasi di data dictionary:

| Column | Classification | Handling |
|---|---|---|
| `email` | Confidential | Jangan log plaintext, mask di non-prod environment |
| `password_hash` | Restricted | Jangan pernah return di API response |
| `name` | Confidential | Mask di log, bisa ditampilkan ke user sendiri |
| `order_number` | Internal | Bisa ditampilkan ke user terkait |

---

## 6. Naming Conventions

Konsistensi penamaan membuat data dictionary lebih mudah dipahami dan diprediksi.

| Aturan | Contoh Benar | Contoh Salah |
|---|---|---|
| Nama tabel: `snake_case`, plural | `order_items` | `OrderItem`, `order_item` |
| Nama kolom: `snake_case` | `first_name` | `firstName`, `FirstName` |
| Foreign key: `{singular_table}_id` | `user_id` | `userId`, `fk_user` |
| Boolean: prefix `is_` atau `has_` | `is_active` | `active`, `status_active` |
| Timestamp: suffix `_at` | `created_at` | `creation_date`, `createdAt` |
| Monetary values: jelaskan mata uang | `total_amount` (Rupiah di deskripsi) | `total` (mata uang apa?) |

---

## 7. Memelihara Data Dictionary

### Update Bersamaan dengan Migration

Setiap kali membuat schema migration, **update data dictionary di PR yang sama**. Jangan buat PR terpisah — dijamin lupa.

```
Workflow:
1. Buat migration file: 005_add_phone_to_users.sql
2. Update data-dictionary.md: tambah kolom `phone` di tabel `users`
3. Commit keduanya dalam satu PR
4. Reviewer: cek migration DAN data dictionary
```

### Validasi Otomatis (Opsional)

Untuk tim yang lebih besar, buat script sederhana yang membandingkan schema database aktual dengan data dictionary dan melaporkan perbedaan.

### Siapa yang Bertanggung Jawab?

- **Developer yang membuat migration** bertanggung jawab update data dictionary
- **Reviewer** memastikan data dictionary ter-update di setiap PR yang mengubah schema

---

## 8. Kesalahan Umum

### ❌ Tidak Punya Data Dictionary

Tim mengandalkan "baca kode" atau "tanya yang bikin". Sangat tidak scalable.

### ❌ Data Dictionary Outdated

Data dictionary yang tidak sesuai schema aktual lebih berbahaya daripada tidak ada — memberikan informasi salah.

**Solusi:** Jadikan update data dictionary bagian dari definition of done saat mengubah schema.

### ❌ Hanya Mencantumkan Tipe Data Tanpa Deskripsi Bisnis

```
❌  status | VARCHAR(20) | NOT NULL
✅  status | VARCHAR(20) | NOT NULL | Status lifecycle order: pending → paid → shipped → delivered → cancelled
```

Tanpa deskripsi bisnis, data dictionary tidak lebih berguna dari `\d table_name` di psql.

### ❌ Tidak Ada Klasifikasi Data

Tidak tahu kolom mana yang berisi PII, sehingga data sensitif bocor ke log, error message, atau third-party analytics.

### ❌ Menyimpan Data Dictionary di Tool Terpisah

Data dictionary di Confluence/Google Docs sementara kode di Git. Dijamin diverge.

**Solusi:** Simpan data dictionary di repository yang sama dengan kode, dalam format Markdown.

---

## 9. Template Ringkas

Untuk setiap tabel baru, salin dan isi template ini:

```markdown
### Tabel: `nama_tabel`

**Deskripsi:** [Apa yang disimpan tabel ini dalam konteks bisnis]

| Column | Type | Nullable | Default | Constraints | FK | Business Description |
|---|---|---|---|---|---|---|
| `id` | BIGSERIAL | NOT NULL | auto | PK | - | Identifier unik |
| ... | ... | ... | ... | ... | ... | ... |
| `created_at` | TIMESTAMPTZ | NOT NULL | `NOW()` | - | - | Waktu record dibuat |
| `updated_at` | TIMESTAMPTZ | NOT NULL | `NOW()` | - | - | Waktu terakhir diubah |
```

---

## 10. Dokumen Terkait

- [Database Design](./database-design.md) — prinsip dan pattern perancangan database
- [System Architecture](./system-architecture.md) — konteks arsitektur keseluruhan
- [API Design](./api-design.md) — bagaimana data diekspos via API
- **Template:** [Data Dictionary Template](../templates/design/data-dictionary-template.md)
