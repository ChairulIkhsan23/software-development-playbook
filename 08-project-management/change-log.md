# Change Log

Panduan tata kelola kendali perubahan ruang lingkup, jadwal, biaya, dan baseline proyek perangkat lunak.

---

## 1. Project Change Log vs Software Changelog

Jangan mencampuradukkan catatan rilis kode dengan log pengendalian proyek:

| Dimensi | Project Change Log (`docs/change-log.md`) | Software Changelog (`CHANGELOG.md`) |
|---|---|---|
| **Definisi** | Dokumen tata kelola perubahan *baseline* proyek (scope, deadline, biaya, kontrak) | Catatan perubahan teknis kode untuk end-user / developer (Keep a Changelog) |
| **Audiens** | Sponsor proyek, PM, Tech Lead, Client Management | Pengguna aplikasi, customer support, engineer pengonsumsi API |
| **Pemicu** | Perubahan persyaratan bisnis, revisi anggaran, pergeseran milestone | Git tag baru, release version (misal: v1.2.0), hotfix production |
| **Format Standar** | Tabel Change Request (CR ID, Dampak, Status Approval) | List semver: `Added`, `Changed`, `Deprecated`, `Removed`, `Fixed` |
| **Contoh Entri** | "CR-04: Penambahan VA BCA memperpanjang timeline sprint 1 minggu" | "v1.2.0: Menambahkan validasi regex nomor telepon pada form registrasi" |

---

## 2. Ambang Batas Perubahan (Change Control Thresholds)

Tidak setiap penyesuaian kecil memerlukan birokrasi Change Request formal:

- **Minor Change (Tanpa CR)**: Bug fix teknis, perubahan teks label UI, atau refactoring internal tanpa mengubah kontrak API dan tanpa memundurkan timeline sprint. Cukup dicatat di tiket sprint backlog.
- **Major Change (Wajib Formal CR)**:
  - Penambahan fitur baru di luar daftar In-Scope Project Charter.
  - Pergeseran tanggal milestone rilis publik > 3 hari kerja.
  - Kebutuhan penambahan budget cloud infrastructure > $200/bulan.
  - Perubahan arsitektur dasar yang membatalkan kontrak API pihak ketiga yang sudah disepakati.

---

## 3. Alur Kerja Change Request (CR Workflow)

Perubahan pada proyek yang sudah memiliki baseline terkunci wajib melalui siklus 5 tahap:

```
[1. Submit CR] ──► [2. Analisis Dampak] ──► [3. Review CCB / Sponsor]
                           │                               │
             (Scope, Schedule, Cost, Risk)                 ▼
                                                   [4. Keputusan]
                                              ┌────────────┼────────────┐
                                           Disetujui    Ditolak    Ditunda
                                              │            │            │
                                              ▼            ▼            ▼
                                      [5. Update Base] [Selesai]   [Backlog]
```

### Tahap 1: Pengajuan (Submit)
Stakeholder atau tim teknis mengisi formulir permohonan Change Request ringkas:
- Masalah/peluang bisnis baru yang mendasari perubahan.
- Deskripsi fitur atau penyesuaian teknis yang diminta.
- Justifikasi urgensi (mengapa tidak bisa menunggu Fase 2).

### Tahap 2: Analisis Dampak Terintegrasi (Impact Analysis)
Tech Lead dan PM mengevaluasi lima dimensi dampak:
- **Scope**: Berapa banyak user story baru atau komponen arsitektur yang bertambah?
- **Schedule**: Berapa hari/sprint timeline bergeser jika kapasitas tim tetap?
- **Cost / Resources**: Apakah membutuhkan penambahan lisensi server atau engineer kontraktor?
- **Quality**: Apakah perubahan memengaruhi performa P95 latency atau test coverage?
- **Risk**: Apakah ada celah keamanan baru atau dependensi vendor yang tidak stabil?

### Tahap 3: Peninjauan oleh Change Control Board (CCB) / Sponsor
Untuk tim kecil (3-8 orang), CCB cukup terdiri dari: **Project Sponsor**, **Product Owner**, dan **Tech Lead**.
Untuk solo developer: evaluasi dilakukan bersama klien atau co-founder.

### Tahap 4: Keputusan Formal
Tiga opsi keputusan biner:
1. **Approved**: Perubahan diterima bersama penyesuaian jadwal atau biaya baru.
2. **Rejected**: Permintaan ditolak dengan alasan objektif terdokumentasi.
3. **Deferred**: Permintaan valid namun diparkir ke backlog versi berikutnya.

### Tahap 5: Pembaruan Baseline (Update Baselines)
Jika disetujui, update Project Plan, sesuaikan WBS, dan perbarui tanggal rilis resmi.

---

## 4. Manajemen Baseline (Scope & Schedule Baseline)

Baseline adalah rencana kerja resmi yang disepakati bersama sponsor pada saat Project Charter disahkan.

- **Scope Baseline**: WBS disetujui + PRD ditandatangani. Perubahan scope tanpa penyesuaian waktu memicu *crunch time* yang merusak kualitas software.
- **Schedule Baseline**: Tanggal milestone kritis yang dijanjikan ke manajemen atau klien.
- Aturan Emas: **Baseline lama tidak boleh dihapus atau ditimpa secara diam-diam**. Selalu catat versi baseline:
  - Baseline v1.0 (Tanggal Charter) -> Baseline v1.1 (Setelah CR-01 disetujui).

