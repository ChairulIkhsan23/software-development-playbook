# Risk Register

Panduan identifikasi, penilaian, mitigasi, dan pemantauan risiko teknis maupun operasional dalam pengembangan perangkat lunak.

---

## 1. Siklus Hidup Manajemen Risiko (Risk Lifecycle)

Manajemen risiko bukan sesi dokumentasi formalitas satu kali di awal proyek. Ini adalah loop berkelanjutan sepanjang siklus hidup software:

```
[1. Identifikasi] ──► [2. Penilaian (P x I)] ──► [3. Rencana Respon] ──► [4. Monitoring & Review]
        ▲                                                                        │
        └────────────────────────── Evaluasi Berkala ────────────────────────────┘
```

1. **Identification**: Petakan potensi kegagalan teknis, dependensi pihak ketiga, kendala sumber daya, dan ketidakpastian ruang lingkup sebelum menjadi insiden live.
2. **Assessment**: Evaluasi probabilitas kejadian dan dampak keparahan untuk mendapatkan skor numerik objektif.
3. **Response Planning**: Susun aksi mitigasi preventif dan siapkan rencana darurat (contingency plan) jika risiko benar-benar terjadi.
4. **Monitoring & Review**: Tinjau status risiko secara berkala pada setiap sprint planning atau retro tim.

---

## 2. Struktur Standar Risk Register

Setiap item risiko dalam register wajib memiliki atribut-atribut kunci berikut:

| Kolom | Definisi & Standar Input |
|---|---|
| **Risk ID** | Identifier unik berurutan (misal: `RSK-01`, `RSK-02`). |
| **Category** | Kategori sumber: `Technical`, `Scope`, `Schedule`, `Resource`, atau `External`. |
| **Description** | Format sebab-akibat: *"Jika [kejadian terjadi], maka [dampak buruk muncul]"*. |
| **Probability (P)** | Skala kemungkinan terjadi: `1` (Sangat Jarang) hingga `5` (Hampir Pasti). |
| **Impact (I)** | Skala keparahan dampak: `1` (Dapat Diabaikan) hingga `5` (Bencana / Blocker Rilis). |
| **Risk Score** | Hasil kali matematis: **Probability x Impact** (Nilai rentang: `1` hingga `25`). |
| **Response Strategy** | Pilihan strategi: `Avoid`, `Mitigate`, `Transfer`, atau `Accept`. |
| **Action Plan** | Langkah konkret terukur untuk menurunkan probabilitas sebelum kejadian. |
| **Contingency Plan** | Prosedur penanganan fallback darurat jika risiko benar-benar terpicu. |
| **Risk Owner** | Satu nama individu yang bertanggung jawab memantau dan mengeksekusi mitigasi. |
| **Trigger Sign** | Tanda peringatan dini (early warning sign) sebelum risiko meledak. |
| **Status** | Kondisi terkini: `Identified`, `Mitigating`, `Materialized`, `Closed`. |

---

## 3. Rubrik Penilaian Probabilitas & Dampak (Skala 1-5)

Jangan menebak angka tanpa rubrik objektif:

### Skala Probabilitas (P)
- **1 (Sangat Jarang)**: Kemungkinan < 5% terjadi selama siklus proyek.
- **2 (Rendah)**: Kemungkinan 5% - 20% (pernah terjadi sekali di proyek lain).
- **3 (Sedang)**: Kemungkinan 21% - 50% (50/50 kemungkinan terjadi).
- **4 (Tinggi)**: Kemungkinan 51% - 80% (sangat sering terjadi jika tidak diintervensi).
- **5 (Hampir Pasti)**: Kemungkinan > 80% (pasti terjadi kecuali dihindari).

### Skala Dampak (I)
- **1 (Dapat Diabaikan)**: Deviasi jadwal < 1 hari, tidak ada dampak finansial.
- **2 (Minor)**: Deviasi jadwal 2-3 hari, bisa diselesaikan dalam sprint yang sama.
- **3 (Moderat)**: Deviasi 1 sprint penuh, fitur sekunder tertunda.
- **4 (Mayor)**: Deviasi rilis publik > 2 minggu, biaya tambahan > 20% budget.
- **5 (Bencana)**: Kebocoran data keamanan, downtime fatal production, pembatalan proyek.

---

