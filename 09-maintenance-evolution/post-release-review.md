# Post-Release Review & Retrospective

Panduan pelaksanaan retrospektif pasca peluncuran (*post-release review*) untuk mengevaluasi dampak rilis, stabilitas teknis, dan efisiensi delivery tim engineering.

---

## 1. Tujuan Post-Release Review

Berbeda dengan sprint retrospective rutin yang berfokus pada dinamika kerja dua mingguan, **Post-Release Review** diadakan **2 hingga 4 minggu setelah rilis major** ke production. 

Jeda waktu 2-4 minggu memberikan ruang yang cukup agar data adopsi riil terkumpul, bug tersembunyi muncul ke permukaan, dan sistem melewati siklus beban kerja normal (*steady state*).

Tujuan utama:
1. **Evaluasi Dampak Nyata vs Ekspektasi:** Membandingkan target Product Requirement Document (PRD) dengan metrik telemetri aktual.
2. **Pembelajaran Proses Eksekusi:** Menemukan bottleneck selama fase perancangan, development, testing, hingga deployment.
3. **Mengidentifikasi Debt & Stabilisasi:** Menjaring bug regresi dan technical debt yang tercipta saat mengejar tanggal rilis.
4. **Merayakan Kemenangan (Celebrate Wins):** Mengapresiasi kontribusi tim dan pencapaian milestone penting.

---

## 2. Empat Pilar Penilaian Pasca Rilis

```
+------------------------------------------------------------------------+
| 1. Feature Adoption & Metrics   : Real vs Target PRD, Drop-off rate     |
| 2. Operational Stability        : MTTR, Incident P1-P3, Error logs     |
| 3. Delivery & Process Review    : Estimation error, Bottlenecks, Morale |
| 4. User Feedback & Satisfaction : CSAT/NPS, Support tickets, Qualitative|
+------------------------------------------------------------------------+
```

### A. Adopsi Fitur dan Metrik Produk (vs PRD Goals)
- **Target vs Realisasi:** Jika PRD menargetkan 25% active users menggunakan fitur baru dalam 30 hari pertama, berapa angka riil di dashboard analitik?
- **Funnel Drop-off:** Di langkah mana pengguna berhenti saat mencoba fitur baru? Apakah ada friksi UX yang menghambat penyelesaian transaksi?

### B. Stabilitas Operasional (Operational Stability)
- **Volume Bug & Regresi:** Berapa banyak tiket bug yang dilaporkan pasca-rilis? Apakah ada fitur lama yang rusak akibat deployment baru ini?
- **Insiden Produksi (P1 / P2 / P3):** Apakah terjadi downtime, lonjakan latency p99, atau kegagalan transaksi berbayar?
- **Pemanfaatan Kapasitas:** Apakah server, database connection pool, atau memori cache menunjukkan gejala kelebihan beban (*overloading*)?

### C. Evaluasi Proses Delivery (Process Review)
- **Akurasi Estimasi:** Apakah durasi delivery melenceng jauh dari perkiraan awal? Jika ya, apakah karena *scope creep*, arsitektur yang tidak siap, atau kendala dependensi eksternal?
- **Bottlenecks:** Di mana tiket tertahan paling lama? (Contoh: Menunggu spec product, menunggu review PR, atau tahap manual QA testing).
- **Sentimen & Morale Tim:** Apakah tim mengalami *burnout*, jam kerja lembur berlebihan (*crunch time*), atau sebaliknya merasa bangga dan puas dengan hasilnya?

### D. Umpan Balik Pengguna (Customer Satisfaction)
- **Support Ticket Sentiment:** Kategorisasi tiket yang masuk ke tim Customer Support terkait fitur baru (apakah berupa *bug report*, *usability confusion*, atau *feature request* baru).
- **Skor Kepuasan:** Skor CSAT atau NPS sesaat setelah pengguna menyelesaikan alur fitur baru.

---

## 3. Format dan Panduan Fasilitasi Sesi Review

Sesi idealnya berdurasi **60-90 menit** dan dihadiri oleh seluruh pemangku kepentingan (Engineers, Product Manager, Designer, QA, dan DevOps/SRE).

### Agenda Sesi (90 Menit)
1. **00-10 Menit: Pembukaan & Paparan Angka Riil (PM/Lead)**  
   Mempresentasikan metrik adopsi PRD vs data analitik aktual, jumlah insiden, dan volume tiket support.
