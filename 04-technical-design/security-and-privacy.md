# Security and Privacy

## Prinsip Security by Design

Keamanan bukan fitur yang ditambahkan nanti — keamanan adalah fondasi desain sistem.

| Prinsip | Penjelasan | Contoh |
|---------|-----------|--------|
| **Least Privilege** | Berikan akses minimal yang dibutuhkan | Service account hanya punya permission ke tabel yang dipakai |
| **Defense in Depth** | Lapisan keamanan berlapis, jangan bergantung pada satu | WAF + input validation + parameterized query + encrypted storage |
| **Fail Secure** | Jika terjadi error, default ke kondisi aman | Gagal autentikasi = tolak akses, bukan izinkan |
| **Zero Trust** | Jangan percaya siapa pun secara default | Verifikasi setiap request meski dari jaringan internal |

---

## OWASP Top 10 dan Mitigasinya

| # | Ancaman | Mitigasi |
|---|---------|----------|
| A01 | **Broken Access Control** | Enforce authz di backend (bukan hanya UI). Default deny. Cek ownership per resource. |
| A02 | **Cryptographic Failures** | HTTPS everywhere (TLS 1.3). Enkripsi data sensitif at-rest (AES-256). Jangan hardcode secret. |
| A03 | **Injection** | Parameterized queries / ORM. Jangan concatenate string ke SQL/command. |
| A04 | **Insecure Design** | Threat modeling sejak fase desain. Abuse case di requirements. |
| A05 | **Security Misconfiguration** | Matikan debug mode di production. Review default config. Automate hardening. |
| A06 | **Vulnerable Components** | Dependency scanning otomatis (Dependabot/Snyk). Update rutin. |
| A07 | **Auth Failures** | Rate limit login. MFA untuk akun sensitif. Jangan expose apakah email terdaftar. |
| A08 | **Data Integrity Failures** | Verifikasi signature update/plugin. Gunakan trusted CI/CD pipeline. |
| A09 | **Logging & Monitoring Gaps** | Log semua auth event. Alert untuk anomali. Jangan log data sensitif. |
| A10 | **SSRF** | Whitelist URL tujuan. Blokir request ke IP internal/metadata endpoint. |

---

## Klasifikasi Data

Sebelum melindungi data, tentukan levelnya:

| Level | Label | Contoh | Perlakuan |
|-------|-------|--------|-----------|
| 1 | **Public** | Halaman marketing, dokumentasi API | Tidak perlu enkripsi khusus |
| 2 | **Internal** | Email karyawan, dokumen internal | Akses hanya untuk user terautentikasi |
| 3 | **Confidential** | Data pelanggan, transaksi bisnis | Enkripsi at-rest + akses berbasis role |
| 4 | **Restricted** | Password hash, nomor KTP, kartu kredit | Enkripsi at-rest + field-level encryption + audit log setiap akses |

**Aturan:** Perlakuan mengikuti level tertinggi. Jika satu tabel punya data Level 2 dan Level 4, seluruh tabel diperlakukan sebagai Level 4.

---

## Proteksi Data

### Encryption in Transit

- **Semua** komunikasi via HTTPS (TLS 1.3).
- Redirect HTTP ke HTTPS. Pasang header `Strict-Transport-Security`.
- Internal service-to-service: gunakan mTLS atau service mesh.

### Encryption at Rest

- Database: aktifkan Transparent Data Encryption (TDE) atau disk encryption.
- File storage: server-side encryption (SSE) di cloud storage.
- Field-level encryption untuk data Level 4 (KTP, kartu kredit) — enkripsi sebelum masuk database.

### Key Management

```
✅ Gunakan: managed service (AWS KMS, GCP Cloud KMS, Azure Key Vault)
✅ Rotasi key secara berkala (minimal per tahun)
✅ Pisahkan encryption key per environment (dev ≠ staging ≠ production)

❌ Jangan: simpan encryption key di source code
❌ Jangan: pakai key yang sama untuk semua tujuan
❌ Jangan: buat algoritma enkripsi sendiri
```

---

## Input Validation dan Sanitization

```python
# ✅ Whitelist approach — hanya terima yang diharapkan
def validate_age(value):
    if not isinstance(value, int) or not (0 <= value <= 150):
        raise ValidationError("Age must be integer between 0-150")
    return value

# ❌ Blacklist approach — coba blokir yang berbahaya (selalu ada celah)
def sanitize_input(value):
    return value.replace("<script>", "")  # Mudah di-bypass
```

**Prinsip:**

- Validasi di **backend** (frontend validation hanya UX, bukan security).
- Gunakan **whitelist** (izinkan yang valid) bukan **blacklist** (blokir yang berbahaya).
- Validasi: tipe data, panjang, format, range.
- Sanitize output (HTML encoding) untuk mencegah XSS.
- Gunakan parameterized query untuk mencegah SQL injection.

---

## Secrets Management

| Metode | Keamanan | Kapan Digunakan |
|--------|----------|-----------------|
| **Hardcode di source code** | ❌ Terburuk | **Jangan pernah** |
| **Environment variables** | ⚠️ Cukup | Development, deployment sederhana |
| **`.env` file (gitignored)** | ⚠️ Cukup | Development lokal |
| **Secret manager (Vault, AWS Secrets Manager)** | ✅ Terbaik | Production |

