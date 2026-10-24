---
title: "Non-Functional Requirements (NFR) Specification"
project: "[Nama Proyek]"
version: "1.0.0"
author: "[Nama System Architect / Tech Lead]"
status: "[Draft | In Review | Approved]"
last_updated: "[YYYY-MM-DD]"
---

# Non-Functional Requirements (NFR): [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini menetapkan standar kualitas arsitektur sistem yang dapat diukur secara kuantitatif.
> Format ID standar: `NFR-[CAT]-[NNN]`, contoh: `NFR-PERF-001`, `NFR-SEC-002`.
> Hindari pernyataan kualitatif seperti "aplikasi harus cepat"; selalu sertakan target angka dan kondisi beban.

---

## 1. Taksonomi Kategori Kualitas [Wajib]

> **Panduan Pengisian:**
> Gunakan kategori standar berikut saat mengklasifikasikan kebutuhan non-fungsional:

- `PERF`: Performance & Latency (kecepatan respons dan throughput)
- `SCAL`: Scalability (kemampuan menangani lonjakan beban trafik)
- `AVAIL`: Availability & Uptime (ketersediaan layanan operasional)
- `SEC`: Security & Privacy (proteksi data, otentikasi, enkripsi)
- `REL`: Reliability & Recovery (toleransi kesalahan, RPO, RTO)
- `MAINT`: Maintainability (kemudahan pemeliharaan, coverage tes, durasi CI/CD)

## 2. Tabel Persyaratan Non-Fungsional (NFR Table) [Wajib]

> **Panduan Pengisian:**
> Masukkan setiap kebutuhan non-fungsional ke dalam tabel.
> Prioritas: `P1` (Wajib saat rilis v1), `P2` (Penting di fase rilis berikutnya), `P3` (Nice-to-have).

| ID | Kategori | Pernyataan Kebutuhan | Metrik Kuantitatif | Nilai Target | Kondisi / Profil Beban | Metode Pengukuran | Prioritas |
|---|---|---|---|---|---|---|---|
| `NFR-PERF-001` | PERF | Endpoint API membaca data harus responsif | Latensi p95 | `< 200 ms` | 500 permintaan bersamaan (concurrent) | Grafana APM / Datadog | [Wajib - P1] |
| `NFR-PERF-002` | PERF | Proses checkout selesai cepat | Latensi p99 | `< 800 ms` | Beban puncak normal (100 req/dtk) | K6 Load Test script | [Wajib - P1] |
| `NFR-SCAL-001` | SCAL | Sistem mampu menampung lonjakan pesanan kilat | Throughput | `> 2.500 RPS` | Autoscaling aktif pada CPU > 70% | Locust / K6 stress test | [Wajib - P1] |
| `NFR-AVAIL-001` | AVAIL | API publik tetap aktif sepanjang bulan | Persentase Uptime | `≥ 99.9%` | 24/7 (toleransi downtime < 43 mnt/bln) | Pingdom / BetterStack probe | [Wajib - P1] |
| `NFR-SEC-001` | SEC | Seluruh data transit terproteksi | Versi TLS | `TLS 1.3` | Semua koneksi HTTP publik | SSL Labs Automated Scan | [Wajib - P1] |
| `NFR-SEC-002` | SEC | Mencegah pembobolan brute-force pada login | Rate Limiting | `Max 5 req/mnt/IP` | Endpoint `/api/v1/auth/login` | OWASP ZAP / Integration Test | [Wajib - P1] |
| `NFR-REL-001` | REL | Pemulihan basis data dari bencana (RPO) | RPO (Data Loss) | `< 15 menit` | Kegagalan instance primer | Simulasi restore snapshot harian | [Wajib - P1] |
| `NFR-REL-002` | REL | Waktu pemulihan layanan total (RTO) | RTO (Downtime) | `< 30 menit` | Failover multi-AZ | Simulasi disaster recovery berkala | [Wajib - P2] |
| `NFR-MAINT-001` | MAINT | Cakupan automated testing backend terjaga | Test Coverage | `≥ 80% line coverage` | Seluruh modul domain bisnis inti | CI Pipeline SonarQube / Jest | [Kondisional - P2] |
| `NFR-MAINT-002` | MAINT | Waktu siklus build pipeline cepat | Durasi CI/CD | `< 8 menit` | Setiap Pull Request merge ke `main` | GitHub Actions Execution Log | [Opsional - P3] |

## 3. Metodologi Verifikasi & Pengujian Beban [Wajib]

> **Panduan Pengisian:**
> Jelaskan alat bantu dan tahapan pengujian untuk memvalidasi NFR sebelum rilis ke produksi.

- **Load & Stress Testing:** Dijalankan di lingkungan staging menggunakan `k6` sebelum setiap rilis mayor.
- **Security Scanning:** SAST otomatis di CI/CD (`Semgrep` / `Trivy`) dan DAST berkala sebelum rilis fitur baru.
- **Monitoring Produksi:** Alarm peringatan terkirim via Slack/PagerDuty jika p95 melampaui ambang batas selama 3 menit berurutan.
- **Disaster Recovery Drill:** Uji pemulihan data cadangan dilakukan setiap kuartal untuk membuktikan target RTO/RPO.

## 4. Kriteria Keberatan & Ambang Alarm (Thresholds) [Wajib]

> **Panduan Pengisian:**
> Tentukan nilai batas bahaya (warning) dan genting (critical) untuk pemantauan sistem produksi.

| Metrik Pemantauan | Ambang Warning | Ambang Critical | Tindakan Otomatis |
|---|---|---|---|
| Latensi API p95 | `> 350 ms` | `> 700 ms` | Trigger webhook tim on-call |
| CPU Utilization | `> 75%` selama 2 menit | `> 90%` selama 1 menit | Scale-out penambahan container instance |
| Error Rate (HTTP 5xx) | `> 1.0%` dari total traffic | `> 5.0%` dari total traffic | PagerDuty incident severity high |

## 5. Trade-Offs & Batasan Arsitektur [Opsional] *(Boleh dihapus jika tidak ada konflik)*

> **Panduan Pengisian:**
> Uraikan kompromi teknis yang sengaja diambil ketika dua NFR saling bertentangan (misal: konsistensi ketat vs performa tinggi).

- **Konsistensi vs Latensi:** Memilih *Eventual Consistency* untuk pelaporan analitik demi menjaga latensi transaksi utama tetap `< 200 ms`.
- **Keamanan vs Kecepatan Onboarding:** Verifikasi email 2-faktor diwajibkan, meskipun sedikit menambah waktu onboarding pengguna baru.
