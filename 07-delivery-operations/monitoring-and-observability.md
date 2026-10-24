# Monitoring and Observability Guide

Panduan penerapan pilar observabilitas modern, perancangan metrik performa (RED & USE method), logging terstruktur, strategi pencegahan kelelahan peringatan (*alert fatigue*), dan evaluasi tooling.

---

## 1. Perbedaan Mendasar: Monitoring vs Observability

- **Monitoring (Memantau yang Diketahui):** Memberitahu Anda saat sistem sedang rusak berdasarkan ambang batas yang sudah ditentukan sebelumnya (*predefined thresholds*). Contoh: "CPU server di atas 90%" atau "Web server mengembalikan error 500".
- **Observability (Memahami yang Tidak Diketahui):** Kemampuan untuk menyimpulkan kondisi internal suatu sistem hanya dengan memeriksa output eksternalnya. Memungkinkan engineer menanyakan pertanyaan kompleks tanpa perlu merilis kode baru: "Mengapa pengguna dari geolokasi X mengalami latensi tinggi hanya saat checkout keranjang berisi lebih dari 5 item?".

```
+--------------------------------------------------------------+
| Monitoring    : "Sistem down!" (Menjawab: APA yang rusak)     |
| Observability : "Mengapa down & di mana titik macetnya?"      |
|                 (Menjawab: MENGAPA sistem mengalami anomali)  |
+--------------------------------------------------------------+
```

Observability dibangun di atas data instrumentasi yang kaya sehingga tim dapat menelusuri pola anomali yang belum pernah diprediksi sebelumnya.

---

## 2. Tiga Pilar Observabilitas

### A. Metrics (Data Agregat Kuantitatif)
Metrik adalah data numerik teragregasi yang dihitung dalam rentang waktu tertentu. Sangat hemat penyimpanan dan ideal untuk dashboard serta pemicu peringatan (*alerting*).

Gunakan dua metodologi standar industri:
1. **RED Method (Untuk Request-Driven Services / API):**
   - **Rate:** Jumlah request per detik (Requests Per Second - RPS).
   - **Errors:** Jumlah request yang gagal (Error rate HTTP 5xx atau exception rate).
   - **Duration:** Distribusi waktu respons transaksi (Latency p50, p95, p99).
2. **USE Method (Untuk Infrastruktur & Resource):**
   - **Utilization:** Persentase waktu resource sibuk melayani pekerjaan (misal: Memory 75%, CPU 80%).
   - **Saturation:** Tingkat antrean pekerjaan yang belum terlayani (misal: CPU run queue, DB connection pool waiting queue).
   - **Errors:** Jumlah peristiwa kesalahan pada tingkat perangkat keras atau driver.

### B. Logs (Catatan Kejadian Terstruktur)
Hindari string log polos (`console.log("user logged in")`). Gunakan format **JSON terstruktur** agar mudah diindeks dan difilter oleh log aggregator:

```json
{
  "timestamp": "2026-10-25T08:30:00.123Z",
  "level": "error",
  "message": "Payment processing failed",
  "correlation_id": "c1f7a8b9-4321-4def-a1b2-c3d4e5f6a7b8",
  "service": "checkout-service",
  "user_id": "usr_987654",
  "error_code": "GATEWAY_TIMEOUT",
  "duration_ms": 5012
}
```
**Correlation ID / Trace ID:** Wajib disuntikkan pada setiap request masuk di API gateway dan diteruskan ke seluruh downstream service via HTTP header (`X-Correlation-ID`) agar satu alur transaksi dapat dilacak utuh dari frontend ke database.

### C. Traces (Distributed Tracing)
Menampilkan perjalanan lengkap sebuah request saat melintasi berbagai microservice, database, cache, dan message broker. OpenTelemetry (OTel) menyediakan standar instrumen trace yang terdiri dari:
- **Trace:** Representasi alur transaksi lengkap ujung ke ujung.
- **Span:** Satuan unit kerja tunggal di dalam trace (misal: eksekusi query SQL tertentu, pemanggilan RPC eksternal).

### Tipe Metrik Utama
Dalam instrumen OpenTelemetry dan Prometheus:
- **Counter:** Nilai kumulatif yang hanya bertambah (misal: total HTTP requests, total error count).
- **Gauge:** Nilai seketika yang dapat naik dan turun (misal: memory usage, active connections).
- **Histogram:** Distribusi sampel data ke dalam bucket (misal: latency durasi request).

---

## 3. Desain Health Check Probes

