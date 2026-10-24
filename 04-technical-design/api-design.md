# API Design Guide

## 1. REST Principles

REST (Representational State Transfer) memperlakukan data sebagai **resource** yang diakses melalui URL dan dimanipulasi menggunakan HTTP methods.

Prinsip utama:
- **Stateless** — setiap request berisi semua informasi yang dibutuhkan. Server tidak menyimpan session state
- **Resource-oriented** — URL merepresentasikan resource (benda), bukan action (kata kerja)
- **Uniform interface** — gunakan HTTP methods secara konsisten untuk semua resource

---

## 2. Resource Naming Conventions

### Gunakan Kata Benda Plural

```
✅  /api/v1/users
✅  /api/v1/orders
✅  /api/v1/order-items

❌  /api/v1/getUsers
❌  /api/v1/createOrder
❌  /api/v1/user          (singular)
```

### Gunakan kebab-case untuk Multi-word Resources

```
✅  /api/v1/order-items
✅  /api/v1/product-categories

❌  /api/v1/orderItems     (camelCase)
❌  /api/v1/order_items    (snake_case — ini konvensi database, bukan URL)
```

### Nested Resources untuk Relasi

```
✅  /api/v1/users/123/orders         → semua order milik user 123
✅  /api/v1/orders/456/items          → semua item dalam order 456

❌  /api/v1/users/123/orders/456/items/789/reviews   → terlalu dalam (max 2 level nesting)
```

**Aturan:** Maksimal 2 level nesting. Jika lebih dalam, pertimbangkan endpoint terpisah dengan filter.

---

## 3. HTTP Methods — Semantik dan Idempotency

| Method | Semantik | Idempotent? | Safe? | Request Body | Contoh |
|---|---|---|---|---|---|
| `GET` | Baca data | Ya | Ya | Tidak | `GET /api/v1/users/123` |
| `POST` | Buat data baru | Tidak | Tidak | Ya | `POST /api/v1/users` |
| `PUT` | Ganti seluruh resource | Ya | Tidak | Ya | `PUT /api/v1/users/123` |
| `PATCH` | Update sebagian field | Ya* | Tidak | Ya | `PATCH /api/v1/users/123` |
| `DELETE` | Hapus resource | Ya | Tidak | Tidak | `DELETE /api/v1/users/123` |

- **Idempotent:** Memanggil request yang sama berkali-kali menghasilkan state yang sama
- **Safe:** Tidak mengubah state server
- *`PATCH` idempotent jika implementasinya benar (set field ke value, bukan increment)

### Kapan PUT vs PATCH?

```json
// PUT — kirim SELURUH representasi resource
PUT /api/v1/users/123
{
  "name": "Budi Updated",
  "email": "budi@example.com",
  "role": "member"
}

// PATCH — kirim HANYA field yang berubah
PATCH /api/v1/users/123
{
  "name": "Budi Updated"
}
```

**Praktik umum:** Kebanyakan API modern menggunakan `PATCH` untuk update. `PUT` digunakan jika client memang perlu mengganti seluruh resource.

---

## 4. URL Structure

```
/api/v{version}/{resource}/{id}/{sub-resource}
```

Contoh:

```
GET    /api/v1/products                → list semua products
GET    /api/v1/products/42             → detail product 42
POST   /api/v1/products                → buat product baru
PATCH  /api/v1/products/42             → update product 42
DELETE /api/v1/products/42             → hapus product 42
GET    /api/v1/products/42/reviews     → list review untuk product 42
```

---

## 5. Request/Response Format

### JSON sebagai Default

Semua request dan response menggunakan JSON. Set header:

```
Content-Type: application/json
Accept: application/json
```

### Response Format: Envelope vs Flat

**Envelope (recommended):**

```json
{
  "data": {
    "id": 123,
    "name": "Budi",
    "email": "budi@example.com"
  },
  "meta": {
    "request_id": "req_abc123"
  }
}
```

**Flat:**

```json
{
  "id": 123,
  "name": "Budi",
  "email": "budi@example.com"
}
```

**Envelope lebih disarankan karena:**
- Bisa menambah `meta` (pagination, request_id) tanpa breaking change
- Konsisten antara single resource dan list (data selalu di field `data`)
- Error response punya struktur yang sama

