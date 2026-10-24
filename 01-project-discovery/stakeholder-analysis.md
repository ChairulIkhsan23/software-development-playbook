# Stakeholder Analysis

## 1. Apa Itu Stakeholder?

Stakeholder adalah siapa saja yang **terpengaruh oleh** atau **dapat mempengaruhi** proyek. Ini bukan hanya sponsor atau boss — termasuk user akhir, tim support, compliance, bahkan vendor eksternal.

### Mengapa Identifikasi Stakeholder Sejak Awal?

- **Mencegah kejutan di akhir** — stakeholder yang tidak dilibatkan sejak awal sering menolak atau meminta perubahan besar saat sudah terlambat.
- **Memahami kebutuhan yang sebenarnya** — setiap stakeholder punya perspektif berbeda tentang masalah dan solusi.
- **Mengamankan dukungan** — proyek tanpa sponsor yang jelas sering kehilangan prioritas dan anggaran.
- **Mengelola ekspektasi** — komunikasi yang tepat ke orang yang tepat mencegah miskomunikasi.

---

## 2. Power-Interest Grid

Framework klasik untuk mengkategorikan stakeholder berdasarkan dua dimensi: **kekuasaan** (kemampuan mempengaruhi proyek) dan **minat** (seberapa peduli terhadap proyek).

```
                    POWER (Kekuasaan)
                 Rendah          Tinggi
            ┌─────────────┬─────────────┐
    Tinggi  │ Keep        │ Manage      │
  INTEREST  │ Informed    │ Closely     │
   (Minat)  ├─────────────┼─────────────┤
    Rendah  │ Monitor     │ Keep        │
            │             │ Satisfied   │
            └─────────────┴─────────────┘
```

### Penjelasan Tiap Kuadran

| Kuadran | Contoh Stakeholder | Strategi | Taktik Konkret |
|---------|-------------------|----------|----------------|
| **Manage Closely** (High Power, High Interest) | Sponsor eksekutif, Business Owner, Product Owner | Kelola secara intensif — mereka bisa membatalkan atau mengubah arah proyek | Update mingguan, undang ke semua review session, libatkan dalam keputusan desain utama |
| **Keep Satisfied** (High Power, Low Interest) | CTO, Tim Compliance, Tim Legal, CISO | Jaga kepuasan — mereka jarang terlibat tapi bisa mem-veto keputusan | Laporan ringkas bulanan, libatkan hanya untuk approval gate, jangan banjiri dengan detail |
| **Keep Informed** (Low Power, High Interest) | End user, Tim operasional, Tim support/helpdesk | Berikan informasi rutin — mereka yang paling terdampak | Demo berkala, beta testing, feedback session, newsletter progress |
| **Monitor** (Low Power, Low Interest) | Vendor non-kritis, Tim di departemen lain yang tidak terkait langsung | Pantau dengan effort minimal | Update email bulanan atau quarterly, libatkan hanya jika ada kebutuhan spesifik |

### Cara Mengisi Power-Interest Grid

1. **Brainstorm daftar stakeholder** — list semua orang/tim/organisasi yang terlibat atau terdampak.
2. **Nilai power masing-masing** — tanyakan: "Bisakah orang ini menghentikan atau mengubah proyek secara signifikan?"
3. **Nilai interest masing-masing** — tanyakan: "Seberapa peduli orang ini terhadap hasil proyek?"
4. **Plot di grid** dan tentukan strategi komunikasi.
5. **Review berkala** — posisi stakeholder bisa berubah seiring proyek berjalan.

---

## 3. Matriks RACI

RACI adalah framework untuk menjelaskan **siapa melakukan apa** dalam setiap keputusan atau deliverable proyek.

### Penjelasan Setiap Huruf

| Huruf | Peran | Aturan | Contoh |
|-------|-------|--------|--------|
| **R** — Responsible | Orang yang **mengeksekusi** pekerjaan | Bisa lebih dari 1 orang per task | Developer yang menulis kode, designer yang membuat mockup |
| **A** — Accountable | Orang yang **bertanggung jawab** atas hasil akhir dan membuat keputusan final | **Hanya 1 orang** per task — ini aturan kritis | Product Owner yang menyetujui requirements, Tech Lead yang sign-off arsitektur |
| **C** — Consulted | Orang yang **dimintai masukan** sebelum keputusan dibuat (komunikasi dua arah) | Dimintai pendapat aktif | Subject matter expert, UX researcher, security engineer |
| **I** — Informed | Orang yang **diberi tahu** setelah keputusan dibuat (komunikasi satu arah) | Tidak perlu feedback — hanya notifikasi | Management, tim terkait di departemen lain |

### Contoh Matriks RACI

| Deliverable | Product Owner | Tech Lead | Developer | UX Designer | Sponsor |
|-------------|:---:|:---:|:---:|:---:|:---:|
| Problem Statement | A | C | I | C | I |
| User Research | C | I | I | R, A | I |
| Technical Architecture | C | A | R | I | I |
| Sprint Backlog | A | C | R | C | I |
| Release Decision | C | C | I | I | A |