2. **10-35 Menit: Pengumpulan Masukan (Silent Brainstorming)**  
   Peserta menulis kartu evaluasi di papan kolaboratif (Miro, FigJam, atau GitHub Discussions) pada 3 kolom:
   - *What went well* (Apa yang sukses dan layak dipertahankan)
   - *What could be improved* (Apa yang meleset, membuat stres, atau lambat)
   - *Surprises & learnings* (Hal mengejutkan yang baru disadari setelah rilis)
3. **35-65 Menit: Diskusi & Root Cause Grouping**  
   Kelompokkan tema yang berulang dan diskusikan akar masalah secara objektif tanpa menyalahkan individu (*blameless culture*).
4. **65-90 Menit: Penetapan Action Items & Backlog Feeding**  
   Setiap masalah kritis harus memiliki satu PIC (*Person in Charge*) dan tanggal target penyelesaian.

### Penyesuaian untuk Solo Developer & Tim Kecil (30 Menit)
Bagi solo developer atau tim 2-3 orang:
- Lewati seremoni formal. Buat catatan 1 halaman di markdown repository.
- Bandingkan checklist PRD dengan log Sentry dan metrik Google Analytics/PostHog.
- Catat 3 pembelajaran terbesar dan ubah langsung menjadi tiket GitHub Issues.

---

## 4. Konversi Action Items Menjadi Backlog (Backlog Feeding)

Hasil evaluasi tidak boleh berhenti sebagai artefak pasif. Konversikan temuan menjadi tiket nyata dengan matriks dampak-usaha (*Impact vs Effort Matrix*):

| Tingkat Dampak | Rendah (Low Effort) | Tinggi (High Effort) |
|---|---|---|
| **High Impact** | **Quick Wins:** Kerjakan di sprint berikutnya (misal: perbaiki teks tombol yang membingungkan). | **Major Projects:** Jadwalkan di roadmap kuartal mendatang (misal: re-arsitektur query reporting). |
| **Low Impact** | **Fill-ins:** Kerjakan saat waktu luang atau boy-scout rule. | **Time Sinks / Deprecate:** Jangan dikerjakan; hindari membuang resource. |

---

## 5. Template Dokumentasi Post-Release Review

```markdown
# Post-Release Review: [Nama Rilis / Fitur]
- **Tanggal Sesi:** YYYY-MM-DD
- **Fasilitator:** [Nama] | **Notulis:** [Nama]
- **Peserta:** [Daftar Hadir Tim]

## 1. Ringkasan Metrik
- Target PRD: 10.000 user aktif bulanan | Realisasi: 7.200 (72% tercapai)
- Bug Lolos ke Prod: 2 Critical (terselesaikan dalam 4 jam), 5 Minor
- Uptime Rilis: 99.95%

## 2. Apa yang Berhasil dengan Baik
- Automated end-to-end test di CI pipeline berhasil mencegah breaking changes API.
- Kolaborasi harian dev dan QA berhasil memotong siklus verifikasi staging.

## 3. Area Perbaikan
- Skema database lambat merespons karena lupa index pada foreign key baru.
- Copywriting onboarding membingungkan pengguna baru (lonjakan tiket bantuan).

## 4. Tindakan Lanjut (Action Items)
| Tindakan | Kategori | PIC | Target Selesai |
|---|---|---|---|
| Tambahkan compound index pada tabel `order_items` | Performance | Budi | Sprint 24 |
| Perbaiki copywriting microcopy panduan onboarding | UX/Copy | Siti | 3 Hari |
| Buat checklist validasi schema review sebelum migration | Process | Joko | Sprint 25 |
```

---

## 6. Kesalahan Umum (Common Mistakes)

1. **Retrospektif Mencari Kambing Hitam (*Blame Game*):** Sesi berubah menjadi arena tuding-menuding kesalahan individu, bukan menganalisis kelemahan sistem dan proses kerja.
2. **Dilakukan Terlalu Cepat (Hari H Rilis):** Mengadakan review 2 hari setelah rilis, di mana metrik bisnis belum terkumpul dan bug edge case belum ditemukan oleh user.
3. **Action Items Menguap:** Menghasilkan catatan bagus di dokumen tanpa pernah memasukkannya ke sprint backlog berikutnya.
4. **Hanya Membahas Masalah Teknis:** Mengabaikan data bisnis dan feedback pengguna, sehingga engineering tidak memahami nilai dari apa yang mereka bangun.

---

## 7. Dokumen Terkait

- [Product Metrics Review](product-metrics-review.md) — Analisis metrik adopsi dan funnel produk secara mendalam.
- [Performance Review](performance-review.md) — Meninjau penurunan performa yang terdeteksi pasca deployment.
- [Technical Debt](technical-debt.md) — Menjadwalkan pelunasan hutang teknis yang teridentifikasi selama sesi review.
