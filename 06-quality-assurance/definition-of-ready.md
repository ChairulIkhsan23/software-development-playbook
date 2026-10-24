# Definition of Ready (DoR)

Definition of Ready (DoR) adalah kumpulan kriteria kesepakatan bersama yang harus dipenuhi oleh sebuah kebutuhan (*User Story* atau tiket pekerjaan) sebelum tim engineer menerimanya ke dalam siklus pengembangan aktif (*Sprint Backlog* atau kolom *In Progress*).

---

## 1. Mengapa DoR Sangat Krusial untuk Mencegah Churn?

Memulai pengerjaan tugas yang belum matang adalah penyebab utama keterlambatan proyek, pemborosan waktu (*churn*), dan frustrasi tim:
- **Mencegah Asumsi Liar**: Menghentikan skenario di mana developer mengira fitur A yang diminta, tetapi stakeholder mengharapkan fitur B.
- **Mengeliminasi Blokir di Tengah Sprint**: Mencegah developer berhenti bekerja di hari ketiga sprint hanya karena API pihak ketiga belum memiliki dokumentasi atau akses kunci (*API key*).
- **Menjaga Fokus dan Moral Tim**: Melindungi developer dari perubahan kebutuhan mendadak yang merusak arsitektur kode di tengah jalan.
- **Akurasi Estimasi Waktu**: Kebutuhan yang terdefinisi jelas menghasilkan estimasi kerja yang jauh lebih realistis dan dapat diandalkan oleh manajemen.

---

## 2. Daftar Periksa Komponen DoR (The DoR Checklist)

Sebuah pekerjaan dianggap berstatus **Ready** apabila memenuhi seluruh item daftar periksa berikut:

### A. Format Cerita Pengguna (User Story) Jelas
Tugas harus mendefinisikan persona, kebutuhan, dan nilai bisnisnya:
> *"Sebagai seorang [tipe pengguna], saya ingin [tindakan tertentu], sehingga [manfaat / nilai bisnis tercapai]."*

### B. Acceptance Criteria (AC) Tidak Ambigu
Kriteria penerimaan harus terukur dan dapat diuji. Sangat disarankan menggunakan format **Given-When-Then** (Gherkin) atau poin checklist objektif:
- *Given*: Kondisi awal sistem sebelum aksi.
- *When*: Tindakan atau pemicu yang dilakukan user.
- *Then*: Hasil atau perubahan kondisi yang diharapkan.

### C. Desain UI/UX Telah Final dan Disetujui
- Tautan prototipe desain (Figma/Penpot) berstatus *Approved*.
- Mencakup seluruh variasi status antarmuka: status normal, loading, form kosong (*empty state*), dan pesan error validasi input.
- Penyesuaian layout tampilan desktop dan layar ponsel (*mobile responsive*) telah tersedia.

### D. Ketergantungan Eksternal Telah Teridentifikasi
- Jika membutuhkan API pihak ketiga: Dokumentasi, akun sandbox, dan kredensial uji sudah tersedia dan berfungsi.
- Jika membutuhkan perubahan skema database lintas tim: Kesepakatan kontrak data telah ditandatangani.
- Izin akses infrastruktur (*firewall whitelist*, kredensial environment) telah disetujui tim DevOps.
- Komponen upstream dari tim lain sudah ter-deploy di lingkungan integrasi staging.

### E. Kelayakan Teknis Terverifikasi (Feasibility Check & Spike)
- Tidak ada ketidakpastian arsitektur besar. Jika ada hal baru yang belum pernah dicoba, tim telah menyelesaikan investigasi awal (*Technical Spike*) terlebih dahulu.
- **Kapan Membutuhkan Spike?**: Ketika estimasi usaha tidak dapat ditentukan karena ketidakpastian teknologi pihak ketiga atau performa algoritma.
- **Output Dokumen Spike**: Contoh prototipe kode terisolasi, rekomendasi arsitektur, dan perkiraan risiko teknis.

