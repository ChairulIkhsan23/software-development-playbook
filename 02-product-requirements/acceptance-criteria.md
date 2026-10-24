# Acceptance Criteria

## Apa Itu Acceptance Criteria?

Acceptance Criteria (AC) adalah sekumpulan kondisi spesifik yang **harus terpenuhi** agar sebuah user story atau fitur dianggap selesai dan siap diterima oleh Product Owner. AC menjadi kontrak antara tim development dan stakeholder — semua pihak sepakat tentang apa yang dimaksud "done" untuk setiap item pekerjaan.

## Perbedaan AC, Test Case, dan Definition of Done

| Aspek | Acceptance Criteria | Test Case | Definition of Done (DoD) |
|---|---|---|---|
| **Cakupan** | Per user story / fitur | Per skenario pengujian | Berlaku untuk semua story |
| **Ditulis oleh** | Product Owner + tim | QA Engineer | Tim secara kolektif |
| **Fokus** | *Apa* yang harus terjadi | *Bagaimana* cara menguji | *Standar kualitas* universal |
| **Contoh** | "User bisa reset password via email" | "Input email valid → cek inbox → klik link → form muncul → submit → berhasil" | "Code reviewed, unit test pass, deployed ke staging" |

**Hubungan ketiganya:** AC menentukan *apa* yang harus benar → test case menjabarkan *langkah-langkah* verifikasi AC → DoD memastikan *standar proses* terpenuhi (review, testing, deploy).

## Format Gherkin (Given-When-Then)

Format Gherkin menggunakan struktur skenario yang mudah dibaca oleh semua pihak, termasuk non-teknis.

### Struktur Dasar

```gherkin
Feature: [Nama fitur]

  Scenario: [Deskripsi skenario spesifik]
    Given [kondisi awal / state sistem]
    And [kondisi awal tambahan, opsional]
    When [aksi yang dilakukan pengguna / trigger]
    And [aksi tambahan, opsional]
    Then [hasil yang diharapkan]
    And [hasil tambahan yang diharapkan]
```

### Contoh Konkret: Pembayaran

```gherkin
Feature: Pembayaran Pesanan

  Scenario: Berhasil melakukan pembayaran dengan saldo mencukupi
    Given pengguna memiliki saldo sebesar Rp 100.000
    And pengguna memiliki barang di keranjang belanja senilai Rp 75.000
    When pengguna menekan tombol "Bayar Sekarang"
    Then sistem memotong saldo sebesar Rp 75.000
    And status pesanan berubah menjadi "PAID"
    And pengguna menerima notifikasi konfirmasi pembayaran

  Scenario: Gagal melakukan pembayaran karena saldo tidak mencukupi
    Given pengguna memiliki saldo sebesar Rp 20.000
    And pengguna memiliki barang di keranjang belanja senilai Rp 50.000
    When pengguna menekan tombol "Bayar Sekarang"
    Then sistem menampilkan pesan error "Saldo tidak mencukupi"
    And saldo pengguna tidak berubah
    And status pesanan tetap "UNPAID"

  Scenario: Pembayaran timeout karena gangguan payment gateway
    Given pengguna memiliki saldo mencukupi
    And payment gateway tidak merespons dalam 30 detik
    When pengguna menekan tombol "Bayar Sekarang"
    Then sistem menampilkan pesan "Pembayaran sedang diproses, cek status di halaman riwayat"
    And saldo pengguna di-hold (belum dipotong)
    And sistem mencatat transaksi dengan status "PENDING"
```

### Contoh Konkret: Login

```gherkin
Feature: Login Pengguna

  Scenario: Login berhasil dengan kredensial valid
    Given pengguna sudah terdaftar dengan email "andi@contoh.com"
    And akun pengguna dalam status aktif
    When pengguna memasukkan email "andi@contoh.com" dan password yang benar
    And pengguna menekan tombol "Masuk"
    Then pengguna diarahkan ke halaman dashboard
    And sistem menampilkan nama pengguna di header

  Scenario: Login gagal setelah 5 percobaan salah
    Given pengguna sudah gagal login 4 kali berturut-turut
    When pengguna memasukkan password salah untuk ke-5 kalinya
    Then akun dikunci selama 15 menit
    And sistem menampilkan pesan "Akun dikunci. Coba lagi setelah 15 menit."
    And sistem mengirim email notifikasi keamanan ke pengguna
```

