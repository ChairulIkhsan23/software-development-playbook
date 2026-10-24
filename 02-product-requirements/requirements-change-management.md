# Requirements Change Management

## Mengapa Perubahan Harus Dikelola?

Requirement **pasti berubah**. Market bergeser, user feedback masuk, teknologi berevolusi. Masalahnya bukan perubahan itu sendiri, melainkan perubahan yang:

- Masuk tanpa analisis dampak → sprint berantakan
- Tidak terdokumentasi → tidak ada yang tahu kenapa kode berubah 3 bulan lalu
- Tidak disetujui oleh pihak yang tepat → konflik di kemudian hari
- Terjadi terus-menerus tanpa batas → scope creep

Change management bukan birokrasi untuk memperlambat. Ini adalah **mekanisme untuk membuat keputusan perubahan secara sadar**, bukan reaktif.

## Alur Change Request (CR)

```
 ┌──────────┐    ┌──────────────┐    ┌───────────────┐    ┌──────────────┐    ┌──────────┐
 │ 1.SUBMIT │───▶│ 2.ANALYZE    │───▶│ 3.REVIEW &    │───▶│ 4.IMPLEMENT  │───▶│ 5.VERIFY │
 │          │    │   IMPACT     │    │   DECIDE      │    │              │    │          │
 └──────────┘    └──────────────┘    └───────────────┘    └──────────────┘    └──────────┘
  Pemohon         Engineering +       CCB / Decision       Dev Team           QA + PO
  mengisi form    QA analisis         Maker memutuskan     eksekusi           verifikasi
                  dampak              Accept/Defer/Reject                     hasil
```

### Tahap 1: Submit — Pengajuan Perubahan

Siapapun boleh mengajukan CR: developer, QA, stakeholder, customer support. Yang penting: **tulis dengan jelas**.

### Tahap 2: Analyze Impact — Analisis Dampak

Tim engineering dan QA menganalisis:
- **Scope:** Requirement lain yang terdampak
- **Teknis:** Modul, service, database schema yang harus diubah
- **Timeline:** Berapa lama effort tambahan, apakah deadline bergeser
- **Cost:** Resource tambahan yang dibutuhkan
- **Risk:** Risiko regresi, risiko integrasi, risiko data

### Tahap 3: Review & Decide — Keputusan

Decision maker (atau Change Control Board) memutuskan salah satu:

| Keputusan | Arti | Tindakan Selanjutnya |
|---|---|---|
| **Accept** | Perubahan diterima | Masukkan ke sprint, sesuaikan scope/timeline |
| **Defer** | Ditunda ke rilis berikutnya | Masukkan ke backlog dengan prioritas, catat di change log |
| **Reject** | Ditolak | Catat alasan penolakan, informasikan pemohon |

### Tahap 4: Implement — Eksekusi

- Update PRD, user story, dan acceptance criteria **sebelum** coding
- Update traceability matrix
- Develop dan test sesuai perubahan
- Pastikan perubahan tidak merusak fitur existing (regression test)

### Tahap 5: Verify — Verifikasi

- QA memverifikasi bahwa perubahan sesuai CR
- Product Owner meng-accept perubahan
- Change log di-update dengan status final

## Format Change Request

Setiap CR harus memuat informasi berikut:

```markdown
## Change Request

**CR ID:** CR-2024-015
**Tanggal:** 2024-03-15
**Pemohon:** Rina (Customer Support Lead)
**Status:** ⏳ Pending Review

### Deskripsi Perubahan
Menambahkan opsi "Bayar Nanti" (pay later) pada halaman checkout untuk pesanan
di bawah Rp 500.000.

### Alasan / Justifikasi Bisnis
- 35% customer meninggalkan checkout karena belum siap bayar (data analytics Q4)
- Kompetitor A dan B sudah menyediakan fitur serupa
- Estimasi peningkatan conversion rate: 10-15%

### Requirement yang Terdampak
- FR-CHK-01: Alur checkout (perlu tambah opsi payment method)
- FR-PAY-01: Payment processing (perlu integrasi pay-later provider)
- FR-ORD-03: Order status (perlu tambah status "AWAITING_PAYMENT")
- NFR-SEC-02: Fraud detection (perlu aturan baru untuk pay-later)

### Analisis Dampak

**Scope:**
- 4 user story baru
- 2 user story existing perlu dimodifikasi
- 1 integrasi third-party baru (pay-later provider)

**Timeline:**
- Estimasi effort: 3 person-weeks
- Jika diterima sekarang: rilis mundur 1 sprint (2 minggu)
- Alternatif: kurangi scope fitur "Wishlist" untuk buat ruang

**Cost:**
- Development effort: 3 person-weeks
- Biaya integrasi pay-later provider: Rp 5jt/bulan
- Biaya testing tambahan: 1 person-week

**Risk:**
- Risiko fraud meningkat jika aturan pay-later tidak ketat
- Risiko regresi pada alur checkout existing
- Dependensi pada API stability pay-later provider

### Keputusan
**Status:** ✅ Accepted
**Diputuskan oleh:** Budi (Product Manager), 2024-03-18
**Catatan:** Diterima dengan scope dikurangi — hanya untuk verified user, limit Rp 300.000.
Fitur Wishlist di-defer ke sprint berikutnya untuk buat ruang.
```

