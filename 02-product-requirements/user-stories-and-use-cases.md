# User Stories and Use Cases

## Pendahuluan

User stories dan use cases adalah dua cara mendeskripsikan kebutuhan dari perspektif pengguna. Keduanya saling melengkapi — bukan pengganti satu sama lain.

Dokumen ini menjelaskan kapan pakai yang mana, cara menulisnya, dan teknik memecah stories yang terlalu besar.

---

## 1. User Story

### Format Standar

```
Sebagai seorang [peran/role],
Saya ingin [melakukan sesuatu / goal],
Agar [manfaat yang didapat / benefit].
```

Versi Bahasa Inggris (sering dipakai di codebase):
```
As a [role],
I want [goal],
So that [benefit].
```

### Contoh User Stories

```
US-001: Sebagai pedagang pasar,
        saya ingin mencatat penjualan hanya dengan ketik nominal,
        agar pencatatan tidak mengganggu waktu melayani pembeli.

US-002: Sebagai pemilik warung,
        saya ingin melihat total penjualan hari ini,
        agar saya tahu apakah hari ini untung atau rugi.

US-003: Sebagai admin toko,
        saya ingin mengekspor laporan bulanan ke PDF,
        agar saya bisa serahkan ke akuntan tanpa perlu ketik ulang.
```

**Tips:** Bagian "agar/so that" sering dilewati — tapi ini bagian paling penting. Tanpa "agar", kamu tidak tahu **mengapa** fitur ini dibutuhkan.

---

## 2. Kriteria INVEST

Setiap user story harus memenuhi 6 kriteria INVEST. Jika tidak memenuhi, pecah atau tulis ulang.

### I — Independent (Independen)

Story harus bisa dikerjakan tanpa harus menyelesaikan story lain terlebih dahulu.

```
❌ Buruk (dependent):
US-A: "Sebagai user, saya ingin membuat akun"
US-B: "Sebagai user, saya ingin mengatur profil" (harus selesai setelah US-A)

✅ Lebih baik:
Gabungkan jika saling bergantung erat, atau pastikan US-B bisa dikerjakan
paralel dengan stub/mock untuk US-A.
```

### N — Negotiable (Bisa Dinegosiasi)

Story bukan kontrak fixed — detailnya bisa didiskusikan antara PM, developer, dan designer.

```
Story: "Sebagai user, saya ingin login"
Negosiasi: Login pakai email? Google? Phone? OTP?
→ Detail ditentukan saat grooming, bukan saat story ditulis.
```

### V — Valuable (Bernilai)

Story harus memberikan value ke user atau bisnis. Bukan task teknis.

```
❌ Bukan story (task teknis):
"Sebagai developer, saya ingin setup Redis agar caching berjalan"

✅ Story yang valuable:
"Sebagai pembeli, saya ingin halaman produk terbuka dalam 1 detik
agar saya tidak meninggalkan app"
(Redis mungkin solusinya, tapi itu implementation detail)
```

### E — Estimable (Bisa Diestimasi)

Tim harus bisa memperkirakan effort story ini. Jika tidak bisa — story terlalu besar atau terlalu vague.

```
❌ Tidak bisa diestimasi:
"Sebagai user, saya ingin sistem berjalan dengan baik"

✅ Bisa diestimasi:
"Sebagai user, saya ingin menerima notifikasi push
saat transaksi saya berhasil disimpan"
```

### S — Small (Cukup Kecil)

Story harus bisa selesai dalam 1 sprint (biasanya 1-2 minggu). Jika lebih besar — pecah.

```
❌ Terlalu besar (epic):
"Sebagai user, saya ingin fitur laporan keuangan lengkap"

✅ Dipecah:
US-A: "Saya ingin melihat ringkasan penjualan hari ini"
US-B: "Saya ingin melihat grafik penjualan mingguan"
US-C: "Saya ingin mengekspor laporan bulanan ke PDF"
```

### T — Testable (Bisa Diuji)

Story harus punya acceptance criteria yang jelas sehingga QA bisa verifikasi.

```
❌ Tidak testable:
"Sebagai user, saya ingin pengalaman yang menyenangkan"

✅ Testable:
"Sebagai user, saya ingin melihat konfirmasi visual (centang hijau)
setelah transaksi berhasil disimpan"
```

---

## 3. Acceptance Criteria dalam Stories

Setiap story harus punya acceptance criteria. Gunakan format **Given-When-Then** (GWT):

```
Given [kondisi awal / precondition]
When  [aksi yang dilakukan user]
Then  [hasil yang diharapkan]
```

### Contoh Lengkap

