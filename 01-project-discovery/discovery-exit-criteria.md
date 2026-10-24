# Discovery Exit Criteria

## 1. Tujuan Exit Criteria

Exit criteria adalah **gerbang kualitas** yang memastikan discovery sudah cukup matang sebelum lanjut ke tahap [Product Requirements](../02-product-requirements/). Tanpa exit criteria yang jelas:

- Tim terlalu cepat pindah ke solutioning padahal masalah belum dipahami.
- Atau sebaliknya — terjebak di discovery tanpa akhir (*analysis paralysis*).

Exit criteria memberikan **definisi "selesai" yang objektif** untuk fase discovery.

---

## 2. Minimum Viable Discovery Checklist

Checklist ini adalah **standar minimum** yang harus dipenuhi sebelum proyek lanjut ke fase requirements. Sesuaikan level of detail berdasarkan ukuran dan risiko proyek (lihat Bagian 5).

### ✅ Problem Validated (Masalah Tervalidasi)

- [ ] Problem statement telah ditulis menggunakan template standar.
- [ ] Masalah dikonfirmasi oleh minimal 3-5 user/stakeholder independen.
- [ ] Ada data pendukung (kuantitatif atau kualitatif) yang membuktikan masalah nyata.
- [ ] Problem statement telah disepakati oleh sponsor bisnis dan lead engineer.

**Bukti:** Dokumen [Problem Discovery](problem-discovery.md) yang sudah di-review dan disetujui.

### ✅ Users Identified (Pengguna Teridentifikasi)

- [ ] Minimal 5 user telah diwawancara atau diobservasi.
- [ ] Persona pengguna utama telah dibuat (minimal 2, maksimal 4 untuk proyek kecil-menengah).
- [ ] Alur kerja inti (core workflow) pengguna saat ini telah dipetakan.
- [ ] Pain points utama telah diprioritaskan berdasarkan frekuensi dan intensitas.

**Bukti:** Dokumen [User Research](user-research.md) dengan persona dan temuan wawancara.

### ✅ Stakeholders Mapped (Stakeholder Terpetakan)

- [ ] Daftar stakeholder telah dibuat lengkap dengan role dan kepentingan.
- [ ] Power-Interest Grid telah diisi dan strategi komunikasi per kuadran ditentukan.
- [ ] Matriks RACI telah dibuat untuk deliverable/keputusan utama.
- [ ] Sponsor proyek (Accountable utama) telah dikonfirmasi.

**Bukti:** Dokumen [Stakeholder Analysis](stakeholder-analysis.md) yang sudah divalidasi.

### ✅ Feasibility Assessed (Kelayakan Dinilai)

- [ ] Lima pilar kelayakan telah dievaluasi (teknis, finansial, operasional, jadwal, legal).
- [ ] Setiap pilar memiliki penilaian: ✅ Layak, ⚠️ Perlu Mitigasi, atau ❌ Tidak Layak.
- [ ] Item yang ⚠️ memiliki rencana mitigasi yang jelas.
- [ ] Tidak ada pilar yang ❌ tanpa resolusi.

**Bukti:** Dokumen [Feasibility Analysis](feasibility-analysis.md) dengan checklist yang terisi.

### ✅ Assumptions Documented (Asumsi Terdokumentasi)

- [ ] Semua asumsi kunci telah dituliskan di assumption mapping matrix.
- [ ] Setiap asumsi memiliki penilaian confidence dan impact.
- [ ] Asumsi high-impact, low-confidence telah divalidasi atau punya rencana validasi yang jelas.
- [ ] Constraint (batasan) proyek telah dikonfirmasi dengan pemiliknya.
- [ ] Dependency telah diidentifikasi dan di-track.

**Bukti:** Dokumen [Assumptions and Constraints](assumptions-and-constraints.md) yang terisi lengkap.

### ✅ Risks Identified (Risiko Teridentifikasi)

- [ ] Risiko-risiko awal telah diidentifikasi (minimal dari asumsi yang tervalidasi invalid).
- [ ] Risiko dengan dampak kritis (High Impact) memiliki rencana mitigasi.
- [ ] Risk owner telah ditunjuk untuk setiap risiko kritis.

**Bukti:** Risk register awal (bisa berupa tabel sederhana di dokumen assumptions atau dokumen terpisah).

### ✅ Open Questions Tracked (Pertanyaan Terbuka Ter-track)

- [ ] Daftar pertanyaan yang belum terjawab telah didokumentasikan.
- [ ] Setiap pertanyaan memiliki PIC dan target tanggal untuk dijawab.
- [ ] Tidak ada pertanyaan terbuka yang bersifat blocking untuk memulai requirements.

**Bukti:** Daftar open questions (bisa di-track di issue tracker, spreadsheet, atau dokumen terpisah).

---

## 3. Gate 1 Decision: Go / No-Go / Pivot

Setelah checklist di atas diisi, lakukan **Gate 1 Review** — sesi review formal dengan stakeholder kunci.

### Peserta Gate Review

| Role | Kontribusi |
|------|------------|
| **Sponsor Bisnis** | Konfirmasi masalah dan business case masih valid |
| **Product Owner** | Presentasi temuan discovery |
| **Tech Lead / Lead Engineer** | Konfirmasi kelayakan teknis |
| **Stakeholder "Keep Satisfied"** | Approval jika diperlukan (compliance, legal, security) |

### Keputusan yang Mungkin

#### GO ✅ — Lanjut ke Product Requirements

**Syarat:**

- Semua item di checklist ter-check (✅).
- Semua pilar kelayakan menunjukkan ✅ atau ⚠️ dengan mitigasi.
- Stakeholder kunci menyetujui untuk lanjut.

