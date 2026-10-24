# Backup and Recovery Guide

Panduan komprehensif strategi pencadangan (*backup*), perhitungan metrik pemulihan data (RPO & RTO), verifikasi otomatis, serta simulasi pemulihan bencana (*Disaster Recovery*).

Template acuan: [Backup Recovery Checklist](../templates/delivery/backup-recovery-checklist.md)

---

## 1. Fondasi Backup: RPO dan RTO

Dalam perencanaan kontinuitas bisnis (*Business Continuity Plan*), dua metrik utama menentukan arsitektur backup yang harus dibangun:

```
                  Waktu Kejadian Bencana
                            │
◀──────── RPO ──────────────┼────────────── RTO ────────────▶
  Data yang Hilang          │        Waktu Sistem Mati
(Toleransi Kehilangan Data) │    (Waktu Pemulihan Berjalan)
```

### Recovery Point Objective (RPO)
RPO adalah batas maksimal usia data yang dapat ditoleransi hilang saat terjadi bencana.
- **Contoh Kasus:** Jika backup database dilakukan setiap hari pukul 00:00 WIB, dan server terbakar pada pukul 14:00 WIB, maka data transaksi selama 14 jam (00:00–14:00) hilang permanen.
- **RPO = 14 jam.**
- Untuk meminimalkan RPO hingga mendekati 0, gunakan Write-Ahead Logging (WAL) streaming atau Point-in-Time Recovery (PITR).

### Recovery Time Objective (RTO)
RTO adalah batas waktu maksimal yang dibutuhkan sistem untuk pulih dan kembali melayani pengguna setelah insiden terjadi.
- **Contoh Kasus:** Database crash pukul 10:00 WIB. Tim membutuhkan 30 menit mengunduh file backup 100 GB, 45 menit me-restore database engine, dan 15 menit menjalankan verifikasi DNS. Sistem aktif kembali pukul 11:30 WIB.
- **RTO = 90 menit (1.5 jam).**

---

## 2. Jenis-Jenis Metode Backup

| Tipe Backup | Cara Kerja | Waktu Backup | Waktu Restore | Biaya Storage |
|---|---|---|---|---|
| **Full Backup** | Menyalin seluruh dataset secara lengkap dari awal | Lambat | Sangat Cepat (hanya 1 file) | Tinggi |
| **Differential** | Menyalin data yang berubah sejak Full Backup terakhir | Sedang | Cepat (Full + 1 Differential) | Sedang |
| **Incremental** | Menyalin data yang berubah sejak backup terakhir (apapun tipenya) | Sangat Cepat | Paling Lambat (Full + semua chain incremental) | Paling Rendah |
| **Point-in-Time Recovery (PITR)** | Menggabungkan Base Backup + replay continuous transaction log (WAL / binlog) | Kontinu | Presisi (bisa kembali ke detik spesifik `14:23:05`) | Sedang ke Tinggi |

Untuk database transaksional produksi (PostgreSQL/MySQL), standar industri minimum adalah **Full Snapshot harian + Continuous WAL Archiving (PITR)**.

---

## 3. Aturan Cadangan 3-2-1 (The 3-2-1 Rule)

Prinsip dasar perlindungan data dari kegagalan perangkat keras, korupsi data, dan serangan *ransomware*:

```
3 Copy Data      -> Salinan 1: Database Live Produksi
                 -> Salinan 2: Local Backup / Snapshot Disk Cepat
                 -> Salinan 3: Secondary Cloud Storage

2 Media Berbeda  -> Media 1: Block Storage / SAN Server
                 -> Media 2: Object Storage (S3 / GCS) terisolasi

1 Lokasi Offsite -> Salinan disimpan di Region atau Akun Cloud Berbeda
                 (Beda Cloud Provider atau Minimal Cross-Region S3)
```

---

## 4. Otomatisasi dan Verifikasi: "Un-tested Backup is No Backup"