```markdown
### US-001: Catat Penjualan Cepat

**Story:**
Sebagai pedagang pasar,
saya ingin mencatat penjualan hanya dengan ketik nominal,
agar pencatatan tidak mengganggu waktu melayani pembeli.

**Acceptance Criteria:**

AC-1: Happy path
  Given user sudah login dan berada di halaman utama
  When  user mengetik "25000" dan tap tombol Simpan
  Then  transaksi tersimpan dengan nominal Rp 25.000
  And   muncul toast "Transaksi tersimpan"
  And   transaksi muncul di riwayat hari ini

AC-2: Input kosong
  Given user di halaman utama
  When  user tap tombol Simpan tanpa mengisi nominal
  Then  muncul pesan error "Masukkan nominal transaksi"
  And   transaksi TIDAK tersimpan

AC-3: Input invalid
  Given user di halaman utama
  When  user mengetik huruf atau simbol (bukan angka)
  Then  sistem hanya menerima input angka (keyboard numerik)

AC-4: Offline mode
  Given device tidak ada koneksi internet
  When  user mencatat transaksi
  Then  transaksi tersimpan secara lokal
  And   muncul indikator "Belum di-sync"
```

---

## 4. Teknik Story Splitting

Story terlalu besar? Pecah dengan teknik berikut:

### 4.1 Split by Workflow Step

```
Epic: "Sebagai user, saya ingin membeli produk"

Split:
US-A: "Saya ingin menambahkan produk ke keranjang"
US-B: "Saya ingin melihat isi keranjang dan total harga"
US-C: "Saya ingin melakukan pembayaran"
US-D: "Saya ingin menerima konfirmasi pesanan"
```

### 4.2 Split by Business Rule Variation

```
Epic: "Sebagai user, saya ingin mendapat diskon"

Split:
US-A: "Saya ingin mendapat diskon 20% jika pengguna baru (< 7 hari)"
US-B: "Saya ingin mendapat diskon 10% jika belanja > Rp 100.000"
US-C: "Saya ingin mendapat free shipping jika belanja > Rp 200.000"
```

### 4.3 Split by Data Type / Input

```
Epic: "Sebagai user, saya ingin login"

Split:
US-A: "Saya ingin login dengan email dan password"
US-B: "Saya ingin login dengan Google account"
US-C: "Saya ingin login dengan nomor telepon dan OTP"
```

### 4.4 Split by Happy Path vs Edge Cases

```
US-A (happy path): "Saya ingin membayar dengan transfer bank"
US-B (edge case):  "Saya ingin retry pembayaran jika transfer gagal"
US-C (edge case):  "Saya ingin diberi tahu jika pembayaran expired"
```

### 4.5 Split by Platform

```
US-A: "Saya ingin catat transaksi di Android"
US-B: "Saya ingin catat transaksi di iOS"
US-C: "Saya ingin catat transaksi di web"
```

**Rule of thumb:** Jika estimasi story > 8 story points (atau > 3 hari kerja), pecah.

---

## 5. Story Mapping

Story mapping adalah teknik visual untuk mengorganisasi stories berdasarkan **alur user** dan **prioritas rilis**.

```
Alur User (horizontal):
───────────────────────────────────────────────────────
  Registrasi → Login → Catat Transaksi → Lihat Laporan
───────────────────────────────────────────────────────

Prioritas (vertical, atas = paling penting):

           Registrasi    Login          Catat           Laporan
Release 1: Email signup   Email login    Input nominal   Total hari ini
           ─────────────────────────────────────────────────────────
Release 2: Google signup  Google login   Pilih kategori  Grafik mingguan
           ─────────────────────────────────────────────────────────
Release 3: Phone signup   Biometric      Edit transaksi  Export PDF
```

**Cara pakai:**
1. Susun aktivitas utama user secara horizontal (backbone)
2. Di bawah setiap aktivitas, susun stories dari prioritas tinggi (atas) ke rendah (bawah)
3. Tarik garis horizontal untuk menandai batas tiap release
4. Semua stories di atas garis Release 1 = MVP

---

## 6. Use Case

Use case cocok untuk **proses multi-langkah yang kompleks** dengan banyak percabangan. Lebih formal dari user story.

### Format Use Case

```markdown
## UC-001: Pembayaran Pesanan

**Judul:** Pembayaran Pesanan via Transfer Bank
**Aktor Utama:** Pembeli
**Aktor Sekunder:** Payment Gateway, Sistem Bank
**Level:** User Goal

**Preconditions:**
- Pembeli sudah login
- Keranjang belanja tidak kosong
- Alamat pengiriman sudah dipilih

**Postconditions (Success):**
- Pesanan tercatat dengan status "Dibayar"
- Stok produk berkurang
- Email konfirmasi terkirim ke pembeli
- Notifikasi terkirim ke penjual

**Main Flow (Happy Path):**
1. Pembeli memilih metode pembayaran "Transfer Bank"
2. Sistem menampilkan nomor Virtual Account dan total bayar
3. Sistem memulai countdown 24 jam
4. Pembeli melakukan transfer via mobile banking
5. Payment gateway mengirim callback "payment received"
6. Sistem memverifikasi nominal pembayaran sesuai total
7. Sistem mengubah status pesanan menjadi "Dibayar"
8. Sistem mengirim email konfirmasi ke pembeli
9. Sistem mengirim notifikasi ke penjual

**Alternative Flows:**
3a. Pembeli memilih metode lain sebelum transfer:
    3a.1. Sistem membatalkan Virtual Account sebelumnya
    3a.2. Kembali ke langkah 1

6a. Nominal tidak sesuai (kurang bayar):
    6a.1. Sistem menandai pembayaran sebagai "Pending Review"
    6a.2. Admin menerima notifikasi untuk verifikasi manual

**Exception Flows:**
3b. Countdown habis (24 jam) dan belum ada pembayaran:
    3b.1. Sistem membatalkan pesanan otomatis
    3b.2. Stok produk dikembalikan
    3b.3. Sistem mengirim notifikasi "Pesanan dibatalkan" ke pembeli

5a. Payment gateway timeout / error:
    5a.1. Sistem retry 3x dengan interval 30 detik
    5a.2. Jika tetap gagal, masukkan ke antrian manual reconciliation
    5a.3. Admin menerima alert
```