**Output:** Discovery notes yang disahkan, project brief yang bisa digunakan sebagai input fase requirements.

#### NO-GO ❌ — Hentikan atau Tunda

**Syarat:**

- Satu atau lebih pilar kelayakan ❌ tanpa resolusi.
- Masalah ternyata tidak cukup signifikan untuk diinvestasikan.
- Risiko terlalu tinggi tanpa mitigasi yang jelas.

**Output:** Dokumentasi alasan no-go. Simpan — ini bisa jadi referensi jika kondisi berubah di masa depan.

#### PIVOT 🔄 — Ubah Pendekatan

**Syarat:**

- Masalah valid tapi pendekatan perlu diubah (scope, teknis, timeline).
- Perlu discovery tambahan untuk area tertentu.

**Output:** Scope yang direvisi, lalu kembali ke checklist yang perlu di-update sebelum Gate Review ulang.

---

## 4. Template Ringkasan Discovery

Gunakan template ini sebagai output akhir fase discovery:

```
## Discovery Summary — [Nama Proyek]

**Tanggal Gate Review:** [dd/mm/yyyy]
**Peserta:** [Nama-nama peserta review]

### Problem Statement
[Copy dari dokumen problem discovery — 2-3 kalimat]

### Target User
[Persona utama dan jumlah estimasi user]

### Feasibility Status
- Teknis: [✅ / ⚠️ / ❌] — [catatan singkat]
- Finansial: [✅ / ⚠️ / ❌] — [catatan singkat]
- Operasional: [✅ / ⚠️ / ❌] — [catatan singkat]
- Jadwal: [✅ / ⚠️ / ❌] — [catatan singkat]
- Legal: [✅ / ⚠️ / ❌] — [catatan singkat]

### Risiko Utama
1. [Risiko 1 — mitigasi]
2. [Risiko 2 — mitigasi]
3. [Risiko 3 — mitigasi]

### Open Questions
1. [Pertanyaan — PIC — target date]

### Keputusan Gate 1
[GO / NO-GO / PIVOT]

### Catatan & Next Steps
[Apa yang harus dilakukan selanjutnya]

**Disetujui oleh:**
- [ ] [Nama Sponsor] — Sponsor Bisnis
- [ ] [Nama Tech Lead] — Lead Engineer
```

---

## 5. Scaling Discovery Berdasarkan Risiko Proyek

Tidak semua proyek butuh discovery yang sama mendalamnya. Sesuaikan effort berdasarkan risiko:

### Proyek Risiko Rendah

**Contoh:** Fitur tambahan di produk existing, bug fix besar, internal tool sederhana.

| Aspek | Level Effort |
|-------|-------------|
| **Durasi discovery** | 1-3 hari |
| **Wawancara user** | 2-3 orang, informal |
| **Stakeholder mapping** | List sederhana, tanpa grid formal |
| **Feasibility** | Quick check verbal dengan tech lead |
| **Dokumentasi** | 1 halaman ringkasan |
| **Gate review** | Persetujuan via chat/email dari sponsor |

### Proyek Risiko Sedang

**Contoh:** Produk baru untuk use case yang dipahami, migrasi sistem, integrasi major.

| Aspek | Level Effort |
|-------|-------------|
| **Durasi discovery** | 1-2 minggu |
| **Wawancara user** | 5-8 orang |
| **Stakeholder mapping** | Power-Interest Grid + RACI dasar |
| **Feasibility** | Checklist 5 pilar diisi formal |
| **Dokumentasi** | Semua dokumen dalam modul ini diisi |
| **Gate review** | Meeting 1 jam dengan stakeholder kunci |

### Proyek Risiko Tinggi

**Contoh:** Produk baru di domain baru, proyek dengan regulasi ketat, proyek dengan budget > Rp 1 miliar.

| Aspek | Level Effort |
|-------|-------------|
| **Durasi discovery** | 4-6 minggu |
| **Wawancara user** | 10-15 orang + observasi lapangan |
| **Stakeholder mapping** | Grid + RACI + stakeholder register lengkap |
| **Feasibility** | Analisis mendalam per pilar dengan data pendukung, mungkin perlu PoC teknis |
| **Dokumentasi** | Semua dokumen + appendix dengan raw data |
| **Gate review** | Presentasi formal 2 jam + sesi Q&A |

---

## 6. Kapan Harus Kembali ke Discovery

Discovery bukan fase one-off. Kembali ke discovery jika:

- **Asumsi kritis terbukti salah** setelah development dimulai.
- **Scope berubah signifikan** — lebih dari 30% fitur berubah dari rencana awal.
- **Stakeholder baru muncul** dengan kebutuhan yang belum dipetakan.
- **Kondisi pasar/regulasi berubah** yang mempengaruhi problem statement.
- **User feedback di UAT** menunjukkan masalah fundamental yang tidak tertangkap.
- **Pivot terjadi** — target user, business model, atau pendekatan teknis berubah.

> **Tips:** Kembali ke discovery bukan berarti mulai dari nol. Fokus hanya pada area yang berubah dan perlu validasi ulang.

---

## 7. Dokumen Terkait

- [Problem Discovery](problem-discovery.md) — exit criteria pertama: masalah tervalidasi.
- [User Research](user-research.md) — exit criteria kedua: user teridentifikasi.
- [Stakeholder Analysis](stakeholder-analysis.md) — exit criteria ketiga: stakeholder terpetakan.
- [Feasibility Analysis](feasibility-analysis.md) — exit criteria keempat: kelayakan dinilai.
- [Assumptions and Constraints](assumptions-and-constraints.md) — exit criteria kelima: asumsi terdokumentasi.
- [Discovery Readiness Checklist](../checklists/discovery-readiness.md) — checklist operasional untuk validasi kesiapan.
