# Product Requirements Document (PRD): DompetKu

```yaml
document_id: DOC-PRD-001
title: Product Requirements Document - DompetKu MVP
version: 1.0.0
status: Approved
owner: "Product Management Team"
created_at: 2026-03-15
updated_at: 2026-03-20
target_release: 2026-05-01
```

---

## 1. Ringkasan Eksekutif & Latar Belakang

DompetKu adalah aplikasi pencatatan keuangan pribadi (*personal finance tracker*) berbasis mobile yang dirancang khusus untuk memecahkan friksi pencatatan harian bagi dewasa muda di Indonesia.

Berdasarkan riset wawancara pengguna awal, 68% responden berhenti menggunakan aplikasi pencatatan keuangan dalam 14 hari pertama karena antarmuka yang membingungkan dan waktu input yang terlalu lama (> 30 detik per entri). DompetKu menyederhanakan alur hingga input transaksi dapat diselesaikan dalam waktu kurang dari 10 detik.

---

## 2. Sasaran & Metrik Keberhasilan (OKRs)

### 2.1 Tujuan Bisnis
- Menghadirkan aplikasi pencatatan pengeluaran harian dengan retensi minggu ke-4 (Day-28 Retention) di atas rata-rata industri (> 25%).

### 2.2 Metrik Kuantitatif
| Metrik | Baseline Industri | Target DompetKu MVP | Metode Pengukuran |
|---|---|---|---|
| **Waktu Input Transaksi** | 32 detik | < 10 detik | Telemetri durasi form (open to submit) |
| **D28 Retention Rate** | 14% | >= 25% | Cohort analytics pada active users |
| **Input Success Rate** | 88% | >= 98% | Ratio transaksi sukses dibanding transaksi gagal/abandoned |
| **Monthly Active Users (MAU)** | - | 5.000 pengguna aktif | Database user metrics akhir bulan ke-3 |

---

## 3. Target Pengguna (User Persona)

### Persona Utama: Andi Pratama (24 Tahun, First-jobber)
- **Profil**: Bekerja sebagai Junior Software Developer di Jakarta. Memiliki 1 rekening bank utama dan 2 akun e-wallet.
- **Pain Point**: Sering kehabisan gaji di akhir bulan tanpa tahu ke mana uang mengalir. Pernah mencoba aplikasi lain namun malas karena harus mengisi terlalu banyak form field.
- **Kebutuhan**: Mencatat jajan dan makan siang seketika setelah bayar via QRIS dengan 3 ketukan layar.

---

## 4. Ruang Lingkup Produk

### 4.1 In-Scope (MVP)
1. Autentikasi aman berbasis email & password dengan JWT.
2. Manajemen akun sumber dana (Kas Tunai, Rekening Bank, E-Wallet).
3. Pencatatan transaksi (Pemasukan & Pengeluaran) beserta pemilihan kategori.
4. Riwayat transaksi dengan penyaringan tanggal dan kategori.
5. Alokasi anggaran bulanan per kategori dengan indikator visual.
6. Ringkasan saldo total dan pengeluaran per bulan berjalan.

### 4.2 Out-of-Scope (Roadmap v2 & v3)
- Sinkronisasi otomatis mutasi bank melalui Open Finance API (v2).
- Pemindaian struk berbasis Optical Character Recognition (OCR) (v2).
- Multi-currency dan konversi kurs valuta asing (v3).
- Fitur split-bill dan transfer antar pengguna (v3).

---

## 5. Functional Requirements (FR)

| ID | Nama Fitur | Deskripsi | Prioritas |
|---|---|---|---|
| `FR-AUTH-001` | Registrasi & Login | Pengguna dapat mendaftar dan masuk menggunakan email valid dan password minimal 8 karakter. | Must-Have |
| `FR-ACCT-001` | Kelola Akun Saldo | Pengguna dapat membuat akun sumber dana (tipe: Cash, Bank, E-Wallet) dan saldo awal. | Must-Have |
| `FR-TRANS-001` | Input Transaksi Cepat | Pengguna dapat mencatat transaksi (jumlah, jenis, kategori, akun, catatan opsional) dalam 1 layar. | Must-Have |
| `FR-TRANS-002` | Riwayat Transaksi | Pengguna dapat melihat daftar transaksi historis terurut kronologis dengan pagination. | Must-Have |
| `FR-TRANS-003` | Filter Transaksi | Pengguna dapat menyaring transaksi berdasarkan rentang tanggal dan kategori. | Should-Have |
| `FR-BUDGET-001` | Anggaran Kategori | Pengguna dapat menetapkan batas anggaran maksimal untuk kategori tertentu per bulan. | Must-Have |
| `FR-DASH-001` | Ringkasan Finansial | Menampilkan total saldo gabungan seluruh akun dan total pengeluaran bulan berjalan di layar beranda. | Must-Have |

