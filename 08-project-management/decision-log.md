# Decision Log

Panduan pencatatan keputusan manajemen proyek, ruang lingkup, proses kerja, dan arah bisnis dalam repositori software.

---

## 1. Tujuan & Nilai Inti Decision Log

Dalam proyek perangkat lunak berdurasi > 3 bulan, amnesia tim adalah musuh tersembunyi nomor satu.

Fungsi utama Decision Log:
- **Mencegah Relitigasi Keputusan**: Menghentikan perdebatan berulang tentang topik yang sudah pernah diputuskan hanya karena ada anggota baru atau stakeholder lupa alasan aslinya.
- **Menangkap Konteks "Mengapa" (The Why)**: Kode hanya mencatat apa yang dibangun; Decision Log mencatat alasan mengapa opsi lain ditolak saat itu.
- **Kejelasan Tanggung Jawab**: Memastikan setiap kesepakatan memiliki pemilik keputusan eksplisit yang sah.

---

## 2. Decision Log vs Architectural Decision Record (ADR)

Jangan campur aduk keputusan bisnis dan arsitektur kode di tempat yang sama:

| Dimensi | Decision Log (Project Level) | Architectural Decision Record (ADR) |
|---|---|---|
| **Fokus Area** | Scope fitur, jadwal, budget, proses kerja tim, lisensi vendor, pivot produk | Pola desain kode, schema database, framework, protokol jaringan, stack teknis |
| **Pemilik Utama** | Project Manager, Product Owner, Engineering Lead | Tech Lead, Software Architect, Core Engineering Team |
| **Audiens Target** | Sponsor bisnis, manajemen, lead lintas divisi | Software engineer, DevOps, reviewer kode |
| **Lokasi File** | `docs/decision-log.md` atau `08-project-management/` | `docs/adr/ADR-001-xxx.md` atau `05-architecture/` |
| **Contoh Kasus** | "Memotong fitur Export CSV demi mengejar rilis MVP Q3" | "Menggunakan PostgreSQL JSONB alih-alih MongoDB untuk skema dinamis" |

---

## 3. Kerangka Pengambilan Keputusan: Model Type 1 vs Type 2 (Jeff Bezos)

Kecepatan eksekusi tim ditentukan oleh kemampuan mengenali sifat reversibilitas keputusan:

```
                          [Keputusan Muncul]
                                  │
                  Apakah bisa dibatalkan dengan mudah?
                                  │
                 ┌────────────────┴────────────────┐
                YA                                TIDAK
                 ▼                                 ▼
         [Type 2: Two-Way Door]           [Type 1: One-Way Door]
     - Keputusan reversibel           - Keputusan ireversibel
     - Ambil cepat (informasi ~70%)   - Analisis mendalam, mitigasi risiko
     - Delegasikan ke engineer        - Wajib persetujuan Tech Lead & Sponsor
```

1. **Type 1 (One-Way Door / Irreversible)**:
   - Keputusan yang sangat sulit atau mustahil dibatalkan tanpa biaya finansial dan reputasi masif.
   - *Contoh*: Memilih vendor cloud proprietary terkunci, merancang skema partition database utama multi-terabyte, migrasi core ledger perbankan.
   - *Tindakan*: Wajib konsultasi menyeluruh, benchmark, uji spike, dan persetujuan formal sponsor.
2. **Type 2 (Two-Way Door / Reversible)**:
   - Keputusan yang mudah dibatalkan jika hasilnya buruk dengan biaya minim.
   - *Contoh*: Mengganti library styling UI, mencoba workflow sprint baru, mengubah format notifikasi email.
   - *Tindakan*: Putuskan secepat mungkin begitu data terkumpul 70%. Jangan buang waktu rapat berminggu-minggu.

<!-- ponytail: Catat keputusan Type 2 dalam satu baris tabel markdown; buat dokumen RFC/proposal mendalam hanya untuk keputusan Type 1. -->

---

## 4. Kerangka Kerja Akuntabilitas: DACI Framework

Hilangkan kebingungan "siapa yang berhak memutuskan" menggunakan model DACI:

