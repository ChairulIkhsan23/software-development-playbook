# Maintenance & Operations Review Checklist

Checklist audit pemeliharaan sistem berkala (bulanan atau kuartalan). Mengevaluasi keandalan infrastruktur, performa, postur keamanan, pelunasan utang teknis, dan kesiapan operasional tim.

---

## 1. System Reliability & Health
- [ ] **SLA & Uptime Review**: Evaluasi uptime bulanan terhadap target (misal: target 99.9%); analisis deviasi dan penyebab downtime.
- [ ] **Error Budget Status**: Hitung sisa error budget kuartal berjalan; tentukan apakah rilis fitur baru perlu diperlambat demi stabilitas.
- [ ] **MTTD & MTTR Trends**: Rata-rata waktu deteksi insiden (MTTD) dan waktu pemulihan (MTTR) dievaluasi tren grafiknya (menurun atau memburuk).
- [ ] **Alert Fatigue Audit**: Tinjau volume alert; bisukan (*silence*) atau kalibrasi ulang notifikasi palsu (*false positive alerts*).
- [ ] **Service Dependency Health**: Pantau SLA dan riwayat gangguan vendor eksternal (Payment Gateway, SMS vendor, Cloud Provider).
- [ ] **Third-Party API Deprecation Watch**: Tinjau changelog vendor pihak ketiga untuk deteksi dini endpoint yang akan dihentikan (*sunset*).

---

## 2. Performance & Capacity
- [ ] **Slow Query Review**: Ekstrak daftar 10 kueri database terlambat (*slowest queries*); tambahkan index atau refactor query yang lambat.
- [ ] **API Latency Trends**: Analisis tren latensi p95 dan p99 pada seluruh endpoint publik selama 30 hari terakhir.
- [ ] **Resource Utilization**: Pantau beban puncak CPU, RAM, IOPS disk, dan kuota koneksi database; siapkan rencana autoscaling atau resizing.
- [ ] **Cloud Cost Optimization**: Audit pengeluaran cloud bulanan; hapus disk yatim (*unattached EBS/disks*), instance idle, dan resource tak terpakai.
- [ ] **Cache Hit Ratio**: Tingkat efisiensi Redis/Memcached dievaluasi (target cache hit ratio > 85%).
- [ ] **Storage Growth Projections**: Proyeksi pertumbuhan volume database dan file storage diestimasi untuk 6 bulan ke depan.

---

## 3. Security & Dependencies
- [ ] **CVE Vulnerability Audit**: Pemindaian kerentanan paket dependensi seluruh repositori; pastikan nol temuan Critical/High tak tertangani.
- [ ] **Dependency Patching**: Paket dependensi minor/patch diperbarui; hapus paket pustaka yang sudah berstatus *deprecated*.
- [ ] **Secrets & Certificate Rotation**: Kunci API pihak ketiga, token database, dan sertifikat SSL/TLS dirotasi sesuai jadwal kepatuhan.
- [ ] **IAM & Access Rights Audit**: Cabut akses akun GitHub, server SSH, konsol AWS/GCP, dan database untuk karyawan yang telah resign atau pindah peran.
- [ ] **Backup Encryption Verification**: Enkripsi berkas cadangan di storage diverifikasi aman dengan algoritma AES-256.
- [ ] **Audit Trail Log Integrity**: Log akses admin dan perubahan data sensitif dipastikan tidak dapat dimodifikasi (*append-only* / immutable).

---

