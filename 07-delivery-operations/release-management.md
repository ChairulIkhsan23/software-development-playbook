# Release Management Guide

Panduan tata kelola rilis perangkat lunak, standardisasi Semantic Versioning, orkestrasi pemisahan deploy dan rilis melalui feature flags, serta otomatisasi changelog.

Template acuan: [Release Plan Template](../templates/delivery/release-plan-template.md)

---

## 1. Perbedaan Mendasar: Release vs Deployment

Banyak tim pemula menyamakan *deployment* dengan *release*. Memisahkan kedua konsep ini adalah kunci stabilitas pengiriman perangkat lunak modern:

- **Deployment (Operasional Teknis):** Tindakan memasang artefak kode biner/container baru ke server atau cluster produksi. Pengguna belum tentu melihat atau menggunakan fitur baru tersebut.
- **Release (Dampak Bisnis/Pengguna):** Tindakan mengekspos fitur atau fungsi baru kepada pengguna akhir (misal: menyalakan toggle, membuka akses URL publik, meluncurkan campaign marketing).

```
[Deploy Code ke Prod] -----> [Health Checks Hijau] -----> [Feature Flag ON]
 (Tindakan Teknis)            (Verifikasi Sistem)           (Rilis ke User)
```

Dengan memisahkan deploy dari rilis, risiko rilis menurun drastis karena tim dapat menguji kode di lingkungan produksi langsung sebelum mengaktifkannya bagi publik.

---

## 2. Semantic Versioning (SemVer 2.0.0)

Semua rilis publik, library, dan service API wajib mematuhi standar format:
`vMAJOR.MINOR.PATCH` (contoh: `v2.4.1`)

```
   v 2 . 4 . 1
     │   │   └── PATCH : Perbaikan bug tanpa mengubah API / backward-compatible
     │   └────── MINOR : Penambahan fitur baru yang backward-compatible
     └────────── MAJOR : Perubahan yang merusak kompatibilitas lama (breaking changes)
```

### Aturan Tambahan SemVer:
- **Pre-release:** Gunakan akhiran hyphen untuk versi beta atau release candidate (contoh: `v1.2.0-rc.1`, `v2.0.0-beta.2`).
- **Versi Awal (`0.y.z`):** Digunakan untuk fase pengembangan awal di mana API masih dapat berubah sewaktu-waktu.
- **Kenaikan Angka:** Ketika `MAJOR` dinaikkan, `MINOR` dan `PATCH` wajib di-reset ke `0` (misal: dari `v1.5.3` ke `v2.0.0`).

---

## 3. Ritme Rilis (Release Cadences)

Pilihlah model ritme yang sesuai dengan kematangan otomatisasi pengujian dan kebutuhan bisnis tim:

| Model Cadence | Karakteristik | Cocok Untuk | Risiko & Mitigasi |
|---|---|---|---|
| **Continuous Release** | Setiap PR yang lolos langsung dirilis ke prod beberapa kali sehari | Tim matang dengan test coverage tinggi, CI/CD solid | Risiko regresi cepat menyebar; mitigasi dengan feature flags |
| **Sprint-Based Release** | Rilis di akhir sprint (misal: setiap 2 minggu sekali) | Tim Scrum dengan jadwal pengujian manual atau dependensi klien | Potensi penumpukan diff besar; mitigasi dengan code freeze pendek |
| **Scheduled / Maintenance Window** | Rilis berkala di jam sepi trafik (misal: Selasa dini hari) | Sistem perbankan, backend legacy, database besar | Fatigue bagi tim on-call; mitigasi dengan otomatisasi skrip migrasi |

### Model Release Train
Untuk tim multi-regu (*multi-squad*), gunakan pendekatan **Release Train**:
- "Kereta rilis" berangkat pada jadwal tetap (contoh: setiap Kamis pukul 14:00 WIB).
- Fitur yang sudah selesai dan lolos QA ikut naik ke kereta rilis minggu ini.
- Fitur yang belum siap tidak boleh menunda keberangkatan kereta; fitur tersebut ditinggal dan menunggu jadwal kereta berikutnya.

---

## 4. Standar Changelog & Conventional Commits