## 4. Matriks Risiko 5x5 (Heat Map) & Zona Keparahan

Matriks probabilitas versus dampak menentukan urgensi tindakan tim engineering:

```
Impact ▲
     5 │   5 (M)    10 (H)    15 (C)    20 (C)    25 (C)
     4 │   4 (L)     8 (M)    12 (H)    16 (C)    20 (C)
     3 │   3 (L)     6 (M)     9 (H)    12 (H)    15 (C)
     2 │   2 (L)     4 (L)     6 (M)     8 (M)    10 (H)
     1 │   1 (L)     2 (L)     3 (L)     4 (L)     5 (M)
       └─────────────────────────────────────────────────►
           1         2         3         4         5   Probability
```

### Panduan Tindakan Berdasarkan Zona:
- **Critical (Skor 15 - 25)**: Risiko level bencana. Hentikan fitur non-inti jika perlu. Buat spike teknis di sprint saat ini. Wajib dilaporkan langsung ke sponsor.
- **High (Skor 9 - 14)**: Berdampak nyata pada jadwal atau performa sistem. Masukkan rencana mitigasi langsung ke dalam backlog sprint berikutnya.
- **Medium (Skor 5 - 8)**: Perhatikan pemicu dini. Review dalam rapat mingguan. Alokasikan buffer waktu secukupnya.
- **Low (Skor 1 - 4)**: Dampak minimal. Terima risiko (*Accept*) dan catat dalam log tanpa alokasi aksi aktif.

---

## 5. Empat Strategi Respon Risiko

1. **Avoid (Menghindari)**: Ubah arsitektur atau ruang lingkup untuk menghilangkan risiko sepenuhnya.
   *Contoh*: Menghapus penyimpanan data kartu kredit di server lokal dan beralih ke tokenization provider pihak ketiga untuk menghindari risiko kepatuhan PCI-DSS.
2. **Mitigate (Memitigasi)**: Mengambil langkah proaktif untuk menurunkan probabilitas atau meminimalkan keparahan dampak.
   *Contoh*: Menyiapkan worker queue asinkron dan mock server lokal agar tim tidak diblokir saat API vendor staging sering down.
3. **Transfer (Mengalihkan)**: Memindahkan dampak finansial atau operasional risiko ke pihak lain.
   *Contoh*: Menggunakan cloud managed database (AWS Aurora/RDS) dengan SLA 99.99% dan failover otomatis daripada mengelola kluster database mandiri.
4. **Accept (Menerima)**: Menerima konsekuensi tanpa tindakan preventif aktif karena biaya mitigasi jauh melebihi potensi dampak kerugian.
   *Contoh*: Menerima potensi downtime 3 menit saat deployment bulanan di dini hari pada aplikasi back-office non-transaksional.

<!-- ponytail: Mulai dengan mitigasi sederhana (mock server, queue buffer, timeout); tambahkan kluster multi-region disaster recovery hanya jika downtime memicu denda penalti finansial riil. -->

---

## 6. Early Warning Triggers & Protokol Eskalasi

Jangan menunggu risiko berubah menjadi insiden live:

### Indikator Peringatan Dini (Early Warning Signs):
- **Vendor Delays**: Respon permohonan sandbox API vendor memakan waktu > 3 hari kerja dari komitmen.
- **Velocity Drift**: Tim menyelesaikan < 70% Story Points rencana selama dua sprint beruntun.
- **Code Churn**: Lebih dari 30% file dirombak ulang pada fase QA akhir menandakan spesifikasi awal tidak matang.
- **Flaky Tests**: Test suite staging gagal intermiten lebih dari 10% eksekusi CI/CD.

### Alur Eskalasi Teknis:
1. **Level 1 (Engineer -> Tech Lead)**: Terjadi saat skor risiko naik ke kategori *Medium* (5-8). Evaluasi dalam standup harian.
2. **Level 2 (Tech Lead -> Product Manager)**: Terjadi saat risiko kategori *High* (9-14) mengancam target scope sprint. Potong fitur non-kritis.
3. **Level 3 (Tech Lead/PM -> Project Sponsor)**: Terjadi saat risiko kategori *Critical* (15-25) mengancam tanggal rilis publik atau anggaran proyek. Picu rapat darurat Change Request.

---

## 7. Format Praktis Entri Risk Register (Markdown Snippet)

