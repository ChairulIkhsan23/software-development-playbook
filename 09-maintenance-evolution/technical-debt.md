# Technical Debt Management

Panduan praktis identifikasi, pelacakan, dan pelunasan technical debt untuk junior developer, solo developer, dan tim kecil.

---

## 1. Apa Itu Technical Debt?

Istilah **Technical Debt** pertama kali diperkenalkan oleh **Ward Cunningham** (1992) sebagai analogi finansial:

> *"Mengeluarkan kode cepat dan kotor sama seperti meminjam uang. Pinjaman mempercepat pengiriman fitur sekarang, tetapi membebani bunga di masa depan berupa perlambatan kecepatan pengembangan sampai pokok hutang dilunasi melalui refactoring."*

- **Principal (Pokok Hutang):** Biaya atau usaha engineering yang dihemat saat mengambil jalan pintas hari ini.
- **Interest (Bunga Hutang):** Tambahan waktu dan friksi yang harus dibayar setiap kali tim menyentuh atau mengembangkan modul tersebut di masa depan.
- **Default (Gagal Bayar):** Sistem menjadi terlalu rapuh untuk diubah, rilis fitur baru memicu regresi fatal, tim berhenti berinovasi karena takut sistem runtuh.

---

## 2. Kuadran Technical Debt (Martin Fowler)

Martin Fowler membagi technical debt ke dalam matriks dua sumbu: **Deliberate vs Inadvertent** (Sadar vs Tidak Sadar) dan **Prudent vs Reckless** (Bijak vs Ceroboh).

```
                  Prudent (Bijak)                Reckless (Ceroboh)
          +------------------------------+------------------------------+
          | "Kita harus rilis sekarang,  | "Kita tidak punya waktu      |
Deliberate| kita paham konsekuensinya,   | untuk membuat desain arsitektur|
 (Sadar)  | dan jadwalkan refactor."     | atau pengujian sama sekali." |
          +------------------------------+------------------------------+
          | "Sekarang kita tahu solusi   | "Apa itu layering? Apa itu   |
Inadvertent| yang benar setelah sistem    | automated testing?"          |
(Tak Sadar| berjalan di production."     |                              |
          +------------------------------+------------------------------+
```

1. **Deliberate & Prudent:** Keputusan bisnis terukur. Tim memilih solusi MVP sederhana untuk memvalidasi product-market fit secepatnya, mencatat hutang tersebut di backlog, lalu memperbaikinya setelah validasi.
2. **Deliberate & Reckless:** Tahu cara yang baik tetapi sengaja diabaikan demi jalan pintas tanpa rencana bayar. Menghasilkan kode berantakan berisiko tinggi.
3. **Inadvertent & Reckless:** Dihasilkan oleh tim minim pengalaman tanpa bimbingan standar engineering atau code review.
4. **Inadvertent & Prudent:** Pembelajaran alami. Setelah fitur berjalan di production selama beberapa bulan, tim memahami domain problem secara lebih mendalam dan menyadari desain awal kurang optimal.

---

## 3. Sumber Penyebab Technical Debt

1. **Tekanan Waktu (Time Pressure):** Deadline ketat yang memaksa hardcoding logic, skipping automated tests, atau mengabaikan edge case handling.
2. **Kebutuhan Domain yang Berubah (Evolving Requirements):** Fitur yang awalnya didesain untuk 100 pengguna lokal tiba-tiba harus melayani 100.000 pengguna dengan multi-tenancy.
3. **Ketiadaan Standar dan Panduan (Lack of Standards):** Proyek tanpa linting, static analysis, styling guide, atau code review checklist yang konsisten.
4. **Refactoring yang Terus Ditunda (Postponed Refactoring):** Sikap "nanti saja diperbaiki kalau sempat" tanpa komitmen sprint backlog yang jelas.

---

## 4. Taksonomi: Identifikasi dan Kategori Debt

| Kategori | Karakteristik / Indikator | Contoh Nyata |
|---|---|---|
| **Code Debt** | Code smell, duplikasi logika, fungsi raksasa (*God function*), magic numbers, bad naming. | File `OrderController.ts` berisi 2.500 baris logika bisnis dan SQL query langsung. |
| **Architectural Debt** | Pelanggaran boundary modular, circular dependency, tight coupling antar service. | Frontend memanggil database langsung atau service A dan service B saling memanggil sync loop. |
| **Test Debt** | Test coverage rendah, brittle tests, mocking berlebihan, flakiness, tanpa integration test. | Testing suite dilewati di CI pipeline (`npm run build --no-tests`) agar deployment cepat. |
| **Documentation Debt** | README kadaluarsa, API spec tidak sinkron dengan implementation, onboarding guide usang. | Endpoint `/api/v2/checkout` tidak terdokumentasi di OpenAPI/Swagger spec. |
| **Infrastructure Debt** | OS/runtime usang, manual deployment steps, config tersimpan di file VM, ketiadaan IaC. | Server production dikonfigurasi manual via SSH tanpa backup script Docker/Terraform. |

