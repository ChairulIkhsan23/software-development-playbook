# Strategi Pengujian Perangkat Lunak (Test Strategy)

Strategi pengujian adalah dokumen arsitektur mutu yang mendefinisikan pendekatan, alokasi sumber daya, tingkat pengujian, serta kriteria evaluasi kualitas dalam siklus hidup pengembangan perangkat lunak (SDLC). Panduan ini disusun khusus untuk junior developer, solo developer, dan tim pengembang skala kecil yang ingin mencapai standar stabilitas tinggi secara efisien tanpa beban birokrasi berlebihan.

---

## 1. Filosofi & Pendekatan Mutu Modern

Dalam paradigma rekayasa perangkat lunak modern, pengujian bukan lagi fase terpisah di ujung siklus rilis yang dibebankan kepada staf QA semata. Mutu harus dirancang dan dibangun ke dalam sistem sejak hari pertama.

Prinsip dasar pengujian pragmatis:
- **Shift-Left Testing**: Geser aktivitas verifikasi sedekat mungkin ke laptop pengembang. Menemukan kesalahan saat mengetik kode jauh lebih murah daripada memperbaikinya saat sudah berjalan di server produksi.
- **Kualitas Milik Kolektif**: Developer bertanggung jawab penuh atas kebenaran kode yang ditulisnya. QA bertindak sebagai arsitek strategi mutu, pelatih pengujian (*testing coach*), dan fasilitator otomatisasi.
- **Kepercayaan Diri untuk Melakukan Refactor**: Suite tes otomatis yang andal memberikan jaminan bahwa refactoring arsitektur atau perbaikan performa tidak menimbulkan efek samping yang merusak.
- **Otomasi Terfokus pada Nilai Investasi (ROI)**: Jangan mengotomatiskan hal yang trivial atau antarmuka eksperimental yang masih berubah setiap minggu.

---

## 2. Model Arsitektur Pengujian: Test Pyramid vs Testing Trophy

Terdapat dua model pendekatan komposisi pengujian yang paling populer di industri:

### A. The Test Pyramid (Mike Cohn)
Model klasik piramida menekankan basis pengujian unit yang sangat tebal:
1. **Unit Tests (~70%)**: Pengujian fungsi murni, utility, dan kelas terisolasi. Kecepatan eksekusi sangat tinggi (milidetik) dengan biaya komputasi rendah.
2. **Integration Tests (~20%)**: Pengujian interaksi antar subsistem (misal: service dengan database atau event bus).
3. **End-to-End (E2E) Tests (~10%)**: Pengujian alur pengguna lengkap dari browser atau aplikasi mobile. Eksekusi paling lambat dan pemeliharaannya paling mahal.

### B. The Testing Trophy (Kent C. Dodds)
Untuk aplikasi web modern berbasis frontend interaktif (React/Vue) dan arsitektur microservices/API, Testing Trophy memberikan keseimbangan yang lebih realistis:
1. **Static Analysis (Dasar)**: Type checker (TypeScript), linter (ESLint), dan formatting. Menangkap kesalahan sintaks dan typo seketika.
2. **Unit Tests**: Menguji logika komputasi murni dan algoritma kompleks secara terisolasi.
3. **Integration Tests (Fokus Terbesar)**: Porsi terbesar piramida modern. Menguji kolaborasi komponen antarmuka atau integrasi modul API dengan database container. Memberikan *Return on Investment* (ROI) tertinggi antara kecepatan dan keyakinan fungsional.
4. **End-to-End Tests (Puncak)**: Terbatas hanya pada skenario transaksi paling kritis (*critical user journeys*).

### Tabel Perbandingan Karakteristik Model
| Dimensi Evaluasi | Classic Test Pyramid | Modern Testing Trophy |
| :--- | :--- | :--- |
| **Porsi Terbesar** | Unit Tests (70%) | Integration Tests (50-60%) |
| **Kecepatan Umpan Balik** | Sangat Cepat | Cepat hingga Moderat |
| **Ketahanan Refactoring** | Rendah (sering terikat implementasi) | Tinggi (berfokus pada perilaku pengguna) |
| **Kesesuaian Tim Kecil** | Proyek berbasis backend murni/algoritmik | Web app modern, SaaS, tim dengan sumber daya terbatas |

---

## 3. Risk-Based Testing (RBT)

Sumber daya pengujian tim kecil selalu terbatas. Terapkan pendekatan berbasis risiko (*Risk-Based Testing*) untuk menentukan area kode yang berhak mendapatkan perhatian pengujian paling mendalam.

Formula Skor Risiko:
$$\text{Skor Risiko} = \text{Dampak Kegagalan (1-5)} \times \text{Probabilitas Terjadi (1-5)}$$