### Kapan Menggunakan RACI

- Saat ada kebingungan tentang siapa yang memutuskan apa.
- Saat proyek melibatkan lebih dari 3 orang.
- Saat ada keputusan cross-functional (melibatkan beberapa tim).

### Aturan Validasi RACI

- Setiap baris **harus punya tepat 1 A** — jika tidak ada, tidak ada yang accountable. Jika lebih dari 1, tidak ada yang benar-benar accountable.
- Jika seseorang selalu muncul sebagai A — cek apakah orang itu bottleneck.
- Jika seseorang tidak muncul sama sekali — pertanyakan apakah mereka memang stakeholder.

---

## 4. Stakeholder Register

Template untuk mendokumentasikan stakeholder secara terstruktur:

| Field | Deskripsi | Contoh |
|-------|-----------|--------|
| **Nama/Role** | Nama atau jabatan stakeholder | Bu Ratna — Head of Finance |
| **Organisasi/Tim** | Unit kerja | Divisi Keuangan |
| **Kategori di Grid** | Posisi di Power-Interest Grid | Manage Closely |
| **Kepentingan Utama** | Apa yang paling mereka pedulikan? | Akurasi laporan keuangan, kepatuhan regulasi |
| **Potensi Penolakan** | Apa yang bisa membuat mereka menolak? | Perubahan workflow yang terlalu drastis |
| **Cara Komunikasi** | Channel dan frekuensi yang mereka preferensi | Meeting tatap muka mingguan, email untuk update formal |
| **Role RACI** | Peran dalam matriks RACI | Accountable untuk approval financial reports |
| **Catatan** | Informasi tambahan | Pernah punya pengalaman buruk dengan proyek IT sebelumnya, perlu pendekatan hati-hati |

---

## 5. Rencana Komunikasi Dasar

Setelah stakeholder dipetakan, buat rencana komunikasi sederhana:

| Kelompok Stakeholder | Frekuensi | Format | Konten | PIC |
|---------------------|-----------|--------|--------|-----|
| Manage Closely | Mingguan | Meeting 30 menit | Progress, blocker, keputusan yang dibutuhkan | Product Owner |
| Keep Satisfied | Bulanan/per milestone | Email ringkas + 1-pager | Status summary, risk highlight, approval yang dibutuhkan | Project Manager |
| Keep Informed | Per sprint/2 minggu | Demo session atau newsletter | Fitur baru, timeline update, cara memberi feedback | Product Owner |
| Monitor | Quarterly atau ad-hoc | Email broadcast | Overview status proyek | Project Manager |

**Tips untuk solo developer atau tim kecil:**

- Tidak perlu dokumen formal yang rumit — cukup satu halaman atau tabel di Notion/Google Docs.
- Yang penting: **siapa perlu tahu apa, kapan, dan lewat channel apa.**
- Jika kamu solo dev, minimal punya 1 orang sebagai "sounding board" untuk keputusan penting.

---

## 6. Kesalahan Umum

| Kesalahan | Dampak | Pencegahan |
|-----------|--------|------------|
| **Melupakan end user sebagai stakeholder** | Membangun sistem yang tidak dipakai karena tidak sesuai kebutuhan pengguna | Selalu masukkan end user di quadrant Keep Informed atau Manage Closely |
| **Memperlakukan semua stakeholder sama** | Waktu habis untuk meeting dengan orang yang sebenarnya cukup di-email | Gunakan Power-Interest Grid untuk menentukan strategi komunikasi |
| **Tidak ada yang Accountable (A)** | Keputusan menggantung, tidak ada yang berani memutuskan | Pastikan setiap deliverable/keputusan punya tepat 1 orang Accountable |
| **Mengabaikan silent stakeholder** | Stakeholder yang tidak vokal tapi punya power tinggi bisa mem-veto di akhir | Identifikasi siapa yang punya power veto walaupun jarang terlihat |
| **Stakeholder register dibuat sekali lalu dilupakan** | Posisi dan interest stakeholder berubah seiring proyek berjalan | Review stakeholder map minimal per fase proyek |
| **Terlalu banyak orang di "Manage Closely"** | Meeting overhead terlalu besar, proyek melambat | Batasi Manage Closely ke 3-5 orang max. Sisanya Keep Satisfied atau Keep Informed |

---

## 7. Dokumen Terkait

- [Problem Discovery](problem-discovery.md) — temuan masalah yang menjadi basis pemetaan stakeholder.
- [User Research](user-research.md) — riset mendalam terhadap stakeholder di quadrant "Keep Informed" (end user).
- [Assumptions and Constraints](assumptions-and-constraints.md) — asumsi tentang stakeholder yang perlu divalidasi.
- [Discovery Exit Criteria](discovery-exit-criteria.md) — stakeholder map adalah salah satu exit criteria discovery.
- [Feasibility Analysis](feasibility-analysis.md) — stakeholder key mempengaruhi penilaian kelayakan operasional.