### Error Response

Gunakan format konsisten untuk semua error:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request body tidak valid",
    "details": [
      {
        "field": "email",
        "message": "Format email tidak valid"
      }
    ]
  }
}
```

### HTTP Status Codes yang Umum Digunakan

| Code | Arti | Kapan Digunakan |
|---|---|---|
| `200` | OK | GET berhasil, PATCH/PUT berhasil |
| `201` | Created | POST berhasil membuat resource baru |
| `204` | No Content | DELETE berhasil, tidak ada response body |
| `400` | Bad Request | Request body tidak valid, validation error |
| `401` | Unauthorized | Belum authenticated (token missing/expired) |
| `403` | Forbidden | Authenticated tapi tidak punya izin |
| `404` | Not Found | Resource tidak ditemukan |
| `409` | Conflict | Duplicate data, optimistic locking conflict |
| `422` | Unprocessable Entity | Validasi bisnis gagal (saldo tidak cukup, stock habis) |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Bug di server |

---

## 6. Pagination

### Cursor-based Pagination (Recommended)

```
GET /api/v1/products?limit=20&cursor=eyJpZCI6MTAwfQ
```

Response:

```json
{
  "data": [ ... ],
  "meta": {
    "limit": 20,
    "next_cursor": "eyJpZCI6MTIwfQ",
    "has_more": true
  }
}
```

| Kelebihan | Kelemahan |
|---|---|
| Performa konsisten (tidak ada OFFSET) | Tidak bisa "jump to page 5" |
| Stabil saat data berubah (insert/delete) | Implementasi sedikit lebih kompleks |
| Cocok untuk infinite scroll | Cursor opaque — client tidak bisa manipulasi |

### Offset-based Pagination

```
GET /api/v1/products?page=3&per_page=20
```

Response:

```json
{
  "data": [ ... ],
  "meta": {
    "page": 3,
    "per_page": 20,
    "total_count": 245,
    "total_pages": 13
  }
}
```

| Kelebihan | Kelemahan |
|---|---|
| Sederhana, intuitif | Performa menurun di halaman besar (OFFSET 10000) |
| Bisa jump ke halaman tertentu | Data bergeser saat ada insert/delete |
| Cocok untuk admin panel dengan page numbers | COUNT(*) bisa lambat di tabel besar |

**Rekomendasi:** Gunakan cursor-based untuk public API dan feed/timeline. Gunakan offset untuk admin panel dimana user butuh navigasi halaman.

---

## 7. Filtering dan Sorting

### Filtering — Query Parameters

```
GET /api/v1/products?status=active&category_id=5&min_price=10000&max_price=50000
```

Konvensi:
- Gunakan nama field yang sama dengan response
- Untuk range, gunakan prefix `min_`/`max_` atau `_gte`/`_lte`
- Untuk multiple values, gunakan comma-separated: `?status=active,pending`

### Sorting

```
GET /api/v1/products?sort=price&order=asc
GET /api/v1/products?sort=-created_at          (prefix - untuk descending)
GET /api/v1/products?sort=category_id,-price   (multi-sort)
```

Pilih salah satu konvensi dan gunakan konsisten di seluruh API.

---

## 8. Versioning Strategies

### URL Path Versioning (Recommended)

```
/api/v1/users
/api/v2/users
```

| Kelebihan | Kelemahan |
|---|---|
| Eksplisit, mudah dipahami | URL berubah antar versi |
| Mudah di-route di API gateway | Bisa menyebabkan duplikasi kode jika tidak dikelola |
| Bisa dicache berdasarkan URL | - |

### Header Versioning

```
GET /api/users
Accept: application/vnd.myapp.v2+json
```

| Kelebihan | Kelemahan |
|---|---|
| URL tetap bersih | Lebih sulit di-test (perlu set header) |
| Lebih "RESTful" secara teori | Tidak terlihat di browser/log |

**Rekomendasi:** URL path versioning. Lebih praktis, lebih mudah di-debug, semua orang langsung paham.

---

## 9. HATEOAS Basics

HATEOAS (Hypermedia as the Engine of Application State) menambahkan link ke response yang menunjukkan **action apa yang bisa dilakukan selanjutnya**.

```json
{
  "data": {
    "id": 456,
    "status": "pending",
    "total_amount": 150000
  },
  "links": {
    "self": "/api/v1/orders/456",
    "pay": "/api/v1/orders/456/pay",
    "cancel": "/api/v1/orders/456/cancel",
    "items": "/api/v1/orders/456/items"
  }
}
```

**Untuk junior dev:** HATEOAS bagus secara teori, tapi jarang diimplementasikan penuh. Minimum yang berguna: sertakan link `self` di setiap resource dan `next`/`prev` di pagination.

---

## 10. Content Negotiation

Client menentukan format response yang diinginkan melalui `Accept` header:

```
Accept: application/json        → response JSON (default)
Accept: text/csv                 → response CSV (jika API mendukung export)
```

**Praktik umum:** Untuk kebanyakan API, cukup support JSON saja. Tambahkan format lain (CSV, PDF) hanya jika ada kebutuhan spesifik (misal: export data).

---

## 11. Rate Limiting

Lindungi API dari abuse dan pastikan fairness antar-client.

### Response Headers

```
X-RateLimit-Limit: 100          → max requests per window
X-RateLimit-Remaining: 67       → sisa requests di window saat ini
X-RateLimit-Reset: 1698307200   → Unix timestamp kapan window reset
Retry-After: 30                 → (saat 429) berapa detik tunggu
```

Saat limit tercapai, return:

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30

{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Terlalu banyak request. Coba lagi dalam 30 detik."
  }
}
```

