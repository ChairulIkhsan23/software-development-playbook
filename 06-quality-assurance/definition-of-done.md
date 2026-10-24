# Definition of Done (DoD)

Definition of Done (DoD) adalah kesepakatan formal dan tegas di dalam tim pengembangan yang mendefinisikan standar mutu minimal yang harus dipenuhi sebelum sebuah potongan kode (*task*, fitur, atau perbaikan bug) dapat dinyatakan selesai (*Done*) dan siap digabungkan ke branch utama atau dirilis ke lingkungan produksi.

---

## 1. Mengapa DoD Sangat Penting untuk Kualitas Perangkat Lunak?

Tanpa DoD yang disepakati, setiap developer memiliki standar pribadi tentang arti "selesai". Seorang developer mungkin merasa selesai ketika kode berjalan di komputernya, sementara yang lain menunggu sampai dokumentasi selesai ditulis.

DoD memberikan manfaat:
- **Standar Mutu Seragam**: Memastikan setiap baris kode memenuhi batas kualitas minimum tanpa memandang siapa yang menulisnya.
- **Mencegah Akumulasi Utang Teknis (Technical Debt)**: Mencegah tim menunda penulisan tes atau pembaruan dokumentasi ke "nanti", yang sering kali tidak pernah dikerjakan.
- **Transparansi Kemajuan**: Stakeholder dan tim manajemen dapat mempercayai status "Done" di papan kerja tanpa harus menebak-nebak apakah fitur tersebut masih mengandung pekerjaan tersembunyi.
- **Mempercepat Onboarding Anggota Baru**: Memberikan kejelasan pedoman mutu bagi junior dev yang baru bergabung dengan tim.

---

## 2. Daftar Periksa Komponen DoD Standar

Sebuah tiket fitur atau perbaikan kode dinyatakan **Done** apabila memenuhi kriteria berikut:

### A. Kode Lengkap & Bersih (Code Complete)
- [ ] Kode telah selesai ditulis dan mematuhi konvensi penamaan serta gaya kode (*style guide*).
- [ ] Tidak ada baris *debug code* tertinggal (seperti `console.log`, `debugger`, atau `print`).
- [ ] Tidak ada file konfigurasi lokal atau rahasia (*API keys*, password lokal) yang sengaja atau tidak sengaja ter-commit.

### B. Pengujian Otomatis Lolos (Automated Tests Passing)
- [ ] Unit test baru telah ditulis untuk mencakup logika bisnis inti dan skenario batas (*edge cases*).
- [ ] Integration test telah diperbarui untuk endpoint atau query database baru.
- [ ] 100% rangkaian pengujian otomatis berhasil lolos (*green build*) di pipeline CI lokal maupun server.

### C. Review Kode Disetujui (Peer Code Review Approved)
- [ ] Pull Request (PR) telah diulas dan mendapatkan persetujuan (*Approved*) dari minimal satu peer developer.
- [ ] Semua komentar dan pertanyaan reviewer telah diselesaikan atau ditindaklanjuti secara tuntas.
- [ ] Kode bebas dari code smell, antipattern, atau duplikasi logika yang tidak perlu.

### D. Analisis Statis & Pemindaian Keamanan Bersih (Clean Scans)
- [ ] Linter (ESLint, Biome, Flake8) lolos tanpa peringatan *warning* atau *error*.
- [ ] Type checking (TypeScript / Mypy) lolos tanpa adanya penggunaan tipe ilegal (`any` tanpa izin khusus).
- [ ] Pemindaian keamanan otomatis (*dependency vulnerability check* / secret scanner) tidak menemukan celah keamanan tingkat tinggi.

### E. Dokumentasi Diperbarui (Documentation Updated)
- [ ] Kontrak API / Swagger / OpenAPI spec telah disesuaikan jika terjadi penambahan atau perubahan endpoint.
- [ ] Berkas `README.md` atau panduan setup lingkungan telah diperbarui jika ada variabel konfigurasi environment (`.env.example`) baru.

