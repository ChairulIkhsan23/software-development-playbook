# Business Rules

## Pendahuluan

Business rules adalah **kebijakan, batasan, dan logika keputusan** yang berasal dari domain bisnis — bukan dari kebutuhan teknis. Business rules ada **sebelum** software dibuat dan akan tetap berlaku meskipun software-nya diganti.

Contoh: "Diskon 20% untuk pelanggan baru" adalah business rule. Ini berlaku baik dicatat di Excel, aplikasi, maupun kertas.

---

## 1. Business Rules vs Konsep Lain

| Konsep | Fokus | Contoh |
|--------|-------|--------|
| **Business Rule** | Kebijakan/logika bisnis | "Penarikan maks Rp 2 juta/hari tanpa KYC" |
| **Functional Requirement** | Perilaku sistem | "Sistem harus menolak penarikan jika melebihi limit harian" |
| **Validation Rule** | Constraint pada input field | "Nominal harus angka positif, maksimal 12 digit" |

**Hubungan:**
- Business rule → diterjemahkan menjadi satu atau lebih functional requirements
- Business rule bisa mempengaruhi validation rules
- Satu business rule bisa berlaku di banyak modul

```
Business Rule: BR-KYC-02
  "Akun tanpa KYC maks penarikan Rp 2 juta/hari"

→ FR-WDR-001: Sistem harus cek status KYC sebelum proses withdrawal
→ FR-WDR-002: Sistem harus cek total penarikan hari ini sebelum approve
→ FR-WDR-003: Sistem harus menampilkan pesan "Verifikasi KYC untuk limit lebih tinggi"
```

---

## 2. Tipe Business Rules

### 2.1 Calculation Rules (Perhitungan)

Rumus atau formula yang menentukan hasil kalkulasi.

```
BR-CALC-001: Pajak PPN
  Pajak PPN = 11% dari harga produk (sebelum diskon)
  Berlaku untuk semua produk kecuali kategori "Makanan Pokok"

BR-CALC-002: Biaya Pengiriman
  IF berat total ≤ 1 kg THEN biaya kirim = Rp 10.000
  IF berat total > 1 kg AND ≤ 5 kg THEN biaya kirim = Rp 10.000 + (Rp 5.000 × setiap kg tambahan)
  IF berat total > 5 kg THEN biaya kirim = Rp 30.000 (flat rate)
```

### 2.2 Constraint Rules (Batasan)

Batasan yang membatasi apa yang boleh dan tidak boleh dilakukan.

```
BR-CONS-001: Limit Transaksi Harian
  Maksimal 50 transaksi per user per hari kalender (00:00 - 23:59 WIB)

BR-CONS-002: Minimum Pembelian
  Pesanan tidak bisa diproses jika total belanja < Rp 10.000

BR-CONS-003: Pembatalan Pesanan
  Pesanan hanya bisa dibatalkan jika status masih "Menunggu Pembayaran"
  Pesanan dengan status "Diproses" atau setelahnya tidak bisa dibatalkan oleh pembeli
```

### 2.3 Authorization Rules (Otorisasi)

Siapa boleh melakukan apa.

```
BR-AUTH-001: Approval Refund
  IF nominal refund ≤ Rp 500.000 THEN CS Agent bisa approve langsung
  IF nominal refund > Rp 500.000 AND ≤ Rp 5.000.000 THEN butuh approval CS Supervisor
  IF nominal refund > Rp 5.000.000 THEN butuh approval Finance Manager

BR-AUTH-002: Akses Laporan Keuangan
  Hanya role "Owner" dan "Finance" yang bisa akses laporan keuangan
  Role "Staff" hanya bisa lihat laporan penjualan
```

### 2.4 Workflow Rules (Alur Proses)

Urutan proses dan transisi status yang harus diikuti.