### F. Estimasi Beban Kerja & Ukuran Cerita Sesuai
- Tim developer telah membahas dan menetapkan estimasi ukuran tugas (*Story Points* atau ukuran T-Shirt) secara kolektif saat sesi *Backlog Refinement*.
- **Aturan Pemecahan**: Jika estimasi lebih besar dari batas maksimal sprint (misal: > 8 story points atau > 3 hari kerja solo dev), cerita **wajib dipecah** (*slice*) menjadi potongan-potongan yang lebih kecil.

---

## 3. Matriks Kesepakatan Tanggung Jawab DoR

| Elemen Kesiapan | Disediakan Oleh | Ditinjau Bersama Oleh | Kriteria Sukses |
| :--- | :--- | :--- | :--- |
| **User Story & Business Value** | Product Owner | Seluruh Tim Sprint | Nilai bisnis dipahami dengan jelas |
| **Acceptance Criteria (AC)** | Product Owner / QA | Developer & QA | Dapat diuji dengan input-output pasti |
| **Desain Antarmuka (Figma)** | UI/UX Designer | Frontend Developer | State loading, empty, & error lengkap |
| **Akses Sandbox & Kredensial API** | Tech Lead / PO | Backend Developer | Berhasil dipanggil dengan curl / Postman |
| **Estimasi Story Points** | Tim Developer | Product Owner | Seluruh developer sepakat pada satu angka |

---

## 3. Studi Kasus Perbandingan: Sebelum vs Sesudah DoR

### Contoh Tiket Mentah (Gagal DoR - Jangan Diterima):
```markdown
Judul: Bikin sistem notifikasi
Deskripsi: Kita butuh notifikasi biar user tahu kalau ada promo baru. Tolong buat secepatnya.
```
*Mengapa Gagal:* Tidak ada kriteria penerimaan, tidak jelas kanal yang digunakan (email, push, SMS), tidak ada desain, dan tidak ada batasan teknis.

### Contoh Tiket Matang (Lolos DoR - Siap Masuk Sprint):
```markdown
Judul: [Notifikasi] Pengiriman Notifikasi Push Promo Flash Sale ke Aplikasi Mobile
User Story: Sebagai pengguna mobile, saya ingin menerima notifikasi promo agar tidak ketinggalan flash sale.

Acceptance Criteria:
1. Given pengguna telah mengaktifkan izin push notifikasi di iOS/Android,
   When admin mempublikasikan campaign flash sale dari dashboard,
   Then banner push notifikasi muncul di layar perangkat user dalam waktu < 2 menit.
2. Given notifikasi diketuk oleh pengguna,
   When aplikasi mobile terbuka,
   Then user langsung diarahkan ke halaman detail produk flash sale (deep linking).
3. Given user menolak izin notifikasi pada pengaturan OS,
   Then pengiriman notifikasi dilewati tanpa menimbulkan error log kritis di server.

Desain: figma.com/file/xyz?node-id=promo-push
Dependensi: Kredensial Firebase Cloud Messaging (FCM) sudah aktif di vault.
Estimasi: 3 Story Points (Kira-kira 1.5 hari kerja).
```

---

## 4. Penegakan DoR Tanpa Menjadi Birokrasi Kaku

DoR diciptakan untuk mempermudah kolaborasi, bukan menjadi tameng penolakan pekerjaan (*gatekeeping*):

```
                        [ Tiket Masuk Refinement ]
                                     |
                       Apakah memenuhi syarat DoR?
                                   /   \
                                 Ya     Tidak
                                /         \
                  [ Masuk Sprint ]         Berapa besar celah kekurangannya?
                                            /                           \
                                     Celah Minor                     Celah Mayor
                                    (Contoh: Typo teks copy)        (Contoh: API belum ada)
                                          |                               |
                                Diskusikan 5 Menit               Kembalikan ke Backlog
                                & Langsung Lengkapi              untuk Dimatangkan PO
```

- **Pragmatisme Kolaboratif**: Jika sebuah tiket hanya kekurangan teks copy tombol, developer dan Product Owner dapat menyepakatinya langsung saat refinement tanpa perlu melempar tiket keluar.
- **Bukan Senjata Permusuhan**: DoR bukan alat bagi developer untuk mempersulit PO, melainkan panduan bersama agar kedua belah pihak terlindungi dari kegagalan delivery.

