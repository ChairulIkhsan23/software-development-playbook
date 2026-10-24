# Functional Requirements

## Pendahuluan

Functional requirement (FR) mendeskripsikan **perilaku spesifik** yang harus dimiliki sistem. FR menjawab pertanyaan: "Sistem harus bisa apa?"

Dokumen ini menjelaskan cara menulis, mengorganisasi, dan mengelola functional requirements yang baik.

---

## 1. Apa Itu Functional Requirement

Functional requirement mendeskripsikan **kapabilitas** yang harus disediakan sistem kepada user atau sistem lain.

Contoh FR:
- "Sistem harus memungkinkan user login menggunakan email dan password"
- "Sistem harus mengirim email konfirmasi dalam 30 detik setelah registrasi"
- "Sistem harus menampilkan riwayat transaksi 30 hari terakhir"

### FR vs Konsep Lain

| Konsep | Fokus | Contoh |
|--------|-------|--------|
| **Functional Requirement** | Apa yang sistem **lakukan** | "Sistem menghitung total belanja termasuk pajak 11%" |
| **Non-Functional Requirement** | Bagaimana sistem **berperilaku** (kualitas) | "Halaman checkout load dalam < 2 detik" |
| **User Story** | Kebutuhan dari **perspektif user** | "Sebagai pembeli, saya ingin lihat total belanja agar tahu berapa yang harus dibayar" |
| **Business Rule** | **Kebijakan bisnis** yang mengatur logika | "Pajak PPN berlaku 11% untuk semua produk kecuali kategori makanan pokok" |
| **Validation Rule** | **Constraint pada input** data | "Email harus format valid, password minimal 8 karakter" |

**Hubungan:** Satu user story bisa menghasilkan beberapa FR. Satu FR bisa mereferensi beberapa business rules.

---

## 2. Prinsip Menulis FR yang Baik

### 2.1 Atomic (Satu Requirement = Satu Perilaku)

```
❌ Buruk:
"Sistem harus memungkinkan user login dan menampilkan dashboard dengan ringkasan penjualan"

✅ Baik:
FR-AUTH-01: "Sistem harus memungkinkan user login menggunakan email dan password"
FR-DASH-01: "Sistem harus menampilkan ringkasan penjualan hari ini di dashboard"
```

### 2.2 Testable (Bisa Diverifikasi)

```
❌ Buruk:
"Sistem harus cepat memproses transaksi"

✅ Baik:
"Sistem harus memproses dan menyimpan transaksi dalam waktu < 500ms"
```

### 2.3 Unambiguous (Tidak Bermakna Ganda)

```
❌ Buruk:
"Sistem harus menampilkan data yang relevan kepada user"

✅ Baik:
"Sistem harus menampilkan 20 transaksi terakhir milik user yang sedang login, diurutkan dari terbaru"
```

### 2.4 Traceable (Bisa Dilacak)

Setiap FR harus punya ID unik yang menghubungkan ke user story, test case, dan kode.

```
User Story US-012 → FR-TXN-01, FR-TXN-02
FR-TXN-01 → Test Case TC-TXN-01a, TC-TXN-01b
FR-TXN-01 → Code: TransactionService.create()
```

### 2.5 Necessary (Memang Dibutuhkan)

Setiap FR harus bisa dijawab: "Jika ini dihapus, user/bisnis kehilangan apa?" Jika jawabannya tidak jelas — pertanyakan apakah FR ini perlu ada.

---

## 3. Konvensi Penamaan ID

Format: `FR-[MODULE]-[NNN]`

| Komponen | Penjelasan | Contoh |
|----------|-----------|--------|
| FR | Prefix tetap (Functional Requirement) | FR |
| MODULE | Kode modul/fitur (3-5 huruf kapital) | AUTH, TXN, RPT, BILL, USR |
| NNN | Nomor urut 3 digit | 001, 002, 010 |

### Daftar Kode Modul (Contoh)