---

## 7. Kapan Pakai Story vs Use Case?

| Situasi | Gunakan |
|---------|---------|
| Fitur sederhana, 1-2 langkah | User Story |
| Tim Agile, iterasi cepat | User Story |
| Sprint planning dan backlog | User Story |
| Proses kompleks, banyak percabangan | Use Case |
| Integrasi antar sistem | Use Case |
| Regulasi yang butuh dokumentasi formal | Use Case |
| Komunikasi dengan stakeholder non-teknis | User Story |
| Training QA untuk test scenario | Use Case |

**Tips:** Bisa pakai keduanya. Tulis user story untuk backlog, lalu buat use case detail untuk proses yang kompleks.

---

## 8. Contoh Lengkap: Dari Story ke Use Case

### User Story

```
US-005: Sebagai pembeli,
        saya ingin membayar pesanan via transfer bank,
        agar saya bisa menyelesaikan pembelian tanpa kartu kredit.
```

### Acceptance Criteria (GWT)

```
AC-1: Given pembeli di halaman checkout
      When  memilih "Transfer Bank" dan tap "Bayar"
      Then  muncul nomor Virtual Account dan total bayar

AC-2: Given Virtual Account sudah ditampilkan
      When  pembeli transfer nominal yang sesuai
      Then  status pesanan berubah ke "Dibayar" dalam < 5 menit

AC-3: Given 24 jam terlewati tanpa pembayaran
      When  countdown habis
      Then  pesanan otomatis dibatalkan dan stok dikembalikan
```

### Use Case Detail

→ Lihat UC-001 di section 6 di atas — itu adalah elaborasi dari US-005.

**Pola:** Story → Acceptance Criteria → Use Case (jika perlu detail flow).

---

## 9. Kesalahan Umum

| Kesalahan | Contoh | Solusi |
|-----------|--------|--------|
| Skip "so that / agar" | "Sebagai user, saya ingin login" (titik) | Selalu tulis benefit — ini menjelaskan MENGAPA |
| Story terlalu besar | "Saya ingin fitur e-commerce lengkap" | Pecah pakai teknik splitting di section 4 |
| Story = task teknis | "Sebagai dev, saya ingin refactor database" | Story harus deliver value ke user. Refactor = task, bukan story |
| Acceptance criteria vague | "Berjalan dengan baik" | Pakai Given-When-Then dengan kondisi spesifik |
| Use case tanpa exception flow | Hanya tulis happy path | Selalu pikirkan: apa yang bisa gagal? |
| Role terlalu generic | "Sebagai user" untuk semua story | Spesifik: "sebagai pembeli", "sebagai penjual", "sebagai admin" |
| Langsung coding tanpa criteria | "Kan udah jelas ya..." | Tulis AC. Definisi "jelas" berbeda tiap orang |
| Campur requirement dan implementasi | "Saya ingin data disimpan di PostgreSQL" | Story = kebutuhan user, bukan pilihan teknologi |

---

## 10. Checklist

### User Story Checklist
- [ ] Format "Sebagai... saya ingin... agar..." lengkap
- [ ] Memenuhi semua 6 kriteria INVEST
- [ ] Acceptance criteria ditulis (minimal 2: happy path + 1 edge case)
- [ ] Story cukup kecil (selesai dalam 1 sprint)
- [ ] Role/persona spesifik (bukan generic "user")

### Use Case Checklist
- [ ] Aktor teridentifikasi jelas
- [ ] Preconditions dan postconditions ditulis
- [ ] Main flow langkah-langkahnya berurutan dan jelas
- [ ] Alternative flows dipertimbangkan
- [ ] Exception flows (error handling) sudah ada
- [ ] Setiap langkah punya satu aksi jelas

---

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — stories diturunkan dari requirements di PRD
- [Functional Requirements](./functional-requirements.md) — stories dipecah jadi FR detail
- [Business Rules](./business-rules.md) — stories mereferensi business rules
- [Acceptance Criteria](./acceptance-criteria.md) — panduan detail menulis acceptance criteria
- [Prioritization](./prioritization.md) — cara prioritas stories di backlog
