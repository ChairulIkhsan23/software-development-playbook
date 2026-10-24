---
title: "Template Project Coding Standards"
document_id: "CS-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
owner: "[Nama Tech Lead / Tim Engineering]"
project_name: "[Nama Proyek]"
target_language: "TypeScript / Node.js"
---

# Project Coding Standards & Style Guide

> **Panduan Pengisian:** Tetapkan standar penulisan kode proyek pada dokumen ini sebelum fase implementasi dimulai. Sesuaikan nilai batas ambang dan perkakas sesuai stack proyek tim Anda.

---

## 1. Bahasa & Lingkungan Runtime [Wajib]

> **Panduan Pengisian:** Tentukan versi bahasa, runtime target, serta mode eksekusi yang wajib dipakai semua kontributor.

- **Bahasa Pemrograman [Wajib]:** `TypeScript v[5.x]` / `[Bahasa Lain]`
- **Runtime Environment [Wajib]:** `Node.js v[20 LTS / 22 LTS]` / `[Go / Python / Java]`
- **Package Manager [Wajib]:** `pnpm v[9.x]` / `npm v[10.x]`
- **Target Kompilasi [Wajib]:** `ES2022`

---

## 2. Konfigurasi Strict Mode [Wajib]

> **Panduan Pengisian:** Cantumkan aturan compiler strict guna mencegah bug runtime di produksi.

- **Compiler Options (`tsconfig.json`) [Wajib]:**
  - `"strict": true` (mengaktifkan semua type checking ketat).
  - `"noImplicitAny": true` (wajib mendeklarasikan tipe eksplisit).
  - `"strictNullChecks": true` (pencegahan null / undefined dereference).
  - `"noUncheckedIndexedAccess": true` (keamanan akses array / object lookup).
- **Enforcement [Wajib]:** Gagal kompilasi di CI jika ditemukan pelanggaran strict mode.

---

## 3. Konvensi Penamaan Berkas & Direktori [Wajib]

> **Panduan Pengisian:** Seragamkan struktur folder dan file agar mudah dicari oleh seluruh anggota tim.

- **Direktori [Wajib]:** `kebab-case` (contoh: `user-management/`, `payment-gateway/`).
- **Komponen UI [Wajib]:** `PascalCase.tsx` (contoh: `UserProfileCard.tsx`, `NavBar.tsx`).
- **Modul Utilitas & Service [Wajib]:** `kebab-case.ts` (contoh: `auth-service.ts`, `date-utils.ts`).
- **Berkas Pengujian [Wajib]:** `[nama-file].test.ts` atau `[nama-file].spec.tsx`.

---

## 4. Aturan Casing Kode (Code Casing Rules) [Wajib]

> **Panduan Pengisian:** Gunakan konvensi penamaan identifier kode berikut secara konsisten.

| Tipe Simbol | Konvensi Casing | Contoh Penggunaan | Status |
|---|---|---|---|
| Variabel & Properti | `camelCase` | `totalAmount`, `currentUser` | [Wajib] |
| Fungsi & Method | `camelCase` | `calculateTax()`, `fetchUserById()` | [Wajib] |
| Class, Interface, Type | `PascalCase` | `PaymentProcessor`, `UserAccount` | [Wajib] |
| Konstanta Global / Enum | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT` | [Wajib] |
| Rute URL / API Path | `kebab-case` | `/api/v1/user-profiles` | [Wajib] |

---

## 5. Batasan Clean Code & Kompleksitas [Wajib]

> **Panduan Pengisian:** Tetapkan batasan kuantitatif agar fungsi tetap mudah dibaca dan diuji.

- **Panjang Maksimal Fungsi [Wajib]:** Maksimal `[30]` baris per fungsi (ekstrak jika melebihi).
- **Kedalaman Nesting [Wajib]:** Maksimal `[3]` level indentasi (gunakan early return / guard clauses).
- **Parameter Fungsi [Wajib]:** Maksimal `[3]` parameter posisi; gunakan objek config/DTO jika lebih.
- **Panjang Baris [Wajib]:** Maksimal `[100 / 120]` karakter per baris (auto-wrap via formatter).

---

## 6. Perkakas Linter & Formatter [Wajib]

> **Panduan Pengisian:** Tautkan konfigurasi linter dan formatter resmi yang dikomit ke repositori.

- **Linter Engine [Wajib]:** ESLint / Biome — konfigurasi di `.eslintrc.json` / `biome.json`.
- **Formatter Engine [Wajib]:** Prettier / Biome — konfigurasi di `.prettierrc`.
- **Git Hooks [Wajib]:** Husky + lint-staged mengeksekusi `lint` dan `format` sebelum `git commit`.
- **Perintah Verifikasi [Wajib]:** `pnpm run lint` dan `pnpm run format:check`.

---

## 7. Kebijakan Komentar Kode (Commenting Policy) [Wajib]

> **Panduan Pengisian:** Tulis komentar secara efisien untuk menjelaskan alasan ('Why'), bukan tindakan ('What').

- **Prinsip Utama [Wajib]:** Kode harus *self-documenting* melalui penamaan variabel dan fungsi yang jelas.
- **Komentar Diperbolehkan [Wajib]:** Menjelaskan workaround bug pihak ketiga, trade-off arsitektur, atau rumus matematika kompleks.
- **Komentar Dilarang [Wajib]:** Menjelaskan hal sepele yang sudah jelas terlihat dari kode (contoh: `// inkremen i: i++`).
- **TODO/FIXME [Wajib]:** Wajib menyertakan ID tiket referensi (format: `// TODO(JIRA-123): refactor to batch query`).

*(Seksi aturan khusus di bawah ini opsional: hapus jika tidak relevan untuk platform target)*
## 8. Aturan Khusus Domain / Framework [Opsional]
- **State Management:** `[Aturan lokal: misal melarang mutasi state langsung]`
- **Database Access:** `[Aturan lokal: misal wajib menggunakan query builder / ORM tertentu]`
