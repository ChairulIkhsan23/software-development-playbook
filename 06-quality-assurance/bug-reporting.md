# Standar Pelaporan Bug (Bug Reporting Standard)

Laporan bug yang efektif adalah jembatan komunikasi antara pelapor (QA, pengguna, atau developer lain) dengan tim teknis yang bertugas memperbaikinya. Tiket bug yang ditulis dengan buruk menyebabkan pemborosan waktu investigasi dan perdebatan bolak-balik (*bug ping-pong*).

---

## 1. Anatomi Laporan Bug yang Sempurna

Sebuah laporan bug yang berkualitas wajib memuat komponen-komponen berikut:

1. **Judul Singkat & Informatif**: Format `[Modul/Komponen] Kondisi kegagalan saat aksi tertentu` (contoh: `[Checkout] Tombol Bayar tidak merespon saat menggunakan kupon diskon 100%`).
2. **Tingkat Keparahan (Severity) & Prioritas (Priority)**: Menentukan urgensi teknis dan bisnis.
3. **Detail Lingkungan (Environment)**:
   - URL / Aplikasi: Staging, Production (v1.4.2).
   - Perangkat / OS: Windows 11, macOS Sonoma, Android 14.
   - Browser / Runtime: Chrome 124, Safari 17.4.
4. **Prasyarat (Preconditions)**: Kondisi akun atau data yang harus dipenuhi sebelum memulai (contoh: *User sudah login dengan akun Member Silver dan memiliki 1 produk di keranjang*).
5. **Langkah-Langkah Mereproduksi (Steps to Reproduce - STR)**: Urutan aksi bernomor 1, 2, 3 yang spesifik dan deterministik.
6. **Hasil yang Diharapkan (Expected Result)**: Perilaku yang seharusnya terjadi sesuai spesifikasi produk.
7. **Hasil Aktual (Actual Result)**: Apa yang sebenarnya terjadi (disertai pesan error eksplisit).
8. **Bukti Pendukung (Evidence)**: Tangkapan layar (*screenshot*), rekaman layar (.mp4 / GIF), kutipan log konsol peramban, atau kutipan log server.
9. **Dampak Bisnis (Business Impact)**: Siapa yang terdampak dan seberapa besar konsekuensi finansial atau operasionalnya.

---

## 2. Contoh Konkret Laporan Bug Lengkap

```markdown
### [Auth] Gagal Login Menggunakan Akun Google SSO pada Safari iOS
- **Severity**: SEV2 (Critical)
- **Priority**: P1 (High)
- **Environment**: Production (v2.1.0), iPhone 14 Pro, iOS 17.4, Mobile Safari
- **Preconditions**: Akun Google belum pernah ditautkan sebelumnya ke platform.

**Langkah-langkah Mereproduksi:**
1. Buka https://app.example.com/login di Safari iOS.
2. Ketuk tombol 'Masuk dengan Google'.
3. Pilih akun Google di popup otorisasi Google.
4. Setujui permintaan perizinan profil.

**Hasil yang Diharapkan:**
User dialihkan kembali ke /dashboard dengan status sudah login.

**Hasil Aktual:**
Layar browser kembali ke /login dengan alert error: "Cross-site cookie blocked". User tetap logout.

**Bukti Pendukung:**
- Rekaman layar: safari-sso-failure.mp4 terlampir.
- Log Konsol Safari: `Unhandled Promise Rejection: SecurityError: The operation is insecure.`

**Dampak Bisnis:**
Sekitar 35% calon pembeli dari kampanye iklan Instagram menggunakan Safari iOS dan gagal mendaftar akun baru.
```

---

## 3. Matriks Severity vs Priority

Sering kali pelapor menyamakan antara tingkat keparahan teknis (*Severity*) dan tingkat kepentingan penanganan (*Priority*). Keduanya memiliki definisi berbeda:

- **Severity (Keparahan Teknis)**: Seberapa parah kerusakan sistem dari sudut pandang arsitektur.
- **Priority (Prioritas Bisnis)**: Seberapa cepat bug tersebut harus diperbaiki berdasarkan kebutuhan operasional/bisnis.

