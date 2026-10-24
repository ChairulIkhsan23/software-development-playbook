# API Error Handling (RFC 7807)

## Mengapa Penanganan Error yang Konsisten Penting?

Error response yang tidak terstruktur membuat developer client frustasi — mereka harus menebak format error
setiap endpoint. Dengan format standar, tim frontend, mobile, dan pihak ketiga bisa:

- Menulis **satu error handler** yang bekerja untuk semua endpoint.
- Menampilkan **pesan error yang tepat** ke pengguna tanpa parsing manual.
- Melakukan **debugging lebih cepat** karena setiap error punya identitas unik.
- Membangun **retry logic** berdasarkan informasi yang terstruktur.

---

## Format Standar: RFC 7807 Problem Details

RFC 7807 mendefinisikan format JSON standar untuk error HTTP API. Gunakan ini untuk **semua** response 4xx dan 5xx.

### Field Wajib

| Field      | Tipe     | Deskripsi                                                                 |
|------------|----------|---------------------------------------------------------------------------|
| `type`     | `string` | URI referensi yang mengidentifikasi jenis error. Bisa URL ke dokumentasi. |
| `title`    | `string` | Ringkasan singkat yang *human-readable*. Tidak berubah antar kejadian.    |
| `status`   | `integer`| HTTP status code (duplikasi dari header untuk kemudahan parsing).         |
| `detail`   | `string` | Penjelasan spesifik untuk kejadian ini. Boleh berbeda tiap request.       |
| `instance` | `string` | URI path request yang menghasilkan error ini.                             |

### Contoh Response

```json
{
  "type": "https://api.example.com/errors/validation-failed",
  "title": "Validation Failed",
  "status": 422,
  "detail": "Data masukan tidak memenuhi kriteria validasi bisnis.",
  "instance": "/api/v1/orders",
  "errors": [
    {
      "field": "quantity",
      "code": "MIN_VALUE",
      "message": "Jumlah pesanan minimal adalah 1 item."
    },
    {
      "field": "email",
      "code": "INVALID_FORMAT",
      "message": "Format email tidak valid."
    }
  ],
  "timestamp": "2026-10-24T16:30:00Z"
}
```

> **Content-Type** untuk RFC 7807: `application/problem+json`

---

## Pemetaan HTTP Status Code

| Status Code | Nama                   | Kapan Digunakan                                                     |
|-------------|------------------------|---------------------------------------------------------------------|
| `400`       | Bad Request            | Format payload salah (malformed JSON, tipe data tidak sesuai).      |
| `401`       | Unauthorized           | Token/credential tidak ada atau expired.                            |
| `403`       | Forbidden              | Token valid tapi user tidak punya izin ke resource ini.             |
| `404`       | Not Found              | Resource tidak ditemukan (atau sengaja disembunyikan dari user).    |
| `409`       | Conflict               | Konflik data — email sudah terdaftar, versi data sudah berubah.    |
| `422`       | Unprocessable Entity   | Validasi logika bisnis gagal (format benar, isi tidak valid).       |
| `429`       | Too Many Requests      | Client melebihi rate limit. Sertakan header `Retry-After`.         |
| `500`       | Internal Server Error  | Kesalahan server yang tidak tertangani. **Jangan** expose detail.   |

### Aturan Penting

- **4xx = masalah di sisi client.** Client bisa memperbaiki request-nya.
- **5xx = masalah di sisi server.** Client tidak bisa memperbaiki, hanya bisa retry.
- Jangan gunakan `200 OK` dengan `{ "success": false }` — itu anti-pattern.

---

## Validation Error Format

Untuk error validasi (422), sertakan array `errors` dengan detail per field:

```json
{
  "type": "https://api.example.com/errors/validation-failed",
  "title": "Validation Failed",
  "status": 422,
  "detail": "2 field gagal validasi.",
  "instance": "/api/v1/users",
  "errors": [
    {
      "field": "password",
      "code": "TOO_SHORT",
      "message": "Password minimal 8 karakter."
    },
    {
      "field": "birth_date",
      "code": "INVALID_DATE",
      "message": "Format tanggal harus YYYY-MM-DD."
    }
  ]
}
```

Field `field` menggunakan dot notation untuk nested object: `address.postal_code`.