### Penentuan Parameter Risiko:
- **Dampak Kegagalan (Impact)**:
  - 5: Kerugian finansial langsung, kebocoran data sensitif (PII), atau pelanggaran hukum/regulasi.
  - 3: Alur kerja utama pengguna terganggu namun terdapat opsi alternatif (*workaround*).
  - 1: Gangguan visual murni atau kesalahan ejaan (*typo*) tanpa kehilangan data.
- **Probabilitas Terjadi (Likelihood)**:
  - 5: Modul yang diakses jutaan kali setiap hari atau memiliki logika kalkulasi rumit.
  - 3: Fitur yang diakses berkala (misal: menu tutup buku bulanan).
  - 1: Halaman statis yang jarang sekali dikunjungi pengguna.

| Kategori Risiko | Rentang Skor | Karakteristik Fitur | Alokasi Strategi Pengujian |
| :--- | :---: | :--- | :--- |
| **Tier 1 (Tinggi)** | 16 - 25 | Pembayaran, autentikasi, enkripsi password, kalkulasi saldo, data medis. | Wajib Unit Test ketat, Integration Test dengan database nyata, dan E2E happy path. |
| **Tier 2 (Sedang)** | 8 - 15 | Filter pencarian, ekspor CSV, manajemen profil pengguna, form kontak. | Integration Test pada API level dan pengujian manual terarah (*exploratory*). |
| **Tier 3 (Rendah)** | 1 - 7 | Halaman statis 'Tentang Kami', banner promosi musiman, footer link. | Cukup verifikasi visual manual dan static linting. |

---

## 4. Metrik Kualitas Perangkat Lunak (Quality Metrics)

Ukur kualitas perangkat lunak dengan metrik yang memberikan wawasan nyata, bukan sekadar angka semu (*vanity metrics*):

1. **Mitos Code Coverage vs Nilai Nyata**:
   - Menuntut 100% code coverage sering kali menghasilkan kode tes manipulatif tanpa assertion tegas hanya untuk memuaskan laporan CI.
   - Targetkan **75% - 85% coverage** pada modul yang memuat *core business logic* dan perhitungan domain penting.
2. **Defect Escape Rate (DER)**:
   - Persentase bug yang lolos ke lingkungan produksi dibandingkan total cacat yang ditemukan.
   - Formula: $\text{DER} = \frac{\text{Bug Production}}{\text{Bug Internal} + \text{Bug Production}} \times 100\%$
   - Indikator sehat: DER di bawah 10%. Jika melonjak > 20%, evaluasi celah pengujian pada sprint sebelumnya.
3. **Mean Time to Detect (MTTD) & Mean Time to Recover (MTTR)**:
   - MTTD: Durasi rata-rata dari bug pertama kali muncul di server hingga tim menyadari masalah tersebut.
   - MTTR: Durasi rata-rata dari bug dilaporkan hingga perbaikan berhasil diterapkan di produksi.
4. **Flaky Test Rate**:
   - Persentase tes otomatis yang menghasilkan status gagal tanpa adanya perubahan kode aktual. Harus ditekan di bawah 2%.

---

## 5. Strategi Lingkungan Pengujian (Test Environments)

Jalankan pengujian pada lingkungan yang terisolasi dengan tujuan yang tegas:

```
[ Local Dev ] --------> [ CI Pipeline ] --------> [ Staging / UAT ] --------> [ Production ]
Unit & Mock DB          Fast Unit, Static &       Full Integration & E2E      Canary Release &
In-Memory Execution     Integration Containers    Sanitized Production Data   Synthetic Monitoring
```

1. **Local Machine (Laptop Developer)**:
   - Menjalankan unit test instan dan perulangan cepat pengujian integrasi lokal menggunakan container Docker ringan.
2. **CI Pipeline (Continuous Integration)**:
   - Mesin runner otomatis (GitHub Actions, GitLab CI) yang mengeksekusi linter, build verifikasi, unit test, dan integration test di setiap Pull Request. PR diblokir jika salah satu tes gagal.
3. **Staging / UAT (User Acceptance Testing)**:
   - Lingkungan replika produksi yang stabil. Digunakan untuk pengujian integrasi pihak ketiga yang sebenarnya, verifikasi performa, dan pengujian penerimaan pengguna oleh Product Owner. Menggunakan data uji tersanitasi.
4. **Production**:
   - Verifikasi pasca-deployment menggunakan *smoke testing* ringan (health check endpoint) dan observabilitas melalui alerting dan monitoring metrik pengguna asli.

