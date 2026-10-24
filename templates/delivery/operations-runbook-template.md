---
title: "Template Operations Runbook"
document_id: "OPS-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
service_name: "[Nama Layanan / Microservice]"
system_tier: "Tier-1 Mission Critical"
sla_target: "99.95% Availability"
on_call_contact: "[Nama SRE / No. Telepon On-Call]"
---

# Service Operations Runbook: `[Nama Layanan]`

> **Panduan Pengisian:** Gunakan dokumen runbook ini sebagai panduan tanggap darurat bagi tim On-Call SRE dan Developer saat terjadi insiden di lingkungan produksi. Seluruh perintah terminal harus bersifat deterministik dan aman.

---

## 1. Metadata Layanan & Target SLA [Wajib]

> **Panduan Pengisian:** Definisikan tingkat kritikalitas layanan, kontak eskalasi, dan target komitmen layanan.

- **Nama Layanan (Service) [Wajib]:** `[Nama Service, misal: auth-service / order-processor]`
- **Tingkat Kritikalitas (Tier) [Wajib]:** `[Tier-1 Kritis / Tier-2 Pendukung / Tier-3 Internal]`
- **Target SLA Ketersediaan [Wajib]:** `99.95% (< 21.9 menit downtime per bulan)`
- **Kontak On-Call Primer [Wajib]:** `[Nama Personel]` - No. HP: `[+62-811-xxxx]` - Slack: `@oncall-primary`
- **Kontak On-Call Sekunder [Wajib]:** `[Nama Personel]` - No. HP: `[+62-812-xxxx]` - Slack: `@oncall-secondary`

---

## 2. Dashboard Pemantauan & Saluran Peringatan (Alerts) [Wajib]

> **Panduan Pengisian:** Cantumkan tautan metrik, log explorer, dan kanal siaga insiden.

- **Dashboard Kinerja (APM) [Wajib]:** `https://grafana.example.com/d/[service-metrics]`
- **Log Aggregator (Kibana/Datadog) [Wajib]:** `https://logs.example.com/explore?query=service:[service-name]`
- **Distributed Tracing (Jaeger) [Opsional]:** `https://jaeger.example.com/search?service=[service-name]`
- **Kanal Notifikasi Peringatan [Wajib]:** Slack `#alerts-[service-name]` & PagerDuty Service `[Service-ID]`

---

## 3. Standard Operating Procedures (SOP Rutin) [Wajib]

> **Panduan Pengisian:** Langkah penanganan operasi reguler yang sering dieksekusi saat pemeliharaan.

### A. Restart Layanan (Rolling Service Restart) [Wajib]
```bash
# Restart pod secara bertahap tanpa downtime
kubectl rollout restart deployment/[service-name] -n production
kubectl rollout status deployment/[service-name] -n production
```

### B. Pembersihan Cache (Cache Clear / Invalidation) [Wajib]
```bash
# Hapus cache Redis berdasarkan pola namespace spesifik (hindari FLUSHALL di produksi)
redis-cli -h [redis-host] -p 6379 -a [password] --scan --pattern "[service-prefix]:*" | xargs redis-cli -h [redis-host] -p 6379 -a [password] del
```

### C. Penskalaan Instans (Scaling Instances) [Wajib]
```bash
# Tingkatkan replika pod secara manual saat lonjakan beban tidak terduga
kubectl scale deployment/[service-name] --replicas=10 -n production
```

### D. Rotasi Rahasia / Kredensial (Secret Rotation) [Kondisional]
1. Perbarui nilai secret di HashiCorp Vault / AWS Secrets Manager.
2. Sinkronkan ke cluster: `kubectl apply -f k8s/sealed-secrets.yaml -n production`.
3. Trigger rolling restart pod agar membaca secret baru.

---

## 4. Skenario Pemecahan Masalah Insiden (Troubleshooting) [Wajib]

> **Panduan Pengisian:** Prosedur investigasi dan mitigasi cepat untuk 3 jenis gangguan produksi teratas.

### Skenario A: Lonjakan Beban CPU / Memory Tinggi (> 90%) [Wajib]
1. Identifikasi pod terbebani: `kubectl top pods -n production -l app=[service-name]`.
2. Ambil heap profile / thread dump jika terjadi memory leak berulang.
3. Skalakan replika sementara atau lakukan rolling restart jika memory menumpuk tak wajar.

### Skenario B: Database Connection Pool Habis (Pool Exhaustion) [Wajib]
1. Cek jumlah koneksi aktif pada database PostgreSQL:
   ```sql
   SELECT count(*), state FROM pg_stat_activity WHERE datname = '[db_name]' GROUP BY state;
   ```
2. Temukan kueri lambat yang menahan koneksi:
   ```sql
   SELECT pid, now() - query_start AS duration, query FROM pg_stat_activity WHERE state != 'idle' ORDER BY duration DESC LIMIT 5;
   ```
3. Putus koneksi kueri macet jika membahayakan database: `SELECT pg_terminate_backend([pid]);`.

### Skenario C: Gangguan API Pihak Ketiga (3rd Party Dependency Failure) [Wajib]
1. Periksa status dashboard penyedia eksternal (contoh: status page payment gateway / SMS OTP).
2. Aktifkan mode circuit breaker atau fallback provider sekunder melalui dynamic feature flag:
   ```bash
   curl -X POST https://api.example.com/admin/flags/payment-gateway-fallback -H "Authorization: Bearer [TOKEN]"
   ```

---

## 5. Tindakan Pasca Insiden (Post-Incident Actions) [Wajib]

> **Panduan Pengisian:** Seluruh insiden berkategori SEV-1 dan SEV-2 wajib ditindaklanjuti dengan audit pasca insiden.

- [ ] [Wajib] Turunkan status insiden dan informasikan pemulihan layanan ke channel stakeholder.
- [ ] [Wajib] Jadwalkan pertemuan Post-Mortem / Blameless RCA maksimal `[3 hari kerja]` setelah insiden.
- [ ] [Wajib] Buat tiket perbaikan preventif (*Action Items*) di backlog sprint berikutnya.

---

*(Seksi dependensi eksternal di bawah ini opsional: hapus jika layanan tidak memiliki downstream)*
## 6. Daftar Layanan Terikat (Upstream/Downstream) [Opsional]
- **Upstream callers:** `api-gateway`, `web-frontend`
- **Downstream dependencies:** `user-db-cluster`, `redis-session-cache`, `notification-service`