Contoh format entri file `docs/risk-register.md`:

```markdown
### RSK-01: API Sandbox Bank Mitra Tidak Stabil
- **Category**: External / Technical
- **P x I**: 4 x 4 = 16 (CRITICAL)
- **Description**: Jika sandbox bank sering timeout, maka integrasi backend checkout terhambat.
- **Strategy**: Mitigate
- **Action Plan**: Bangun mock HTTP server lokal dengan respon biner sukses/gagal di Sprint 1.
- **Contingency**: Gunakan replay payload rekaman jika sandbox bank down > 24 jam.
- **Owner**: Backend Lead
- **Trigger**: Error HTTP 504 gateway timeout lebih dari 3x sehari pada sandbox bank.
- **Status**: Mitigating
```

---

## 8. Kadensi Peninjauan Risiko & Gate Sign-off

Risiko yang tidak dipantau secara rutin adalah ilusi keamanan semu:
- **Sprint Planning (Setiap 1-2 Minggu)**: Periksa status risiko berkategori *Critical* dan *High*. Pastikan tiket mitigasi masuk sprint backlog.
- **Sprint Retrospective**: Evaluasi apakah ada risiko baru yang muncul selama sprint berlangsung dan tutup (*Close*) risiko yang pemicunya sudah lewat.
- **Bi-weekly Stakeholder Update**: Publikasikan perubahan skor risiko pada project status report untuk visibilitas tim manajemen.
- **Gate 4 / Gate 5 Exit Gate**: Dilarang rilis produksi jika masih terdapat risiko berstatus *Critical* tanpa rencana kontinjensi yang telah diuji secara nyata.

---

## 9. Daftar Risiko Umum Proyek Software & Mitigasi Standar

| ID | Risiko Teknis / Proyek | P | I | Skor | Strategi | Mitigasi Standar |
|---|---|---|---|---|---|---|
| `RSK-T1` | API vendor pihak ketiga lambat atau sering down | 4 | 4 | 16 (C) | Mitigate | Siapkan mock server untuk dev lokal, pasang circuit breaker & timeout ketat di backend. |
| `RSK-S1` | Perubahan scope mendadak dari stakeholder bisnis | 4 | 3 | 12 (H) | Avoid | Tegakkan aturan Change Request resmi; tukar fitur sepadan (*scope swap*) jika jadwal tetap. |
| `RSK-R1` | Single point of failure: engineer kunci sakit/resign | 3 | 4 | 12 (H) | Mitigate | Mandatory pair programming pada modul kompleks; wajibkan dokumentasi arsitektur di repo. |
| `RSK-E1` | Regulasi data privasi baru berlaku saat pertengahan rilis | 2 | 5 | 10 (H) | Mitigate | Desain skema data dengan pseudonimisasi dan kemampuan penghapusan data otomatis sejak awal. |

---

## 10. Common Mistakes

- **Risk Register "Tulis dan Lupakan"**: Dibuat saat kick-off proyek lalu tidak pernah dibuka lagi hingga kegagalan terjadi di production.
- **Deskripsi Gejala, Bukan Risiko**: Menulis "Server lambat" bukan risiko, itu gejala. Tulis penyebab dan potensi: "Kurangnya database indexing pada tabel order berpotensi memicu query timeout saat flash sale".
- **Semua Risiko Diberi Label Critical**: Panik berlebihan membuat tim kehilangan fokus pada hal-hal yang benar-benar berbahaya.
- **Risiko Tanpa Pemilik (No Owner)**: Jika kolom pemilik diisi "Tim Engineering", tidak ada satu orang pun yang merasa bertanggung jawab mengeksekusi mitigasi.
- **Mengabaikan Early Warning Sign**: Tanda bahaya sudah terlihat (vendor lambat membalas email 2 minggu), namun tim menunggu hingga deadline terlewati baru bertindak.

---

## 11. Related Docs & Templates

- Template operasional: [Risk Register Template](../templates/project/risk-register.md)
- Perencanaan mitigasi jadwal: [Project Planning](./project-planning.md)
- Pelaporan status risiko: [Project Status Report](./project-status-report.md)
- Pintu gerbang evaluasi: [Stage-Gate Process](./stage-gate-process.md)

---
*skipped: quantitative Monte Carlo financial risk modeling; add when project budget exceeds $250k with strict liquidated damages penalties.*