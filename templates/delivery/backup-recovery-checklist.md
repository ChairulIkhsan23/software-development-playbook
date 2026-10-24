---
title: "Template Backup & Disaster Recovery Checklist"
document_id: "BDR-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
owner: "[Nama Database Administrator / Lead SRE]"
system_scope: "Production Infrastructure & Persistent Storage"
rpo_target: "< 1 Jam (Recovery Point Objective)"
rto_target: "< 2 Jam (Recovery Time Objective)"
---

# Backup & Disaster Recovery (DR) Checklist

> **Panduan Pengisian:** Gunakan dokumen checklist ini untuk memastikan seluruh komponen penyimpanan data dicadangkan secara berkala dan prosedur pemulihan bencana (*Disaster Recovery*) teruji secara periodik.

---

## 1. Jadwal Pencadangan & Kebijakan Retensi Data [Wajib]

> **Panduan Pengisian:** Tentukan jadwal otomatisasi pencadangan, jenis cadangan, dan masa retensi arsip.

| Tipe Pencadangan | Frekuensi & Waktu Eksekusi | Target Komponen | Lokasi Penyimpanan | Kebijakan Retensi | Status |
|---|---|---|---|---|---|
| Inkremental Harian | Setiap hari pukul 01:00 WIB | Database WAL & Delta Snapshot | AWS S3 Standard / GCS | Disimpan selama `30 Hari` | [Wajib] |
| Cadangan Penuh Mingguan | Setiap Minggu pukul 02:00 WIB | Database Full Dump & Redis Snapshot | S3 Glacier Instant Retrieval | Disimpan selama `90 Hari` | [Wajib] |
| Arsip Bulanan | Hari ke-1 setiap bulan pukul 03:00 WIB | Database Full Dump + User Object Storage | S3 Glacier Flexible Archive | Disimpan selama `365 Hari` | [Wajib] |
| Replikasi Kontinu | Real-time Streaming (< 1 detik) | Read-Replica DB lintas Availability Zone | Multi-AZ Standby Instance | Aktif kontinu | [Wajib] |

---

## 2. Checklist Verifikasi Integritas Cadangan [Wajib]

> **Panduan Pengisian:** Centang pemeriksaan ini pada setiap siklus audit backup otomatis harian dan mingguan.

### A. Validasi Otomatisasi & Checksum [Wajib]
- [ ] [Wajib] Job cron backup selesai dengan exit status `0` tanpa timeout.
- [ ] [Wajib] Nilai hashing SHA-256 dibuat otomatis saat upload dan divalidasi integritasnya terhadap file cadangan.
- [ ] [Wajib] Ukuran berkas cadangan divalidasi tidak mengalami anomali penurunan drastis (> 20% deviasi).

### B. Keamanan & Enkripsi Data [Wajib]
- [ ] [Wajib] Enkripsi *at-rest* aktif menggunakan algoritma AES-256 dengan kunci AWS KMS / GCP KMS yang terkelola terpisah.
- [ ] [Wajib] Hak akses bucket penyimpanan cadangan dibatasi secara ketat (*principle of least privilege*); akses publik dinonaktifkan total (*Block Public Access*).

### C. Replikasi Luar Wilayah (Offsite & Cross-Region) [Wajib]
- [ ] [Wajib] Replikasi otomatis lintas region (*Cross-Region Replication / CRR*) aktif menuju region sekunder (contoh: Jakarta -> Singapore).
- [ ] [Wajib] Penguncian objek (*Object Lock / Immutability WORM*) aktif untuk melindungi data dari ransomware.

---

## 3. Prosedur Uji Simulasi Pemulihan Bencana (DR Drill) [Wajib]

> **Panduan Pengisian:** Jalankan latihan simulasi pemulihan bencana minimal satu kali setiap kuartal di lingkungan isolasi DR.

### Target Batas Pemulihan (Recovery Targets) [Wajib]:
- **Target RPO (Toleransi Kehilangan Data):** Maksimal `< 1 Jam` transaksi terakhir.
- **Target RTO (Toleransi Waktu Downtime Pemulihan):** Maksimal `< 2 Jam` hingga sistem online kembali.

### Langkah-langkah Verifikasi Pemulihan (Restoration Steps) [Wajib]:
1. **Penyediaan Infrastruktur Pengujian:** Siapkan server database uji yang terisolasi dari jaringan produksi.
2. **Pengunduhan Berkas Cadangan:** Tarik file snapshot cadangan penuh mingguan terbaru dari cloud storage sekunder.
3. **Eksekusi Pemulihan Basis Data:** Jalankan instruksi restorasi dump:
   ```bash
   pg_restore -h [dr-db-host] -p 5432 -U [db_user] -d [db_test] --clean --if-exists [backup_file.dump]
   ```
4. **Validasi Konsistensi Data:** Eksekusi kueri penghitungan integritas record tabel pengguna dan transaksi terbaru.
5. **Pencatatan Metrik Durasi:** Ukur selisih waktu mulai download hingga data selesai diverifikasi.

---

## 4. Tabel Log Simulasi Pemulihan Bencana (DR Drill Log) [Wajib]

> **Panduan Pengisian:** Catat hasil nyata dari setiap pelaksanaan simulasi pemulihan berkala.

| Tanggal Drill | Lingkup Data yang Dipulihkan | Capaian RTO Riil | Capaian RPO Riil | Hasil Akhir (Pass / Fail) | Nama Penguji |
|---|---|---|---|---|---|
| `[YYYY-MM-DD]` | Database Utama (PostgreSQL 120GB) | 48 Menit (Target: < 120m) | 15 Menit (Target: < 60m) | Pass | `[Nama SRE]` |
| `[YYYY-MM-DD]` | Object Storage Asset Pengguna (1.5TB) | 75 Menit (Target: < 120m) | 30 Menit (Target: < 60m) | Pass | `[Nama SRE]` |

---

*(Seksi cadangan pihak ketiga di bawah ini opsional: hapus jika tidak ada eksternal SaaS)*
## 5. Pencadangan Konfigurasi Pihak Ketiga (SaaS Backup) [Opsional]
- [ ] Ekspor konfigurasi aturan routing CDN Cloudflare / DNS records tersimpan dalam repositori GitOps.
- [ ] Ekspor berkala daftar pengguna dan kebijakan IAM tersimpan dalam vault cadangan terpisah.
