---
title: "Spesifikasi REST API - [Nama Layanan / Modul]"
version: "1.0.0"
base_path: "/api/v1"
author: "[Nama Backend Lead / Developer]"
status: "[Draft | In Review | Approved | Deprecated]"
last_updated: "[YYYY-MM-DD]"
---

# API Specification: [Nama Layanan / Modul]

> **Panduan Pengisian:**
> Template ini mendefinisikan kontrak REST API untuk tim backend, frontend, dan QA.
> Salin blok `Endpoint Card` untuk setiap endpoint baru dalam modul ini.
> Pertahankan format konsisten dan selalu sertakan contoh payload nyata.

---

## 1. Lingkungan & Base URL [Wajib]

> **Panduan Pengisian:**
> Cantumkan URL dasar untuk setiap tahapan deployment agar tim klien dapat mengonfigurasi environment variable.

| Lingkungan | Base URL | Keterangan |
|---|---|---|
| Development / Local | `http://localhost:[PORT]/api/v1` | Server lokal pengembang |
| Staging | `https://staging-api.[domain.com]/api/v1` | Testing internal & QA |
| Production | `https://api.[domain.com]/api/v1` | Lingkungan live |

## 2. Header Global & Autentikasi [Wajib]

> **Panduan Pengisian:**
> Definisikan header yang wajib dikirim pada sebagian besar atau seluruh request, beserta skema otorisasi.

- **Skema Autentikasi:** `Bearer Token (JWT)` via header `Authorization: Bearer <token>`
- **Format Pertukaran Data:** `application/json` (Request & Response)

| Header | Tipe | Status | Deskripsi |
|---|---|---|---|
| `Authorization` | String | [Kondisional] | Wajib jika endpoint dilindungi (`Bearer <jwt>`) |
| `Content-Type` | String | [Wajib] | Nilai standar: `application/json` |
| `Accept` | String | [Wajib] | Nilai standar: `application/json` |
| `X-Correlation-ID` | UUID | [Opsional] | ID pelacakan request terdistribusi untuk observabilitas |

## 3. Template Endpoint Card [Wajib]

> **Panduan Pengisian:**
> Gandakan seksi ini untuk setiap endpoint. Hapus parameter atau body jika metode HTTP tidak membutuhkannya (misal: GET tanpa request body).

### `[POST] /api/v1/[resources]`

- **Summary [Wajib]:** [Deskripsi 1 kalimat mengenai fungsi endpoint, misal: Membuat pesanan baru]
- **Auth Diperlukan [Wajib]:** [Ya / Tidak] | **Role yang Diizinkan:** `[customer | admin]`
- **Persyaratan Idempotensi [Kondisional]:** [Wajib menggunakan header `Idempotency-Key` (UUIDv4) untuk mencegah duplikasi pemrosesan pesanan]

#### URL & Query Parameters [Kondisional] *(Boleh dihapus jika tidak ada)*
| Parameter | Lokasi | Tipe | Status | Deskripsi |
|---|---|---|---|---|
| `[id]` | Path | UUID | [Wajib] | Identifier unik sumber daya |
| `page` | Query | Integer | [Opsional] | Nomor halaman paginasi (Default: `1`) |
| `limit` | Query | Integer | [Opsional] | Jumlah data per halaman (Default: `20`, Max: `100`) |

#### Request Body (JSON) [Kondisional] *(Boleh dihapus jika method GET/DELETE tanpa body)*
```json
{
  "item_id": "itm-849204",
  "quantity": 2,
  "notes": "Tolong packing kayu [Opsional]"
}
```

#### Success Response: `201 Created` / `200 OK` [Wajib]
```json
{
  "success": true,
  "data": {
    "id": "ord-982341",
    "item_id": "itm-849204",
    "quantity": 2,
    "status": "pending_payment",
    "created_at": "2026-10-25T10:00:00Z"
  }
}
```

#### Error Responses (Standar RFC 7807 Problem Details) [Wajib]

> **Panduan Pengisian:**
> Gunakan format standar RFC 7807 (`application/problem+json`) untuk memudahkan penanganan error di sisi frontend.

- **400 Bad Request / 422 Unprocessable Entity:**
```json
{
  "type": "https://api.[domain.com]/errors/validation-failed",
  "title": "Validation Failed",
  "status": 422,
  "detail": "Data input yang diberikan tidak valid.",
  "instance": "/api/v1/[resources]",
  "invalid_params": [
    { "name": "quantity", "reason": "Harus bernilai minimal 1" }
  ]
}
```

- **401 Unauthorized:** Token tidak ada atau sudah kedaluwarsa (`"title": "Unauthorized"`).
- **403 Forbidden:** Role token tidak memiliki izin mengakses sumber daya (`"title": "Forbidden"`).
- **404 Not Found:** Sumber daya dengan ID tersebut tidak ditemukan (`"title": "Not Found"`).
- **500 Internal Server Error:** Kegagalan sistem backend yang tidak terduga (`"title": "Internal Server Error"`).