| Kode | Modul |
|------|-------|
| AUTH | Authentication & Authorization |
| USR | User Management |
| TXN | Transaction |
| RPT | Reporting |
| BILL | Billing & Payment |
| NOTIF | Notification |
| ADMIN | Admin Panel |
| CAT | Catalog / Categories |

**Tips:** Tentukan daftar kode modul di awal proyek dan pakai konsisten. Tambahkan kode baru hanya jika modul benar-benar baru.

---

## 4. Format Requirement

### Format Ringkas (Tabel)

Cocok untuk overview dan tracking.

| ID | Deskripsi | Prioritas | Status |
|---|-----------|-----------|--------|
| FR-AUTH-001 | Sistem harus memungkinkan user login menggunakan email dan password | Must Have | Approved |
| FR-AUTH-002 | Sistem harus mengirim email reset password dalam 60 detik setelah permintaan | Must Have | Draft |
| FR-TXN-001 | Sistem harus menerima input transaksi berupa nominal dan kategori | Must Have | Approved |

### Format Detail (Per Requirement)

Cocok untuk requirement yang kompleks atau butuh penjelasan lebih.

```markdown
### FR-TXN-001: Input Transaksi

**Deskripsi:**
Sistem harus menerima input transaksi dari user berupa nominal (wajib)
dan kategori (opsional). Jika kategori tidak diisi, sistem melakukan
auto-kategorisasi berdasarkan keyword.

**Rationale:**
Mengurangi friction pencatatan. Data riset menunjukkan 42% user berhenti
karena terlalu banyak field.

**Prioritas:** Must Have

**Acceptance Criteria:**
- GIVEN user di halaman utama
  WHEN user mengetik nominal dan tap tombol simpan
  THEN transaksi tersimpan dan muncul di riwayat hari ini

- GIVEN user input nominal tanpa kategori
  WHEN user tap simpan
  THEN sistem auto-assign kategori berdasarkan keyword

- GIVEN user input nominal = 0 atau negatif
  WHEN user tap simpan
  THEN sistem menampilkan error "Nominal harus lebih dari 0"

**Dependencies:**
- FR-CAT-001 (auto-kategorisasi engine harus tersedia)
- BR-TXN-01 (transaksi > Rp 10.000.000 perlu konfirmasi)

**Source:** PRD v1.0, User Story US-012
```

---

## 5. Kategorisasi by Module/Feature

Organisasi requirement per modul memudahkan navigasi dan assignment ke tim.

```
functional-requirements/
├── FR-AUTH-xxx    Authentication
│   ├── FR-AUTH-001  Login email/password
│   ├── FR-AUTH-002  Reset password
│   └── FR-AUTH-003  Session management
├── FR-TXN-xxx    Transaction
│   ├── FR-TXN-001  Input transaksi
│   ├── FR-TXN-002  Edit transaksi
│   └── FR-TXN-003  Void transaksi
├── FR-RPT-xxx    Reporting
│   ├── FR-RPT-001  Laporan harian
│   └── FR-RPT-002  Laporan bulanan
└── FR-NOTIF-xxx  Notification
    └── FR-NOTIF-001 Push notification reminder
```

Untuk proyek kecil, cukup satu file dengan section per modul. Untuk proyek besar, pisahkan per file.

---

## 6. Requirement Lifecycle

Setiap requirement melewati lifecycle berikut:

```
Draft → In Review → Approved → In Progress → Implemented → Verified → Done
                        ↓
                    Rejected / Deferred
```

| Status | Artinya |
|--------|---------|
| **Draft** | Baru ditulis, belum di-review |
| **In Review** | Sedang di-review oleh tech lead / PM |
| **Approved** | Disepakati, siap diimplementasi |
| **Rejected** | Ditolak (catat alasan) |
| **Deferred** | Ditunda ke versi berikutnya (catat alasan) |
| **In Progress** | Sedang dikerjakan developer |
| **Implemented** | Kode selesai, siap ditest |
| **Verified** | Lolos QA testing |
| **Done** | Live di production |

