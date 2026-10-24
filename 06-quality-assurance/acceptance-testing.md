# Panduan User Acceptance Testing (UAT Guide)

User Acceptance Testing (UAT) adalah tahap pengujian formal di mana pengguna bisnis, pemilik produk (*Product Owner*), atau perwakilan pengguna akhir memvalidasi bahwa sistem perangkat lunak telah memenuhi kebutuhan bisnis dan siap dirilis ke lingkungan produksi.

---

## 1. Siapa yang Menjalankan UAT?

Berbeda dengan unit test atau integration test yang dijalankan oleh developer, UAT dijalankan oleh:
- **Product Owner (PO) / Product Manager (PM)**: Memastikan implementasi fitur sesuai dengan visi produk dan menyelesaikan masalah bisnis.
- **Pengguna Bisnis Internal (Business Stakeholders)**: Tim operasional, keuangan, atau customer support yang akan mengoperasikan aplikasi sehari-hari.
- **Subjek Ahli (Subject Matter Experts - SME)**: Pakar domain kepatuhan (legal, kepatuhan finansial, akuntansi).
- **Kelompok Beta Tester**: Pengguna eksternal terpilih yang diundang untuk mencoba aplikasi sebelum peluncuran massal.
- **End-User Representative**: Klien perwakilan pada proyek agensi custom software development.

---

## 2. Prasyarat Memulai Sesi UAT (Pre-requisites)

Jangan memulai UAT jika fase teknis belum tuntas. UAT bukan tempat mencari kesalahan kompilasi atau typo tampilan yang sepele:

1. **Definition of Done (DoD) Teknis Terpenuhi**: Seluruh unit test, integration test, code review, dan static analysis lolos di CI.
2. **Lingkungan Staging Stabil**: Lingkungan uji bebas dari gangguan deployment yang sedang berlangsung (*deployment freeze* selama sesi uji).
3. **Data Uji Realistis & Tersanitasi**: Database staging harus memuat variasi data transaksi riil tanpa membocorkan data pribadi (*PII - Personally Identifiable Information*).
4. **Skenario Uji Disepakati**: Naskah skenario alur kerja telah didistribusikan kepada tester minimal 24 jam sebelum sesi dimulai.

---

## 3. Pembuatan Skenario UAT Berdasarkan Acceptance Criteria

Skenario UAT ditulis dalam bahasa bisnis yang lugas, bukan instruksi teknis kode:

```markdown
### Skenario UAT: Penerbitan Faktur Pajak Otomatis
- **Dasar Cerita Pengguna**: US-FIN-042 (Otomasi Faktur Akhir Bulan)
- **Tujuan Bisnis**: Tim Finance dapat mengunduh rekapan faktur tanpa perlu rekap Excel manual.
- **Alur Pengujian**:
  1. Masuk sebagai staf akuntansi (`finance.lead@example.com`).
  2. Buka menu Laporan Keuangan > Penjualan Bulan Berjalan.
  3. Pilih tombol 'Generate Faktur Elektronik'.
  4. Unduh berkas PDF yang dihasilkan.
- **Kriteria Keberhasilan**:
  - Kolom PPN terhitung tepat 11%.
  - Nomor Seri Faktur Pajak (NSFP) terisi otomatis dan tidak kembar.
  - Berkas PDF dapat dibuka dan format tabel rapi.
```

### Format Penulisan Skenario Berbasis Gherkin:
```gherkin
Feature: Pencairan Dana Penjualan (Payout)
  Scenario: Merchant mencairkan saldo yang telah melewati masa holding
    Given merchant memiliki saldo aktif sebesar Rp 5.000.000
    And rekening bank tujuan berstatus terverifikasi
    When merchant mengajukan pencairan sebesar Rp 2.000.000
    Then sistem memotong saldo aktif menjadi Rp 3.000.000
    And status transaksi tercatat sebagai PENDING_TRANSFER
    And email konfirmasi dikirimkan ke email terdaftar merchant
```

---

## 4. Perbandingan Pengujian: Alpha vs Beta Testing

| Kriteria | Alpha Testing | Beta Testing |
| :--- | :--- | :--- |
| **Audiens** | Karyawan internal, tim produk, tim QA di luar tim pembuat. | Pengguna riil eksternal, pelanggan awal (*early adopters*). |
| **Lingkungan** | Lingkungan staging atau sandbox internal. | Lingkungan produksi terbatas (*canary / feature flag*) atau TestFlight. |
| **Fokus Utama** | Menemukan cacat fungsi mayor dan kelengkapan skenario bisnis. | Menilai kepuasan pengguna, performa perangkat asli, dan kegunaan (*usability*). |
| **Kerahasiaan** | Tertutup, NDA internal perusahaan. | Dapat terbuka publik atau semi-tertutup. |

---

## 5. Klasifikasi Cacat UAT & Alur Triage

Ketika peserta UAT menemukan masalah, tim melakukan sesi *Triage Meeting* harian untuk mengkategorikan temuan:

```
                  +--------------------------------+
                  | Temuan Masalah Baru dalam UAT  |
                  +---------------+----------------+
                                  |
                   Apakah memblokir proses bisnis?
                               /     \
                             Ya       Tidak
                            /           \
                 [ BLOCKER / CRITICAL ]   [ DEFERRED / BACKLOG ]
                 Harus diperbaiki         Ditunda ke sprint berikutnya.
                 sebelum rilis lanjut.    Tidak memblokir go-live.
```