---

## 5. Penyesuaian DoR Berdasarkan Ukuran Tim

| Aspek | Solo Developer / Tim Mikro (1-3 Orang) | Tim Tumbuh (4-10 Orang) | Skala Enterprise (Multi-Tim) |
| :--- | :--- | :--- | :--- |
| **Bentuk DoR** | Catatan ringkas di issue: AC minimal 3 poin + sketsa UI kasar. | Checklist formal di template issue Linear/Jira + Figma link. | Stage-gate review dengan persetujuan lintas fungsi (Security, Legal, Arch). |
| **Proses Review** | Evaluasi mandiri sebelum mulai menulis kode. | Sesi Backlog Refinement mingguan bersama PO & Tech Lead. | Formal sign-off dalam sprint planning lintas tim. |
| **Toleransi Risiko** | Fleksibel; perubahan spesifikasi mudah dikomunikasikan instan. | Moderat; perubahan di tengah sprint harus ditukar poinnya. | Ketat; perubahan scope memerlukan persetujuan change management. |

---

## 6. Protokol Fast-Track untuk Tiket Darurat (Emergency Exceptions)

Terkadang muncul kebutuhan darurat seperti bug kritis di produksi atau perubahan regulasi mendesak yang tidak sempat melalui sesi backlog refinement mingguan:

1. **Kriteria Fast-Track**: Hanya berlaku untuk insiden tingkat SEV1/SEV2 atau permintaan darurat C-Level yang memiliki batas waktu hukum pasti.
2. **DoR Minimum yang Tidak Boleh Ditiadakan**:
   - Pernyataan masalah dan dampak risiko wajib tertulis di tiket.
   - Minimal 1 kriteria penerimaan tertulis secara eksplisit.
   - Persetujuan lisan dari Tech Lead dan Product Owner.
3. **Penyelesaian Utang Spesifikasi**: Setelah perbaikan darurat dirilis ke produksi, pemilik produk tetap wajib melengkapi dokumentasi tiket selambat-lambatnya 24 jam kemudian.

---

## 7. Anti-Patterns dalam Penerapan DoR

1. **Analysis Paralysis**: Mencoba mendefinisikan setiap detail kecil hingga ke level implementasi kode sebelum sprint dimulai, menunda pengembangan berbulan-bulan.
2. **Senjata Menolak Pekerjaan (Weaponized DoR)**: Tim developer secara dogmatis menolak berdiskusi hanya karena dokumen kurang satu format baris kecil.
3. **Penyusupan Kebutuhan Tak Terlihat**: Menyetujui tiket yang kelihatannya sederhana, padahal menyembunyikan ketergantungan besar yang belum siap di backend.
4. **DoR yang Tidak Pernah Berevolusi**: Mempertahankan aturan yang sudah tidak relevan dengan skala atau kecepatan tim saat ini.
5. **Mengabaikan Desain Error State**: Menganggap desain selesai padahal desainer hanya menggambar tampilan sukses (*happy flow*) tanpa sketsa tampilan error atau jaringan offline.

---

## 8. Dokumen Terkait & Template

- [Template Definition of Ready](../templates/testing/definition-of-ready.md) — Dokumen template siap pakai untuk sprint backlog.
- [Definition of Done](./definition-of-done.md) — Kriteria penentu saat pekerjaan selesai dikerjakan.
- [User Stories and Use Cases](../02-product-requirements/user-stories-and-use-cases.md) — Panduan penulisan spesifikasi kebutuhan.
- [Acceptance Criteria Guide](../02-product-requirements/acceptance-criteria.md) — Standar penulisan kriteria penerimaan format Gherkin.
- [Estimation and Prioritization](../08-project-management/estimation-and-prioritization.md) — Teknik estimasi story point dan pembagian ukuran tugas.
- [Test Strategy](./test-strategy.md) — Integrasi kesiapan kebutuhan dengan arsitektur mutu pengujian.

