# Environment Configuration Guide

Panduan manajemen konfigurasi lingkungan, pemisahan credential, validasi runtime, dan proteksi rahasia (*secrets*) sesuai prinsip industri modern.

---

## 1. Prinsip Konfigurasi 12-Factor App

Menurut metodologi **The Twelve-Factor App (Faktor III - Config)**:
- **Pemisahan Ketat antara Kode dan Konfigurasi:** Basis kode aplikasi harus sepenuhnya agnostik terhadap lingkungan tempat ia berjalan. Kode yang berjalan di laptop lokal, staging, dan produksi harus 100% identik dari binary/commit yang sama.
- **Konfigurasi Disuntikkan via Variabel Lingkungan:** Nilai yang bervariasi antar-lingkungan (kredensial database, endpoint API eksternal, port server) harus disimpan dalam variabel lingkungan OS (*env vars*), bukan di-hardcode ke file kode.
- **Dilarang Mengelompokkan File Konfigurasi ke Format Kode:** Jangan membuat file seperti `config.production.json` atau `config.staging.ts` yang di-commit ke Git.

---

## 2. Pemisahan Lingkungan (Environment Separation)

Sistem wajib memisahkan lingkungan secara tegas guna mencegah kebocoran data dan gangguan operasional:

| Lingkungan | Tujuan | Data yang Digunakan | Mekanisme Penyuntikan |
| :--- | :--- | :--- | :--- |
| **Local (Dev Machine)** | Pengembangan harian dan debugging fitur baru. | Mock data atau database lokal (Docker container). | File `.env.local` yang diabaikan Git. |
| **Development** | Integrasi berkelanjutan branch tim internal. | Data dummy / sintetis tersanitasi. | Pipeline CI/CD runner env vars. |
| **Staging (UAT)** | Simulasi identik dengan produksi untuk uji QA dan persetujuan klien. | Data dummy yang mirip produksi (anonymized data). | Secret Manager terisolasi (staging cluster). |
| **Production** | Melayani pengguna nyata dan transaksi bisnis langsung. | Data bisnis riil dengan proteksi enkripsi tinggi. | Dedicated Cloud Secret Manager + IAM role ketat. |

---

## 3. Matriks Manajemen File `.env`

Hierarki pengelolaan file variabel lingkungan dalam repositori proyek:

| File | Status Git | Tujuan Utama | Contoh Nilai |
| :--- | :--- | :--- | :--- |
| **`.env.example`** | **Wajib Commit** | Template publik seluruh key yang diperlukan aplikasi. | `DATABASE_URL=postgresql://user:pass@localhost:5432/db` |
| **`.env.local`** | **Dilarang Commit** | Credential lokal developer di mesin masing-masing. | Password database postgres lokal, sandbox API key. |
| **`.env.test`** | **Opsional Commit** | Konfigurasi mock database saat menjalankan unit/integration test. | `DATABASE_URL=postgresql://test:test@localhost:5433/test_db` |
| **`.env.production`** | **Dilarang Keras** | Jangan pernah membuat file ini; gunakan Secret Manager di cloud. | - |

### Template Standar `.env.example`:
```bash
# Server Configuration
PORT=3000
NODE_ENV=development # development | staging | production

# Database Connection (Local Docker)
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/mydb?schema=public"

# Auth & Tokens
JWT_SECRET="ganti_dengan_random_string_minimal_32_karakter"
SESSION_EXPIRY_HOURS=24

# External Payment Gateway (Sandbox)
PAYMENT_API_KEY="sandbox_sk_dummy_12345"
PAYMENT_BASE_URL="https://sandbox.api.payment.com"

# Feature Flags
ENABLE_NEW_CHECKOUT=false
```

---

## 4. Manajemen Rahasia (Secrets Management)

### A. Larangan Hardcoded Secrets
Menyimpan password, connection string, atau access token di repositori kode publik maupun privat adalah pelanggaran keamanan fatal. Sekali ter-push ke Git, riwayat commit menyimpannya selamanya meskipun file telah dihapus pada commit berikutnya.

### B. Penyimpanan Rahasia di Lingkungan Produksi
Gunakan platform penyedia rahasia terpusat:
- **Cloud Native:** AWS Secrets Manager / Parameter Store (SSM), GCP Secret Manager, Azure Key Vault.
- **Dedicated Tools:** HashiCorp Vault, Doppler, Infisical.
- Penyuntikan ke container dilakukan secara runtime menggunakan IAM Service Account tanpa menulis file rahasia ke disk.

```bash
# Contoh eksekusi aplikasi menggunakan Doppler CLI:
doppler run -- node dist/main.js

# Contoh pengambilan secrets dari AWS SSM di container startup:
aws ssm get-parameters-by-path --path "/production/app/" --with-decryption
```