### F. Deployed ke Lingkungan Staging & Acceptance Criteria Terverifikasi
- [ ] Kode berhasil di-build dan di-deploy secara otomatis ke lingkungan Staging.
- [ ] Seluruh kriteria penerimaan (Acceptance Criteria) yang tertera di tiket telah diverifikasi secara manual oleh QA atau Product Owner.

### G. Kepatuhan Aksesibilitas & Performa Dasar (Non-Functional DoD)
- [ ] Skor Google Lighthouse performa web $\ge 80$ pada link halaman publik yang diperbarui.
- [ ] Aksesibilitas dasar: kontras teks terbaca, navigasi tombol dapat diakses via keyboard (Tab/Enter), dan atribut `alt` pada elemen gambar terisi.
- [ ] Tidak ada regresi latensi database query (kueri bebas dari masalah N+1 queries).

---

## 3. Penegakan Otomatis DoD Melalui CI/CD & Branch Protection

Jangan mengandalkan ingatan manusia semata untuk menegakkan DoD. Konfigurasikan sistem repositori (GitHub / GitLab) secara otomatis:

1. **Branch Protection Rules**:
   - Wajib lolos pemeriksaan status CI (*Require status checks to pass before merging*): linter, typecheck, dan automated tests.
   - Wajib minimal 1 approval dari reviewer resmi (*Require pull request reviews before merging*).
   - Larang force push (`git push --force`) ke branch utama (`main` / `master`).
2. **Automated PR Bot**:
   - Pasang bot seperti Codecov untuk melaporkan perubahan delta coverage di setiap PR.
   - Pasang secret scanner (seperti GitGuardian atau Trivy) untuk memblokir commit yang memuat private key.

---

## 4. Contoh Implementasi DoD pada Template Pull Request GitHub

Terapkan DoD langsung ke dalam alur kerja harian developer dengan memasukkan checklist ke dalam berkas `.github/pull_request_template.md`:

```markdown
### Ringkasan Perubahan
Menambahkan endpoint kalkulasi biaya pengiriman berdasarkan kurir pihak ketiga.

### Kepatuhan Definition of Done (DoD)
- [x] Kode mematuhi linting dan type checking lokal.
- [x] Unit test baru ditambahkan (`shipping-calc.spec.ts`).
- [x] Integration test untuk endpoint `/api/v1/shipping/estimate` lolos.
- [x] Variabel baru `COURIER_API_KEY` telah dicatat di `.env.example`.
- [x] Dokumentasi OpenAPI telah di-generate ulang.
- [x] Diuji di lingkungan staging dan diverifikasi oleh Product Owner.
```

---

## 4. Contoh Implementasi DoD pada Template Pull Request GitHub

Terapkan DoD langsung ke dalam alur kerja harian developer dengan memasukkan checklist ke dalam berkas `.github/pull_request_template.md`:

```markdown
### Ringkasan Perubahan
Menambahkan endpoint kalkulasi biaya pengiriman berdasarkan kurir pihak ketiga.

### Kepatuhan Definition of Done (DoD)
- [x] Kode mematuhi linting dan type checking lokal.
- [x] Unit test baru ditambahkan (`shipping-calc.spec.ts`).
- [x] Integration test untuk endpoint `/api/v1/shipping/estimate` lolos.
- [x] Variabel baru `COURIER_API_KEY` telah dicatat di `.env.example`.
- [x] Dokumentasi OpenAPI telah di-generate ulang.
- [x] Diuji di lingkungan staging dan diverifikasi oleh Product Owner.
```

---

## 5. Matriks Perbedaan: DoD vs Acceptance Criteria vs Release Readiness

Ketiga konsep ini sering kali membingungkan bagi developer pemula. Berikut batas perbedaannya:

