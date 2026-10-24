# Dependency Management Guide

Panduan pengelolaan pustaka pihak ketiga (*third-party dependencies*), standarisasi package manager, integritas lockfile, evaluasi keamanan, dan strategi pembaruan rutin.

---

## 1. Ekosistem Package Manager Modern

Pilih package manager yang efisien, deterministik, dan didukung penuh oleh tim:

| Ekosistem | Rekomendasi Utama | Alternatif | Karakteristik Kunci |
| :--- | :--- | :--- | :--- |
| **Node.js / TypeScript** | `pnpm` | `npm`, `yarn` | Menggunakan hard link & content-addressable store, menghemat disk space drastis, mencegah phantom dependencies. |
| **Python** | `poetry` atau `uv` | `pip + venv` | `poetry` dan `uv` menyediakan manajemen lockfile otomatis, deterministik, dan resolusi dependensi yang ketat. |
| **Go** | `go modules` | - | Standar resmi bawaan bahasa Go (`go.mod` dan `go.sum`) dengan checksum database publik terpusat. |

**Aturan Tim:** Dalam satu repositori, wajib menetapkan satu package manager resmi. Jangan mencampur penggunaan `npm install` dengan `pnpm install` karena akan merusak konsistensi pohon dependensi lokal.

### Penguncian Versi Runtime (`engines`):
Tentukan versi Node.js dan package manager minimum di `package.json`:
```json
{
  "engines": {
    "node": ">=20.0.0",
    "pnpm": ">=9.0.0"
  },
  "packageManager": "pnpm@9.12.0"
}
```

---

## 2. Kategori Dependensi (Dependencies Types)

Penempatan dependensi pada kategori yang salah dapat memperbesar ukuran bundle produksi atau merusak runtime:

| Tipe Dependensi | Penjelasan | Contoh Paket |
| :--- | :--- | :--- |
| **`dependencies`** | Pustaka yang dieksekusi langsung pada runtime produksi aplikasi. | `express`, `pg`, `zod`, `react` |
| **`devDependencies`** | Pustaka pembantu saat proses development, kompilasi, linting, dan testing. | `typescript`, `eslint`, `vitest`, `prettier` |
| **`peerDependencies`** | Pustaka yang diharapkan disediakan oleh aplikasi induk (biasanya pada pembuatan plugin/library bersama). | `react` pada UI library modular |

### Dependensi Internal dalam Workspace (Monorepo)
Jika mengelola monorepo, gunakan protokol workspace untuk mereferensikan paket internal:
```json
{
  "dependencies": {
    "@my-org/shared-types": "workspace:*"
  }
}
```
Protokol ini memastikan package manager selalu menghubungkan kode internal terkini melalui *symlink* tanpa mengunduh paket palsu dari registry publik.

---

## 3. Mengapa Lockfile Wajib Selalu di-Commit

File lock (`pnpm-lock.yaml`, `package-lock.json`, `poetry.lock`, `go.sum`) mencatat snapshot eksak dari seluruh pohon dependensi, termasuk sub-dependensi rekursif berserta hash integritas kriptografisnya (`sha512`).

### Tiga Alasan Krusial:
1. **Deterministik Lintas Mesin:** Menjamin bahwa kode yang berjalan di laptop developer A, laptop developer B, server staging, dan container produksi menggunakan versi byte paket yang persis sama.
2. **Mencegah "Works on My Machine":** Tanpa lockfile, build CI yang berjalan hari ini bisa mengunduh versi rilis sub-dependensi baru yang rusak (*broken release*) meskipun tidak ada perubahan kode di aplikasi Anda.
3. **Instalasi Aman & Cepat di CI/CD:** Selalu gunakan perintah instalasi ketat di pipeline CI yang menolak perubahan lockfile tanpa sengaja:
   - Node (npm): `npm ci`
   - Node (pnpm): `pnpm install --frozen-lockfile`
   - Python (poetry): `poetry install --no-root --sync`

### Menyelesaikan Konflik Git pada Lockfile:
Jangan pernah menyelesaikan merge conflict pada file lock secara manual dengan teks editor:
```bash
# 1. Ambil versi branch target:
git checkout --ours pnpm-lock.yaml

# 2. Regenerasi ulang lockfile secara otomatis:
pnpm install

# 3. Stage dan selesaikan merge:
git add pnpm-lock.yaml
git commit -m "chore(deps): regenerate lockfile after merge"
```

---

## 4. Semantic Versioning dalam Dependensi

Pustaka open-source umumnya mengikuti Semantic Versioning: `MAJOR.MINOR.PATCH` (contoh: `2.4.1`).

| Simbol di `package.json` | Arti | Rentang Pembaruan yang Diizinkan |
| :--- | :--- | :--- |
| **Caret (`^2.4.1`)** | Mengizinkan pembaruan Minor dan Patch. | `>= 2.4.1 < 3.0.0` (Fitur baru & perbaikan bug, no breaking changes). |
| **Tilde (`~2.4.1`)** | Hanya mengizinkan pembaruan Patch. | `>= 2.4.1 < 2.5.0` (Hanya perbaikan bug). |
| **Exact Pin (`2.4.1`)** | Versi terkunci mutlak. | Hanya versi `2.4.1`. Wajib untuk package yang sering memicu regresi. |

