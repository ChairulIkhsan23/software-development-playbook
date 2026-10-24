# Logging and Error Handling Guide

Panduan standar pembuatan log terstruktur (structured logging), hirarki level log, sanitasi data sensitif, arsitektur penanganan kesalahan (*error handling*), dan observabilitas sistem.

---

## 1. Mengapa Plain Text Log Mati di Produksi

Di lingkungan produksi modern yang berjalan di atas container (Docker/Kubernetes) atau serverless, format teks bebas (`console.log("User login: " + userId)`) tidak dapat diandalkan:
- **Sulit Diparsing Mesin:** Sistem log aggregator tidak dapat mengindeks teks acak secara efisien untuk pembuatan metrik atau visualisasi dashboard.
- **Pencarian Lambat & Mahal:** Mencari kueri regex pada jutaan baris string teks membebani CPU dan meningkatkan biaya analitik.
- **Structured JSON adalah Standar:** Dengan format JSON berbasis key-value, log aggregator dapat langsung memfilter berdasarkan atribut (misal: `level = 'ERROR' AND duration_ms > 2000`).

---

## 2. Atribut Standar Log Terstruktur (JSON Schema)

Setiap output log server wajib diekspor dalam format JSON satu baris (*NDJSON*) dengan skema field standar:

```json
{
  "timestamp": "2026-10-25T08:30:15.123Z",
  "level": "INFO",
  "correlation_id": "req_8f1a23c4-129a-4c28-98e1-5e28a491cd77",
  "environment": "production",
  "service": "billing-service",
  "message": "Payment transaction processed successfully",
  "context": {
    "order_id": "ord_99412",
    "payment_method": "qris",
    "amount_idr": 150000,
    "duration_ms": 320
  },
  "user_id": "usr_4412"
}
```

### Definisi Field Wajib:
- `timestamp`: Format ISO-8601 dengan presisi milidetik dan zona waktu UTC.
- `level`: Tingkat keparahan log (`DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`).
- `correlation_id` / `trace_id`: ID unik yang di-generate di pintu gerbang API (*API Gateway / Middleware*) dan diteruskan ke seluruh pemanggilan microservice untuk melacak perjalanan satu request pengguna.
- `service`: Nama layanan atau microservice penghasil log.
- `environment`: Lingkungan eksekusi (`development`, `staging`, `production`).
- `message`: Kalimat deskriptif singkat tentang peristiwa yang terjadi.
- `context`: Objek terstruktur yang berisi metadata tambahan terkait domain event.
- `user_id`: Identitas pengguna (jika request diautentikasi).

---

## 3. Hirarki dan Aturan Penggunaan Level Log

Gunakan level log sesuai tingkat kepentingan operasional:

| Level | Kapan Harus Digunakan? | Contoh Peristiwa | Notifikasi Alert? |
| :--- | :--- | :--- | :--- |
| **`DEBUG`** | Diagnostik mendalam saat development lokal. Nonaktif di produksi secara default. | Payload raw request, intermediate variable states, waktu eksekusi query lokal. | Tidak |
| **`INFO`** | Kejadian normal dalam siklus hidup aplikasi yang menandai milestone bisnis/sistem. | Service berhasil boot, user berhasil login, checkout order selesai diproses. | Tidak |
| **`WARN`** | Peristiwa tidak biasa atau anomali yang tidak menghentikan request saat ini, tetapi perlu diawasi. | Percobaan retry koneksi DB ke-2, disk storage mencapai kapasitas 80%, penggunaan API deprecated. | Opsional (jika spike) |
| **`ERROR`** | Terjadi kegagalan fungsi atau request tertentu yang memerlukan investigasi teknis. | Database query timeout, payment gateway timeout, kegagalan unhandled HTTP 500. | Ya (Slack/Pager) |
| **`FATAL`** | Kerusakan total yang menyebabkan seluruh proses aplikasi mati (*crash*) atau tidak dapat melayani request baru. | Koneksi database primer terputus total saat startup, kehabisan memori (*OOM*). | Ya (Instan PagerDuty) |

---

## 4. Sanitasi Log & Proteksi Data Sensitif (PII Redaction)

Mencatat data pribadi pengguna ke dalam log adalah pelanggaran hukum privasi (seperti UU PDP, GDPR) dan celah kebocoran keamanan.

### Data yang Wajib Disensor (Redacted):
1. **Kredensial:** Password akun, API tokens, JWT, SSH private keys, webhook secrets.
2. **Data Finansial:** Nomor kartu kredit (PAN), CVV, PIN perbankan.
3. **Personally Identifiable Information (PII):** NIK (KTP), nomor paspor, alamat email lengkap, nomor telepon pribadi.