Changelog yang rapi mempermudah tim dukungan pelanggan (*support*), QA, dan pengguna dalam melacak perubahan.

Gunakan standar **[Keep a Changelog](https://keepachangelog.com/)** dengan kategori:
- `Added` untuk fitur baru.
- `Changed` untuk perubahan fungsionalitas yang sudah ada.
- `Deprecated` untuk fitur yang segera dihapus pada rilis berikutnya.
- `Removed` untuk fitur yang sudah dihapus.
- `Fixed` untuk perbaikan bug.
- `Security` untuk perbaikan kerentanan keamanan.

### Otomatisasi via Conventional Commits
Dengan menerapkan format commit terstandarisasi:
- `feat: allow users to export PDF invoices` (memicu MINOR)
- `fix: resolve crash on checkout form when voucher empty` (memicu PATCH)
- `feat!: switch auth to OAuth2 only` (memicu MAJOR karena ada tanda seru/breaking change)

Tools seperti `release-please`, `semantic-release`, atau `changesets` akan otomatis menghasilkan file `CHANGELOG.md` dan menaikkan versi SemVer di Git tag tanpa intervensi manual.

---

## 5. Git Tagging & GitHub Releases

Setiap rilis produksi wajib memiliki immutable Git Tag yang sesuai dengan versinya:

```bash
# Membuat tag rilis secara manual
git checkout main
git pull origin main
git tag -a v1.4.0 -m "Release v1.4.0: Billing module overhaul"
git push origin v1.4.0
```

Pada GitHub/GitLab Releases:
- Lampirkan daftar PR yang digabungkan.
- Cantumkan tautan ke commit range pembanding (`https://github.com/.../compare/v1.3.2...v1.4.0`).
- Unggah checksum biner atau artifact build jika mempublikasikan binary/distribusi aplikasi.

---

## 6. Feature Flags untuk Memisahkan Rilis dari Deploy

Feature Flags (Feature Toggles) memungkinkan kode baru berada di produksi dalam status nonaktif (*dark launching*) hingga tim bisnis siap meluncurkannya.

```javascript
// Contoh evaluasi Feature Flag sederhana
const isNewCheckoutEnabled = await featureFlags.isEnabled('new-checkout-flow', user.id);

if (isNewCheckoutEnabled) {
  return renderModernCheckout();
} else {
  return renderLegacyCheckout();
}
```

### Manfaat Utama:
- **Instant Kill-Switch:** Jika fitur baru memicu crash atau spike error di produksi, matikan toggle dalam hitungan detik tanpa perlu redeploy atau rollback git.
- **Canary / Percentage Rollout:** Aktifkan fitur untuk 10% pengguna internal, lalu 25%, hingga 100%.
- **Targeted Testing:** Mengaktifkan fitur hanya untuk email karyawan internal di lingkungan produksi (*dogfooding*).

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Big Bang Releases:** Menimbun perubahan kode selama berbulan-bulan lalu merilis semuanya sekaligus. Ketika terjadi error, sulit mengisolasi commit penyebabnya.
2. **Tidak Menaikkan MAJOR saat Breaking Change:** Mengubah parameter API publik tetapi hanya merilis sebagai rilis `PATCH`, merusak aplikasi klien atau mobile app.
3. **Changelog Manual yang Terabaikan:** Menulis changelog seadanya di pesan chat tanpa dokumentasi terstruktur di Git repository.
4. **Hutang Teknis Feature Flag (Zombie Flags):** Membiarkan ratusan flag lama tetap hidup di codebase setelah fitur dirilis 100%, memperkeruh percabangan logika if-else.
5. **Menunda Rilis Karena Satu Fitur Macet:** Menghentikan seluruh jadwal deployment demi menunggu satu PR yang belum tuntas alih-alih melepaskan PR tersebut dari sprint.

---

## 8. Dokumen Terkait

- [Release Plan Template](../templates/delivery/release-plan-template.md)
- [Release Readiness Checklist](release-readiness.md)
- [Deployment Guide](deployment-guide.md)
- [CI/CD Pipelines](ci-cd.md)
- [Environment Strategy](environment-strategy.md)