---

## Error Codes Catalog

Buat dan dokumentasikan katalog error code yang konsisten di seluruh API:

| Code                  | HTTP Status | Deskripsi                                 |
|-----------------------|-------------|-------------------------------------------|
| `VALIDATION_FAILED`   | 422         | Satu atau lebih field gagal validasi.     |
| `RESOURCE_NOT_FOUND`  | 404         | Resource yang diminta tidak ditemukan.     |
| `DUPLICATE_ENTRY`     | 409         | Data sudah ada (unique constraint).       |
| `UNAUTHORIZED`        | 401         | Credential tidak valid atau expired.      |
| `FORBIDDEN`           | 403         | Tidak punya izin untuk aksi ini.          |
| `RATE_LIMITED`        | 429         | Melebihi batas request yang diizinkan.    |
| `INTERNAL_ERROR`      | 500         | Kesalahan internal server.                |
| `SERVICE_UNAVAILABLE` | 503         | Dependency eksternal sedang tidak tersedia.|

Simpan katalog ini di dokumentasi API yang bisa diakses semua developer.

---

## Logging Error: Jangan Bocorkan Data Sensitif

```
✅ Log internal (server-side):
   [ERROR] 2026-10-24T16:30:00Z request_id=abc-123 user_id=42
   error=DUPLICATE_ENTRY detail="email already exists" email=j***@example.com

❌ Jangan kirim ke client:
   Stack trace, query SQL, path file server, nama tabel database, IP internal.
```

**Prinsip:**

- **Log lengkap di server** — termasuk stack trace, request ID, user context.
- **Response ke client minimal** — hanya informasi yang bisa ditindaklanjuti.
- **Mask data sensitif** di log: email, nomor telepon, token.
- Gunakan `request_id` di response agar client bisa laporkan error spesifik ke tim support.

---

## Retry Guidance: Header Retry-After

Untuk status `429` dan `503`, sertakan header yang membantu client tahu kapan boleh retry:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1698163830
```

| Header                 | Deskripsi                                          |
|------------------------|----------------------------------------------------|
| `Retry-After`          | Detik yang harus ditunggu sebelum retry.           |
| `X-RateLimit-Limit`    | Total request yang diizinkan per window.           |
| `X-RateLimit-Remaining`| Sisa request dalam window saat ini.                |
| `X-RateLimit-Reset`    | Unix timestamp kapan window direset.               |

---

## Pola Error Handling di Sisi Client

```javascript
async function apiCall(url, options) {
  const response = await fetch(url, options);

  if (response.ok) return response.json();

  const error = await response.json(); // RFC 7807 format

  switch (error.status) {
    case 401:
      // Redirect ke login atau refresh token
      return handleAuthError(error);
    case 422:
      // Tampilkan error per field di form
      return handleValidationErrors(error.errors);
    case 429:
      // Retry setelah Retry-After
      const retryAfter = response.headers.get('Retry-After');
      return scheduleRetry(retryAfter);
    case 500:
      // Tampilkan pesan generic + request_id untuk support
      return handleServerError(error);
    default:
      return handleGenericError(error);
  }
}
```

---

## Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|-----------|--------|--------|
| Mengembalikan `200` dengan `success: false` | Client tidak bisa pakai standard HTTP error handling | Gunakan HTTP status code yang benar |
| Expose stack trace di response | Bocornya informasi internal server | Log stack trace di server, kirim pesan generic ke client |
| Error format tidak konsisten antar endpoint | Client harus tulis parser berbeda per endpoint | Pakai RFC 7807 di semua endpoint |
| Tidak ada `request_id` di response | Sulit korelasikan laporan user dengan log server | Sertakan `request_id` di setiap error response |
| Pesan error dalam satu bahasa saja | User internasional tidak paham | Kirim error code + message, biarkan client yang localize |

---

## Dokumen Terkait

- [API Design](api-design.md) — Konvensi desain RESTful API.
- [Authentication and Authorization](authentication-and-authorization.md) — Detail tentang 401 vs 403.
- [Integration Design](integration-design.md) — Retry dan circuit breaker untuk error dari dependency.
- [Security and Privacy](security-and-privacy.md) — Apa yang boleh dan tidak boleh di-log.
