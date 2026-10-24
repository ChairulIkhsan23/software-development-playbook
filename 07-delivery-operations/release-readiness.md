# Release Readiness Review (Gate 5)

Panduan tata kelola peninjauan kesiapan rilis produksi (*Release Readiness Review / Gate 5*), verifikasi 6 dimensi kesiapan, prosedur pertemuan Go/No-Go, dan protokol pembatalan (*release abort*).

Checklist detail acuan: [Release Readiness Checklist](../checklists/release-readiness.md)

---

## 1. Definisi dan Tujuan Gate 5

Release Readiness Review (Gate 5) adalah gerbang kendali mutu (*quality gate*) formal terakhir sebelum sebuah versi perangkat lunak diluncurkan ke lingkungan produksi langsung.

Tujuan utama Gate 5 bukan untuk memperlambat laju rilis tim (*velocity*), melainkan untuk:
- Memastikan tidak ada risiko tersembunyi yang dapat melumpuhkan layanan produksi.
- Menyelaraskan kesiapan aspek teknis, data, operasional, dan tim pendukung bisnis.
- Menjamin tersedianya jalur darurat (*rollback plan*) yang terdefinisi dengan jelas apabila terjadi kegagalan.

```
[Sprint Dev] -> [CI Pipeline] -> [Staging UAT] -> [Gate 5 Review] -> [Deploy Prod]
                                                         │
                                               (Go / No-Go Decision)
```

---

## 2. Checklist Kesiapan Pra-Rilis: 6 Dimensi Kritis

Sebelum mengajukan pertemuan keputusan rilis, Product Owner dan Tech Lead wajib memverifikasi item berikut:

### Dimensi 1: Kualitas (Quality)
- [ ] Seluruh kriteria *Definition of Done* (DoD) pada setiap ticket user story telah terpenuhi.
- [ ] Pengujian fungsional dan *Acceptance Testing* telah disetujui tertulis oleh Product Owner.
- [ ] Status *Open Defects*: **0 Blocker**, **0 Critical**, dan tidak ada regresi fitur yang belum termitigasi.
- [ ] Seluruh automated test (unit, integration, E2E) berstatus hijau pada pipeline CI.

### Dimensi 2: Operasional (Operational)
- [ ] Dashboard monitoring APM telah disiapkan dan mampu menampilkan metrik RED untuk fitur baru.
- [ ] Ambang batas alert telah dikonfigurasi dan rute paging on-call telah diverifikasi aktif.
- [ ] Dokumen Standard Operating Procedure (*runbook*) telah diperbarui dengan skenario penanganan darurat.
- [ ] Personel *On-Call Engineer* telah ditunjuk dan siap siaga selama jadwal rilis.

### Dimensi 3: Keamanan (Security)
- [ ] Pemindaian statis (SAST) dan dependency audit (`npm audit`, Trivy) berstatus bersih dari kerentanan High/Critical.
- [ ] Tidak ada hardcoded credentials atau API key yang tertinggal di dalam commit git.
- [ ] Izin akses (*authorization rules*) dan endpoint API baru telah melewati review kontrol akses (RBAC).

### Dimensi 4: Data dan Skema (Data & Migration)
- [ ] Skrip migrasi database telah lolos uji coba di database staging bervolume setara produksi.
- [ ] DDL skema mematuhi aturan backward compatibility (kolom baru bernilai nullable atau default).
- [ ] Snapshot backup database terbaru telah divalidasi dan siap digunakan untuk *point-in-time recovery*.
- [ ] Perubahan query SQL berat telah melewati analisis performa index (`EXPLAIN ANALYZE`).

### Dimensi 5: Bisnis dan Dukungan (Business & Support)
- [ ] Dokumen rilis (*release notes*) dan changelog pengguna akhir telah difinalisasi.
- [ ] Tim Customer Support dan Operasional Bisnis telah mendapatkan pengarahan terkait perubahan fitur.
- [ ] Dokumentasi panduan pengguna (*user guide / help center*) telah diperbarui.

### Dimensi 6: Rencana Pembatalan (Rollback Plan)
- [ ] Skenario pembatalan (*rollback step-by-step*) telah didokumentasikan dan diuji di staging.
- [ ] Kriteria pemicu pembatalan (*abort criteria*) telah disepakati secara objektif (misal: error rate > 1% selama 5 menit).
- [ ] Image container versi sebelumnya (*previous stable release*) tersedia dan siap dideploy seketika.

---

## 3. Matriks Keputusan Go / No-Go

