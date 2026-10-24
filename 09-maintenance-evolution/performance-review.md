# Performance Review & Optimization

Panduan audit berkala dan optimasi performa komprehensif pada lapisan Frontend, Backend, Database, dan Infrastruktur untuk menjaga kecepatan serta efisiensi biaya sistem.

---

## 1. Ritme Audit Performa Berkala (Quarterly Cadence)

Kinerja sistem menurun secara perlahan (*performance regression*) seiring bertambahnya data, kompleksitas fitur, dan volume pengguna. 

Audit performa terstruktur dijalankan **setiap kuartal (Quarterly Review)** dengan alur kerja:
1. **Baseline Measurement:** Mengumpulkan metrik produksi 30 hari terakhir sebagai pembanding dasar.
2. **Bottleneck Identification:** Menemukan 10 kueri terlambat, 5 endpoint paling lambat (p99), dan halaman dengan skor Web Vitals terburuk.
3. **Targeted Remediation:** Menentukan 2-3 inisiatif optimasi dengan rasio dampak terhadap *effort* paling tinggi.
4. **Post-Optimization Verification:** Menguji ulang pada lingkungan staging/load test dan memantau telemetri pasca rilis produksi.

---

## 2. Empat Domain Kinerja Utama

```
+-----------------------------------------------------------------------+
| 1. Frontend / Client      : Core Web Vitals, JS Bundle, Render Lag    |
| 2. Backend / API Service  : p50/p95/p99 Latency, Throughput, Errors   |
| 3. Database Layer         : Slow Queries, Indexing, Pool, N+1 Query   |
| 4. Infrastructure         : CPU, Memory, Disk I/O, Network Saturation|
+-----------------------------------------------------------------------+
```

### A. Frontend / Client
Fokus pada pengalaman langsung pengguna (*user-perceived performance*):
- **Core Web Vitals:**
  - **LCP (Largest Contentful Paint):** Target `< 2.5s` (waktu muat konten visual utama).
  - **CLS (Cumulative Layout Shift):** Target `< 0.1` (stabilitas visual tanpa pergeseran elemen tiba-tiba).
  - **INP (Interaction to Next Paint):** Target `< 200ms` (responsivitas terhadap klik/input pengguna).
- **Bundle Size Optimization:** Pantau ukuran file JavaScript dan CSS melalui code-splitting, tree-shaking, dan dynamic imports.
- **Render Performance:** Cegah re-render komponen yang tidak perlu pada SPA (React/Vue/Svelte) dan optimalkan aset gambar (format WebP/AVIF, lazy loading).

### B. Backend / API
Fokus pada efisiensi pemrosesan dan ketahanan beban:
- **Latency Percentiles:** Jangan hanya mengandalkan rata-rata (*mean*). Gunakan:
  - **p50 (Median):** Pengalaman pengguna tipikal.
  - **p95:** Pengalaman sebagian besar pengguna saat beban meningkat.
  - **p99:** Indikator tail latency untuk mendeteksi blocking I/O, garbage collection pause, atau resource contention.
- **Throughput (Requests Per Second - RPS):** Kapasitas maksimum sistem melayani request sebelum response time terdegradasi.
- **Error Rate:** Rasio HTTP 5xx terhadap total request (target `< 0.05%`).

### C. Database Layer
Penyebab utama lebih dari 80% insiden performa aplikasi:
- **Slow Query Log Analysis:** Aktifkan slow query log dengan ambang batas `> 100ms` - `200ms`.
- **Missing Indexes:** Periksa kueri yang melakukan *Sequential Scan / Full Table Scan* pada tabel besar menggunakan `EXPLAIN ANALYZE`.
- **N+1 Query Detection:** Identifikasi ORM yang mengeksekusi satu kueri induk diikuti puluhan kueri anak dalam loop.
- **Connection Pool Exhaustion:** Pantau jumlah koneksi aktif vs batas pool (`max_connections`). Pastikan idle connection timeout dikonfigurasi dengan tepat.

### D. Infrastructure & System Resources
Ketersediaan sumber daya komputasi di cloud atau VPS:
- **CPU & Memory Utilization:** Waspadai pemanfaatan rata-rata yang konsisten berada di atas `75-80%`.
- **Disk I/O & IOPS:** Pantau *read/write latency* pada volume database untuk mendeteksi antrian disk (*disk queuing*).
- **Network Saturation:** Periksa batasan bandwidth antar subnet, egress cost, dan packet drops.

---

## 3. Alat Profiling dan Observabilitas

