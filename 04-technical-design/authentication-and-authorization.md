# Authentication and Authorization

## Autentikasi vs Otorisasi

Dua konsep ini sering tercampur, padahal berbeda:

| Aspek        | Authentication (AuthN)            | Authorization (AuthZ)                    |
|--------------|-----------------------------------|------------------------------------------|
| Pertanyaan   | **Siapa** kamu?                   | **Apa** yang boleh kamu lakukan?         |
| Kapan        | Saat login / setiap request       | Setelah identitas terverifikasi          |
| Contoh       | Verifikasi username + password    | Cek apakah user boleh hapus order ini    |
| Gagal = ?    | 401 Unauthorized                  | 403 Forbidden                            |

---

## Metode Autentikasi

### Session-Based Authentication

Cocok untuk: **aplikasi web monolitik** di mana client dan server satu domain.

1. User login → server buat session, simpan di Redis/database.
2. Server kirim Session ID via cookie (`HttpOnly`, `Secure`, `SameSite=Strict`).
3. Setiap request, browser otomatis kirim cookie → server cek session.

**Kelebihan:** Simpel, revokasi langsung (hapus session dari store).
**Kekurangan:** Butuh shared session store untuk multi-server.

### JWT (JSON Web Token)

Cocok untuk: **SPA, mobile app, microservices** — di mana client dan server terpisah.

- **Access Token:** Berumur pendek (15–30 menit). Dikirim via `Authorization: Bearer <token>` header.
- **Refresh Token:** Berumur panjang (7–30 hari). Disimpan di cookie `HttpOnly` + `Secure` + `SameSite=Strict`.

```
┌─────────┐     1. Login (credential)     ┌─────────┐
│  Client  │ ──────────────────────────► │  Server  │
│          │ ◄────────────────────────── │          │
│          │  2. Access Token + Refresh   │          │
│          │     Token (HttpOnly cookie)  │          │
│          │                              │          │
│          │  3. API call + Bearer token  │          │
│          │ ──────────────────────────► │          │
│          │                              │          │
│          │  4. Access expired?          │          │
│          │     POST /auth/refresh       │          │
│          │ ──────────────────────────► │          │
│          │ ◄────────────────────────── │          │
│          │  5. New Access Token +       │          │
│          │     New Refresh Token        │          │
└─────────┘     (token rotation)          └─────────┘
```

**Token Rotation:** Setiap kali refresh token dipakai, keluarkan refresh token baru dan invalidasi yang lama.
Jika refresh token lama dipakai lagi → **deteksi token theft** → revoke semua session user.

### OAuth 2.0 / OpenID Connect (OIDC)

Cocok untuk: **"Login with Google/GitHub"**, integrasi pihak ketiga.

- **OAuth 2.0** = framework otorisasi (mendapat akses ke resource).
- **OIDC** = layer autentikasi di atas OAuth 2.0 (mendapat identitas user).
- Gunakan **Authorization Code Flow + PKCE** untuk SPA dan mobile app.
- Jangan pakai Implicit Flow — sudah deprecated.

### API Keys

Cocok untuk: **server-to-server communication**, integrasi pihak ketiga.

- Bukan pengganti autentikasi user — API key mengidentifikasi **aplikasi**, bukan **orang**.
- Kirim via header (`X-API-Key`), **bukan** di URL query string.
- Implementasi: rate limit per API key, bisa dirotasi dan direvoke.

---

## Multi-Factor Authentication (MFA)

**Wajib** untuk: akun admin, akses data sensitif, operasi kritis (hapus akun, transfer dana).

| Faktor            | Contoh                               |
|--------------------|--------------------------------------|
| Something you know | Password, PIN                        |
| Something you have | TOTP (Google Authenticator), SMS OTP |
| Something you are  | Fingerprint, Face ID                 |

Gunakan **TOTP** (Time-based One-Time Password) — lebih aman dari SMS yang rentan SIM-swap.

---

## Model Otorisasi

### RBAC (Role-Based Access Control)

Hak akses dikelompokkan berdasarkan **peran** (role).

```
User ──► Role ──► Permission

Contoh:
  admin    → [create, read, update, delete, manage_users]
  editor   → [create, read, update]
  viewer   → [read]
```

**Kapan pakai:** Sebagian besar aplikasi. Simpel dan cukup untuk kebanyakan kasus.

### ABAC (Attribute-Based Access Control)

Hak akses berdasarkan **kombinasi atribut** (user, resource, environment).

```
Rule: ALLOW if
  user.role == "editor" AND
  resource.owner_id == user.id AND
  resource.status == "draft" AND
  environment.time IN business_hours
```