## Format Rule-Based (Alternatif)

Untuk fitur yang lebih cocok dideskripsikan sebagai aturan, bukan skenario:

```markdown
### AC untuk User Story: Upload Foto Profil

**Rules:**
1. Format file yang diterima: JPG, PNG, WebP
2. Ukuran maksimal file: 5 MB
3. Resolusi minimal: 200x200 pixel
4. Gambar otomatis di-crop menjadi rasio 1:1 (square)
5. File lama otomatis dihapus saat upload file baru
6. Jika upload gagal, foto profil sebelumnya tetap ditampilkan

**Boundary conditions:**
- File selain format yang diterima → tampilkan error "Format file tidak didukung. Gunakan JPG, PNG, atau WebP."
- File melebihi 5 MB → tampilkan error "Ukuran file maksimal 5 MB."
- Resolusi di bawah 200x200 → tampilkan error "Resolusi minimal 200x200 pixel."
```

## Menulis AC yang Baik

### Prinsip SMART untuk AC

| Prinsip | Penjelasan | Contoh Buruk | Contoh Baik |
|---|---|---|---|
| **Specific** | Jelas dan tidak ambigu | "Halaman harus cepat" | "Halaman loading dalam < 3 detik di koneksi 3G" |
| **Measurable** | Bisa diukur / diverifikasi | "Pengalaman pengguna bagus" | "Error rate di bawah 1% per 1000 request" |
| **Testable** | Bisa diuji secara objektif | "Sistem aman" | "Password di-hash menggunakan bcrypt dengan cost factor 12" |
| **Complete** | Mencakup happy path + edge case | "User bisa login" | "User bisa login + gagal login + akun terkunci + reset password" |
| **Independent** | Tidak bergantung pada story lain | "Setelah fitur X selesai..." | Setiap AC bisa diverifikasi mandiri |

### Tips Praktis

1. **Gunakan data konkret** — bukan "beberapa item" tapi "3 item" atau "maksimal 50 item"
2. **Sebutkan state awal** — Given yang jelas menghindari asumsi berbeda antar penguji
3. **Sertakan negative case** — apa yang terjadi saat input salah, koneksi putus, data kosong
4. **Hindari detail implementasi** — tulis *apa* yang terjadi, bukan *bagaimana* secara teknis
5. **Satu skenario = satu perilaku** — jangan gabung beberapa perilaku dalam satu skenario

## AC untuk Berbagai Tipe Requirement

### UI / Frontend

```markdown
**AC: Form Registrasi**
1. Field wajib: Nama Lengkap, Email, Password, Konfirmasi Password
2. Field wajib ditandai dengan asterisk (*) berwarna merah
3. Validasi realtime: email format valid, password minimal 8 karakter
4. Tombol "Daftar" disabled sampai semua field wajib terisi valid
5. Setelah submit berhasil, redirect ke halaman verifikasi email
6. Jika email sudah terdaftar, tampilkan error inline "Email sudah terdaftar"
7. Form accessible: semua field punya label, error terhubung via aria-describedby
```

### API / Backend

```markdown
**AC: Endpoint GET /api/v1/orders**
1. Return HTTP 200 dengan array JSON berisi daftar order milik user yang terautentikasi
2. Support pagination: query param `page` (default 1) dan `limit` (default 20, max 100)
3. Support filter: `status=PAID|UNPAID|CANCELLED`, `date_from`, `date_to`
4. Response time < 500ms untuk 1000 records
5. Return HTTP 401 jika token tidak valid atau expired
6. Return HTTP 400 jika parameter limit > 100
7. Data order milik user lain TIDAK BOLEH muncul (tenant isolation)
```