---

## 5. Strategi Pelunasan Technical Debt

### A. 15-20% Capacity Rule Per Sprint
Alokasikan porsi tetap 15% hingga 20% dari total story points atau jam kerja tim setiap sprint khusus untuk item technical debt.
- Sprint berkapasitas 40 point: gunakan 6-8 point untuk melunasi debt prioritas tinggi.
- Jangan jadikan kapasitas debt sebagai buffer cadangan untuk fitur bisnis yang molor.

### B. Boy Scout Rule
> *"Tinggalkan perkemahan lebih bersih daripada saat Anda menemukannya."*

Setiap kali engineer membuka file untuk memperbaiki bug atau menambahkan field:
- Ekstrak fungsi kecil yang terlalu panjang.
- Perbaiki penamaan variabel yang ambigu.
- Tambahkan unit test pada fungsi yang belum ter-cover sebelum mengubahnya.
- Pastikan perubahan tetap kecil (diff reviewable < 100 baris) agar tidak menghambat pull request utama.

### C. Dedicated Refactoring Sprints
Gunakan refactoring sprint terisolasi hanya untuk perubahan struktural besar:
- Migrasi database schema core, pemisahan monolith rapuh, atau upgrade framework major.
- Jalankan maksimal 1-2 kali per tahun dengan milestone, metrik keberhasilan, dan roll-back plan yang jelas.

---

## 6. Menghitung Biaya Technical Debt

Technical debt bukan sekadar preferensi estetika programmer; debt memiliki dampak finansial nyata:

```
Total Biaya Debt = (Penurunan Velocity Tim) + (Biaya Perbaikan Bug Regresi) + (Biaya Downtime / SLA Breach)
```

Indikator terukur:
1. **Cycle Time & Lead Time:** Fitur baru yang dulu selesai dalam 2 hari kini memakan waktu 2 minggu karena harus menambal kode rapuh di sekitarnya.
2. **Defect Escape Rate:** Rasio bug baru yang lolos ke production meningkat setiap kali rilis baru dilakukan.
3. **Escalating Onboarding Time:** Developer baru butuh waktu berminggu-minggu hanya untuk memahami aliran data sistem.

---

## 7. Format Tech Debt Register

Kelola technical debt secara transparan di issue tracker (GitHub Issues / Jira) menggunakan format register:

| Field | Keterangan | Contoh |
|---|---|---|
| **ID** | Identifier unik tiket | `TECHDEBT-104` |
| **Category** | Kategori debt | `Code Debt / Database` |
| **Title** | Ringkasan masalah | Migrasi N+1 Query pada endpoint `/api/v1/invoices` |
| **Description** | Gejala teknis dan lokasi modul | Query invoice me-load relasi `customer` dan `items` dalam loop `forEach` di `InvoiceService.go`. |
| **Impact** | Risiko bisnis / engineering jika dibiarkan | Latency endpoint naik drastis saat invoice > 100 baris; database pool cepat jenuh. |
| **Effort** | Estimasi estimasi perbaikan | Small (1-2 days) |
| **Priority** | Urutan pelunasan | High |

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Menyembunyikan Debt dari Product Manager:** Developer mengeluh tanpa menyajikan data dampak bisnis (misal: "fitur X lambat dirilis karena modul Y rapuh").
2. **Big Bang Rewrite:** Merombak seluruh sistem dari nol tanpa rencana migrasi inkremental. Risikonya proyek mandek berbulan-bulan dan melahirkan bug baru.
3. **Menyamakan Bad Code dengan Tech Debt:** Kode ceroboh tanpa test yang dibuat karena malas bukan instrumen hutang yang bijak, melainkan murni sabotase kualitas sistem.
4. **Mencatat Tanpa Tindakan:** Menumpuk ratusan issue bertanda `tech-debt` di backlog tanpa pernah mengalokasikan kuota pengerjaan sprint.

---

## 9. Dokumen Terkait

- [Dependency Updates](dependency-updates.md) — Menangani obsolescence dan patching dependensi pihak ketiga.
- [Performance Review](performance-review.md) — Mengukur bottleneck performa akibat akumulasi architectural debt.
- [Deprecation and Migration](deprecation-and-migration.md) — Pola penghentian modul usang secara terstruktur.