- **Blocker / Showstopper**: Perhitungan uang salah, data corrupt, atau halaman crash sehingga alur tidak selesai. Peluncuran ditunda hingga perbaikan diverifikasi ulang.
- **Deferred / Nice-to-have**: Penyesuaian warna tombol, penataan margin, atau usulan fitur tambahan baru. Dicatat di backlog berikutnya.

---

## 6. UAT untuk Aplikasi Internal vs Aplikasi Publik (B2C)

- **Aplikasi Internal (B2B / Internal Tools)**:
  - Fokus pada efisiensi kerja pengguna, kecepatan input form, keakuratan laporan angka, dan kompatibilitas peramban kantor.
  - Sesi UAT sering dipandu langsung (*guided walkthrough*) di ruang meeting.
- **Aplikasi Konsumen Publik (B2C Apps)**:
  - Fokus pada *onboarding*, kemudahan navigasi tanpa panduan, *error recovery*, dan performa pada berbagai spesifikasi ponsel serta jaringan internet lambat.

---

## 7. Panduan Praktis UAT untuk Solo Developer & Agensi Kecil

Bagi solo dev atau tim agensi kecil yang berhadapan langsung dengan klien:
1. **Buat Demo Singkat Berupa Video (Loom / Screen Recording)**: Pandu klien melihat alur utama sebelum mereka mencoba sendiri.
2. **Kunci Jangka Waktu Review (Testing Window)**: Tetapkan durasi UAT (misal: 3-5 hari kerja). Buat klausul tertulis: jika tidak ada respon koreksi dalam batas waktu tersebut, fitur disetujui otomatis.
3. **Gunakan Log Matriks Persetujuan**:

| ID Skenario | Fitur yang Diuji | Diuji Oleh | Status (Pass/Fail) | Catatan / Tiket Terkait |
| :--- | :--- | :--- | :---: | :--- |
| UAT-01 | Registrasi Akun Pembeli | Budi (PO) | PASS | Berjalan mulus |
| UAT-02 | Pembayaran Kartu Kredit | Siti (Finance) | FAIL | Terpotong dua kali (BUG-104) |
| UAT-03 | Export Laporan Transaksi | Budi (PO) | PASS | Sesuai format CSV |

---

## 8. Kriteria Sign-Off Formal & Keputusan Go / No-Go

Sesi UAT diakhiri dengan rapat keputusan **Go / No-Go**:
1. **0 Open Blocker Bugs**: Tidak ada satu pun cacat berstatus Blocker/Critical yang belum diselesaikan.
2. **Ketercakupan Skenario**: Minimal 95% skenario UAT berhasil lolos verifikasi.
3. **Dokumen Persetujuan (Sign-off Memo)**: Product Owner dan pimpinan unit bisnis menandatangani persetujuan tertulis sebagai bukti sah bahwa perangkat lunak diterima secara kontraktual.

### Contoh Format Sign-Off Memorandum:
```markdown
### Lembar Persetujuan Rilis (UAT Sign-Off Memo)
- Nama Proyek: Sistem Penagihan Otomatis v2.0
- Tanggal Pelaksanaan: 24 Oktober 2026
- Lingkungan Pengujian: https://uat.app.example.com
- Total Skenario Diuji: 28 Skenario (27 Lolos, 1 Gagal Minor)
- Status Cacat Terbuka: SEV1: 0 | SEV2: 0 | SEV3: 1 (Jadwal Sprint 44)

Pernyataan Persetujuan:
Dengan ini pihak bisnis menyetujui hasil pengujian dan memberikan izin peluncuran sistem ke lingkungan produksi.
- Disetujui oleh Product Owner: [ Nama & Tanda Tangan ]
- Disetujui oleh Head of Finance: [ Nama & Tanda Tangan ]
```

---

## 9. Kesalahan Umum (Common Mistakes)

1. **UAT Tanpa Skenario Jelas**: Mengundang pengguna bisnis dan hanya berkata "Silakan coba aplikasinya bebas". Ini memicu kebingungan dan pengujian yang tidak fokus.
2. **Memperlakukan UAT sebagai Sesi Bug Finding Awal**: Menyerahkan sistem mentah yang belum lolos unit/integration testing kepada stakeholder. Hal ini merusak reputasi tim engineer.
3. **Mengabaikan Tanda Tangan Sign-off**: Merilis aplikasi tanpa persetujuan formal, sehingga jika timbul komplain bisnis pasca-rilis, developer dijadikan kambing hitam.
4. **Scope Creep Terselubung**: Mengizinkan stakeholder menyisipkan permintaan fitur baru di tengah UAT dengan dalih "koreksi bug".
5. **Menggunakan Data Produksi Mentah**: Menyalin database produksi ke UAT tanpa sanitasi, yang melanggar aturan privasi data pribadi (GDPR / UU PDP) dan berisiko mengirim email notifikasi uji ke pelanggan asli.
6. **Tidak Mengunci Versi Selama UAT (No Freeze)**: Developer terus melakukan deployment perbaikan di tengah-tengah user sedang mengetes, membuat tester bingung karena antarmuka berubah tiba-tiba.

---

## 10. Dokumen Terkait & Template

- [Checklist Acceptance Testing](../templates/testing/acceptance-checklist.md) — Daftar periksa verifikasi UAT lengkap.
- [Definition of Done](./definition-of-done.md) — Syarat kesiapan kode sebelum diserahkan ke UAT.
- [Bug Reporting Standard](./bug-reporting.md) — Cara mencatat tiket cacat selama sesi pengujian berlangsung.
- [Release Readiness Guide](../07-delivery-operations/release-readiness.md) — Protokol rilis pasca persetujuan UAT.
