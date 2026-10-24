# Dependency Updates & Package Maintenance

Panduan operasional pemeliharaan pustaka pihak ketiga (*third-party dependencies*) untuk menjaga keamanan, stabilitas, dan keterbaruan ekosistem proyek.

---

## 1. Urgensi Pemeliharaan Dependensi

Dalam arsitektur modern, lebih dari 70-80% baris kode dalam aplikasi produksi berasal dari open-source packages (npm, Go modules, PyPI, crates, dsb.). Mengabaikan pembaruan dependensi menimbulkan tiga risiko utama:

1. **Security Vulnerabilities (CVE):** Penyerang mengeksploitasi kelemahan yang telah dipublikasikan di database CVE/NVD (*known exploits*).
2. **Ecosystem Drift & Breaking Changes:** Menunda upgrade selama bertahun-tahun menyebabkan tim tertinggal beberapa versi major, membuat migrasi masa depan sangat mahal dan berisiko tinggi.
3. **Abandonment & Supply Chain Attack:** Pustaka yang tidak dirawat (*abandoned*) rawan ditinggalkan pembuatnya, kehilangan kompatibilitas dengan runtime baru, atau mengalami pengambilalihan akun package oleh pihak jahat (*malicious takeover*).

---

## 2. Klasifikasi Pembaruan Berdasarkan SemVer

Gunakan prinsip **Semantic Versioning** (`MAJOR.MINOR.PATCH`) untuk menentukan ritme dan level verifikasi:

```
Versi:  1   .   4   .   2
        ^       ^       ^
      MAJOR   MINOR   PATCH
```

| Tipe Versi | Dampak Perubahan | Jadwal Pembaruan | Strategi & Mekanisme |
|---|---|---|---|
| **PATCH** (`0.0.X`) | Bugfix, patch keamanan, backward-compatible. | Mingguan (*Weekly*) | Otomatis via bot (Dependabot / Renovate) + auto-merge jika test CI lulus. |
| **MINOR** (`0.X.0`) | Fitur baru, non-breaking, backward-compatible. | Bulanan (*Monthly batch*) | Review changelog, review PR terkelompok (*grouped PR*), uji di branch staging. |
| **MAJOR** (`X.0.0`) | Breaking changes, perubahan API, arsitektur baru. | Kuartalan (*Quarterly spike*) | Buat spike ticket khusus, baca migration guide, alokasikan 1-3 hari kerja. |

---

## 3. Pipeline Otomatisasi Pembaruan Dependensi

Mekanisme otomatisasi memangkas beban kognitif developer tanpa mengorbankan kualitas rilis:

```
+------------------+      +------------------+      +-------------------+      +------------------+
| Bot PR           |      | Automated CI     |      | Safety Checks     |      | Auto-Merge       |
| (Renovate /      | ---> | Test Suite       | ---> | (Linter, E2E,     | ---> | (Hanya untuk     |
| Dependabot)      |      | (Unit/Integ Test)|      | Build Artifact)   |      | Patch Teruji)    |
+------------------+      +------------------+      +-------------------+      +------------------+
```

### Konfigurasi Dasar Renovate / Dependabot
- **Grouped Pull Requests:** Kelompokkan patch non-breaking dalam satu PR tunggal mingguan agar reviewer tidak dibanjiri notifikasi (*PR fatigue*).
- **Automated CI Validation:** Jangan pernah mengizinkan auto-merge tanpa verifikasi build, type check, unit test, dan smoke test yang lulus 100%.
- **Lockfile Integrity:** Pastikan `package-lock.json`, `pnpm-lock.yaml`, `go.sum`, atau `poetry.lock` selalu di-commit ke source control untuk menjamin determinisme build.

Contoh konfigurasi sederhana `dependabot.yml`:
```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    open-pull-requests-limit: 10
    groups:
      dev-dependencies:
        patterns:
          - "@types/*"
          - "eslint*"
          - "prettier*"
      production-patches:
        update-types:
          - "patch"
```

---

## 4. Response SLA Terhadap Kerentanan Keamanan (CVSS)

