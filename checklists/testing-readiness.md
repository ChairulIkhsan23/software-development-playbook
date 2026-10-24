# Testing Readiness Checklist

Checklist verifikasi kesiapan sebelum siklus pengujian formal (QA / Test Cycle) dimulai. Memastikan lingkungan uji, data simulasi, aset pengujian, dan manajemen defek siap pakai agar QA tidak terhambat masalah infrastruktur.

---

## 1. Test Environment Readiness
- [ ] **Dedicated Staging Deployed**: Lingkungan Staging terisolasi telah dideploy dengan build versi terbaru (commit hash diverifikasi).
- [ ] **Environment Parity Verified**: Versi database engine, runtime Node.js/Python, dan konfigurasi OS di staging identik dengan production.
- [ ] **Stable Network & SSL**: Domain staging aktif dengan sertifikat HTTPS valid tanpa peringatan browser SSL/TLS.
- [ ] **APM & Log Monitoring Active**: Logging terpusat (Datadog/Sentry/Grafana) aktif dan mampu menangkap error runtime real-time.
- [ ] **Feature Flags Configured**: Flag fitur yang relevan dengan scope pengujian telah diaktifkan di panel konfigurasi staging.
- [ ] **Zero Deployment Blockers**: Tidak ada job CI/CD deployment yang berstatus failed atau hanging.
- [ ] **Third-Party Sandboxes Active**: Integrasi gateway pembayaran sandbox, SMS OTP mock, dan email trap (Mailtrap) terhubung aktif.
- [ ] **Isolated Email & Webhook Trap**: Email staging tidak terkirim ke pelanggan nyata; webhook diarahkan ke listener simulasi.

---

## 2. Test Data Readiness
- [ ] **Synthetic Data Seeded**: Data uji sintetis mencakup katalog produk, akun pengguna multi-role, dan riwayat transaksi sudah ter-seed.
- [ ] **Edge Case Data Prepared**: Dataset batas disiapkan (karakter unicode ekstrim, string panjang, nilai desimal presisi, file attachment 0KB & batas maksimum).
- [ ] **PII Masked & Sanitized**: Data salinan dari produksi wajib disanitasi total; nomor telepon, email nyata, dan NIK disamarkan via Faker / script anonymizer.
- [ ] **Isolated Test Accounts**: Kredensial akun uji (Admin, Regular User, Guest, Suspended) tercatat rapi dan siap digunakan tim QA.
- [ ] **Database State Reset Script**: Tersedia skrip otomatis untuk me-reset data staging kembali ke kondisi awal (*fresh seed*) dalam hitungan menit.
- [ ] **Data Cleanup Routine**: Prosedur pembersihan data sementara pasca-pengujian disepakati agar database tidak kehabisan storage.

---

## 3. Test Assets Readiness
- [ ] **Test Cases Documented & Reviewed**: Seluruh test scenario fungsional dan non-fungsional ditulis di test management tool dan disetujui Product Owner.
- [ ] **Traceability to Acceptance Criteria**: Setiap skenario uji memiliki tautan langsung ke Acceptance Criteria pada tiket user story.
- [ ] **Automated Test Suites Runnable**: Suite automated test (E2E Playwright/Cypress dan API Postman/Newman) dapat dieksekusi di CI/lokal tanpa flakiness.
- [ ] **Test Tools & Proxies Configured**: Alat bantu inspeksi jaringan (Postman, Charles Proxy, Browser DevTools) telah disiapkan.
- [ ] **Cross-Device Testing Matrix**: Matriks browser target (Chrome, Safari, Firefox) dan viewport mobile (iOS Safari, Android Chrome) ditetapkan.
- [ ] **Exploratory Test Charters Ready**: Panduan pengujian eksploratori bebas disiapkan untuk area rawan cacat.

---

## 4. Defect Management Setup
- [ ] **Bug Tracking System Configured**: Proyek Jira/GitHub Issues siap dengan template laporan bug standar (Steps to Reproduce, Expected vs Actual, Evidence).
- [ ] **Severity & Priority Matrix Defined**: Standar klasifikasi tingkat keparahan bug (Blocker, Critical, Major, Minor) dipahami seragam oleh dev dan QA.
- [ ] **Bug Triage Schedule Agreed**: Jadwal pertemuan harian *Bug Triage* (15 menit antara QA, Dev Lead, dan PM) ditentukan.
- [ ] **Bug SLA Communicated**: Waktu tanggap perbaikan bug disepakati (Blocker: < 4 jam, Critical: < 24 jam).
- [ ] **Labeling & Tagging Policy**: Tag label rilis (`release-1.2.0`), komponen backend/frontend, dan tipe bug terkonfigurasi.

---

## 5. Team Communication & Access
- [ ] **Access Credentials Distributed**: Seluruh anggota QA memiliki akun login aktif untuk staging, panel admin, dan database read-only.
- [ ] **Communication Channel Live**: Kanal obrolan darurat (#qa-testing-cycle) dibuat di Slack/Discord untuk pelaporan blocker instan.
- [ ] **Developer On-Call Assigned**: 1 developer ditugaskan siaga merespons isu staging dan memverifikasi bug triage.
- [ ] **Testing Window Scheduled**: Batas waktu mulai dan berakhirnya pengujian disepakati bersama oleh tim produk dan teknis.
- [ ] **Daily QA Standup Set**: Sesi sinkronisasi singkat status pengujian terjadwal setiap pagi selama test cycle.

---

## Kriteria Blocker Siklus Pengujian (Entry Gate Blockers)
Pengujian formal dilarang dimulai jika salah satu kondisi blocker berikut aktif:

| Kondisi Masalah | Kategori | Alasan Larangan |
|---|---|---|
| **Staging Crash / 500 Error di Halaman Login** | Environment | QA tidak dapat mengakses aplikasi untuk menguji alur kerja lain. |
| **Data Uji PII Nyata Belum Disanitasi** | Security / Legal | Pelanggaran hukum perlindungan data pribadi jika diakses tester. |
| **Test Case Belum Disetujui PM** | Process | Risiko pengujian mengacu pada asumsi usang atau fitur yang batal. |
| **Skrip Reset Data Tidak Tersedia** | Data | Pengujian regresi macet karena data kotor setelah siklus uji pertama. |

---

## Panduan Tindakan Remediasi (Blocker Remediation)
- **Lingkungan Staging Sering Crash**:
  Hentikan pengujian formal. Developer on-call wajib rollback ke build stabil terakhir atau fokus 100% pada stability patch sebelum QA lanjut.
- **Data Uji Gagal Ter-seed**:
  Jalankan skrip migrasi dan factory seed lokal di staging: `npm run db:reset && npm run db:seed:test`. Verifikasi ke database GUI.
- **Ketidakcocokan Versi Staging vs Production**:
  Audit variabel lingkungan dan Dockerfile image tag. Samakan image hash antara staging dan release candidate.
- **Bug Tracker Belum Dikonfigurasi**:
  Gunakan template bug report minimalis di GitHub Issues berbasis Markdown:
  *Langkah Reproduksi*, *Hasil Sebenarnya*, *Hasil yang Diharapkan*, dan *Tangkapan Layar/Log Console*.
- **Mailtrap / Payment Sandbox Down**:
  Gunakan mock internal atau feature flag offline mocking sementara untuk melanjutkan pengujian modul lain.
- **Flaky E2E Automated Tests**:
  Karantina tes yang tidak stabil ke file terpisah (`*.flaky.ts`) agar tidak memblokir pipeline utama, lalu perbaiki selector atau wait condition.