```typescript
// Contoh middleware sanitasi pada logger (Pino / Winston):
const SENSITIVE_KEYS = ["password", "token", "authorization", "creditCard", "cvv", "nik"];

function redactSensitiveData(data: Record<string, unknown>): Record<string, unknown> {
  const sanitized = { ...data };
  for (const key of Object.keys(sanitized)) {
    if (SENSITIVE_KEYS.some((sensitive) => key.toLowerCase().includes(sensitive))) {
      sanitized[key] = "[REDACTED]";
    }
  }
  return sanitized;
}
```

---

## 5. Agregasi Log Terpusat (Centralized Logging)

Jangan menyimpan log ke file disk server lokal (`app.log`) yang rentan hilang saat container di-restart:
- **Tulis Log ke `stdout` & `stderr`:** Sesuai prinsip 12-Factor App, aplikasi cukup mencetak JSON ke output standar.
- **Log Collectors (Agents):** Gunakan agent seperti FluentBit, Promtail, Vector, atau CloudWatch Agent untuk membaca stream stdout container.
- **Log Storage & Visualisasi:**
  - **Grafana Loki:** Sangat efisien, biaya penyimpanan murah, terintegrasi mulus dengan Prometheus dan Grafana dashboard.
  - **Elasticsearch / OpenSearch (ELK):** Ideal untuk pencarian teks skala besar dan analisis data kompleks.
  - **Cloud Managed:** AWS CloudWatch Logs, GCP Cloud Logging, Datadog.

---

## 6. Arsitektur Penanganan Kesalahan (Error Handling)

### A. Custom Application Errors
Bangun hierarki kelas error khusus untuk membedakan kesalahan operasional (*client error*) dari kegagalan sistem tak terduga (*programmer error*):

```typescript
// Base Error Class
export abstract class AppError extends Error {
  abstract readonly statusCode: number;
  abstract readonly isOperational: boolean;

  constructor(message: string, public readonly code: string) {
    super(message);
    Object.setPrototypeOf(this, new.target.prototype);
  }
}

// Operational Error: Kesalahan input user (HTTP 400)
export class BadRequestError extends AppError {
  readonly statusCode = 400;
  readonly isOperational = true;
  constructor(message: string, code = "BAD_REQUEST") {
    super(message, code);
  }
}

// Operational Error: Data tidak ditemukan (HTTP 404)
export class NotFoundError extends AppError {
  readonly statusCode = 404;
  readonly isOperational = true;
  constructor(message: string, code = "NOT_FOUND") {
    super(message, code);
  }
}
```

### B. Tangani Unhandled Rejections & Uncaught Exceptions
Aplikasi Node.js harus memiliki safety net tingkat proses:

```typescript
process.on("unhandledRejection", (reason: Error) => {
  logger.fatal({ err: reason }, "Unhandled Promise Rejection terdeteksi!");
  process.exit(1); // Fail-fast: biarkan orchestrator (k8s/docker) me-restart pod
});

process.on("uncaughtException", (error: Error) => {
  logger.fatal({ err: error }, "Uncaught Exception terdeteksi!");
  process.exit(1);
});
```

---

## 7. Pemaparan Error yang Aman (Internal vs Client-Facing)

Jangan pernah mengekspos raw error stack trace atau query SQL internal ke response pengguna publik:
- **Response ke Client (Aman):**
  ```json
  {
    "success": false,
    "error": {
      "code": "INTERNAL_SERVER_ERROR",
      "message": "Terjadi kesalahan internal pada server kami. Silakan coba beberapa saat lagi.",
      "request_id": "req_8f1a23c4-129a-4c28-98e1-5e28a491cd77"
    }
  }
  ```
- **Log Internal Server (Lengkap):** Server mencatat stack trace, file path, nomor baris, dan konteks database menggunakan `correlation_id` yang sama untuk investigasi developer.

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Swallowing Errors:** Menangkap error di blok `try-catch` kosong tanpa logging atau re-throwing (`catch (e) {}`), membuat insiden menghilang tanpa jejak.
2. **Log Spamming:** Menaruh level `INFO` di dalam perulangan loop ribuan data, menyebabkan biaya penyimpanan log meledak.
3. **Mencetak String PII Terbuka:** Melakukan log raw payload registrasi user yang berisi plain text password.
4. **Log Tanpa Konteks:** Menulis log `logger.error("Terjadi error")` tanpa menyertakan objek error atau ID entitas yang gagal diproses.
5. **Menggunakan `console.log` di Produksi:** Mengabaikan library logger terstruktur (Pino/Winston/Zap/Logrus) dan mengandalkan console bawaan tanpa metadata level dan timestamp.

---

## 9. Dokumen Terkait

- [Environment Configuration](environment-configuration.md)
- [Coding Standards](coding-standards.md)
- [Repository Structure](repository-structure.md)
- [Code Review Guide](code-review.md)