<!-- ponytail: Gunakan format persetujuan asinkron via PR Git untuk tim lincah; adopsi formal CCB meeting berjadwal jika melibatkan kontrak pihak ketiga bernilai besar. -->

---

## 5. Alokasi Anggaran Kontinjensi & Change Budget

Setiap proyek software harus memiliki penyangga (*buffer*) resmi sejak awal:
- **Scope Buffer (20% Kapasitas)**: Pisahkan 20% dari total Story Points untuk tiket berkategori *Could Have* (MoSCoW). Jika CR baru disetujui, tukar dengan tiket buffer tersebut (*Scope Swap*).
- **Time Contingency**: Tambahkan toleransi 15-20% pada tanggal rilis publik untuk mengantisipasi rework pasca pengujian keamanan atau UAT.
- **Budget Reserve**: Alokasikan dana cadangan 10% dari total anggaran untuk biaya lisensi mendadak atau lonjakan compute cloud selama pengujian beban.

---

## 6. Mencegah Scope Creep Tanpa Kehilangan Kelincahan (Agile)

Agile bukan berarti menerima semua permintaan baru tanpa batas. Agile adalah fleksibilitas yang disiplin.

```
                    [Permintaan Fitur Baru Masuk]
                                  │
                 Apakah kapasitas sprint masih cukup?
                                  │
                 ┌────────────────┴────────────────┐
                YA                                TIDAK
                 ▼                                 ▼
         [Kerjakan Langsung]              [Terapkan Trade-off]
                                    1. Tukar Fitur (Scope Swap), ATAU
                                    2. Tambah Waktu (Mundur Deadline), ATAU
                                    3. Tolak & Masukkan Backlog Next Release
```

Tiga teknik menolak scope creep secara diplomatis dan elegan:
1. **The Iron Triangle Trade-off**: "Kita bisa menambahkan pembayaran QRIS sekarang, dengan konsekuensi rilis mundur 10 hari, atau kita keluarkan fitur notifikasi SMS."
2. **Backlog Parking Lot**: "Ini ide yang sangat bagus. Kami masukkan ke Backlog Fase 1.1 agar tidak mengganggu stabilitas go-live minggu depan."
3. **Cost-to-Value Challenge**: Minta stakeholder menyediakan data estimasi transaksi bisnis sebelum engineer menghabiskan 40 jam menulis kode.

---

## 7. Format Contoh Entri Project Change Log

Simpan file di `08-project-management/change-log.md` atau `docs/project-change-log.md`:

```markdown
### CR-01: Penambahan Metode Pembayaran Dynamic QRIS (Disetujui)
- **Date**: 2026-10-22 | **Requestor**: Product Owner
- **Description**: Merchant menuntut integrasi dynamic QRIS untuk mempercepat transaksi gerai offline.
- **Impact Analysis**:
  - *Scope*: Tambah 2 worker queue dan 1 endpoint callback webhook (+13 Story Points).
  - *Schedule*: Timeline rilis mundur 1 sprint (10 hari kalender).
  - *Cost*: Tambahan fee registrasi QRIS provider $150.
- **Decision**: Approved by Project Sponsor (VP Engineering)
- **Baseline Affected**: Schedule Baseline v1.1, Scope Baseline v1.1

### CR-02: Fitur PayLater Internal Monolith (Ditolak)
- **Date**: 2026-10-24 | **Requestor**: Commercial Lead
- **Description**: Permintaan membangun sistem credit scoring internal dalam aplikasi.
- **Impact Analysis**: Menuntut perubahan lisensi perbankan dan menambah 8 sprint kerja baru.
- **Decision**: Rejected by CCB (Alasan: Melanggar batas Out-of-Scope pada Project Charter).
- **Baseline Affected**: Tidak ada (Baseline tetap v1.1).
```

---

## 8. Checklist Verifikasi Pengajuan Change Request

Sebelum mengajukan tiket CR ke meja sponsor, pastikan:
- [ ] Apakah ada alternatif teknis yang tidak menuntut perubahan baseline tanggal rilis?
- [ ] Apakah estimasi waktu tambahan sudah memperhitungkan testing QA dan regression test?
- [ ] Apakah trade-off fitur pengganti (*scope swap*) sudah disiapkan untuk ditawarkan?
- [ ] Apakah risiko keamanan baru sudah dinilai bersama Security Specialist?

---

## 9. Common Mistakes

- **Verbal Scope Creep**: Menyetujui penambahan fitur lewat obrolan kopi atau chat Slack tanpa pencatatan resmi.
- **Menambah Scope Tanpa Menambah Waktu**: Mengorbankan waktu istirahat developer dan testing quality demi memenuhi semua kemauan stakeholder.
- **Mencatat Changelog Fitur di Log Proyek**: Mengisi log kendali proyek dengan commit message teknis seperti "Fix typo di CSS".
- **Persetujuan Tanpa Analisis Dampak**: Sponsor langsung bilang "ya" tanpa meminta Tech Lead menghitung risiko arsitektur.
- **Tidak Memperbarui Baseline**: CR disetujui, namun tanggal di project plan dan dashboard tidak diperbarui, sehingga status proyek terlihat terlambat semu.

---

## 10. Related Docs & Templates

- Template dokumen: [Change Log Template](../templates/governance/change-log-template.md)
- Otorisasi awal: [Project Charter](./project-charter.md)
- Perencanaan jadwal: [Project Planning](./project-planning.md)
- Laporan status performa: [Project Status Report](./project-status-report.md)

---
*skipped: automated enterprise workflow ticketing integrations; add when project involves multi-vendor contractual service level agreements.*