## Change Control Board (CCB)

### Apa Itu CCB?

CCB adalah kelompok kecil yang punya otoritas untuk menyetujui atau menolak perubahan requirement. Tujuannya: **keputusan cepat oleh orang yang tepat**, bukan rapat besar yang memperlambat.

### Komposisi CCB

| Peran | Tanggung Jawab di CCB | Wajib Ada? |
|---|---|---|
| **Product Owner / PM** | Menilai value bisnis dan prioritas | Ya |
| **Engineering Lead** | Menilai feasibility, effort, dan risiko teknis | Ya |
| **QA Lead** | Menilai dampak terhadap test coverage dan regresi | Ya |
| **Sponsor / Stakeholder** | Menilai dampak terhadap budget dan business goal | Untuk CR besar |
| **UX Designer** | Menilai dampak terhadap user experience | Jika ada perubahan UI |

### Skala untuk Tim Kecil

**Solo dev:** Kamu adalah CCB. Yang penting: **tulis keputusan dan alasannya**. Jangan hanya ubah kode — catat CR meski singkat.

**Tim 2-3 orang:** CCB = semua anggota tim. Diskusi 15 menit di standup atau async di Slack/chat.

**Tim 4-8 orang:** CCB = PO + Tech Lead + QA Lead. Review CR di weekly meeting atau async dengan deadline respon 48 jam.

### Kapan CCB Diperlukan?

Tidak semua perubahan perlu CCB formal:

| Tipe Perubahan | Perlu CCB? | Contoh |
|---|---|---|
| **Kosmetik / typo** | Tidak | Fix typo di label, ubah warna minor |
| **Bug fix** | Tidak (kecuali mengubah behavior) | Fix null pointer, fix validasi |
| **Perubahan requirement kecil** | Review cepat (async) | Tambah 1 field di form, ubah batas validasi |
| **Perubahan requirement besar** | Ya, CCB formal | Fitur baru, ubah alur bisnis, integrasi baru |
| **Perubahan arsitektur** | Ya, CCB + Architecture Review | Migrasi database, ganti framework |

## Prosedur Impact Analysis

Saat CR masuk, lakukan analisis dampak dengan checklist ini:

### Checklist Impact Analysis

```markdown
## Impact Analysis Checklist — CR-[ID]

### Requirement Impact
- [ ] Requirement lain yang terdampak sudah diidentifikasi
- [ ] Acceptance criteria yang berubah sudah di-list
- [ ] User story baru / modifikasi sudah diestimasi

### Technical Impact
- [ ] Modul / service yang harus diubah sudah diidentifikasi
- [ ] Perubahan database schema sudah dianalisis (migrasi data?)
- [ ] API contract yang berubah sudah diidentifikasi (breaking change?)
- [ ] Dependensi third-party yang terdampak sudah dicek
- [ ] Backward compatibility sudah dipertimbangkan

### Testing Impact
- [ ] Test case yang harus diubah / ditambah sudah di-list
- [ ] Scope regression test sudah ditentukan
- [ ] Test environment yang dibutuhkan sudah tersedia

### Timeline & Resource Impact
- [ ] Effort tambahan sudah diestimasi (dalam person-days/weeks)
- [ ] Dampak terhadap sprint/milestone saat ini sudah dihitung
- [ ] Fitur lain yang harus di-defer sudah diidentifikasi (trade-off)

### Risk Assessment
- [ ] Risiko regresi sudah dinilai (Low/Medium/High)
- [ ] Risiko integrasi sudah dinilai
- [ ] Mitigation plan sudah disiapkan untuk risiko Medium/High
```