### C. Pemindaian Pre-commit Otomatis
Pasang pemindai credential otomatis untuk mencegah human error:

| Tool | Cara Kerja | Keunggulan |
| :--- | :--- | :--- |
| **Gitleaks** | Scan commit history dan staged files mencari format key & high entropy string. | Cepat, binary mandiri, mudah dipasang di pre-commit hook dan GitHub Actions. |
| **TruffleHog** | Mendeteksi rahasia dan melakukan verifikasi apakah key tersebut masih aktif (*live verification*). | Menghindari false positive pada key yang sudah mati. |
| **git-secrets** | Mencegah commit yang memuat pola regex credential AWS/cloud tertentu. | Ringan, fokus pada pola regex kustom. |

```bash
# Contoh eksekusi gitleaks pada CI pipeline:
gitleaks detect --source . --verbose
```

---

## 5. Validasi Konfigurasi Saat Startup (Fail Fast)

Jangan biarkan aplikasi berjalan setengah jalan lalu crash di tengah proses transaksi karena variabel lingkungan penting hilang. Validasi seluruh env vars saat aplikasi boot menggunakan skema deklaratif (**Zod**):

```typescript
// src/config/env.ts
import { z } from "zod";

const envSchema = z.object({
  NODE_ENV: z.enum(["development", "staging", "production"]).default("development"),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url("DATABASE_URL harus berupa URL database valid"),
  JWT_SECRET: z.string().min(32, "JWT_SECRET wajib memiliki panjang minimal 32 karakter"),
  ENABLE_NEW_CHECKOUT: z.enum(["true", "false"]).transform((v) => v === "true").default("false"),
});

const parseEnv = () => {
  const result = envSchema.safeParse(process.env);
  if (!result.success) {
    console.error("❌ Kesalahan Konfigurasi Variabel Lingkungan:");
    console.error(JSON.stringify(result.error.format(), null, 2));
    process.exit(1); // Fail Fast: hentikan proses booting segera
  }
  return result.data;
};

export const env = parseEnv();
```

---

## 6. Dasar Konfigurasi Feature Flags

Feature flags memungkinkan tim merilis kode ke produksi tanpa langsung mengaktifkan fitur tersebut ke seluruh pengguna:
- **Tujuan:** Memisahkan proses *code deployment* dari *feature release*, mempermudah A/B testing, dan menjadi *kill-switch* darurat saat fitur mengalami insiden.
- **Tingkat Sederhana (Env-based):** Gunakan env flag untuk fitur yang belum stabil:
  ```typescript
  if (env.ENABLE_NEW_CHECKOUT) {
    return renderNewCheckoutFlow();
  }
  return renderLegacyCheckoutFlow();
  ```
- **Tingkat Lanjut (Dynamic Flags):** Gunakan platform seperti Unleash, PostHog, LaunchDarkly, atau GrowthBook untuk mengatur rollout bertahap (10% -> 50% -> 100%) berdasarkan atribut user tanpa redeployment kode.

---

## 7. Checklist Kesiapan Konfigurasi Produksi

Sebelum merilis versi baru ke lingkungan produksi, verifikasi hal-hal berikut:
1. Seluruh variabel baru pada kode sudah terdaftar di `.env.example`.
2. Nilai rahasia produksi sudah disuntikkan ke Cloud Secret Manager (bukan file lokal).
3. Secret scanning pipeline (Gitleaks) lulus tanpa peringatan.
4. Nilai `NODE_ENV` bernilai `production` untuk mengaktifkan optimasi performa framework.
5. Fitur eksperimental yang belum stabil dimatikan via feature flag.

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Commit File `.env` ke Git:** Merilis rahasia ke remote repository karena lupa menambahkan `.env` ke `.gitignore`.
2. **Tidak Memperbarui `.env.example`:** Menambahkan dependensi env baru di kode tanpa memperbarui template, membuat rekan satu tim crash saat pull code.
3. **Mengakses `process.env` Tercecer di Seluruh Kode:** Mengakses raw env langsung di dalam komponen UI atau service mendalam alih-alih melalui modul konfigurasi tervalidasi terpusat.
4. **Membocorkan Secret ke Client-side:** Menggunakan prefix public env (seperti `NEXT_PUBLIC_` atau `VITE_`) untuk secret API key backend, sehingga terbaca di browser pengguna.
5. **Konfigurasi Statis dalam Docker Image:** Memasukkan credential langsung saat `docker build`, sehingga siapapun yang memiliki akses docker image dapat mengekstrak rahasia tersebut.

---

## 9. Dokumen Terkait

- [Repository Structure](repository-structure.md)
- [Coding Standards](coding-standards.md)
- [Developer Onboarding](developer-onboarding.md)
- [Logging and Error Handling](logging-and-error-handling.md)