Backup yang gagal di-restore sama dengan tidak memiliki backup sama sekali. Kerusakan file (*silent corruption*) atau ketidakcocokan versi database engine sering kali baru terdeteksi saat bencana terjadi.

### Alur Verifikasi Otomatis Harian:
1. **Trigger Terjadwal:** Script cron atau CloudWatch Events memicu dump snapshot setiap malam.
2. **Penyimpanan Terisolasi:** File backup diunggah ke Object Storage dengan status *Immutable Object Lock* (WORM - Write Once, Read Many).
3. **Automated Restore Drill:**
   - Server uji sementara (*ephemeral runner*) berputar secara otomatis.
   - Mengunduh file backup terbaru.
   - Menjalankan restore database engine.
   - Mengeksekusi query integritas data (`SELECT COUNT(*) FROM users;`).
   - Mengirimkan laporan status "Backup Validated" ke Slack tim DevOps.
   - Menghancurkan server uji untuk menghemat biaya.

---

## 5. Simulasi Pemulihan Bencana (Disaster Recovery Drills)

Jangan menunggu server produksi mati untuk melatih tim. Jadwalkan gladi resik bencana secara berkala:

- **Cadence Minimum:**
  - **Uji Coba Otomatis Restore:** Dijalankan setiap minggu atau setiap malam.
  - **Gladi Resik Manual (DR Drill):** Dijalankan minimal **1 kali per semester (6 bulan)**.
- **Skenario Simulasi DR:**
  1. *Primary Region Cloud Down:* Mengarahkan DNS dan memulihkan database dari snapshot cross-region.
  2. *Ransomware / Malicious DROP TABLE:* Memulihkan database ke detik sebelum perintah DROP dieksekusi via PITR.
  3. *Korupsi Harddisk Server:* Me-restore data ke instance virtual machine yang baru dibuat dari awal via IaC.

---

## 6. Enkripsi dan Keamanan Backup

File backup database memuat seluruh data rahasia perusahaan dalam satu berkas, menjadikannya target utama peretasan.

- **Encryption in Transit:** Wajib menggunakan protokol TLS 1.3 saat mengunggah berkas backup antar-server atau ke cloud storage.
- **Encryption at Rest:**
  - Gunakan kunci enkripsi berkekuatan AES-256 (KMS-managed keys).
  - Pisahkan hak akses KMS key dari user aplikasi biasa.
- **Prinsip Immutability:** Aktifkan fitur *Object Lock* / *Retention Policy* pada bucket S3 (misal: kunci penghapusan selama 30 hari) agar peretas tidak dapat menghapus backup saat akun terkompromi.

---

## 7. Kesalahan Umum (Common Mistakes)

1. **Menyimpan Backup di Server yang Sama:** Menaruh file dump database di partisi `/var/backups` pada disk server yang sama dengan database live. Ketika disk terbakar, aplikasi dan backup musnah bersamaan.
2. **Tidak Pernah Mencoba Restore:** Mengira backup sukses hanya karena ukuran file `.sql.gz` bertambah, padahal file tersebut korup atau kosong.
3. **Mengabaikan Backup Konfigurasi dan Secrets:** Mem-backup isi database tetapi lupa mencadangkan environment variable, sertifikat TLS, dan kunci enkripsi aplikasi.
4. **Kunci Enkripsi Disimpan Bersama Backup:** Menaruh file private key enkripsi di folder yang sama dengan file backup terenkripsi.
5. **Akses Bucket Backup Terbuka:** Memberikan permission `DeleteObject` pada kredensial CI/CD yang digunakan untuk mengunggah backup.

---

## 8. Dokumen Terkait

- [Backup Recovery Checklist](../templates/delivery/backup-recovery-checklist.md)
- [Database Migration and Rollback](database-migration-and-rollback.md)
- [Incident Response Guide](incident-response.md)
- [Environment Strategy](environment-strategy.md)
- [Operations Runbook Template](../templates/delivery/operations-runbook-template.md)