**Rekomendasi Tim:** Gunakan caret (`^`) secara default pada aplikasi reguler asalkan didukung dengan automated testing dan lockfile yang di-commit. Untuk library inti atau mission-critical, pertimbangkan exact pinning.

---

## 5. Evaluasi Dependensi Baru (Vetting Process)

Sebelum menambahkan dependensi baru (`pnpm add <pkg>`), lakukan evaluasi menggunakan checklist kriteria berikut:

1. **Prinsip YAGNI & Native Platform:** Apakah masalah ini benar-benar membutuhkan library eksternal, atau bisa diselesaikan dengan 5-10 baris fungsi native / Web API modern? (Contoh: gunakan `fetch` native alih-alih `axios`, gunakan `crypto` native alih-alih paket enkripsi tidak terawat).
2. **Ukuran Bundle (Frontend):** Periksa dampak berat bundle melalui alat seperti `bundlephobia.com`. Tolak library yang tidak mendukung *tree-shaking*.
3. **Aktivitas Pemeliharaan:** Periksa commit terakhir, frekuensi rilis, dan rasio open issues di GitHub. Hindari library yang tidak diperbarui lebih dari 12 bulan (*abandonware*).
4. **Kompatibilitas Lisensi:**
   - *Aman:* MIT, Apache 2.0, BSD, ISC.
   - *Berisiko (Wajib Tinjauan Legal):* GPL, AGPL (lisensi copyleft dapat memaksa repositori proprietary dibuka ke publik).
5. **Popularitas & Reputasi Keamanan:** Periksa jumlah unduhan mingguan dan riwayat CVE kerentanan keamanan di masa lampau.

---

## 6. Pemindaian Keamanan & Kebijakan Remediasi

Otomatisasi pemeriksaan celah keamanan dependensi pada alur kerja harian:

- **Audit Bawaan CLI:** Jalankan `pnpm audit` atau `npm audit` secara lokal dan di CI.
- **Bot Pembaruan Otomatis (Dependabot / Renovate):** Pasang bot di repositori Git untuk otomatis membuat Pull Request pembaruan versi paket saat ditemukan celah keamanan baru.

### SLA Penanganan Kerentanan Berdasarkan Skor CVSS:
| Tingkat Keparahan | Nilai CVSS | Batas Waktu Remediasi (SLA) | Tindakan Wajib |
| :--- | :--- | :--- | :--- |
| **Critical** | 9.0 - 10.0 | < 24 Jam | Upgrade segera atau cabut paket; rilis hotfix darurat ke produksi. |
| **High** | 7.0 - 8.9 | < 7 Hari Kerja | Masukkan ke sprint aktif, update versi, uji regresi. |
| **Medium** | 4.0 - 6.9 | < 30 Hari | Update pada siklus sprint maintenance berikutnya. |
| **Low** | 0.1 - 3.9 | Sesuai Kebutuhan | Update saat ada revisi dependensi berkala. |

---

## 7. Strategi Pembaruan Dependensi yang Aman

Pembaruan pustaka tidak boleh dilakukan serentak tanpa perencanaan:

```text
Cadence Pembaruan Dependensi:
├── PATCH Updates   --> Update mingguan (otomatis via Dependabot / PR terpisah)
├── MINOR Updates   --> Update 2-4 minggu sekali (uji unit & integrasi)
└── MAJOR Updates   --> Rencanakan khusus dalam sprint task (baca migration guide)
```

### Resep Praktis Pembaruan Dependensi:
Gunakan tool seperti `npm-check-updates` (ncu) untuk memeriksa versi paket secara selektif:

```bash
# Periksa paket yang memiliki pembaruan minor/patch:
pnpm dlx npm-check-updates --target minor

# Terapkan pembaruan minor ke package.json:
pnpm dlx npm-check-updates -u --target minor

# Install dan perbarui file lockfile secara deterministik:
pnpm install
```

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Mengabaikan Lockfile:** Menambahkan lockfile ke `.gitignore` atau membiarkan versi lockfile tidak sinkron dengan manifest `package.json`.
2. **Menambah Library untuk Masalah Sepele:** Memasang dependensi seperti `is-odd`, `pad-left`, atau fungsi array sederhana yang dapat ditulis dalam 1 baris kode sendiri.
3. **Memakai Dependensi yang Ditinggalkan (Deprecated):** Menggunakan paket yang sudah tidak dirawat pembuatnya dan memiliki lusinan peringatan kerentanan.
4. **Mengupdate Semua Dependensi Sekaligus:** Menjalankan perintah pembaruan massal (`pnpm update --latest`) menjelang tanggal rilis produksi.
5. **Tidak Memeriksa Lisensi Perangkat Lunak:** Memasukkan library berlisensi viral ke dalam produk tertutup komersial tanpa konsultasi legal.

---

## 9. Dokumen Terkait

- [Environment Configuration](environment-configuration.md)
- [Coding Standards](coding-standards.md)
- [Repository Structure](repository-structure.md)
- [Branching and Commits](branching-and-commits.md)
