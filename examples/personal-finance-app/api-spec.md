# Spesifikasi REST API: DompetKu

```yaml
document_id: DOC-API-001
title: Core REST API Specification - DompetKu MVP
version: 1.0.0
base_url: https://api.dompetku.app/v1
authentication: Bearer Token (JWT in Authorization Header)
error_format: RFC 7807 (application/problem+json)
```

---

## 1. Konvensi Global API

- Seluruh payload dikirim dan diterima dalam format JSON (`Content-Type: application/json`).
- Format stempel waktu menggunakan standar ISO 8601 UTC (`YYYY-MM-DDTHH:mm:ss.sssZ`).
- Seluruh nilai moneter (`amount`, `balance`) direpresentasikan dalam tipe data integer atau string numerik desimal presisi tetap (Rupiah tanpa sen).
- Setiap request yang mengalami kegagalan mengembalikan response error terstruktur mengikuti standar **RFC 7807 Problem Details**.

---

## 2. Endpoint Autentikasi

### 2.1 Login Pengguna
- **Metode & Path**: `POST /api/v1/auth/login`
- **Autentikasi**: Publik (Tidak memerlukan token)
- **Fungsi**: Memvalidasi kredensial email/password dan menghasilkan access token.

#### Request Body
```json
{
  "email": "andi.pratama@example.com",
  "password": "PasswordSuperRahasia123!"
}
```

#### Response Sukses (200 OK)
```json
{
  "status": "success",
  "data": {
    "user": {
      "id": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
      "email": "andi.pratama@example.com",
      "full_name": "Andi Pratama"
    },
    "tokens": {
      "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
      "refresh_token": "d8e3c4b1-6a2e-4b9e-9f3a-1b2c3d4e5f6a",
      "expires_in": 900
    }
  }
}
```

#### Response Error (401 Unauthorized - RFC 7807)
```json
{
  "type": "https://api.dompetku.app/errors/invalid-credentials",
  "title": "Invalid Credentials",
  "status": 401,
  "detail": "Kombinasi email dan password yang Anda masukkan tidak sesuai.",
  "instance": "/api/v1/auth/login"
}
```

---

## 3. Endpoint Transaksi

### 3.1 Daftar Transaksi (Paginasi Cursor)
- **Metode & Path**: `GET /api/v1/transactions`
- **Autentikasi**: Wajib (`Bearer <access_token>`)
- **Query Parameters**:
  - `limit`: Integer (Default: 20, Maksimal: 100)
  - `cursor`: String UUID transaksi terakhir untuk navigasi halaman berikutnya
  - `category_id`: UUID (Opsional, untuk filter kategori)
  - `start_date`: String `YYYY-MM-DD` (Opsional)
  - `end_date`: String `YYYY-MM-DD` (Opsional)

#### Response Sukses (200 OK)
```json
{
  "status": "success",
  "data": [
    {
      "id": "c1a2b3c4-d5e6-7f8a-9b0c-1d2e3f4a5b6c",
      "account_id": "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e",
      "account_name": "BCA Utama",
      "category": {
        "id": "e4f5a6b7-c8d9-0e1f-2a3b-4c5d6e7f8a9b",
        "name": "Makanan & Minuman",
        "icon": "fast-food-outline",
        "color_hex": "#FF9800"
      },
      "type": "EXPENSE",
      "amount": 35000,
      "transaction_date": "2026-03-20",
      "notes": "Makan siang ayam geprek",
      "created_at": "2026-03-20T05:30:00.000Z"
    }
  ],
  "meta": {
    "limit": 20,
    "has_more": true,
    "next_cursor": "c1a2b3c4-d5e6-7f8a-9b0c-1d2e3f4a5b6c"
  }
}
```

---

### 3.2 Pencatatan Transaksi Baru
- **Metode & Path**: `POST /api/v1/transactions`
- **Autentikasi**: Wajib (`Bearer <access_token>`)
- **Idempotency**: Didukung via header `Idempotency-Key: <UUID>`

#### Request Body
```json
{
  "account_id": "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e",
  "category_id": "e4f5a6b7-c8d9-0e1f-2a3b-4c5d6e7f8a9b",
  "type": "EXPENSE",
  "amount": 35000,
  "transaction_date": "2026-03-20",
  "notes": "Makan siang ayam geprek"
}
```

#### Response Sukses (201 Created)
```json
{
  "status": "success",
  "data": {
    "id": "c1a2b3c4-d5e6-7f8a-9b0c-1d2e3f4a5b6c",
    "account_id": "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e",
    "category_id": "e4f5a6b7-c8d9-0e1f-2a3b-4c5d6e7f8a9b",
    "type": "EXPENSE",
    "amount": 35000,
    "transaction_date": "2026-03-20",
    "notes": "Makan siang ayam geprek",
    "account_balance_after": 465000,
    "created_at": "2026-03-20T05:30:00.000Z"
  }
}
```

#### Response Error Validasi (422 Unprocessable Entity - Nilai Negatif)
```json
{
  "type": "https://api.dompetku.app/errors/business-rule-violation",
  "title": "Business Rule Violation",
  "status": 422,
  "detail": "Nominal transaksi harus lebih besar dari 0.",
  "instance": "/api/v1/transactions",
  "invalid_params": [
    {
      "name": "amount",
      "reason": "Nilai 'amount' tidak boleh kurang dari atau sama dengan nol."
    }
  ]
}
```

---

## 4. Endpoint Anggaran & Ringkasan

### 4.1 Ringkasan Anggaran Kategori Bulanan
- **Metode & Path**: `GET /api/v1/budgets/summary?month_year=2026-03`
- **Autentikasi**: Wajib (`Bearer <access_token>`)

#### Response Sukses (200 OK)
```json
{
  "status": "success",
  "data": {
    "month_year": "2026-03",
    "total_budgeted": 2500000,
    "total_spent": 1850000,
    "overall_usage_percent": 74.0,
    "categories": [
      {
        "category_id": "e4f5a6b7-c8d9-0e1f-2a3b-4c5d6e7f8a9b",
        "category_name": "Makanan & Minuman",
        "monthly_limit": 1500000,
        "spent": 1250000,
        "usage_percent": 83.33,
        "status": "WARNING",
        "is_overbudget": false
      }
    ]
  }
}
```
