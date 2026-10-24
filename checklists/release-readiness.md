# Release Readiness Checklist (Gate 5)

Checklist gerbang kelayakan rilis final (Gate 5) sebelum penerapan (*production deployment*). Memastikan stabilitas sistem, proteksi data, kesiapan operasional, dan mitigasi risiko downtime.

---

## 1. Quality Gates
- [ ] **Definition of Done (DoD) Met**: 100% tiket fitur dalam bundel rilis memenuhi seluruh kriteria DoD.
- [ ] **User Acceptance Testing (UAT) Sign-off**: Hasil pengujian UAT disahkan secara tertulis oleh Product Manager dan Business Sponsor.
- [ ] **Zero Open SEV-1 / SEV-2 Bugs**: Nol bug terbuka dengan tingkat keparahan Critical (SEV-1) maupun Major (SEV-2).
- [ ] **Automated Regression Suite Green**: Seluruh unit, integration, dan E2E regression tests berstatus lolos 100% di pipeline CI/CD.
- [ ] **Manual Smoke Test Passed on Staging**: Verifikasi alur utama sukses dijalankan manual pada build release candidate (RC).
- [ ] **Performance Baseline Maintained**: Latensi respons API (p95) dan konsumsi memori tidak mengalami regresi dibanding versi rilis sebelumnya.

---

## 2. Operational Readiness
- [ ] **Monitoring & APM Dashboards Active**: Dasbor metrik sistem (CPU, RAM, latency, RPS, HTTP 5xx error rate) aktif dan terpantau.
- [ ] **Alerting Thresholds & Channels Verified**: Integrasi notifikasi pager/Slack (Sentry, Datadog, Prometheus) teruji aktif dan tidak hening.
- [ ] **Standard Operations Runbook Updated**: Panduan operasional layanan, SOP restart service, dan troubleshooting langkah demi langkah mutakhir.
- [ ] **On-Call Engineer Assigned**: Jadwal engineer siaga (*primary & secondary on-call*) disepakati untuk periode rilis dan 24 jam pasca-deploy.
- [ ] **Deep Health Check Endpoints Verified**: Endpoint `/healthz` dan `/readyz` memvalidasi koneksi database, Redis cache, dan antrean pesan secara akurat.
- [ ] **Log Retention & Rotation Configured**: Kapasitas disk server memadai dan log agregator siap menerima lonjakan volume pasca-rilis.

---

## 3. Deployment & Data
- [ ] **Staging Migration Rehearsal Successful**: Skrip migrasi database telah diuji eksekusi pada salinan data riil di staging tanpa data loss.
- [ ] **Rollback Script Tested**: Prosedur dan skrip rollback database (*down migration*) teruji sukses memulihkan skema ke versi awal.
- [ ] **Production Backup Snapshot Verified**: Snapshot backup database dan filesystem produksi terbaru selesai dibuat dan diverifikasi integritasnya.
- [ ] **Deployment Window & Schedule Agreed**: Waktu deployment dijadwalkan pada jam trafik terendah (*low-traffic maintenance window*).
- [ ] **Canary / Blue-Green Strategy Ready**: Strategi rilis bertahap (*canary deployment* atau alokasi trafik 10% -> 50% -> 100%) disiapkan.
- [ ] **Zero-Downtime Migration Pattern**: Penambahan kolom/tabel baru bersifat *backward-compatible* dengan kode versi lawas.

---

## 4. Security & Compliance
- [ ] **Security Vulnerability Scan Clean**: Pemindaian SAST (SonarQube) dan DAST bersih dari temuan severity Critical dan High.
- [ ] **Dependency Audit Passed**: Audit paket dependensi (`npm audit` / Snyk) bebas dari CVE aktif yang dapat dieksploitasi.
- [ ] **Secret & Access Hygiene**: Kredensial produksi tidak tercampur di staging; akses SSH server produksi dibatasi hanya untuk personel berwenang.
- [ ] **Regulatory & PDP Compliance Confirmed**: Perubahan fitur tidak melanggar ketentuan pemrosesan data pribadi UU PDP atau standar kepatuhan industri.

---

## 5. Business & Communication
- [ ] **Release Notes Published**: Catatan rilis internal dan eksternal selesai disusun, memuat ringkasan fitur baru, perbaikan bug, dan *breaking changes*.
- [ ] **Customer Support Team Briefed**: Tim Customer Support dan Operasional telah menerima pelatihan dan dokumen FAQ alur baru.
- [ ] **Status Page Prepared**: Templat pesan pemeliharaan atau insiden disiapkan di Statuspage untuk antisipasi gangguan.
- [ ] **Executive & Marketing Notification**: Stakeholder pimpinan dan tim pemasaran mendapatkan konfirmasi jadwal aktivasi fitur.
- [ ] **In-App Notification Scheduled**: Pengumuman pembaruan sistem dijadwalkan tampil di antarmuka pengguna setelah rilis stabil.

---

## 6. Post-Deployment Smoke Verification (T+0 s.d T+30 Menit)
- [ ] **Health Endpoint 200 OK**: Endpoint `/healthz` mengembalikan status sehat seketika setelah kontainer baru aktif.
- [ ] **Critical User Flow Verified**: Login pengguna, pembuatan data baru, dan alur pembayaran live diverifikasi menggunakan akun internal.
- [ ] **Error Log Inspection**: Log error (Sentry/Datadog) dipantau; tidak ada lonjakan unhandled exception baru.
- [ ] **CDN Cache Purged**: Cache aset statis (JS bundle, CSS) di-purge agar pengguna langsung menerima versi web terbaru.

---

## Protokol Keputusan Go / No-Go (Decision Protocol)
Pertemuan evaluasi Go/No-Go diselenggarakan 1-2 jam sebelum jendela rilis:

| Peran | Pihak Bertanggung Jawab | Otoritas Keputusan |
|---|---|---|
| **Release Lead** | Tech Lead / Engineering Manager | Memimpin sidang rilis dan memverifikasi kelayakan teknis. |
| **Product Sign-off** | Product Owner / Head of Product | Mengonfirmasi kesiapan nilai bisnis dan dokumen rilis. |
| **QA Sign-off** | QA Lead / Senior Tester | Memastikan seluruh kriteria kualitas dan zero-bug terpenuhi. |
| **Ops Sign-off** | DevOps / SRE / Lead Infra | Mengonfirmasi stabilitas backup, infra, dan prosedur rollback. |

### Matriks Keputusan Final
- **GO**: 100% checklist tercentang. Deployment dijalankan sesuai instruksi runbook.
- **ABORT / NO-GO**: Ditemukan 1 bug SEV-1, backup database gagal dibuat, atau skrip rollback belum teruji. Rilis dibatalkan seketika.
- **ROLLBACK TRIGGER**: Jika pasca-deploy error rate HTTP 5xx > 1% selama 5 menit beruntun atau latensi p95 melonjak 2x lipat, picu rollback instan tanpa kompromi.

---

## Prosedur Eksekusi Rollback Darurat
Jika terpicu rollback pasca-deployment:
1. **Alihkan Trafik Jaringan**: Arahkan load balancer kembali ke target grup / container versi lama (*blue/previous stable*).
2. **Rollback Skema Database**: Eksekusi skrip down-migration jika perubahan skema bersifat destruktif.
3. **Konfirmasi Stabilitas**: Verifikasi status `/healthz` dan pastikan error rate kembali normal < 0.1%.
4. **Komunikasi Insiden**: Perbarui pesan di Statuspage dan kabari tim Customer Support terkait pembatalan rilis.