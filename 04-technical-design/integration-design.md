# Integration Design

## Tipe Integrasi

| Tipe | Cara Kerja | Kapan Digunakan | Contoh |
|------|-----------|-----------------|--------|
| **REST API** | Request-response sinkron via HTTP | CRUD standar, query data real-time | Ambil data produk dari katalog service |
| **Webhook** | Server push notifikasi via HTTP POST ke URL terdaftar | Event notification dari pihak ketiga | Payment gateway kirim notifikasi pembayaran |
| **Message Queue** | Asinkron via broker (RabbitMQ, SQS, Kafka) | Proses yang bisa ditunda, decoupling antar service | Kirim email setelah registrasi |
| **File-Based** | Transfer file (CSV, XML) via SFTP/S3 | Batch processing, integrasi dengan sistem legacy | Import data dari ERP setiap malam |
| **Database-Level** | Akses langsung ke database service lain | ❌ **Hindari** — coupling ketat, schema change = break | Hanya jika tidak ada alternatif lain |

> **Prinsip:** Hindari database-level integration. Jika dua service perlu data yang sama, gunakan API atau event.

---

## Designing for Failure

Setiap integrasi eksternal **pasti** akan gagal. Pertanyaannya bukan *apakah*, tapi *kapan* dan *bagaimana* sistem merespons.

### Timeout

```
✅ Selalu set timeout untuk setiap HTTP call ke service eksternal.
✅ Timeout default: 5 detik (sesuaikan berdasarkan SLA dependency).
✅ Timeout connect dan timeout read terpisah.

❌ Jangan: biarkan default timeout (sering infinity atau terlalu lama).
❌ Jangan: timeout lebih lama dari batas kesabaran user.
```

### Retry dengan Exponential Backoff + Jitter

```
Attempt 1: segera
Attempt 2: tunggu 1 detik  + random(0-500ms)
Attempt 3: tunggu 2 detik  + random(0-500ms)
Attempt 4: tunggu 4 detik  + random(0-500ms)
Attempt 5: tunggu 8 detik  + random(0-500ms)
→ Menyerah, masukkan ke dead letter queue.
```

**Kenapa jitter?** Tanpa jitter, semua client retry di waktu yang sama → thundering herd → server makin overload.

**Retry hanya untuk:**

- Network error (timeout, connection refused).
- Server error (500, 502, 503, 504).
- Rate limited (429) — pakai `Retry-After` header.

**Jangan retry untuk:**

- Client error (400, 401, 403, 404, 422) — request-nya yang salah, retry tidak akan membantu.

### Circuit Breaker

```
┌──────────┐    gagal > threshold     ┌──────────┐
│  CLOSED  │ ───────────────────────► │   OPEN   │
│(normal)  │                          │(tolak    │
│          │                          │ semua)   │
└──────────┘                          └────┬─────┘
     ▲                                     │
     │          setelah timeout period      │
     │                                     ▼
     │        berhasil              ┌──────────────┐
     └───────────────────────────── │  HALF-OPEN   │
                                    │(coba 1 req)  │
                                    └──────────────┘
```

**Contoh threshold:** Jika 50% request gagal dalam 1 menit terakhir, buka circuit selama 30 detik.

**Saat circuit open:** Return error/fallback segera tanpa mengirim request ke dependency. Ini mencegah:

- Resource exhaustion (thread pool habis menunggu timeout).
- Cascading failure ke service lain.

### Dead Letter Queue (DLQ)

Message yang gagal diproses setelah semua retry → masuk DLQ, bukan hilang.

- Monitor DLQ — alert jika ada message masuk.
- Sediakan tooling untuk inspect dan replay message dari DLQ.
- Set retention period (misal 14 hari) agar DLQ tidak membengkak.

---

## Idempotency Key

### Apa dan Mengapa?

Operasi idempoten = dijalankan 1x atau 100x, hasilnya sama. Penting karena retry bisa menyebabkan duplikasi (double payment, double order).

### Pola Implementasi

```
Client                          Server
  │                               │
  │  POST /payments               │
  │  Idempotency-Key: uuid-abc    │
  │ ────────────────────────────► │
  │                               │ ─── Cek: uuid-abc pernah diproses?
  │                               │     Belum → proses + simpan result
  │  201 Created                  │
  │ ◄──────────────────────────── │
  │                               │
  │  (network timeout, client     │
  │   tidak terima response)      │
  │                               │
  │  POST /payments (retry)       │
  │  Idempotency-Key: uuid-abc    │
  │ ────────────────────────────► │
  │                               │ ─── uuid-abc sudah ada → return
  │  201 Created (same response)  │     response tersimpan
  │ ◄──────────────────────────── │
```

**Implementasi server:**

1. Client kirim `Idempotency-Key` header (UUID yang di-generate client).
2. Server cek di datastore — jika key sudah ada, return stored response.
3. Jika belum, proses request, simpan key + response, return response.
4. Key expired setelah 24 jam (configurable).

**Wajib untuk:** Payment, transfer, order creation — operasi apapun yang tidak boleh terjadi ganda.

---

## Keamanan Webhook

### Validasi Signature (HMAC)

```python
import hmac
import hashlib

def verify_webhook(payload_body, signature_header, secret):
    expected = hmac.new(
        key=secret.encode(),
        msg=payload_body,
        digestmod=hashlib.sha256
    ).hexdigest()

    # Gunakan constant-time comparison untuk mencegah timing attack
    return hmac.compare_digest(f"sha256={expected}", signature_header)
```

### Replay Protection