| Domain | Kategori Tool | Rekomendasi Tool | Output yang Dianalisis |
|---|---|---|---|
| **Frontend** | Browser DevTools, RUM, Sintetik | Chrome DevTools, Lighthouse, PageSpeed Insights, Sentry Performance. | Main thread blocking time, script parse duration, layout shift breakdown. |
| **Backend** | APM & Distributed Tracing | OpenTelemetry, Prometheus + Grafana, Datadog, Jaeger. | Trace waterfall, database call duration per endpoint, memory flamegraph. |
| **Database** | Query Analyzer & Monitor | `pg_stat_statements` (PostgreSQL), MySQL Performance Schema, pghero. | Most time-consuming queries, cache hit ratio, index hit ratio. |
| **Load Testing** | Benchmark & Stress Testing | k6, Locust, Apache Bench (`ab`), autocannon. | RPS vs latency curve, point of failure, saturation plateau. |

### Contoh Diagnostik Cepat Kueri Database
```sql
-- Analisis query lambat di PostgreSQL
EXPLAIN (ANALYZE, BUFFERS)
SELECT o.id, o.created_at, c.email
FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE o.status = 'PENDING'
ORDER BY o.created_at DESC
LIMIT 50;
-- Periksa apakah output menggunakan "Seq Scan" atau "Index Scan".
```

---

## 4. Benchmarking dan Load Testing Baseline

Sebelum merilis fitur skala besar atau mengaudit kapasitas kuartalan:
1. **Smoke Test:** Beban minimal (1-5 virtual users) untuk memvalidasi alur dasar tanpa response error.
2. **Load Test:** Beban normal hingga puncak historis (misal: 2x traffic harian) selama 30-60 menit untuk memantau stabilitas p95/p99 latency.
3. **Stress / Spike Test:** Beban mendadak hingga 3x-5x lipat untuk menguji kemampuan auto-scaling dan *graceful degradation* (apakah sistem pulih otomatis saat lonjakan reda).
4. **Baseline Comparison:** Simpan hasil pengujian sebagai JSON/HTML report untuk dibandingkan dengan kuartal berikutnya.

---

## 5. Optimasi Cost-Performance (Efisiensi Biaya)

Optimasi performa sering kali berbanding lurus dengan penghematan biaya infrastruktur cloud:
- **Rightsizing Instances:** Turunkan spesifikasi (*downscale*) CPU/RAM mesin yang pemanfaatannya rata-rata `< 20%`.
- **Caching Layer (Cache Hit Ratio):**
  - Pasang CDN (Cloudflare/CloudFront) untuk aset statis (target cache hit `> 90%`).
  - Gunakan Redis/Memcached untuk kueri berat yang jarang berubah (target cache hit `> 80%`).
  - Atur header HTTP `Cache-Control: public, max-age=...` yang tepat pada endpoint API publik.
- **Database Storage Archiving:** Arsipkan data transaksi lawas (> 2 tahun) ke cold storage (S3/GCS) untuk memperkecil ukuran tabel dan indeks inti.

---

## 6. Template Checklist Audit Kinerja Kuartalan

| Item Pemeriksaan | Target Standar | Status Terakhir | Tindakan Koreksi |
|---|---|---|---|
| Core Web Vitals LCP | `< 2.5s` pada 75th percentile | `3.2s` (Gagal) | Kompres hero banner ke WebP + aktifkan CDN caching. |
| Core Web Vitals INP | `< 200ms` | `120ms` (Lulus) | Tidak ada tindakan. |
| Endpoint P99 Latency | `< 500ms` untuk semua core API | `1.4s` (Gagal) | Tambahkan compound index pada tabel `transactions`. |
| DB Cache Hit Ratio | `> 99%` pada buffer cache | `94%` (Perhatian) | Naikkan `shared_buffers` atau evaluasi kueri tanpa indeks. |
| Cloud CPU Utilization | Rata-rata `40-60%` | `15%` (Over-provisioned) | Turunkan tipe VM dari `large` ke `medium`. |

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Premature Optimization:** Mengoptimalkan kode algoritma yang hanya memakan waktu 2 milidetik padahal kueri database membutuhkan 1.500 milidetik.
2. **Mengabaikan Tail Latency (Rata-rata Menipu):** Rata-rata latency terlihat bagus (50ms), padahal 5% pengguna mengalami timeout `> 5s` karena p99 diabaikan.
3. **Menguji Hanya di Localhost:** Menarik kesimpulan performa dari laptop developer berspesifikasi tinggi dengan database lokal berisi 10 baris dummy data.
4. **Menambah Hardware Tanpa Memperbaiki Kueri:** Menghabiskan anggaran cloud dengan menaikkan RAM server 4x lipat untuk menutupi kueri database yang tidak memiliki indeks.

---

## 8. Dokumen Terkait

- [Technical Debt](technical-debt.md) — Pelunasan architectural debt dan kueri usang yang membebani sistem.
- [Dependency Updates](dependency-updates.md) — Mengurangi ukuran dependensi untuk mendongkrak performa frontend.
- [Post-Release Review](post-release-review.md) — Mengevaluasi dampak rilis baru terhadap performa operasional.