```
BR-WF-001: Status Pesanan
  Pesanan harus mengikuti alur:
  Menunggu Pembayaran → Dibayar → Diproses → Dikirim → Selesai
  
  Transisi yang dilarang:
  - Tidak bisa loncat dari "Menunggu Pembayaran" ke "Dikirim"
  - "Selesai" tidak bisa kembali ke status manapun

BR-WF-002: Verifikasi KYC
  Dokumen KYC harus melalui: Upload → Pending Review → Approved/Rejected
  Jika Rejected, user bisa re-upload (kembali ke Upload)
  Maksimal 3x percobaan, setelah itu butuh manual review oleh compliance team
```

### 2.5 Temporal Rules (Waktu)

Aturan yang bergantung pada waktu.

```
BR-TIME-001: Masa Berlaku Promo
  Kode promo berlaku dari tanggal mulai 00:00 WIB sampai tanggal akhir 23:59 WIB
  Kode promo yang sudah expired tidak bisa digunakan

BR-TIME-002: Auto-cancel Pesanan
  Pesanan dengan status "Menunggu Pembayaran" otomatis dibatalkan
  setelah 24 jam sejak pesanan dibuat
```

---

## 3. Format Dokumentasi Business Rule

### Format Detail (Recommended)

```markdown
### BR-DISC-001: Diskon Pengguna Baru

**Nama:** Diskon First Purchase untuk Pengguna Baru
**Tipe:** Calculation + Constraint
**Versi:** v1.2
**Terakhir Diubah:** 2025-03-15
**Sumber:** Keputusan meeting Product & Marketing, 2025-01-10

**Logika:**
IF    akun user dibuat ≤ 7 hari kalender yang lalu
AND   user belum pernah melakukan transaksi berhasil
THEN  berikan diskon 20% dari subtotal
      dengan maksimal potongan Rp 50.000

**Exceptions:**
- Tidak berlaku untuk produk kategori "Digital Goods"
- Tidak bisa digabung dengan promo lain
- Diskon dihitung dari harga sebelum pajak

**Modul Terkait:**
- Checkout Service
- Promo Engine
- Order Summary UI

**FR Terkait:** FR-PROMO-001, FR-PROMO-002, FR-CHK-003
**Test Case:** TC-PROMO-001a, TC-PROMO-001b, TC-PROMO-001c
```

### Format Tabel (Untuk Overview)

| ID | Nama | Logika (Ringkas) | Tipe | Modul | Prioritas |
|---|------|-------------------|------|-------|-----------|
| BR-DISC-001 | Diskon Pengguna Baru | User ≤ 7 hari → diskon 20% maks Rp 50K | Calc | Checkout, Promo | Must Have |
| BR-KYC-002 | Limit Penarikan | Tanpa KYC → maks Rp 2 juta/hari | Constraint | Wallet, Withdrawal | Must Have |
| BR-PASS-003 | Penguncian Akun | 5x gagal login → kunci 15 menit | Constraint | Auth | Must Have |
| BR-AUTH-001 | Approval Refund | ≤ 500K: CS. > 500K: Supervisor | Authorization | Refund | Should Have |

---

## 4. Konvensi Penamaan ID

Format: `BR-[CATEGORY]-[NNN]`

| Prefix | Kategori | Contoh |
|--------|----------|--------|
| BR-CALC | Calculation | BR-CALC-001 |
| BR-CONS | Constraint | BR-CONS-001 |
| BR-AUTH | Authorization | BR-AUTH-001 |
| BR-WF | Workflow | BR-WF-001 |
| BR-TIME | Temporal | BR-TIME-001 |
| BR-DISC | Discount/Promo | BR-DISC-001 |
| BR-KYC | Know Your Customer | BR-KYC-001 |
| BR-PASS | Password/Security | BR-PASS-001 |

Alternatif untuk proyek kecil — gunakan kode modul: `BR-[MODULE]-[NNN]` (contoh: BR-CHK-001 untuk Checkout).

---

## 5. Organisasi Katalog Business Rules

### Struktur Folder (Proyek Besar)