Ketika alat pemindaian keamanan (GitHub Security Alerts, Snyk, Trivy) mendeteksi celah keamanan, tim wajib merespons sesuai **Common Vulnerability Scoring System (CVSS)**:

| Severity Level | Skor CVSS | Target SLA Resolusi | Contoh Skenario & Tindakan |
|---|---|---|---|
| **CRITICAL** | 9.0 - 10.0 | **< 24 Jam** | Remote Code Execution (RCE) tanpa autentikasi. Segera rilis hotfix atau terapkan temporary patch/WAF rule. |
| **HIGH** | 7.0 - 8.9 | **< 7 Hari** | Privilege escalation, data leakage signifikan. Jadwalkan update darurat pada sprint berjalan. |
| **MEDIUM** | 4.0 - 6.9 | **< 30 Hari** | Denial of Service terisolasi, XSS dengan interaksi pengguna spesifik. Masukkan ke sprint backlog terdekat. |
| **LOW** | 0.1 - 3.9 | **< 90 Hari** | Celah informasi non-sensitif, perbaiki saat siklus rilis reguler atau batch update berikutnya. |

---

## 5. Checklist Verifikasi Manual (Minor & Major Updates)

Sebelum melakukan merge PR untuk pembaruan minor atau major:
- [ ] **Baca Changelog / Release Notes:** Periksa bagian *Breaking Changes*, *Deprecations*, dan *Security*.
- [ ] **Analisis Ukuran Bundle:** Periksa apakah pembaruan menyebabkan pembengkakan bundle JS/biner (*bundle bloat*).
- [ ] **Kesesuaian Lisensi (License Compliance):** Pastikan lisensi package tetap permisif (MIT, Apache 2.0, BSD) dan tidak berubah menjadi copyleft ketat (GPL/AGPL) yang berisiko bagi proprietary software.
- [ ] **Uji Coba Staging:** Jalankan smoke test skenario kritis (checkout, login, data export) di staging environment.

---

## 6. Menangani Deprecated dan Abandoned Packages

Tanda-tanda paket open-source tidak sehat:
- Tidak ada commit atau rilis baru dalam 12-18 bulan terakhir.
- Ratusan unresolved issue dan open pull request tanpa respon maintainer.
- Menggunakan API runtime yang telah deprecated (misal: Node.js buffer API lawas).

**Langkah Mitigasi:**
1. **Audit Kebutuhan Nyata:** Apakah fungsi pustaka tersebut benar-benar diperlukan? Bisakah digantikan oleh 10-20 baris kode standard library modern?
2. **Cari Alternatif Aktif:** Pilih pustaka pengganti yang memiliki komunitas aktif, dokumentasi lengkap, dan track record keamanan yang baik.
3. **Vendor / Forking Sementara:** Jika pengganti belum tersedia dan fungsi sangat krusial, fork repository ke namespace organisasi tim, perbaiki issue keamanan secara lokal, dan dokumentasikan rencana penggantian permanen.

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Blind Auto-Update pada Major Version:** Mengizinkan wildcard (`"package": "*"`) atau bot auto-merge untuk major version yang membawa breaking changes ke production.
2. **Ignorance Terhadap Security Advisory:** Mematikan bot alert karena merasa terganggu dengan email notifikasi tanpa mendelegasikan tanggung jawab review.
3. **Lockfile Desynchronization:** Menjalankan update langsung di package file tanpa memperbarui lockfile, memicu perbedaan versi antara mesin developer dan server CI/CD.
4. **Menambahkan Dependency untuk Masalah Sepele:** Memasang package 500 KB hanya untuk memformat tanggal sederhana atau melakukan pengecekan `is-number`.

---

## 8. Dokumen Terkait

- [Technical Debt](technical-debt.md) — Menghitung biaya dan prioritas pelunasan hutang dependensi usang.
- [Performance Review](performance-review.md) — Meninjau dampak ukuran bundle dependency terhadap performa frontend.
- [Deprecation and Migration](deprecation-and-migration.md) — Pola migrasi ketika mengganti library core dengan alternatif baru.