### Strategi Data Pengujian Antar Lingkungan:
- **Local**: Data in-memory tiruan (*factories*) atau SQLite/Docker volume lokal yang dapat di-reset instan.
- **CI**: Database ephemeral baru yang dibuat per test runner melalui container Docker lalu langsung dimusnahkan.
- **Staging**: Snapshot data anonim yang telah melalui proses scrubbing (menghilangkan password riil, NIK, dan email asli nasabah).

---

## 6. Pembagian Peran dan Tanggung Jawab (RACI Matrix)

| Aktivitas Kualitas | Developer | QA Engineer / Specialist | Product Owner | DevOps / SRE |
| :--- | :---: | :---: | :---: | :---: |
| Penulisan Unit Test | **R / A** | C | I | I |
| Penulisan Integration Test API | **R / A** | C | I | I |
| Perancangan Automated E2E Suite | C | **R / A** | I | C |
| Exploratory Testing Mendalam | C | **R / A** | C | I |
| Validasi Acceptance Criteria (UAT) | C | C | **R / A** | I |
| Pemeliharaan Pipeline CI Testing | C | C | I | **R / A** |

*Keterangan: R = Responsible (pelaksana), A = Accountable (penanggung jawab akhir), C = Consulted (dikonsultasikan), I = Informed (diinformasikan).*

---

## 7. Matriks Tooling Berdasarkan Layer Pengujian

| Layer Pengujian | Ekosistem TypeScript / Node.js | Ekosistem Python | Ekosistem Go |
| :--- | :--- | :--- | :--- |
| **Static Analysis** | ESLint, TypeScript Compiler, Biome | Flake8, Ruff, Mypy | `golangci-lint` |
| **Unit Testing** | Vitest, Jest | Pytest, Unittest | Built-in `testing`, Testify |
| **Integration Testing** | Testcontainers-node, Supertest | Testcontainers-python | Testcontainers-go |
| **API & Contract Testing** | Playwright API, Pact, Newman | Requests, Schemathesis | Built-in `net/http/httptest` |
| **End-to-End (E2E)** | Playwright, Cypress | Playwright-python | Playwright-go |
| **Performance / Load** | k6, Artillery | Locust | k6, Vegeta |

---

## 8. Alur Eksekusi Pengujian Otomatis (Continuous Testing Pipeline)

Agar tim pengembang tidak terhambat saat bekerja harian, bagi alur eksekusi tes menjadi tahapan sekuensial:
1. **Pre-commit Hook (Husky / Git hook)**: Format kode otomatis dan jalankan type-check ringan (< 5 detik).
2. **Pull Request CI Check**: Jalankan linter lengkap, unit test, dan integration test API (< 4 menit).
3. **Post-Merge to Main**: Jalankan visual regression test dan critical E2E happy path (< 10 menit).
4. **Nightly Cron Build**: Jalankan full regression suite, performance benchmark k6, dan security vulnerability dependency scan (1 - 2 jam).

---

## 9. Kesalahan Umum dalam Strategi Pengujian (Common Mistakes)

1. **The Ice Cream Cone Anti-Pattern**: Mengabaikan unit dan integration test, tetapi membangun ratusan skenario manual atau E2E UI test yang lambat, rentan patah, dan mahal dipelihara.
2. **Testing Implementation Details**: Menguji variabel internal atau method private alih-alih menguji kontrak input-output publik. Akibatnya, setiap kali kode direfaktor, tes langsung gagal walaupun fungsi program tidak rusak.
3. **Menoleransi Flaky Tests**: Membiarkan tes yang kadang hijau kadang merah di CI dan menyuruh tim cukup klik tombol *re-run*. Hal ini meruntuhkan disiplin dan keyakinan tim terhadap sistem otomatisasi.
4. **Uji Coba Langsung di Production**: Mengabaikan pengujian staging dan mengandalkan laporan keluhan konsumen di media sosial sebagai mekanisme deteksi bug.
5. **Data Uji Usang**: Membiarkan skenario uji gagal karena data kalender yang hardcoded di tahun lampau atau akun uji yang terhapus secara manual.

---

## 10. Dokumen Terkait

- [Unit Testing Guide](./unit-testing.md) — Panduan penulisan unit test bersih dengan AAA pattern.
- [Integration Testing Guide](./integration-testing.md) — Pengujian batas subsistem dan database Testcontainers.
- [End-to-End Testing Guide](./end-to-end-testing.md) — Otomasi pengujian peramban untuk alur pengguna kritis.
- [Regression Testing Guide](./regression-testing.md) — Strategi pemeliharaan test suite pra-rilis.
- [Definition of Done](./definition-of-done.md) — Kriteria kelayakan mutlak sebelum rilis kode ke produksi.
