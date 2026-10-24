# Discovery Readiness Checklist (Gate 1)

Checklist validasi kelayakan masalah sebelum masuk fase Product Requirements Document (PRD). Cegah pemborosan sumber daya akibat membangun solusi yang salah.

---

## 1. Problem Validation
- [ ] **Problem Statement**: Masalah terdefinisi spesifik via format *Who-What-Why*. Bebas dari bias solusi awal.
- [ ] **Empirical Evidence**: Bukti valid dari minimal 5 user interview, data analitik riil, atau tiket support historis.
- [ ] **Root Cause Analysis**: Akar masalah terkonfirmasi tuntas via teknik *5 Whys* atau *Fishbone Diagram*.
- [ ] **Cost of Inaction (CoI)**: Dampak kerugian finansial, reputasi, atau efisiensi operasional jika dibiarkan terhitung jelas.
- [ ] **Current Workaround**: Cara pengguna mengatasi masalah saat ini terdokumentasi lengkap bersama friksinya.
- [ ] **Market & Competitor Reality**: Solusi kompetitor atau alternatif open-source sudah dianalisis kelemahannya.

---

## 2. Target Users & Stakeholders
- [ ] **Primary User Persona**: Persona utama terdefinisi konkret (pekerjaan, pain point, trigger emosional, konteks pakai).
- [ ] **Secondary Users & Beneficiaries**: Pihak sekunder terdampak (admin operasional, tim finance, auditor) teridentifikasi.
- [ ] **Stakeholder Map (RACI)**: Matriks penugasan terdefinisi tegas (*Responsible, Accountable, Consulted, Informed*).
- [ ] **Key Decision Maker**: 1 orang sponsor bisnis pemilik otoritas budget dan arah strategis ditunjuk resmi.
- [ ] **Inter-team Alignment**: Tidak ada konflik tujuan atau gesekan KPI antar departemen terkait.

---

## 3. Feasibility Assessment
- [ ] **Technical Feasibility**: Kapabilitas stack, integrasi API pihak ketiga, dan limitasi teknis dinilai realistis.
- [ ] **Financial Feasibility**: Estimasi kasar ROI positif; proyeksi biaya infrastruktur dan maintenance masuk batas anggaran.
- [ ] **Operational Feasibility**: Tim support, sales, dan operasi sanggup menjalankan alur kerja baru tanpa resistensi.
- [ ] **Schedule Feasibility**: Estimasi kasar waktu deliver MVP relevan dengan window peluang pasar.
- [ ] **Legal & Compliance**: Kepatuhan terhadap UU PDP, standar lisensi dependensi, dan aturan industri terverifikasi aman.

---

## 4. Assumptions & Constraints
- [ ] **Assumptions Register**: Seluruh asumsi kritis terkait perilaku user, teknologi, dan bisnis dicatat rapi.
- [ ] **Hypothesis Validation Plan**: Asumsi berisiko tinggi diuji awal via eksperimen cepat atau technical spike.
- [ ] **Hard Constraints Identified**: Batasan kaku (deadline regulator, budget mati, legacy system) ditandai eksplisit.
- [ ] **External Dependencies**: Ketergantungan terhadap vendor pihak ketiga, partner eksternal, atau izin khusus terpetakan.
- [ ] **Risk Thresholds**: Titik pemicu (*pivot* atau *kill switch*) disepakati jika asumsi dasar terbukti gugur.

---

## 5. Discovery Deliverables
- [ ] **Project Charter**: Dokumen charter disahkan sponsor utama dengan batasan scope jelas.
- [ ] **Discovery Synthesis Report**: Ringkasan interview user, transkrip, dan temuan data disimpan terpusat.
- [ ] **Solution Hypotheses**: Minimal 2 alternatif pendekatan solusi disiapkan untuk dievaluasi pada PRD.
- [ ] **Gate 1 Exit Criteria Sign-off**: Tech Lead, Product Manager, dan Sponsor Bisnis menandatangani kelulusan fase.

---

## Panduan Penilaian (Assessment Scoring Guide)
Hitung skor kesiapan: `(Jumlah Checklist Tercentang / 25) * 100%`

| Rentang Skor | Status Gerbang | Rekomendasi Tindakan |
|---|---|---|
| **>= 85%** | **GO (Pass)** | Kesiapan solid. Langsung lanjutkan ke penyusunan PRD (Gate 2). |
| **70% - 84%** | **CONDITIONAL** | Lanjut bersyarat. Tuntaskan item minor yang tertunda maksimal dalam 3 hari kerja. |
| **< 70%** | **NO-GO (Fail)** | Tahan. Kembali ke eksplorasi riset. Dilarang menulis dokumen spesifikasi produk. |

### Critical Mandatory Items
Gagal pada salah satu item di bawah otomatis menghasilkan status **NO-GO**, berapa pun total persentase skor:
1. *Problem Statement* tervalidasi empiris.
2. *Cost of Inaction* terhitung nyata.
3. *Technical Feasibility* disetujui Tech Lead / Senior Dev.
4. *Key Decision Maker* menandatangani scope awal.

---

## Panduan Remediasi (Remediation Actions)
Jika item tertentu tidak lolos, jalankan tindakan korektif berikut:

- **Problem Statement Lemah / Solutif**:
  Gelar 1 sesi *problem reframing* berdurasi 60 menit bersama stakeholder. Hapus semua terminologi fitur/teknologi dari deskripsi masalah.
- **Bukti Empiris Tidak Cukup**:
  Jadwalkan minimal 3 wawancara user tambahan atau deploy survei mikro 1 pertanyaan pada produk eksisting.
- **RACI Mengambang**:
  Tunjuk Tech Lead sebagai penanggung jawab teknis dan Product Manager/Owner sebagai *Single Accountable*. Hindari kepemilikan ganda.
- **Kelayakan Teknis Meragukan**:
  Alokasikan time-box 2 hari untuk *Technical Spike* / PoC pembuktian pustaka atau API yang diragukan.
- **Hambatan Kepatuhan / Legalitas**:
  Kirim memo resmi ke tim legal atau konsultan kepatuhan sebelum membuka editor kode. Batalkan inisiatif jika risiko regulasi tak termitigasi.
- **Scope Merambat di Discovery**:
  Potong asumsi liar. Fokus hanya pada 1 alur utama (*primary job-to-be-done*) yang menghasilkan dampak terbesar.