### Business Logic

```markdown
**AC: Perhitungan Diskon Member**
1. Member Silver (6-12 bulan): diskon 5% dari subtotal
2. Member Gold (12-24 bulan): diskon 10% dari subtotal
3. Member Platinum (>24 bulan): diskon 15% dari subtotal
4. Diskon member TIDAK bisa digabung dengan kode promo
5. Jika ada kode promo, sistem otomatis memilih yang lebih menguntungkan bagi customer
6. Diskon dihitung sebelum pajak dan ongkos kirim
7. Minimum pembelian untuk diskon member: Rp 50.000
```

### Non-Functional Requirement (NFR)

```markdown
**AC: Performance – Halaman Produk**
1. Time to First Byte (TTFB): < 200ms (server-side)
2. Largest Contentful Paint (LCP): < 2.5 detik di 4G
3. Cumulative Layout Shift (CLS): < 0.1
4. Halaman tetap usable (bisa scroll, bisa klik) saat gambar masih loading
5. Graceful degradation: jika CDN down, gambar fallback ke origin server
```

## Siapa yang Menulis AC?

| Peran | Tanggung Jawab |
|---|---|
| **Product Owner** | Menulis draft AC berdasarkan kebutuhan bisnis |
| **Developer** | Menambahkan edge case teknis dan boundary condition |
| **QA Engineer** | Menambahkan negative case dan test scenario |
| **UX Designer** | Menambahkan AC terkait interaksi dan accessibility |

**Best practice:** AC ditulis kolaboratif saat **refinement session** (backlog grooming). PO bawa draft, tim review dan lengkapi bersama.

## Kapan Menulis AC?

1. **Saat backlog refinement** — sebelum story masuk sprint
2. **Sebelum sprint planning** — AC harus final agar estimasi akurat
3. **Bukan saat development** — terlambat menulis AC = risiko rework

Jika AC belum jelas saat planning, story **tidak boleh masuk sprint**. Tandai sebagai "Not Ready" dan refinement ulang.

## Checklist Review AC

Gunakan checklist ini sebelum story dianggap "Ready for Development":

- [ ] Setiap AC bisa dijawab YES/NO (testable)
- [ ] Happy path tercakup
- [ ] Minimal 1 negative/error case tercakup
- [ ] Boundary condition disebutkan (min, max, empty, null)
- [ ] Tidak ada jargon ambigu ("cepat", "aman", "user-friendly")
- [ ] Tidak ada detail implementasi (nama function, nama tabel DB)
- [ ] Konsisten dengan AC di story terkait (tidak konflik)
- [ ] Non-functional requirement disebutkan jika relevan (performance, security)
- [ ] Accessibility requirement disebutkan jika ada komponen UI baru

## Kesalahan Umum

| Kesalahan | Contoh | Cara Memperbaiki |
|---|---|---|
| **Terlalu vague** | "Sistem harus responsif" | "Halaman render dalam < 3 detik di 3G, layout tidak pecah di viewport 320px–1920px" |
| **Terlalu teknis** | "Data disimpan di tabel `users` kolom `status`" | "Status akun berubah menjadi Aktif setelah verifikasi email" |
| **Missing edge case** | Hanya happy path | Tambahkan: input kosong, timeout, concurrent access, data duplikat |
| **Terlalu banyak AC** | 25 AC dalam satu story | Pecah story menjadi beberapa story yang lebih kecil |
| **AC = test step** | "Klik tombol A, isi field B, tekan Enter" | Tulis *apa* yang terjadi, bukan langkah-langkah pengujian |
| **Copy-paste tanpa konteks** | AC generic dari template | Sesuaikan dengan konteks fitur dan domain bisnis |

## Dokumen Terkait

- [User Stories and Use Cases](./user-stories-and-use-cases.md) — format user story yang dilengkapi AC
- [Functional Requirements](./functional-requirements.md) — requirement yang diturunkan menjadi AC
- [PRD Guide](./prd-guide.md) — dokumen induk yang memuat konteks bisnis