## Pencegahan Scope Creep

Scope creep terjadi saat perubahan kecil terus masuk tanpa kontrol, sehingga cakupan proyek membengkak tanpa disadari.

### Tanda-tanda Scope Creep

- Sprint velocity terus turun meski tim tidak berubah
- Tanggal rilis bergeser berulang kali
- Fitur "sedikit tambahan" yang tidak pernah berakhir
- Tim merasa selalu "hampir selesai" tapi tidak pernah benar-benar selesai
- Tidak ada yang bisa menjelaskan scope rilis saat ini dengan jelas

### Strategi Pencegahan

1. **Semua perubahan lewat CR** — sekecil apapun, kalau mengubah requirement, catat
2. **Trade-off eksplisit** — setiap fitur masuk = fitur lain keluar atau timeline bergeser. Tidak boleh "tambah saja"
3. **Freeze period** — 1-2 sprint sebelum rilis, tidak ada CR baru yang diterima (kecuali critical bug)
4. **Visualisasi scope** — tampilkan burndown/burnup chart yang menunjukkan scope creep secara visual
5. **Review CR count** — jika ada >3 CR per sprint, tanyakan: "Apakah requirement gathering kita cukup baik?"

## Maintenance Change Log

Setiap perubahan yang diterima harus dicatat di change log untuk audit trail.

### Format Change Log

| CR ID | Tanggal | Deskripsi Singkat | Dampak | Keputusan | Diputuskan Oleh |
|---|---|---|---|---|---|
| CR-001 | 2024-01-10 | Tambah login via Google | 2 story baru, +1 sprint | Accepted | Budi (PM) |
| CR-002 | 2024-01-22 | Ubah batas upload file 5MB → 10MB | 1 story modifikasi | Accepted | Budi (PM) |
| CR-003 | 2024-02-05 | Integrasi loyalty program | 8 story baru, +3 sprint | Deferred ke v2.0 | Budi (PM) + Andi (CTO) |
| CR-004 | 2024-02-15 | Hapus fitur dark mode dari MVP | -2 story | Accepted (scope reduction) | Budi (PM) |

> Template lengkap: [Change Log Template](../templates/governance/change-log-template.md)

### Tips Maintenance

1. **Update real-time** — jangan tunggu akhir sprint untuk update change log
2. **Sertakan alasan** — bukan hanya "Rejected" tapi "Rejected karena effort 3x lipat dari value"
3. **Link ke artefak** — link CR ke issue tracker, PR, dan requirement yang terdampak
4. **Review di retrospective** — berapa banyak CR per sprint? Apakah ada pola? Apa yang bisa diperbaiki di requirement gathering?

## Kesalahan Umum

| Kesalahan | Dampak | Cara Menghindari |
|---|---|---|
| **Tidak ada proses CR** | Perubahan masuk tanpa kontrol → scope creep | Mulai simple: form singkat + keputusan tercatat |
| **Proses terlalu berat** | Tim bypass proses → kembali ke tanpa kontrol | Sesuaikan berat proses dengan ukuran perubahan |
| **Tidak ada impact analysis** | Perubahan diterima tanpa tahu konsekuensi | Minimal: effort estimate + daftar item terdampak |
| **CR tanpa trade-off** | Scope terus bertambah tanpa ada yang dikorbankan | Selalu tanyakan: "Apa yang kita kurangi untuk buat ruang?" |
| **Tidak melibatkan engineering** | Keputusan bisnis tanpa input teknis → estimasi meleset | Engineering Lead wajib ada di CCB |
| **Change log tidak di-maintain** | 3 bulan kemudian tidak ada yang ingat kenapa scope berubah | Jadikan bagian dari Definition of Done |

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — dokumen yang harus di-update saat CR diterima
- [Acceptance Criteria](./acceptance-criteria.md) — AC yang berubah akibat CR
- [Prioritization](./prioritization.md) — re-prioritisasi setelah CR diterima
- [Requirements Traceability](./requirements-traceability.md) — update traceability matrix setelah perubahan
- [Change Log Template](../templates/governance/change-log-template.md) — template siap pakai
