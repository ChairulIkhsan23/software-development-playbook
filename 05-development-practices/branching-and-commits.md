# Branching and Commits Standard

Panduan pengelolaan alur kerja Git, model branching, konvensi penamaan branch, format commit terstandarisasi, dan disiplin riwayat Git untuk efisiensi tim.

---

## 1. Model Git Branching

Pemilihan model branching harus disesuaikan dengan irama rilis dan skala tim teknis:

| Aspek | GitHub Flow (Rekomendasi Utama) | Git Flow | Trunk-Based Development |
| :--- | :--- | :--- | :--- |
| **Deskripsi** | Satu branch utama (`main`), fitur dibuat via short-lived feature branch, deploy langsung setelah merge. | Memiliki branch permanen `main` dan `develop`, serta branch release dan hotfix terpisah. | Developer commit langsung ke `main` atau feature branch berumur sangat pendek (< 1 hari). |
| **Siklus Hidup** | Fitur selesai dalam hitungan jam hingga 2-3 hari. | Siklus rilis terjadwal berkala (mingguan/bulanan). | Rilis terus-menerus beberapa kali sehari (CI/CD matang). |
| **Kelebihan** | Sederhana, minim merge conflict, mudah dipahami solo dev & tim kecil. | Cocok untuk software versioned (desktop, mobile app store). | Integrasi instan, meniadakan merge hell. |
| **Kelemahan** | Membutuhkan automated testing yang solid sebelum merge. | Sangat rumit, branch `develop` sering kotor, overhead manajemen tinggi. | Membutuhkan disiplin tinggi dan *feature flags*. |

**Standar Default Tim:** Gunakan **GitHub Flow**. Semua fitur dan perbaikan bug dibuat dari `main` dan kembali di-merge ke `main` melalui Pull Request.

---

## 2. Konvensi Penamaan Branch

Nama branch harus deskriptif, ringkas, menggunakan huruf kecil, dan dipisahkan tanda strip (`kebab-case`):

```text
<kategori>/<issue-id>-<deskripsi-singkat>
```

### Kategori Resmi:
- `feat/`: Penambahan fitur baru bagi pengguna (contoh: `feat/PAY-101-qris-payment`).
- `fix/`: Perbaikan bug atau kesalahan logika (contoh: `fix/AUTH-42-session-timeout`).
- `refactor/`: Restrukturisasi kode tanpa mengubah perilaku fungsional (contoh: `refactor/order-service-cleanup`).
- `chore/`: Tugas pemeliharaan rutin, konfigurasi CI, atau update tooling (contoh: `chore/bump-node-version`).
- `docs/`: Pembaruan atau penambahan dokumentasi (contoh: `docs/api-contracts`).
- `test/`: Penambahan atau perbaikan unit test dan integration test (contoh: `test/add-payment-e2e`).

---

## 3. Standar Pesan Commit (Conventional Commits)

Format commit pesan wajib mengikuti spesifikasi **Conventional Commits v1.0.0**:

```text
<type>(<scope>): <keterangan singkat imperatif>

[opsional: body penjelasan detail mengapa perubahan dilakukan]

[opsional: footer memuat BREAKING CHANGE atau referensi issue tiket]
```

### Tabel Tipe Commit:
| Tipe | Makna | Contoh |
| :--- | :--- | :--- |
| `feat` | Fitur baru untuk pengguna | `feat(auth): add google oauth2 login handler` |
| `fix` | Perbaikan bug pada sistem | `fix(billing): prevent double charge on fast checkout clicks` |
| `refactor` | Perubahan kode tanpa ubah fungsi/bugfix | `refactor(db): extract user query to dedicated repository` |
| `perf` | Peningkatan performa kode | `perf(search): cache autocomplete queries with redis` |
| `test` | Menambah atau memperbaiki automated tests | `test(order): add integration test for refund scenario` |
| `chore` | Update build config, package, atau tooling | `chore(deps): update prisma ORM to v5.12.0` |
| `docs` | Perubahan dokumentasi saja | `docs(readme): add local setup troubleshooting steps` |

### Aturan Pokok:
1. **Gunakan Kalimat Imperatif:** Tulis "add feature" atau "tambah validasi", bukan "added" atau "menambahkan".
2. **Huruf Kecil & Tanpa Titik:** Keterangan singkat diawali huruf kecil dan tidak diakhiri tanda titik.
3. **Breaking Change (`!`):** Jika commit memperkenalkan perubahan yang merusak kompatibilitas backward, tambahkan tanda `!` sebelum titik dua atau tulis `BREAKING CHANGE:` pada footer.

```bash
# Contoh commit fitur biasa:
git commit -m "feat(auth): add google oauth2 login handler"

# Contoh breaking change:
git commit -m "feat(api)!: remove deprecated v1 user endpoint"
```