```bash
# ✅ Environment variable
DATABASE_URL=$DATABASE_URL

# ✅ .env file yang di-gitignore
echo ".env" >> .gitignore

# ❌ JANGAN PERNAH commit ini
# config.py
DB_PASSWORD = "super_secret_123"  # ← ini akan ada di git history SELAMANYA
```

**Jika secret terlanjur ter-commit:** Rotasi secret segera. Menghapus dari git history saja TIDAK cukup.

---

## Security Headers Checklist

| Header | Nilai Rekomendasi | Fungsi |
|--------|------------------|--------|
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` | Paksa HTTPS |
| `Content-Security-Policy` | `default-src 'self'; script-src 'self'` | Cegah XSS |
| `X-Content-Type-Options` | `nosniff` | Cegah MIME sniffing |
| `X-Frame-Options` | `DENY` | Cegah clickjacking |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | Batasi info referrer |
| `Permissions-Policy` | `camera=(), microphone=(), geolocation=()` | Batasi akses hardware |
| `X-XSS-Protection` | `0` (atau hapus) | Browser modern pakai CSP, header ini deprecated |

---

## Prinsip Privasi

| Prinsip | Penjelasan |
|---------|-----------|
| **Data Minimization** | Hanya kumpulkan data yang benar-benar dibutuhkan. Tidak perlu tanggal lahir jika tidak ada fitur yang membutuhkannya. |
| **Purpose Limitation** | Data hanya digunakan untuk tujuan yang sudah diinformasikan ke user. |
| **Storage Limitation** | Tentukan retention period. Hapus data yang sudah tidak dibutuhkan. |
| **Right to Access** | User bisa minta salinan semua data pribadinya (data export). |
| **Right to Deletion** | User bisa minta hapus akunnya dan semua data terkait (right to be forgotten). |
| **Consent** | Dapatkan persetujuan eksplisit sebelum mengumpulkan data yang tidak esensial. |

---

## GDPR / Privacy Compliance Basics

Meskipun proyek Anda bukan di Eropa, prinsip ini adalah best practice:

1. **Privacy policy** yang jelas dan mudah dipahami.
2. **Consent management** — checkbox opt-in (bukan opt-out) untuk marketing.
3. **Data processing records** — dokumentasikan data apa yang disimpan, di mana, dan untuk apa.
4. **Breach notification** — siapkan prosedur notifikasi jika terjadi data breach (72 jam untuk GDPR).
5. **Data Protection Impact Assessment (DPIA)** — untuk fitur yang memproses data sensitif dalam volume besar.

---

## Threat Modeling: STRIDE

Lakukan threat modeling di awal desain fitur, bukan setelah development selesai:

| Threat | Pertanyaan | Contoh Mitigasi |
|--------|-----------|-----------------|
| **S**poofing | Bisakah seseorang berpura-pura jadi user lain? | Autentikasi kuat, MFA |
| **T**ampering | Bisakah data dimodifikasi tanpa terdeteksi? | Integrity check, digital signature |
| **R**epudiation | Bisakah user menyangkal aksinya? | Audit log yang immutable |
| **I**nformation Disclosure | Bisakah data bocor ke pihak tidak berwenang? | Enkripsi, access control |
| **D**enial of Service | Bisakah sistem dibuat tidak tersedia? | Rate limiting, auto-scaling |
| **E**levation of Privilege | Bisakah user mendapat akses lebih dari seharusnya? | Least privilege, input validation |

---

## Security Testing

| Tipe | Apa yang Dites | Tools | Kapan |
|------|---------------|-------|-------|
| **SAST** (Static) | Source code — cari pola rawan | SonarQube, Semgrep, CodeQL | Setiap PR / CI pipeline |
| **DAST** (Dynamic) | Aplikasi yang berjalan — simulasi serangan | OWASP ZAP, Burp Suite | Sebelum release / staging |
| **Dependency Scanning** | Library pihak ketiga — CVE yang diketahui | Snyk, Dependabot, Trivy | Setiap build + jadwal harian |
| **Secret Scanning** | Source code — credential yang ter-commit | git-secrets, TruffleHog | Pre-commit hook + CI |
| **Penetration Testing** | Keseluruhan sistem — oleh security professional | Manual oleh pentester | Per kuartal atau major release |

---

## Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|-----------|--------|--------|
| Security sebagai afterthought | Refactor mahal, potensi breach | Security by design sejak fase desain |
| Validasi hanya di frontend | Bypass mudah via API | Validasi **selalu** di backend |
| Secret di source code | Credential bocor selamanya via git history | Secret manager + pre-commit scanning |
| Log berisi data sensitif | Breach via log aggregator | Mask PII di log, jangan log credential |
| Dependency tidak di-update | Vulnerability yang sudah diketahui publik | Automated dependency scanning + update |
| Satu encryption key untuk semua | Satu key bocor = semua data terekspos | Key per purpose, key rotation |

---

## Dokumen Terkait

- [Authentication and Authorization](authentication-and-authorization.md) — Detail implementasi autentikasi dan otorisasi.
- [API Error Handling](api-error-handling.md) — Jangan expose informasi internal di error response.
- [Non-Functional Requirements](non-functional-requirements.md) — NFR untuk keamanan dan privasi.
- [Integration Design](integration-design.md) — Keamanan webhook dan API key management.