- **Driver (D)**: Satu orang yang mengawal keputusan dari awal hingga tuntas (mengumpulkan data, memfasilitasi diskusi, menulis log). Biasanya PM atau Tech Lead.
- **Approver (A)**: Tepat SATU orang yang memiliki hak veto dan tanda tangan akhir. Tidak boleh ada Approver ganda untuk mencegah kebuntuan (*deadlock*).
- **Contributors (C)**: Pihak yang memiliki keahlian teknis atau data dan wajib dimintai masukan (misal: Senior Backend, QA Lead, Security Specialist).
- **Informed (I)**: Pihak yang diberi tahu setelah keputusan disahkan karena hasil keputusan memengaruhi alur kerja mereka (misal: Tim Support, Sales, Frontend).

### Perbandingan Ringkas DACI vs RACI

| Elemen DACI | Padanan RACI | Peran Kunci |
|---|---|---|
| **Driver** | Responsible (R) | Menggerakkan inisiatif hingga tuntas |
| **Approver** | Accountable (A) | Pemilik hak veto tunggal |
| **Contributor** | Consulted (C) | Memberi masukan data & uji teknis |
| **Informed** | Informed (I) | Menerima pengumuman pasca-approval |

---

## 5. Struktur Standar Decision Log

Simpan log dalam bentuk format terstruktur di repositori proyek:

| Kolom | Definisi & Standar Entri |
|---|---|
| **Decision ID** | Nomor identifikasi unik (misal: `DEC-01`, `DEC-02`). |
| **Date** | Tanggal pengesahan keputusan format ISO (`YYYY-MM-DD`). |
| **Context / Problem** | Masalah nyata atau batasan yang memicu kebutuhan keputusan. |
| **Decision Taken** | Pernyataan keputusan biner dan tegas yang disepakati. |
| **Alternatives Considered** | Daftar 1-3 opsi alternatif yang ditolak beserta alasan penolakannya. |
| **Stakeholders (DACI)** | Nama Driver, Approver, dan Kontributor yang terlibat. |
| **Rationale** | Argumen logis dan bukti utama mengapa opsi ini dipilih. |
| **Expected Impact** | Dampak pada jadwal, biaya, performa teknis, atau alur kerja. |
| **Review Date / Outcome** | Tanggal review retrospektif untuk mengecek apakah hipotesis terbukti benar. |

### Contoh Kasus Nyata:

```markdown
### DEC-04: Menunda Fitur Export Excel pada Rilis MVP v1.0 (Type 2)
- **Date**: 2026-10-20
- **Context**: Rilis MVP v1.0 tersisa 2 sprint. Modul generation Excel kompleks berpotensi memicu memory leak pada server berkapasitas 2GB.
- **Decision Taken**: Rilis MVP v1.0 hanya mendukung export raw CSV. Format Excel `.xlsx` ditunda ke rilis v1.1.
- **Alternatives Considered**:
  - *Opsi A (Sewa server besar 16GB)*: Ditolak karena menaikkan biaya infrastruktur $300/bulan sebelum validasi pasar.
  - *Opsi B (Mundur rilis 3 minggu)*: Ditolak oleh sponsor karena komitmen jadwal pameran bisnis.
- **DACI**: Driver: PM Checkout | Approver: VP Product | Contributors: Tech Lead, Backend Dev | Informed: Sales Team.
- **Rationale**: 90% pengguna bisnis enterprise dapat mengimpor format CSV ke spreadsheet tanpa kendala data.
- **Expected Impact**: Menghemat 10 hari kerja engineering dan menjaga tanggal go-live tepat waktu.
- **Review Date**: 2026-12-01 (Hasil evaluasi: komplain CSV dari user hanya 2 tiket support; keputusan sukses).

### DEC-05: Memilih Managed AWS Aurora PostgreSQL (Type 1)
- **Date**: 2026-10-22
- **Context**: Kebutuhan high availability multi-AZ dengan automated failover < 30 detik untuk SLA keuangan 99.95%.
- **Decision Taken**: Menggunakan AWS Aurora Serverless v2 PostgreSQL alih-alih self-hosted PostgreSQL di EC2 instance.
- **Alternatives Considered**: Self-hosted cluster dengan Patroni (Ditolak karena tidak ada staf DBA dedicated).
- **DACI**: Driver: Tech Lead | Approver: VP Engineering | Contributors: DevOps Lead | Informed: Finance.
- **Rationale**: Mengurangi operational burden engineering dan menyediakan point-in-time recovery 35 hari otomatis.
- **Expected Impact**: Biaya cloud naik $180/bulan, namun menghemat 40 jam setup maintain cluster database mandiri.
```