**Kapan pakai:** Ketika RBAC tidak cukup — butuh kontrol berdasarkan kepemilikan data, status, waktu, dsb.

### Perbandingan RBAC vs ABAC

| Aspek          | RBAC                        | ABAC                                |
|----------------|-----------------------------|--------------------------------------|
| Kompleksitas   | Rendah                      | Tinggi                               |
| Fleksibilitas  | Terbatas pada role          | Sangat fleksibel (multi-atribut)     |
| Performa       | Cepat (lookup sederhana)    | Lebih lambat (evaluasi rule)         |
| Cocok untuk    | CRUD standar, tim kecil     | Multi-tenant, data ownership complex |
| Mulai dengan   | ✅ Ini dulu                  | Tambahkan jika RBAC tidak cukup     |

### Desain Role Hierarchy

```
super_admin          → Semua permission + manage system config
  └── admin          → Manage users + semua operasi bisnis
       └── manager   → CRUD data di scope timnya
            └── member → CRUD data miliknya sendiri
                 └── viewer → Read-only
```

**Tips:** Mulai dengan role sesedikit mungkin. Tambah role baru hanya jika ada kebutuhan nyata.

---

## Pola Permission Checking

```python
# ❌ Hardcode role di business logic
if user.role == "admin":
    delete_order(order_id)

# ✅ Cek permission, bukan role
if user.has_permission("orders:delete"):
    delete_order(order_id)

# ✅ Cek ownership untuk ABAC
if user.has_permission("orders:delete") and order.owner_id == user.id:
    delete_order(order_id)
```

**Kenapa?** Jika role berubah, cukup update mapping role→permission di satu tempat. Tidak perlu ubah business logic.

---

## Session Management

| Aspek               | Rekomendasi                                           |
|----------------------|-------------------------------------------------------|
| Session timeout      | Idle timeout 30 menit, absolute timeout 8–24 jam.    |
| Concurrent sessions  | Batasi max 3–5 device. Tampilkan daftar active session. |
| Session revocation   | User bisa logout dari semua device. Admin bisa force-logout user. |
| Regenerate session   | Buat session ID baru setelah login (cegah session fixation). |
| Logout               | Hapus session di server + clear cookie di client.     |

---

## Penyimpanan Password yang Aman

```
✅ Gunakan: bcrypt (cost factor 12+) atau Argon2id
✅ Salt otomatis (bcrypt dan argon2 sudah include)
✅ Pepper (application-level secret) sebagai lapisan tambahan

❌ Jangan: MD5, SHA-1, SHA-256 tanpa salt
❌ Jangan: buat algoritma hashing sendiri
❌ Jangan: simpan password di plain text atau reversible encryption
```

```python
# Contoh menggunakan bcrypt
import bcrypt

hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))
is_valid = bcrypt.checkpw(password.encode(), hashed)
```

---

## Security Headers

| Header                        | Nilai                     | Fungsi                                        |
|-------------------------------|---------------------------|-----------------------------------------------|
| `Strict-Transport-Security`   | `max-age=31536000`        | Paksa HTTPS selama 1 tahun.                   |
| `Content-Security-Policy`     | `default-src 'self'`      | Cegah XSS dengan whitelist sumber konten.     |
| `X-Content-Type-Options`      | `nosniff`                 | Cegah MIME-type sniffing.                     |
| `X-Frame-Options`             | `DENY`                    | Cegah clickjacking via iframe.                |
| `Access-Control-Allow-Origin` | Domain spesifik, bukan `*`| CORS — batasi origin yang boleh akses API.    |

---

## Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|-----------|--------|--------|
| Simpan JWT di `localStorage` | Rentan XSS — script jahat bisa baca token | Simpan access token di memory, refresh token di `HttpOnly` cookie |
| Tidak ada token rotation | Refresh token yang bocor bisa dipakai selamanya | Rotasi refresh token setiap kali dipakai |
| Role terlalu broad (`admin` bisa segalanya) | Satu akun compromised = seluruh sistem terancam | Terapkan least privilege — pecah permission granular |
| Tidak ada rate limit di login | Brute-force attack bisa menebak password | Rate limit + account lockout setelah N percobaan gagal |
| Session tidak expire | Session yang abandoned tetap valid selamanya | Set idle timeout + absolute timeout |
| Bypass authz di frontend saja | User bisa bypass lewat API langsung | **Selalu** enforce authorization di backend |

---

## Dokumen Terkait

- [API Design](api-design.md) — Konvensi endpoint autentikasi.
- [API Error Handling](api-error-handling.md) — Format response 401 dan 403.
- [Security and Privacy](security-and-privacy.md) — Keamanan data dan OWASP mitigations.
- [Integration Design](integration-design.md) — API key management untuk integrasi.