---

## 4. Prinsip Atomic Commits

Satu commit hanya boleh merepresentasikan **satu unit perubahan logis yang utuh**:
- **Jangan Menggabungkan Tugas Berbeda:** Jangan satukan perbaikan bug auth dengan perubahan gaya tombol checkout dalam satu commit.
- **Mudah di-Revert:** Jika terjadi error di produksi, commit atomik dapat di-revert secara instan dengan `git revert <hash>` tanpa mencabut fitur lain yang aman.
- **Setiap Commit Wajib Lolos Build:** Jangan commit kode setengah jalan yang merusak kompilasi atau menggagalkan unit test.

---

## 5. Merapikan Riwayat: Interactive Rebase & Squash

Sebelum mengajukan atau menggabungkan Pull Request, rapikan riwayat commit lokal Anda agar riwayat branch utama tetap bersih:

### A. Interactive Rebase Lokal
Gabungkan commit-commit kecil (*typo fix*, *wip*) menjadi commit logis sebelum push ke remote:

```bash
# Rebase 3 commit terakhir di branch lokal
git rebase -i HEAD~3
```

Pada teks editor yang muncul, pilih perintah per commit:
```text
pick e4a1b2c feat(auth): add login form UI
squash a1b2c3d fix typo on input label
squash f5e6d7c update validation error text
```

### B. Strategi Merge: Squash and Merge
Gunakan strategi **Squash and Merge** saat menggabungkan PR ke `main`. Strategi ini merangkum commit iterasi kerja menjadi 1 commit rapi di branch utama dengan pesan PR yang deskriptif, sehingga riwayat commit branch `main` tetap bersih dan linear.

---

## 6. Automasi Git Hooks (Husky & lint-staged)

Cegah kode kotor dan format pesan commit yang keliru masuk ke repositori dengan hook otomatis:

```json
// Konfigurasi pada package.json:
{
  "lint-staged": {
    "*.{ts,tsx,js}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```

### Konfigurasi Commitlint (`commitlint.config.js`):
```javascript
export default {
  extends: ["@commitlint/config-conventional"],
  rules: {
    "type-enum": [
      2,
      "always",
      ["feat", "fix", "refactor", "perf", "test", "chore", "docs"],
    ],
    "subject-case": [2, "never", ["upper-case"]],
  },
};
```

1. **`pre-commit` hook:** Menjalankan `lint-staged` untuk memformat file yang di-stage dan mengecek tipe data TypeScript sebelum commit diizinkan.
2. **`commit-msg` hook:** Memvalidasi format pesan commit menggunakan `@commitlint/cli` agar selalu sesuai dengan spesifikasi Conventional Commits.

---

## 7. Dasar Penandatanganan Commit (Commit Signing)

Gunakan kunci SSH atau GPG untuk menandatangani commit Anda:
- **Tujuan:** Membuktikan secara kriptografis bahwa commit benar-benar dibuat oleh Anda dan bukan pemalsuan identitas author (menampilkan badge **Verified** di GitHub/GitLab).
- **Setup Cepat dengan SSH Key (Git >= 2.34):**

```bash
# Konfigurasi Git untuk sign menggunakan SSH key yang sudah ada:
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

---

## 8. Git Alias Berguna untuk Produktivitas Tim

Tambahkan alias berikut ke file `~/.gitconfig` untuk mempercepat navigasi harian:

```ini
[alias]
  s = status -s
  co = checkout
  br = branch
  lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
  unstage = reset HEAD --
  prune-branches = "!git fetch -p && git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -D"
```

---

## 9. Kesalahan Umum (Common Mistakes)

1. **Commit Langsung ke `main`:** Mendorong kode langsung ke branch produksi tanpa review atau pengujian CI.
2. **Commit Pesan Tidak Bermakna:** Pesan commit seperti "fix", "update", "coba lagi", atau "asdfghjk" yang mempersulit pelacakan git blame.
3. **Mengunggah File Sensitif:** Mengikutsertakan `.env`, password, atau private keys ke dalam git tree.
4. **Long-Lived Feature Branches:** Membiarkan feature branch terbuka selama berminggu-minggu tanpa sinkronisasi dari `main`, memicu *merge conflicts* raksasa.
5. **Git Push Force Tanpa Pengaman:** Menggunakan `git push --force` alih-alih `git push --force-with-lease` pada branch tim, yang berisiko menimpa commit rekan kerja secara permanen.

---

## 10. Dokumen Terkait

- [Code Review Guide](code-review.md)
- [Coding Standards](coding-standards.md)
- [Repository Structure](repository-structure.md)
- [Environment Configuration](environment-configuration.md)