---

## 12. API Documentation — OpenAPI / Swagger

Dokumentasikan API menggunakan **OpenAPI Specification** (sebelumnya Swagger):

```yaml
openapi: 3.0.3
info:
  title: E-Commerce API
  version: 1.0.0
paths:
  /api/v1/products:
    get:
      summary: List semua products
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [active, inactive]
      responses:
        '200':
          description: List products berhasil
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
```

**Tools:**
- **Swagger UI** — generate interactive docs dari OpenAPI spec
- **Redoc** — alternatif Swagger UI dengan tampilan lebih clean
- **Postman Collection** — bisa di-export dari OpenAPI spec

**Aturan:** API documentation harus selalu up-to-date. Jika menggunakan code-first approach, generate OpenAPI spec dari kode (misal: decorators di NestJS, annotations di Spring).

---

## 13. Kesalahan Umum

### ❌ Verb di URL

```
❌  POST /api/v1/createUser
❌  GET  /api/v1/getUserById/123
❌  POST /api/v1/deleteUser/123

✅  POST   /api/v1/users
✅  GET    /api/v1/users/123
✅  DELETE /api/v1/users/123
```

### ❌ Penamaan Tidak Konsisten

```
❌  /api/v1/Users          (PascalCase)
    /api/v1/order_items    (snake_case)
    /api/v1/productList    (camelCase + suffix)

✅  /api/v1/users
    /api/v1/order-items
    /api/v1/products
```

### ❌ Menggunakan GET untuk Operasi yang Mengubah Data

```
❌  GET /api/v1/users/123/delete
❌  GET /api/v1/orders/456/approve

✅  DELETE /api/v1/users/123
✅  PATCH  /api/v1/orders/456   { "status": "approved" }
```

### ❌ Mengembalikan 200 untuk Semua Response

```
❌  HTTP 200
    { "success": false, "error": "User not found" }

✅  HTTP 404
    { "error": { "code": "NOT_FOUND", "message": "User tidak ditemukan" } }
```

### ❌ Tidak Ada Pagination

Endpoint yang mengembalikan semua data tanpa limit. Saat data tumbuh, response menjadi sangat besar dan lambat.

### ❌ Breaking Change Tanpa Versioning

Mengubah response format di endpoint yang sudah digunakan client. Selalu buat versi baru untuk breaking change.

---

## 14. Dokumen Terkait

- [System Architecture](./system-architecture.md) — konteks arsitektur keseluruhan
- [Database Design](./database-design.md) — desain database yang diekspos API
- [Data Dictionary](./data-dictionary.md) — detail field yang muncul di API response
- [Architecture Decision Records](./architecture-decision-records.md) — ADR untuk keputusan API