```
business-rules/
├── catalog.md              ← Tabel semua rules (overview)
├── calculation/
│   ├── BR-CALC-001.md      ← Detail per rule
│   └── BR-CALC-002.md
├── constraints/
│   ├── BR-CONS-001.md
│   └── BR-CONS-002.md
├── authorization/
│   └── BR-AUTH-001.md
└── workflows/
    ├── BR-WF-001.md
    └── BR-WF-002.md
```

### Single File (Proyek Kecil / Solo Dev)

Untuk proyek kecil, cukup satu file `business-rules.md` dengan section per kategori. Tidak perlu over-engineer struktur folder kalau rules masih < 30 item.

---

## 6. Versioning dan Change Tracking

Business rules sering berubah (promo baru, regulasi update, kebijakan internal berubah). Tracking perubahan itu krusial.

### Changelog per Rule

```markdown
### Changelog BR-DISC-001

| Versi | Tanggal    | Perubahan                              | Diputuskan Oleh  |
|-------|------------|----------------------------------------|-------------------|
| v1.0  | 2025-01-10 | Initial: diskon 15% maks Rp 30.000    | Meeting Product   |
| v1.1  | 2025-02-01 | Naikkan diskon ke 20% maks Rp 50.000  | Email VP Marketing|
| v1.2  | 2025-03-15 | Exclude kategori Digital Goods         | Ticket JIRA-456   |
```

### Tips Versioning

1. **Selalu catat sumber keputusan** — siapa yang memutuskan, kapan, di meeting/email/ticket apa
2. **Jangan hapus versi lama** — arsipkan. Kamu mungkin perlu tahu kenapa rule berubah
3. **Notify tim engineering** saat rule berubah — jangan asumsi mereka baca dokumen
4. **Tag rule yang sering berubah** — promo rules biasanya volatile, core business rules biasanya stabil

---

## 7. Menulis IF-THEN-ELSE yang Jelas

### Template

```
IF    [kondisi 1]
AND   [kondisi 2 (opsional)]
OR    [kondisi alternatif (opsional)]
THEN  [aksi/hasil]
ELSE  [aksi/hasil alternatif]
```

### Tips

1. **Satu rule, satu keputusan.** Jangan gabung banyak keputusan dalam satu rule.

```
❌ Buruk (terlalu banyak logika):
IF user baru AND belanja > 100K THEN diskon 20%
ELSE IF user lama AND belanja > 200K THEN diskon 10%
ELSE IF user premium THEN free shipping

✅ Baik (dipecah):
BR-DISC-001: IF user baru (≤ 7 hari) THEN diskon 20% maks Rp 50K
BR-DISC-002: IF belanja > Rp 200K AND user lama THEN diskon 10%
BR-SHIP-001: IF user premium THEN free shipping
```

2. **Definisikan setiap istilah.** "User baru" itu apa? Sejak registrasi atau sejak verifikasi?
3. **Cover ELSE.** Selalu tulis apa yang terjadi jika kondisi TIDAK terpenuhi.
4. **Gunakan tabel keputusan** untuk rule dengan banyak kombinasi kondisi:

```
| Status KYC | Nominal Penarikan | Hasil |
|------------|-------------------|-------|
| Verified   | ≤ Rp 50 juta      | Approve otomatis |
| Verified   | > Rp 50 juta      | Butuh approval manager |
| Unverified | ≤ Rp 2 juta       | Approve otomatis |
| Unverified | > Rp 2 juta       | Tolak, minta KYC dulu |
```

---

## 8. Testing Business Rules

Setiap business rule harus punya test case. Business rule yang tidak di-test = bug yang menunggu waktu.

### Mapping Rule ke Test