---

## 6. Template Entri Kilat untuk Solo Dev

Solo developer cukup mencatat format ringkas di root repositori (`DECISIONS.md`):

```markdown
- **2026-10-25 [DEC-01] Gunakan SQLite alih-alih PostgreSQL**:
  - *Sebab*: Aplikasi desktop lokal, zero config deployment.
  - *Trade-off*: Tidak bisa concurrent multi-server write; diterima karena single-user.
  - *Review*: Evaluasi saat pengguna aktif harian > 1,000.
```

---

## 7. Kapan Keputusan Boleh Dibuka Kembali (Triggers for Reversal)

Jangan buka kembali keputusan yang sudah disahkan hanya karena opini subjektif atau pergantian personil tim.

Buka kembali keputusan HANYA jika minimal satu pemicu objektif ini terjadi:
1. **Fakta Lingkungan Berubah**: Regulasi baru, perubahan drastis harga vendor, atau platform dependensi mendadak deprecated.
2. **Asumsi Awal Terbukti Palsu**: Asumsi traffic awal 100 req/s ternyata mencapai 10,000 req/s di data analytics riil.
3. **Biaya Nyata Melebihi Proyeksi**: Implementasi awal membutuhkan effort 3x lebih besar dari estimasi spike teknis.
4. **Tanggal Review Retrospektif Tiba**: Tinjau hasil metrik pada `Review Date` yang dicatat saat keputusan dibuat.

---

## 8. Workflow Pengesahan Keputusan

1. **Trigger**: Masalah muncul di sprint atau pertemuan bisnis.
2. **Drafting**: Driver menyusun entri log baru dan opsi alternatif di branch Git.
3. **DACI Consultation**: Kontributor memberikan argumen dan data benchmark.
4. **Approval**: Approver menyetujui via komentar pull request atau pertemuan resmi 15 menit.
5. **Merge & Broadcast**: File di-merge ke branch `main` dan link dibagikan ke stakeholder dalam daftar *Informed*.

---

## 9. Common Mistakes

- **Mencatat Hasil Tanpa Alasan (No "Why")**: Menulis "Kita pakai Tailwind" tanpa mencatat bahwa alternatif Bootstrap ditolak karena ukuran bundle CSS terlalu besar.
- **Approver Majemuk / Panitia Komite**: Jika semua orang adalah Approver, tidak ada yang bertanggung jawab saat keputusan berantakan. Tentukan satu Approver tunggal.
- **Memperlakukan Keputusan Type 2 Seperti Type 1**: Menghabiskan waktu rapat 2 minggu hanya untuk menentukan format nama folder di repositori.
- **Menghidupkan Kembali Keputusan Tanpa Data Baru**: Membuka perdebatan lama hanya karena preferensi personal anggota baru tanpa membawa bukti metrik baru.
- **Dokumen Tersembunyi di Slack/Email**: Keputusan penting disepakati di thread chat personal lalu hilang terkubur. Selalu salin ke Decision Log repositori.

---

## 10. Related Docs & Templates

- Template dokumen resmi: [Decision Log Template](../templates/governance/decision-log-template.md)
- Arsitektur teknis spesifik: [ADR Template](../templates/design/adr-template.md)
- Manajemen perubahan resmi: [Change Log](./change-log.md)
- Evaluasi tahapan rilis: [Stage-Gate Process](./stage-gate-process.md)

---
*skipped: formal enterprise voting quorums and legal notarization; add when governance spans multiple corporate entities or joint ventures.*