**Tips untuk solo dev:** Minimal pakai 3 status: Draft → Approved → Done. Jangan skip "Approved" — ini memaksa kamu review ulang sebelum coding.

---

## 7. Prioritas: MoSCoW

| Label | Artinya | Guideline |
|-------|---------|-----------|
| **Must Have** | Produk tidak bisa launch tanpa ini | Maksimal 60% dari total requirements |
| **Should Have** | Penting tapi produk masih bisa launch tanpa ini | 20% |
| **Could Have** | Nice-to-have, tambah value tapi tidak kritis | 15% |
| **Won't Have** | Tidak akan dikerjakan di versi ini | 5% (tetap dokumentasikan!) |

---

## 8. Contoh Requirement Set: Modul Authentication

| ID | Deskripsi | Prioritas | Acceptance Criteria (ringkas) |
|---|-----------|-----------|-------------------------------|
| FR-AUTH-001 | Sistem harus memungkinkan user registrasi dengan email, nama, dan password | Must Have | Email valid, password ≥ 8 char, nama ≥ 2 char |
| FR-AUTH-002 | Sistem harus memungkinkan user login dengan email dan password | Must Have | Login berhasil → redirect ke dashboard. Gagal → error message |
| FR-AUTH-003 | Sistem harus mengirim email verifikasi setelah registrasi | Must Have | Email terkirim < 60 detik, link valid 24 jam |
| FR-AUTH-004 | Sistem harus memungkinkan user reset password via email | Must Have | Link reset valid 1 jam, password baru harus beda dari 3 terakhir |
| FR-AUTH-005 | Sistem harus mengunci akun setelah 5x gagal login | Should Have | Terkunci 15 menit, user dapat notifikasi |
| FR-AUTH-006 | Sistem harus mendukung login via Google OAuth | Could Have | Redirect ke Google, auto-create account jika belum ada |

---

## 9. Kesalahan Umum

| Kesalahan | Contoh | Solusi |
|-----------|--------|--------|
| Tidak atomic | "Sistem harus login dan tampilkan dashboard" | Pecah jadi 2 FR terpisah |
| Tidak testable | "Sistem harus user-friendly" | Definisikan metrik: "task completion < 3 langkah" |
| Ambigu | "Sistem harus menampilkan data terkini" | "Sistem menampilkan data yang di-refresh maksimal 5 menit yang lalu" |
| Solusi bukan requirement | "Sistem harus menggunakan Redis untuk caching" | Itu technical decision, bukan FR. FR: "Halaman load < 1 detik" |
| Missing negative case | Hanya tulis happy path | Tambah: apa yang terjadi jika input invalid, network error, dll |
| Tidak ada prioritas | Semua terlihat "penting" | Gunakan MoSCoW, paksa max 60% Must Have |
| ID tidak konsisten | FR-1, REQ-AUTH-1, R001 | Tentukan konvensi di awal, pakai konsisten |

---

## 10. Checklist Menulis FR

- [ ] Setiap FR punya ID unik sesuai konvensi
- [ ] Setiap FR atomic (satu perilaku per requirement)
- [ ] Setiap FR testable (ada angka, kondisi, atau kriteria spesifik)
- [ ] Setiap FR punya prioritas MoSCoW
- [ ] Negative cases sudah dipertimbangkan
- [ ] Dependencies antar FR sudah diidentifikasi
- [ ] FR bisa di-trace balik ke user story atau PRD
- [ ] Tidak ada implementation detail di FR (itu tugas technical spec)

---

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — FR diturunkan dari PRD
- [User Stories & Use Cases](./user-stories-and-use-cases.md) — FR dipecah dari user stories
- [Business Rules](./business-rules.md) — FR mereferensi business rules
- [Acceptance Criteria](./acceptance-criteria.md) — detail penulisan acceptance criteria
- [Requirements Traceability](./requirements-traceability.md) — tracking FR ke test case dan kode
- [Requirements Change Management](./requirements-change-management.md) — proses ubah FR yang sudah approved