Gunakan panduan matriks evaluasi berikut untuk menentukan keputusan akhir:

| Kondisi Temuan | Status Keputusan | Tindakan yang Harus Diambil |
|---|---|---|
| Semua 6 dimensi terpenuhi 100% | **GO** | Lanjutkan deployment produksi sesuai jadwal |
| 1 Blocker / Critical Bug terbuka | **NO-GO** | Batalkan rilis; jadwalkan sprint perbaikan |
| PII terdeteksi di log atau celah auth | **NO-GO** | Batalkan rilis; eskalasi ke tim security |
| Backup snapshot belum selesai/gagal | **NO-GO** | Tunda rilis hingga backup tervalidasi sukses |
| Defect minor visual (typo teks non-kritis) | **CONDITIONAL GO** | Lanjutkan rilis jika disetujui PO; buat ticket perbaikan |

---

## 4. Prosedur Pertemuan Go / No-Go Decision

Pertemuan Go/No-Go harus berlangsung singkat (`10–15 menit`) dan fokus pada pembacaan fakta objektif:

### Peserta Wajib:
- **Product Owner (PO):** Menilai kesiapan bisnis dan persetujuan fitur.
- **Tech Lead / Engineering Manager:** Menilai integritas arsitektur dan stabilitas kode.
- **QA Lead / SDET:** Menilai hasil pengujian dan laporan defect.
- **DevOps / On-Call Lead:** Menilai kesiapan infrastruktur dan jalur rollback.

### Aturan Keputusan:
- **GO:** Diputuskan hanya jika **semua representasi peran memberikan suara setuju (konsensus mutlak)** terhadap 6 dimensi kesiapan.
- **NO-GO:** Jika satu peran menemukan kriteria kritis tidak terpenuhi (misal: terdapat bug kritis atau script backup gagal), rilis otomatis dibatalkan atau ditunda.

---

## 5. Protokol Penanganan Pembatalan Rilis (Release Abort)

Jika keputusan akhir adalah NO-GO atau terjadi kegagalan saat proses deployment di produksi berlangsung:

1. **Deklarasikan Abort Secara Tegas:** Pimpinan rilis mengumumkan pembatalan di war room (`"Release v1.4.0 ABORTED at 21:15 WIB"`).
2. **Eksekusi Rollback Instan:** Alihkan traffic kembali ke armada pod/container versi stabil lama atau nonaktifkan feature toggle.
3. **Konfirmasi Stabilitas:** Pastikan error rate kembali ke 0% dan metrik latensi normal.
4. **Komunikasi Stakeholder:** Informasikan penundaan rilis ke channel internal (`#general-announcements`, CS, Marketing) dalam 15 menit.
5. **Catat Alasan Penolakan:** Dokumentasikan parameter yang memicu pembatalan (misal: deadlock pada tabel pembayaran) dalam post-mortem log.
6. **Jadwalkan Ulang:** Masalah diperbaiki di branch dev dan siklus pengujian diulang dari Gate 1; jangan pernah memaksakan hotfix langsung di produksi tanpa pengujian ulang.

---

## 6. Kesalahan Umum (Common Mistakes)

1. **Review Hanya Formalitas di Atas Kertas:** Menyetujui rilis dengan asumsi "nanti bug-nya diperbaiki di produksi" (*fix-in-prod mentality*).
2. **Tidak Menentukan Kriteria Abort Sebelumnya:** Tim berdebat selama 40 menit saat rilis error karena tidak ada kesepakatan tertulis kapan rilis harus di-rollback.
3. **Customer Support Tidak Diberitahu:** Mengirim fitur baru yang mengubah antarmuka aplikasi secara drastis tanpa melatih agen CS yang menerima komplain pengguna.
4. **Mengabaikan Migrasi Database Saat Rollback:** Kode aplikasi berhasil di-rollback tetapi database terlanjur dirusak oleh DDL destruktif yang tidak kompatibel.
5. **Rilis Dipaksakan Karena Tekanan Deadline:** Melanggar kriteria checklist Gate 5 demi mengejar target tanggal rilis marketing.

---

## 7. Dokumen Terkait

- [Release Readiness Checklist (Checklists)](../checklists/release-readiness.md)
- [Release Management](release-management.md)
- [Deployment Guide](deployment-guide.md)
- [Operations Runbook Template](../templates/delivery/operations-runbook-template.md)
- [Incident Response Guide](incident-response.md)
- [Database Migration and Rollback](database-migration-and-rollback.md)