| Kriteria Pembeda | Definition of Done (DoD) | Acceptance Criteria (AC) | Release Readiness |
| :--- | :--- | :--- | :--- |
| **Ruang Lingkup (Scope)** | Berlaku secara **universal** untuk semua tiket/fitur di proyek. | Berlaku **spesifik** hanya untuk satu cerita pengguna (*User Story*) tertentu. | Berlaku untuk **seluruh paket rilis** sistem (kumpulan fitur). |
| **Pemilik Utama** | Tim Engineer & QA (standar teknis & proses). | Product Owner / Bisnis (perilaku fungsional). | Release Manager, Ops, Tech Lead, Stakeholder Eksekutif. |
| **Fokus Pengujian** | Kualitas kode, unit testing, keamanan, pipeline CI. | Kebenaran fungsionalitas logika bisnis spesifik. | Stabilitas sistem, migrasi database produksi, rollback plan. |
| **Frekuensi Evaluasi** | Setiap penutupan tiket / merge Pull Request. | Saat pengujian fitur individual di staging. | Menjelang peluncuran terjadwal (misal: mingguan/bulanan). |

---

## 6. Evolusi DoD Berdasarkan Kematangan Tim (Crawl, Walk, Run)

Jangan memaksakan DoD perusahaan raksasa ke tim kecil beranggotakan dua orang. Bangun DoD secara bertahap:

```
[ TAHAP 1: CRAWL (Solo / Startup Awal) ]
- Kode lolos compiler / linter
- Ada unit test untuk logika kalkulasi uang / domain inti
- Berhasil dideploy ke staging tanpa crash

[ TAHAP 2: WALK (Tim Kecil 3-6 Developer) ]
+ Semua syarat Tahap 1
+ Wajib 1 Code Review Approval
+ Integration test untuk seluruh endpoint baru
+ Update skema OpenAPI otomatis

[ TAHAP 3: RUN (Tim Mapan / Enterprise) ]
+ Semua syarat Tahap 2
+ E2E critical path automated test
+ Pemindaian celah keamanan SonarQube & dependabot
+ Verifikasi audit log & compliance data
```

---

## 7. Protokol Pengecualian dan Pencatatan Utang Teknis (DoD Waivers)

Jika dalam kondisi mendesak sebuah item DoD terpaksa dilewati (misal: penulisan dokumen ditunda demi hotfix SEV1):
1. **Wajib Persetujuan Tech Lead**: Dev tidak boleh membuat keputusan sendiri untuk melewati DoD.
2. **Buat Tiket Tech-Debt Seketika**: Tiket utang teknis harus langsung dibuat di backlog dan diprioritaskan pada sprint berikutnya.
3. **Pemberian Tag Khusus**: Beri label `tech-debt:dod-exception` agar transparan dalam rapat retrospektif.

---

## 8. Anti-Patterns dalam Penerapan DoD

1. **DoD Aspirasional yang Menjadi Pajangan**: Menulis checklist yang sangat muluk (misal: "wajib 100% coverage dan zero security warnings"), tetapi diabaikan oleh semua orang saat mendekati deadline rilis.
2. **DoD sebagai Hukuman Birokratis**: Menggunakan daftar periksa untuk mempermalukan atau menghukum rekan tim, alih-alih sarana perbaikan proses kerja secara suportif.
3. **Mengabaikan DoD Demi Kecepatan Semu**: Meloloskan PR tanpa review dan tanpa tes dengan alasan "fitur buru-buru rilis". Kode ini akan kembali menjadi bug kritis di kemudian hari.
4. **Pengecualian Hotfix Tanpa Rekonsiliasi**: Memperbolehkan hotfix cepat tanpa tes, tetapi lupa menambahkan kembali tes unit tersebut setelah insiden selesai dipadamkan.
5. **Checklist Tanpa Otomasi**: Mengharuskan developer mengecek linter manual alih-alih memasang CI bot otomatis yang memblokir PR secara langsung.

---

## 9. Dokumen Terkait & Template

- [Template Definition of Done](../templates/testing/definition-of-done.md) — Format checklist siap pakai untuk repository PR.
- [Definition of Ready](./definition-of-ready.md) — Persyaratan sebelum fitur masuk ke antrean pengerjaan.
- [Code Review Guidelines](../05-development-practices/code-review.md) — Panduan pelaksanaan peer code review yang efektif.
- [Release Readiness Guide](../07-delivery-operations/release-readiness.md) — Daftar periksa kesiapan rilis produksi menyeluruh.
- [Test Strategy](./test-strategy.md) — Arsitektur piramida pengujian mutu perangkat lunak.