Sediakan endpoint ringan yang tidak membebani sistem:
- **Liveness Probe (`GET /healthz`):** Mengembalikan HTTP 200 jika event loop / runtime aplikasi masih berjalan. Jangan mengecek database eksternal di endpoint ini agar pod tidak mengalami restart massal saat database melambat sesaat.
- **Readiness Probe (`GET /readyz`):** Mengembalikan HTTP 200 hanya jika aplikasi sudah terhubung ke database dan siap memproses order transaksi. Kembalikan HTTP 503 saat shutdown sedang berlangsung agar load balancer segera mencabut aliran trafik.

---

## 4. Praktik Terbaik Alerting: Anti Alert Fatigue

Peringatan yang terlalu sering berbunyi tanpa tindakan nyata akan diabaikan oleh engineer (*alert fatigue*).

### Aturan Emas Peringatan:
1. **Peringatkan Gejala Pengguna, Bukan Penyebab Mentah (Alert on Symptoms, Not Causes):**
   - ❌ Jangan kirim notifikasi darurat untuk: "CPU Server 01 mencapai 85%".
   - ✅ Kirim notifikasi darurat untuk: "Error rate API pembayaran > 2% selama 3 menit" atau "Latency p95 > 2000ms berdampak pada pengguna".
2. **Actionable Runbooks Wajib:** Setiap notifikasi alert wajib menyertakan tautan langsung ke dokumen SOP penanganan (*runbook link*). Jika alert tidak memiliki tindakan konkret yang dapat diambil engineer, alert tersebut tidak boleh diaktifkan.
3. **Pemisahan Jalur Notifikasi Berdasarkan Urgensi:**
   - **Paging (PagerDuty, Opsgenie, Phone call):** Hanya untuk insiden SEV-1 dan SEV-2 yang membutuhkan respons instan `< 15 menit`.
   - **Chat Channel (#ops-alerts):** Untuk peringatan tren non-kritis (peringatan disk space tersisa 20%, error non-blocking).

---

## 5. Prinsip Perancangan Dashboard

- **Hirarki Visual 30 Detik:** Dashboard utama harus memberikan jawaban dalam 30 detik: "Apakah sistem saat ini sehat atau terbakar?".
- **Letakkan RED Metrics di Bagian Paling Atas:** Error rate global, total throughput RPS, dan p95 latency adalah widget pertama yang dilihat oleh tim on-call.
- **Kelompokkan Berdasarkan Domain Layanan:** Pisahkan tab dashboard untuk API Gateway, Database, Worker Queue, dan Frontend Web Vitals.
- **Hindari Vanity Metrics:** Jangan penuhi dashboard utama dengan metrik statis yang tidak pernah berubah atau tidak dapat ditindaklanjuti.

---

## 6. Perbandingan Tooling Observabilitas

| Solusi | Komponen | Kelebihan | Kelemahan |
|---|---|---|---|
| **Prometheus + Grafana + Loki** | Open source stack (OSS) | Gratis biaya lisensi, kontrol penuh atas data, standar industri k8s | Butuh keahlian manajemen storage & server sendiri |
| **Datadog / New Relic** | All-in-one SaaS Platform | Setup instan, UI intuitif, integrasi otomatis ratusan platform | Biaya langganan melonjak tinggi seiring volume data |
| **OpenTelemetry (OTel)** | Open Standard Vendor-Agnostic | Menghindari vendor lock-in; instrumen sekali, kirim ke mana saja | Membutuhkan setup collector dan pipeline backend |

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Logging Data Sensitif (PII):** Mencetak password, token otentikasi JWT, atau nomor kartu kredit ke dalam file log aplikasi.
2. **Terlalu Banyak Alert Tidak Berguna:** Membuat ratusan rule alert statis yang terus membunyikan alarm sehingga tim akhirnya mematikan notifikasi (*mute*).
3. **Log Hanya Berupa Teks Polos:** Mengabaikan struktur JSON sehingga mustahil melakukan query filter agregasi saat audit insiden.
4. **Hanya Mengukur Nilai Rata-Rata (Average Latency):** Memantau average latency (50ms) yang menyamarkan kenyataan bahwa 5% pengguna mengalami waktu tunggu ekstrem (10.000ms). Selalu gunakan persentil (p95, p99).
5. **Tidak Menyediakan Correlation ID:** Gagal menyematkan trace ID antar service sehingga investigasi error antar microservice memakan waktu berjam-jam.
6. **Liveness Probe Menguji Seluruh Ketergantungan Eksternal:** Menghubungkan `/healthz` ke redis dan db pihak ketiga sehingga anomali kecil memicu *reboot loop* berantai.

---

## 8. Dokumen Terkait

- [Incident Response Guide](incident-response.md)
- [Deployment Guide](deployment-guide.md)
- [Release Readiness Checklist](release-readiness.md)
- [Operations Runbook Template](../templates/delivery/operations-runbook-template.md)
- [Backup and Recovery](backup-and-recovery.md)