| Tingkat | Definisi Severity (Dampak Teknis) | Definisi Priority (Urgensi Waktu) | Contoh Kasus Nyata |
| :--- | :--- | :--- | :--- |
| **Level 1** | **SEV1 (Blocker)**: Sistem crash total, data hilang, transaksi utama terhenti. | **P1 (Immediate)**: Perbaiki segera dalam hitungan jam. Hentikan rilis lain jika perlu. | Server database gagal koneksi; tidak ada user yang bisa login. |
| **Level 2** | **SEV2 (Critical)**: Fitur utama lumpuh tanpa jalan keluar alternatif (*no workaround*). | **P2 (High)**: Perbaiki dalam sprint berjalan / rilis berikutnya. | Pembayaran QRIS gagal, tetapi opsi Transfer Bank dan CC masih bisa digunakan. |
| **Level 3** | **SEV3 (Major / Moderate)**: Kerusakan fungsional, tetapi ada solusi sementara (*workaround*). | **P3 (Medium)**: Jadwalkan di sprint terencana berikutnya. | Tombol export PDF gagal, tetapi export format CSV tetap berjalan. |
| **Level 4** | **SEV4 (Minor / Trivial)**: Masalah tampilan visual kecil, typo teks, pergeseran posisi ikon. | **P4 (Low)**: Perbaiki saat ada waktu luang teknis. | Typo teks "Simpan" tertulis "Smpan" pada halaman FAQ publik. |

> **Catatan Matriks Unik:**
> - *High Severity, Low Priority*: Bug crash pada fitur usang di browser IE11 yang hanya digunakan oleh 0.01% pengunjung.
> - *Low Severity, High Priority*: Typo penulisan nama brand utama perusahaan di halaman muka homepage website resmi.

---

## 4. Siklus Hidup Bug (Bug Lifecycle)

Alur status tiket penanganan bug harus terdefinisi jelas di task tracker (Jira/Linear/GitHub Issues):

```
[ NEW ] ---> [ TRIAGED ] ---> [ IN PROGRESS ] ---> [ READY FOR TEST ]
                 |                                        |
                 +---> [ REJECTED ]                       v
                 +---> [ DUPLICATE ]                 [ VERIFIED ]
                 +---> [ CANNOT REPRODUCE ]               |
                                                          v
                                                     [ CLOSED ]
```

### Panduan SLA Respon & Perbaikan Bug:
| Tingkat Prioritas | SLA Respon Pertama (Triage) | Target Waktu Resolusi (Fix & Deploy) |
| :--- | :--- | :--- |
| **P1 (Immediate)** | $\le 15$ Menit | $\le 4$ Jam (Hotfix produksi) |
| **P2 (High)** | $\le 2$ Jam | $\le 2$ Hari kerja (Sprint berjalan) |
| **P3 (Medium)** | $\le 1$ Hari kerja | Terjadwal di sprint berikutnya |
| **P4 (Low)** | $\le 3$ Hari kerja | Backlog / diselesaikan saat refactor |

1. **New**: Bug baru dibuat oleh penemu masalah.
2. **Triaged**: Diulas oleh Lead/PM untuk validasi severity, priority, dan ditugaskan ke developer.
3. **In Progress**: Developer sedang menginvestigasi dan menulis perbaikan kode beserta regression test-nya.
4. **Ready for Test**: Perbaikan telah di-deploy ke lingkungan staging dan siap divalidasi ulang.
5. **Verified**: QA/Pelapor memverifikasi bahwa bug berhasil diperbaiki dan tidak menimbulkan regresi baru.
6. **Closed**: Tiket ditutup permanen.
7. **Jalur Alternatif**:
   - *Duplicate*: Tiket sama sudah pernah dibuat sebelumnya.
   - *Rejected*: Bukan bug, melainkan perilaku yang memang dirancang demikian (*works as designed*).
   - *Cannot Reproduce*: Masalah tidak dapat diulang karena data atau instruksi kurang lengkap.

---

## 5. Analisis Akar Masalah: Metode 5 Whys