---

## 6. Business Rules (BR)

| ID | Aturan Bisnis | Logika Penegakan |
|---|---|---|
| `BR-TRANS-001` | Nilai transaksi wajib positif | Nilai nominal (`amount`) harus berupa angka lebih besar dari nol (`amount > 0`). Nilai nol atau negatif ditolak dengan error 422. |
| `BR-TRANS-002` | Dampak terhadap saldo akun | Transaksi tipe `EXPENSE` mengurangi saldo akun terkait. Tipe `INCOME` menambah saldo akun. |
| `BR-TRANS-003` | Validasi kepemilikan data | Pengguna hanya dapat mengakses, mengubah, dan menghapus transaksi dan akun milik sendiri (isolasi data pengguna). |
| `BR-BUDGET-001` | Status ambang anggaran | Sistem menghitung persentase pemakaian anggaran kategori: Hijau (< 80%), Kuning (80%–99%), Merah (>= 100% / Overbudget). |

---

## 7. Acceptance Criteria (Contoh Terpilih)

### Skenario 1: Pencatatan Transaksi Pengeluaran Berhasil
```gherkin
Scenario: Pencatatan transaksi pengeluaran valid
  Given Pengguna telah login dan memiliki akun "BCA" dengan saldo Rp 500.000
  When Pengguna memasukkan nominal Rp 35.000, tipe "EXPENSE", kategori "Makanan & Minuman", dan akun "BCA"
  And Pengguna menekan tombol "Simpan"
  Then Sistem menyimpan transaksi baru
  And Saldo akun "BCA" berkurang menjadi Rp 465.000
  And Transaksi muncul di posisi teratas daftar riwayat transaksi
```

### Skenario 2: Penolakan Transaksi Nilai Nol atau Negatif
```gherkin
Scenario: Validasi nominal pengeluaran tidak valid
  Given Pengguna berada di layar tambah transaksi
  When Pengguna memasukkan nominal 0 atau -15000
  Then Tombol simpan tetap disabled
  And Muncul pesan peringatan "Nominal transaksi harus lebih besar dari 0"
```

---

## 8. Persyaratan Non-Fungsional Kunci (NFR Reference)

- **Performa**: Waktu respon API p95 untuk logging transaksi harus < 200 milidetik pada koneksi 4G standar.
- **Ketersediaan**: Target ketersediaan layanan backend minimal 99,5% per bulan kalender.
- **Keamanan Data**: Seluruh transmisi data dienkripsi dengan HTTPS (TLS 1.3). Token autentikasi menggunakan RS256 JWT dengan masa kedaluwarsa ketat.
- **Dukungan Platform**: Berjalan optimal pada Android versi 10.0+ dan iOS 15.0+ dengan resolusi minimum 360x640 px.

---

## 9. Dependensi dan Risiko

| Dependensi / Risiko | Dampak | Rencana Mitigasi |
|---|---|---|
| Keterlambatan approval rilis Google Play Store / Apple App Store | Peluncuran tertunda 3–7 hari | Submit aplikasi versi review 10 hari sebelum target rilis publik. |
| Pengguna kehilangan koneksi saat input transaksi | Data gagal tersimpan, frustrasi user | Siapkan penyimpanan lokal sementara (SQLite/WatermelonDB) dengan sinkronisasi otomatis saat online kembali. |

---

## 10. Dokumen Terkait

- Arsitektur Teknis: [`architecture.md`](./architecture.md)
- Spesifikasi REST API: [`api-spec.md`](./api-spec.md)
- Template PRD Referensi: [`../../templates/product/prd-template.md`](../../templates/product/prd-template.md)