Validasi timestamp di payload webhook:

1. Webhook payload berisi `timestamp`.
2. Server cek: jika `timestamp` lebih dari 5 menit yang lalu → tolak (replay attack).
3. Simpan webhook ID yang sudah diproses → tolak duplikat.

### Best Practices Webhook

- **Respond cepat** — return `200` segera, proses payload secara asinkron.
- **Idempotent processing** — webhook yang sama bisa dikirim lebih dari sekali.
- **Secret rotation** — support dua secret aktif bersamaan saat rotasi.
- **Logging** — log semua webhook masuk (tanpa data sensitif) untuk debugging.

---

## Handling Rate Limiting (429)

```javascript
async function callWithRateLimit(requestFn) {
  const response = await requestFn();

  if (response.status === 429) {
    const retryAfter = parseInt(response.headers.get('Retry-After')) || 60;
    console.log(`Rate limited. Retry after ${retryAfter}s`);
    await sleep(retryAfter * 1000);
    return callWithRateLimit(requestFn); // retry
  }

  return response;
}
```

**Proaktif, bukan reaktif:**

- Track `X-RateLimit-Remaining` — slow down sebelum kena limit.
- Gunakan queue untuk batch request ke API yang punya rate limit ketat.
- Cache response yang bisa di-cache untuk mengurangi jumlah request.

---

## Konsistensi Data Antar Service

### Saga Pattern (Basics)

Ketika satu transaksi bisnis melibatkan beberapa service, gunakan saga:

```
Order Service         Payment Service       Inventory Service
     │                      │                      │
     │  1. Create Order     │                      │
     │  (status: pending)   │                      │
     │ ──────────────────► │                      │
     │                      │  2. Charge Payment   │
     │                      │ ──────────────────► │
     │                      │                      │ 3. Reserve Stock
     │                      │                      │ (success)
     │                      │  ◄────────────────── │
     │  ◄────────────────── │                      │
     │  4. Confirm Order    │                      │
     │  (status: confirmed) │                      │
```

**Jika step 3 gagal** → kompensasi: refund payment (step 2), cancel order (step 1).

### Eventual Consistency

Dalam sistem terdistribusi, data **tidak** selalu konsisten secara instan. Terima kenyataan ini:

- Desain UI yang toleran terhadap data yang belum sinkron ("Pesanan Anda sedang diproses").
- Gunakan event untuk propagasi perubahan antar service.
- Sediakan mekanisme reconciliation untuk deteksi dan perbaiki inkonsistensi.

---

## Strategi Integration Testing

| Level | Apa yang Dites | Cara |
|-------|---------------|------|
| **Unit test dengan mock** | Logic business yang memanggil service eksternal | Mock HTTP client, test error handling |
| **Contract test** | Format request/response sesuai kesepakatan | Pact atau schema validation |
| **Integration test** | Koneksi ke service nyata (sandbox/staging) | Test terhadap sandbox API pihak ketiga |
| **End-to-end test** | Flow lengkap termasuk semua dependency | Environment staging yang lengkap |

### Contract Testing

Pastikan kedua sisi integrasi setuju tentang format data:

```
Provider (API)              Contract                Consumer (Client)
     │                    ┌─────────┐                     │
     │  "Saya kirim       │ Schema  │    "Saya expect     │
     │   field X, Y, Z"   │ + Rules │     field X, Y, Z"  │
     │ ─────────────────► │         │ ◄────────────────── │
     │                    └─────────┘                     │
```

Jika provider ubah response tanpa update contract → contract test gagal → ketahuan sebelum production.

---

## Monitoring Integrasi

| Metrik | Apa yang Diukur | Alert Threshold |
|--------|----------------|-----------------|
| **Latency** (p95) | Waktu response dependency | > 2x dari baseline |
| **Error rate** | Persentase request gagal | > 1% dalam 5 menit |
| **Availability** | Uptime dependency | Circuit breaker terbuka |
| **Throughput** | Request per detik ke dependency | > 80% dari rate limit |
| **DLQ depth** | Jumlah message gagal | > 0 (alert segera) |

**Dashboard minimal untuk setiap integrasi:**

- Latency p50/p95/p99 over time.
- Error rate per status code.
- Circuit breaker state (closed/open/half-open).
- Rate limit usage (remaining/total).

---

## Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|-----------|--------|--------|
| Tidak set timeout | Satu dependency lambat → semua request menumpuk | Selalu set connect + read timeout |
| Retry tanpa backoff | Thundering herd — server makin overload | Exponential backoff + jitter |
| Retry untuk semua error | Retry 400/422 = buang resource sia-sia | Hanya retry untuk 5xx dan network error |
| Tidak ada idempotency key | Double payment, double order | Wajib untuk operasi kritis |
| Webhook tanpa signature check | Siapa saja bisa kirim fake webhook | Validasi HMAC signature + timestamp |
| Asumsikan konsistensi instan | Race condition, data inkonsisten | Desain untuk eventual consistency |
| Tidak monitor dependency | Baru tahu gagal setelah user komplain | Monitoring + alerting per dependency |

---

## Dokumen Terkait

- [API Design](api-design.md) — Konvensi RESTful API untuk integrasi.
- [API Error Handling](api-error-handling.md) — Format error response dan retry guidance.
- [Authentication and Authorization](authentication-and-authorization.md) — API key management.
- [Security and Privacy](security-and-privacy.md) — Keamanan webhook dan secrets management.
- [Architecture Patterns](architecture-patterns.md) — Event-driven dan microservices patterns.