Saat bug kritis berhasil diperbaiki, lakukan Root Cause Analysis (RCA) sederhana agar masalah serupa tidak terulang kembali di masa depan:

*Kasus: Notifikasi tagihan pelanggan terkirim ganda 10 kali.*
1. **Mengapa?** Fungsi worker pengiriman email memproses satu record berkali-kali.
2. **Mengapa?** Job queue Redis mengembalikan pesan yang sama ke antrean (*re-queued*).
3. **Mengapa?** Waktu timeout worker (5 detik) lebih pendek daripada waktu eksekusi pengiriman via SMTP (8 detik).
4. **Mengapa?** Pihak penyedia SMTP sedang mengalami lonjakan latensi respons.
5. **Mengapa?** Aplikasi tidak menerapkan mekanisme idempotency key untuk setiap nomor transaksi.
*Tindakan Korektif:* Tambahkan idempotency check pada worker sebelum mengirimkan email.

---

## 6. Mencegah Bug Ping-Pong

Bug ping-pong terjadi ketika developer mengembalikan tiket dengan status *"Cannot Reproduce"* dan pelapor mengembalikannya kembali dengan status *"Still Broken"*.

Hindari pola ini dengan:
- **Sertakan File HAR / Network Export**: Simpan log jaringan dari Chrome DevTools (`.har`) untuk memperlihatkan request dan response payload yang gagal.
- **Berikan Akun dan Data Uji yang Spesifik**: Tuliskan nama user atau ID transaksi secara eksplisit (`Order ID: #ORD-98214`), bukan instruksi umum "coba checkout pesanan apa saja".
- **Lakukan Verifikasi Bersama (Huddle 5 Menit)**: Jika bug gagal direproduksi 2 kali, adakan panggilan video kilat untuk melakukan demo bersama.
- **Sertakan User-Agent Lengkap**: Jangan hanya menulis "di HP Android", tetapi sebutkan model dan build browser (`Samsung Galaxy S23, Android 14, Chrome 124.0.6367.82`).

---

## 7. Metrik Kesehatan Pengelolaan Bug

Pantau efisiensi penanganan cacat program dengan tiga metrik utama:
1. **Bug Age**: Berapa lama sebuah bug terbuka sejak dilaporkan hingga diverifikasi selesai. Bug P1 tidak boleh memiliki age > 24 jam.
2. **Reopen Rate**: Persentase tiket bug yang sudah dinyatakan *Ready for Test* namun dibuka kembali karena perbaikan developer tidak tuntas. Target sehat: < 5%.
3. **Escaped Defects**: Jumlah bug yang ditemukan oleh customer di produksi dibandingkan temuan internal selama sprint.

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Menggabungkan Banyak Masalah dalam Satu Tiket**: Menulis "Halaman profil lambat dan foto tidak muncul dan tombol logout error". Buat satu tiket untuk setiap satu masalah terisolasi.
2. **Judul yang Terlalu Abstrak**: Menulis judul "Website rusak" atau "Ada error di checkout".
3. **Tidak Menyertakan Preconditions Penting**: Lupa mencatat bahwa bug hanya terjadi jika pengguna menggunakan mode browser incognito atau mata uang asing.
4. **Menyalahkan Developer Secara Pribadi**: Menulis deskripsi dengan nada emosional. Fokuslah pada fakta kegagalan sistem secara objektif.
5. **Menutup Tiket Tanpa Verifikasi Ulang**: Menandai *Closed* hanya berdasarkan klaim chat developer tanpa verifikasi di lingkungan uji.

---

## 9. Dokumen Terkait & Template

- [Template Laporan Bug](../templates/testing/bug-report-template.md) — Format standar pengisian tiket cacat perangkat lunak.
- [Test Case Design](./test-case-design.md) — Penyusunan kasus uji untuk mereproduksi skenario batas.
- [Incident Response Guide](../07-delivery-operations/incident-response.md) — Protokol penanganan bug darurat di produksi.
- [Definition of Done](./definition-of-done.md) — Syarat penyelesaian tiket perbaikan bug.
- [Regression Testing Guide](./regression-testing.md) — Menambahkan automated test untuk mencegah bug berulang.