## 4. Technical Debt Management
- [ ] **Tech Debt Backlog Review**: Tiket utang teknis diprioritaskan ulang bersama Product Owner (alokasi minimal 20% kapasitas sprint untuk tech debt).
- [ ] **Code Quality & Linter Metrics**: Analisis tren duplikasi kode, cakupan tes unit (*test coverage*), dan peringatan compiler/linter.
- [ ] **Framework & Runtime Upgrades**: Perencanaan jadwal migrasi versi LTS (contoh: upgrade Node.js 18 ke 20, Postgres 14 ke 16).
- [ ] **Dead Code Cleanup**: Modul eksperimental yang dibatalkan, flag fitur usang (*stale feature flags*), dan rute API lama dihapus bersih.
- [ ] **Architecture Drift Assessment**: Pastikan implementasi kode di tim tidak menyimpang dari pedoman arsitektur sistem awal.
- [ ] **Documentation Drift Check**: Verifikasi apakah README, diagram arsitektur, dan wiki masih relevan dengan kode produksi terkini.

---

## 5. Operational Assets & Disaster Recovery (DR)
- [ ] **Disaster Recovery Drill Completed**: Simulasi pemulihan cadangan data (*backup restore drill*) berhasil dijalankan di staging tanpa korupsi data.
- [ ] **Runbooks & SOP Accuracy**: Dokumentasi penanganan insiden (*on-call runbooks*) diuji dan diperbarui dengan topologi sistem mutakhir.
- [ ] **Post-Mortem Action Items**: Tindakan korektif (*action items*) dari insiden bulan lalu diverifikasi telah tuntas 100%.
- [ ] **Logging & Tracing Hygiene**: Konfigurasi log retention berjalan; log lama diarsipkan ke cold storage guna hemat biaya disk.
- [ ] **On-Call Roster Updated**: Jadwal rotasi piket teknis kuartal berikutnya disahkan dan diumumkan ke tim.

---

## 6. Compliance & Data Retention
- [ ] **Data Retention Enforcement**: Skrip penghapusan berkala data transaksi kadaluarsa dan soft-deleted records tervalidasi berjalan.
- [ ] **Privacy Law Adherence (UU PDP)**: Prosedur pemenuhan hak subjek data (penghapusan akun, ekspor data pribadi) siap dieksekusi.
- [ ] **Access Key Expiry Check**: Personal Access Token (PAT) dan SSH keys yang berumur lebih dari 90 hari diwajibkan regenerasi.

---

## Panduan Penilaian Audit (Audit Scoring Guide)
Hitung skor kesehatan operasional: `(Total Checklist Tercentang / 31) * 100%`

| Skor Pemeliharaan | Kondisi Sistem | Tindak Lanjut Wajib |
|---|---|---|
| **>= 85%** | **Sehat (Healthy)** | Sistem prima. Pertahankan ritme kerja dan alokasi sprint normal. |
| **70% - 84%** | **Waspada (Degraded)** | Masukkan 3-5 item perbaikan prioritas ke sprint pemeliharaan terdekat. |
| **< 70%** | **Kritis (At Risk)** | *Feature freeze* parsial. Seluruh tim wajib fokus menuntaskan utang teknis dan stabilitas infra. |

---

## Pengelolaan Backlog Perbaikan (Improvement Backlog)
Seluruh item yang tidak tercentang dalam audit wajib dikonversi menjadi tiket Jira/GitHub Issue dengan konvensi berikut:
- **Format Judul Tiket**: `[MAINT] <Komponen Sistem> - <Deskripsi Perbaikan Singkat>` (contoh: `[MAINT] Database - Tambah index komposit kolom status tabel orders`).
- **Klasifikasi Prioritas**:
  - **P1 (Kritis)**: Kerentanan keamanan CVE Critical/High, uji restore backup gagal, sisa kapasitas disk server < 15%.
  - **P2 (Tinggi)**: Kueri lambat eksekusi > 2 detik, akses staf resign belum dicabut, runtime mendekati End-of-Life (EOL).
  - **P3 (Sedang)**: Optimasi biaya cloud, pembersihan stale feature flags, pembaruan dokumen SOP runbook.
- **Aturan Alokasi Tim**: Tim wajib menyisihkan 1 hari penuh per siklus sprint atau 20% story points untuk menyelesaikan backlog pemeliharaan.