```
BR-DISC-001: Diskon Pengguna Baru (20% maks Rp 50K jika akun ≤ 7 hari)

Test Cases:
TC-BR-DISC-001a: Akun umur 3 hari, belanja Rp 100.000
                 → Expected: diskon Rp 20.000 (20% × 100K)

TC-BR-DISC-001b: Akun umur 3 hari, belanja Rp 500.000
                 → Expected: diskon Rp 50.000 (cap tercapai, bukan Rp 100K)

TC-BR-DISC-001c: Akun umur 8 hari, belanja Rp 100.000
                 → Expected: diskon Rp 0 (tidak eligible)

TC-BR-DISC-001d: Akun umur 3 hari, sudah pernah transaksi
                 → Expected: diskon Rp 0 (bukan first purchase)

TC-BR-DISC-001e: Akun umur 3 hari, beli produk Digital Goods
                 → Expected: diskon Rp 0 (excluded category)

TC-BR-DISC-001f: Akun umur 7 hari tepat (boundary)
                 → Expected: diskon berlaku (≤ 7 hari = termasuk hari ke-7)
```

### Boundary Testing

Untuk setiap angka di business rule, test minimal:
- **Tepat di batas** (boundary value)
- **Satu di bawah batas** (boundary - 1)
- **Satu di atas batas** (boundary + 1)

```
Rule: "Maks penarikan Rp 2.000.000/hari tanpa KYC"

Test:
- Tarik Rp 1.999.999 → Approve ✓
- Tarik Rp 2.000.000 → Approve ✓ (tepat di batas)
- Tarik Rp 2.000.001 → Reject ✗
- Tarik Rp 1.000.000 dua kali → kedua approve ✓ (total = 2 juta)
- Tarik Rp 1.000.000 lalu Rp 1.500.000 → kedua reject ✗ (total > 2 juta)
```

---

## 9. Kesalahan Umum

| Kesalahan | Contoh | Solusi |
|-----------|--------|--------|
| Rule hanya di kepala orang | "Oh itu kan semua tahu" | Dokumentasikan. Orang resign, rule hilang |
| Rule ambigu | "Diskon untuk pembelian besar" | Definisikan: besar = berapa? |
| Tidak ada ELSE | "IF VIP THEN free shipping" (non-VIP bagaimana?) | Selalu tulis ELSE |
| Rule conflict | BR-01: diskon maks 1. BR-02: promo stackable | Tentukan priority dan conflict resolution |
| Tidak track versi | Rule berubah tapi tidak tahu kapan/kenapa | Selalu catat changelog + sumber keputusan |
| Campur business rule dan teknis | "IF user VIP THEN query pakai index khusus" | Business rule = WHAT. Technical = HOW |
| Boundary tidak di-test | "Maks 5x" — tapi yang ke-5 gimana? Masuk atau tidak? | Gunakan operator eksplisit: `≤ 5` atau `< 5` |
| Rule tersebar di banyak tempat | Sedikit di PRD, sedikit di Confluence, sedikit di Slack | Satu katalog sebagai single source of truth |

---

## 10. Checklist Business Rules

- [ ] Setiap rule punya ID unik sesuai konvensi
- [ ] Logika ditulis dalam format IF-THEN-ELSE yang eksplisit
- [ ] ELSE case selalu dituliskan
- [ ] Sumber keputusan tercatat (siapa, kapan, dimana)
- [ ] Exceptions dan edge cases sudah didokumentasikan
- [ ] Boundary values sudah jelas (gunakan ≤, <, ≥, > secara eksplisit)
- [ ] Modul/komponen terkait sudah diidentifikasi
- [ ] Test cases sudah dibuat termasuk boundary testing
- [ ] Changelog ada untuk rules yang pernah berubah
- [ ] Tidak ada conflict antar rules (atau conflict sudah ada resolution-nya)

---

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — PRD mereferensi business rules
- [Functional Requirements](./functional-requirements.md) — FR mengimplementasikan business rules
- [User Stories & Use Cases](./user-stories-and-use-cases.md) — stories mereferensi rules yang berlaku
- [Acceptance Criteria](./acceptance-criteria.md) — criteria menguji kepatuhan terhadap business rules
- [Requirements Change Management](./requirements-change-management.md) — proses mengubah rules yang sudah approved
