# Security Review Checklist

Checklist verifikasi keamanan aplikasi berbasis fondasi OWASP Top 10 dan OWASP Application Security Verification Standard (ASVS). Wajib diverifikasi sebelum kode diekspos ke lingkungan publik atau produksi.

---

## 1. Authentication & Session Management
- [ ] **Strong Password Hashing**: Kata sandi di-hash menggunakan algoritma modern adaptif: Argon2id atau bcrypt (cost factor >= 12) dengan salt unik per user.
- [ ] **JWT Token Security & Expiry**: Access token berumur pendek (maksimal 15-30 menit); refresh token disimpan di HTTP-only Secure SameSite cookie atau database berstatus revocable.
- [ ] **Multi-Factor Authentication (MFA)**: Alur TOTP/Authenticator didukung untuk akun berhak akses tinggi (Admin, Finance).
- [ ] **Brute-Force & Credential Stuffing Defense**: Penguncian akun sementara (*account lockout*) atau CAPTCHA aktif setelah 5x gagal login berturut-turut.
- [ ] **Session Invalidation**: Sesi aktif hancur total di server saat logout atau reset password. Session ID dirotasi setelah autentikasi sukses cegah Session Fixation.
- [ ] **Password Reset Flow**: Token reset password berumur pendek (< 15 menit), satu kali pakai (*single-use*), dan dikirim hanya via channel terverifikasi.

---

## 2. Authorization & Access Control
- [ ] **Role-Based Access Control (RBAC)**: Otorisasi diverifikasi di sisi server pada setiap endpoint, controller, dan mutation.
- [ ] **Broken Object Level Authorization (BOLA/IDOR)**: Akses record database berbasis ID wajib memverifikasi kepemilikan tenant/user pemanggil (contoh: `WHERE id = :id AND tenant_id = :tenant_id`).
- [ ] **Function Level Authorization**: Aksi sensitif (delete, export, update role) terlindungi middleware otorisasi; tombol di-hide di UI bukan proteksi keamanan.
- [ ] **Principle of Least Privilege**: Akun koneksi database aplikasi hanya memegang hak baca/tulis minimal; hindari menggunakan superuser / `root`.
- [ ] **Direct File Access Blocked**: File unduhan privat tidak diekspos langsung via URL publik static server tanpa token validasi.
- [ ] **Mass Assignment Protection**: Framework dibatasi agar request payload tidak menimpa kolom internal (`is_admin`, `role`, `balance`).

---

## 3. Input Validation & Injection Prevention
- [ ] **Parameterized Queries**: 100% kueri database menggunakan ORM aman atau parameterized queries / prepared statements (nol string concatenation).
- [ ] **Input Schema Validation**: Validasi tipe data, ukuran payload, dan format regex dilakukan di batas terluar request (contoh: Zod, Joi, Validator).
- [ ] **Cross-Site Scripting (XSS) Prevention**: Output HTML di-escape otomatis oleh template engine; hindari manipulasi DOM berbahaya (`dangerouslySetInnerHTML`, `innerHTML`).
- [ ] **Cross-Site Request Forgery (CSRF)**: State-changing requests (POST, PUT, DELETE) dilindungi token anti-CSRF atau SameSite Strict/Lax cookie policy.
- [ ] **Command & File Path Injection Defense**: Hindari eksekusi shell dinamis (`exec`, `eval`, `system`); cegah Path Traversal (`../`) pada operasi berkas.

---

## 4. Data Protection & Privacy
- [ ] **Encryption in Transit**: Seluruh lalu lintas jaringan dipaksa menggunakan HTTPS dengan TLS 1.3 (minimal TLS 1.2).
- [ ] **Encryption at Rest**: Kolom data sensitif (nomor KTP, nomor rekening, token pihak ketiga) dienkripsi menggunakan AES-256-GCM.
- [ ] **Personally Identifiable Information (PII) Masking**: Data sensitif pengguna disensor pada log aplikasi, analytics, dan notifikasi email/SMS.
- [ ] **No Hardcoded Secrets**: Kredensial, kunci API privat, dan token tidak pernah ditulis di dalam kode sumber; wajib via environment variables atau Secret Manager.
- [ ] **Git History Clean**: Repositori Git bebas dari histori komit berkas `.env` atau private key.

---

## 5. Security Headers & Configuration
- [ ] **Content Security Policy (CSP)**: Header `Content-Security-Policy` terpasang ketat membatasi asal sumber script, style, dan iframe.
- [ ] **Strict-Transport-Security (HSTS)**: Header `Strict-Transport-Security: max-age=31536000; includeSubDomains` aktif pada server produksi.
- [ ] **Clickjacking & MIME Protection**: Header `X-Frame-Options: DENY` dan `X-Content-Type-Options: nosniff` terkonfigurasi.
- [ ] **CORS Configuration**: `Access-Control-Allow-Origin` dibatasi eksplisit ke domain tepercaya; dilarang menggunakan wildcard `*` bersama `credentials: true`.
- [ ] **Rate Limiting**: Rate limiter aktif di level API Gateway / Reverse Proxy (Nginx, Cloudflare) untuk endpoint publik dan login.

---

## 6. Dependency & Supply Chain Security
- [ ] **Automated Dependency Audit**: Pemindaian kerentanan paket otomatis (`npm audit`, `pip-audit`, atau Snyk) dijalankan di CI pipeline.
- [ ] **Lockfile Integrity**: File `package-lock.json` / `pnpm-lock.yaml` ter-commit dan diverifikasi integritasnya via `npm ci`.
- [ ] **No Abandoned Packages**: Dependensi pihak ketiga aktif dirawat dan tidak memuat pustaka *deprecated* dengan risiko keamanan.
- [ ] **Container Image Scanning**: Image Docker di-scan menggunakan Trivy atau Grype; tidak menggunakan base image usang atau root user container.

---

## Perjanjian Waktu Remediasi (Remediation SLA by Severity)
Temuan kerentanan wajib diselesaikan sesuai batas waktu berikut:

| Severity Level | Skor CVSS | Dampak Risiko | Batas Waktu Remediasi (SLA) |
|---|---|---|---|
| **Critical** | 9.0 - 10.0 | RCE, SQLi tanpa autentikasi, ekspos data massal | **< 24 Jam** (Hotfix darurat, rilis langsung) |
| **High** | 7.0 - 8.9 | BOLA/IDOR, Bypass autentikasi, XSS tersimpan | **< 72 Jam** (Prioritas sprint utama) |
| **Medium** | 4.0 - 6.9 | CSRF minor, miskonfigurasi CORS, info disclosure | **< 14 Hari** (Masuk sprint berjalan) |
| **Low** | 0.1 - 3.9 | Versi server terekspos, missing security headers non-kritis | **< 30 Hari** (Backlog pemeliharaan) |

---

## Panduan Tindakan Cepat (Incident Action Guide)
- **Kunci Rahasia Ter-commit ke Git**:
  1. Langsung cabut (*revoke*) kunci lama di konsol provider.
  2. Terbitkan (*issue*) kunci rahasia baru.
  3. Ganti value di Secret Manager / `.env`.
  4. Bersihkan histori Git menggunakan `git filter-repo` atau BFG Repo-Cleaner.
- **Ditemukan Celah SQL Injection / IDOR**:
  Terapkan WAF rule pemblokir sementara di CDN, lalu deploy patch parameterized query / tenant check dalam branch hotfix.