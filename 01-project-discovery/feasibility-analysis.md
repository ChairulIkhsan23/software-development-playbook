# Feasibility Analysis

## 1. Apa Itu Feasibility Analysis?

Feasibility analysis adalah evaluasi sistematis untuk menjawab pertanyaan fundamental: **"Bisakah dan haruskah proyek ini dilanjutkan?"**

Tujuannya bukan untuk menghentikan proyek, tapi untuk:

- **Mengidentifikasi risiko besar** sebelum investasi signifikan.
- **Memberi dasar keputusan** go/no-go/pivot yang rasional, bukan emosional.
- **Menetapkan ekspektasi realistis** tentang apa yang mungkin dicapai.

> **Prinsip:** Lebih baik menghentikan proyek di fase discovery (biaya: waktu beberapa minggu) daripada setelah 6 bulan development (biaya: ratusan juta + opportunity cost).

---

## 2. Lima Pilar Kelayakan

### Pilar 1: Kelayakan Teknis — "Bisakah kita membangunnya?"

Mengevaluasi apakah solusi bisa dibangun dengan teknologi dan keahlian yang tersedia.

**Pertanyaan kunci:**

| Aspek | Pertanyaan | Red Flag 🚩 |
|-------|-----------|-------------|
| **Teknologi** | Apakah teknologi yang dibutuhkan sudah matang dan stabil? | Bergantung pada teknologi experimental atau beta |
| **Keahlian tim** | Apakah tim menguasai stack yang diperlukan? | Tidak ada yang pernah pakai teknologi ini sebelumnya |
| **Integrasi** | Bisakah diintegrasikan dengan sistem existing? | Sistem legacy tanpa API, dokumentasi hilang |
| **Skalabilitas** | Bisakah handle beban yang diharapkan? | Kebutuhan 10x dari yang pernah ditangani tim |
| **Data** | Apakah data yang dibutuhkan tersedia dan berkualitas? | Data tersebar, inkonsisten, atau tidak ada |
| **Infrastruktur** | Apakah infra yang dibutuhkan tersedia? | Butuh GPU cluster tapi budget hanya untuk shared hosting |

### Pilar 2: Kelayakan Finansial — "Bisakah kita membiayainya?"

Mengevaluasi apakah proyek masuk akal secara ekonomi.

**Komponen yang perlu dihitung:**

| Kategori | Item | Estimasi |
|----------|------|----------|
| **Capex (Biaya Pembangunan)** | Gaji tim development (X orang × Y bulan) | Rp ... |
| | Lisensi tools dan software | Rp ... |
| | Hardware/infrastruktur awal | Rp ... |
| **Opex (Biaya Operasional/bulan)** | Cloud hosting | Rp ... /bulan |
| | Lisensi SaaS berulang | Rp ... /bulan |
| | Support dan maintenance | Rp ... /bulan |
| **Benefit (Nilai yang Didapat)** | Penghematan waktu manual | Rp ... /bulan |
| | Pengurangan error cost | Rp ... /bulan |
| | Revenue tambahan (jika ada) | Rp ... /bulan |

**Metrik keputusan:**

- **ROI** = (Total Benefit - Total Cost) / Total Cost × 100%
- **Payback period** = Total Capex / Net Monthly Benefit
- **Rule of thumb untuk proyek internal:** Payback period > 3 tahun → pertanyakan kembali.

### Pilar 3: Kelayakan Operasional — "Bisakah kita menjalankannya?"

Mengevaluasi apakah organisasi siap mengadopsi dan mengoperasikan solusi.

**Pertanyaan kunci:**

| Aspek | Pertanyaan | Red Flag 🚩 |
|-------|-----------|-------------|
| **Change management** | Apakah user bersedia mengubah cara kerja? | Resistensi kuat dari user kunci |
| **Training** | Berapa effort training yang dibutuhkan? | User perlu training berminggu-minggu |
| **Support** | Siapa yang handle support setelah live? | Tidak ada tim support yang ditugaskan |
| **Proses bisnis** | Apakah SOP perlu berubah? | Perubahan proses bisnis fundamental yang belum disetujui management |
| **Adopsi** | Apakah bisa di-rollout bertahap? | Harus big-bang release tanpa fallback |

### Pilar 4: Kelayakan Jadwal — "Bisakah selesai tepat waktu?"

Mengevaluasi apakah timeline yang ditetapkan realistis.

**Pertanyaan kunci:**

| Aspek | Pertanyaan | Red Flag 🚩 |
|-------|-----------|-------------|
| **Scope vs timeline** | Apakah scope sesuai dengan waktu yang tersedia? | 6 bulan scope dipaksa masuk 2 bulan |
| **Ketersediaan tim** | Apakah tim available full-time atau shared? | Semua developer di-share dengan 3 proyek lain |
| **Dependency** | Apakah ada external dependency yang bisa delay? | Bergantung pada vendor dengan track record terlambat |
| **Hard deadline** | Apakah ada deadline yang tidak bisa digeser? | Regulasi berlaku 1 Januari, tanpa grace period |
| **Buffer** | Apakah ada buffer untuk hal tak terduga? | Timeline sudah pas-pasan tanpa buffer |

**Estimasi kasar:**

- Ambil estimasi optimis tim, **kalikan 1.5-2x** untuk proyek dengan banyak unknowns.
- Jika setelah dikalikan masih melebihi deadline → scope harus dikurangi atau deadline dinegosiasi.

### Pilar 5: Kelayakan Legal & Regulasi — "Bolehkah kita membangunnya?"

Mengevaluasi apakah solusi memenuhi aturan hukum dan regulasi yang berlaku.

**Pertanyaan kunci:**

| Aspek | Pertanyaan | Red Flag 🚩 |
|-------|-----------|-------------|
| **Data privacy** | Apakah comply dengan UU PDP / GDPR? | Mengolah data sensitif tanpa mekanisme consent |
| **Lisensi** | Apakah lisensi semua komponen kompatibel? | Pakai library GPL di produk proprietary |
| **Industri** | Apakah ada regulasi sektoral yang berlaku? | Fintech tanpa izin OJK, healthtech tanpa sertifikasi |
| **Data residency** | Apakah data harus disimpan di lokasi tertentu? | Regulasi mengharuskan data di Indonesia, tapi pakai cloud region Singapore |
| **Aksesibilitas** | Apakah ada kewajiban aksesibilitas? | Produk pemerintah tanpa standar WCAG |

---

## 3. Checklist Penilaian Kelayakan

Gunakan checklist ini untuk setiap pilar. Skor: ✅ Layak, ⚠️ Perlu Mitigasi, ❌ Tidak Layak.

```
## Feasibility Assessment — [Nama Proyek]

### Kelayakan Teknis
- [ ] Teknologi yang dibutuhkan sudah matang dan dikuasai tim
- [ ] Integrasi dengan sistem existing memungkinkan (API tersedia, dokumentasi ada)
- [ ] Tidak ada single point of failure pada teknologi kunci
- [ ] Data yang dibutuhkan tersedia, dapat diakses, dan berkualitas memadai
- [ ] Infrastruktur yang dibutuhkan tersedia atau bisa di-provision

### Kelayakan Finansial
- [ ] Estimasi Capex sudah dihitung dan dalam batas budget
- [ ] Estimasi Opex bulanan sudah dihitung dan sustainable
- [ ] ROI atau payback period masuk akal untuk tipe proyek ini
- [ ] Ada budget contingency (minimal 15-20% dari total)

### Kelayakan Operasional
- [ ] User target bersedia mengadopsi sistem baru (sudah divalidasi)
- [ ] Ada rencana training untuk user
- [ ] Ada tim/orang yang bertanggung jawab untuk support pasca-launch
- [ ] Perubahan proses bisnis yang diperlukan sudah diidentifikasi

### Kelayakan Jadwal
- [ ] Timeline realistis dengan scope yang ditetapkan
- [ ] Tim tersedia (tidak over-committed ke proyek lain)
- [ ] External dependency sudah di-track dan on-schedule
- [ ] Ada buffer minimal 20% untuk hal tak terduga

### Kelayakan Legal & Regulasi
- [ ] Kepatuhan data privacy sudah dicek (UU PDP / GDPR)
- [ ] Lisensi semua komponen/library compatible
- [ ] Regulasi sektoral yang berlaku sudah diidentifikasi dan dipenuhi
- [ ] Tidak ada hambatan legal yang memblokir development
```

---

## 4. Framework Keputusan: Go / No-Go / Pivot

Setelah penilaian kelayakan selesai, ada tiga kemungkinan keputusan:

### GO ✅

**Kriteria:** Semua 5 pilar menunjukkan hasil ✅ atau ⚠️ dengan mitigasi yang jelas.

- Lanjutkan ke fase [Product Requirements](../02-product-requirements/).
- Dokumentasikan mitigasi untuk item ⚠️ sebagai bagian dari risk register.

### NO-GO ❌

**Kriteria:** Satu atau lebih pilar menunjukkan ❌ dan tidak ada mitigasi yang feasible.

- Hentikan proyek atau tunda sampai kondisi berubah.
- Dokumentasikan alasan no-go — ini bukan kegagalan, ini penghematan.
- Komunikasikan ke stakeholder dengan jelas dan profesional.

**Contoh no-go yang valid:**

- Teknologi kunci belum matang (kelayakan teknis ❌).
- Budget hanya cukup untuk 30% dari scope minimum (kelayakan finansial ❌).
- Regulasi baru mengharuskan pendekatan yang sama sekali berbeda (kelayakan legal ❌).

### PIVOT 🔄

**Kriteria:** Masalah layak diselesaikan, tapi pendekatan harus berubah.

- Ubah scope (kurangi ke MVP yang lebih kecil).
- Ubah pendekatan teknis (beli vs build, SaaS vs custom).
- Ubah timeline (fase bertahap vs big-bang).
- Ubah target user (mulai dari segment yang lebih kecil).

**Contoh pivot:**

- "Custom ERP terlalu mahal → pakai SaaS ERP + custom integration saja."
- "Full mobile app tidak feasible dalam 3 bulan → mulai dengan responsive web app."

---

## 5. Trade-offs yang Harus Disadari

Setiap keputusan kelayakan melibatkan trade-off. Yang umum:

| Trade-off | Pilihan A | Pilihan B |
|-----------|-----------|-----------|
| **Build vs Buy** | Kontrol penuh, customizable, tapi mahal dan lama | Cepat, lebih murah awal, tapi terbatas dan vendor lock-in |
| **Scope vs Timeline** | Fitur lengkap tapi butuh 12 bulan | MVP tapi bisa live 3 bulan |
| **Quality vs Speed** | Test coverage tinggi, CI/CD lengkap, tapi development lebih lambat | Deliver cepat tapi technical debt menumpuk |
| **Cloud vs On-premise** | Scalable, managed, tapi biaya recurring dan data di luar | Kontrol penuh, one-time cost, tapi butuh tim infra sendiri |
| **Custom vs Framework** | Sesuai kebutuhan persis, tapi effort besar | Banyak built-in, tapi mungkin perlu workaround |

> **Tips:** Tidak ada pilihan yang sempurna. Dokumentasikan trade-off yang dipilih dan alasannya — ini penting untuk context saat review di kemudian hari.

---

## 6. Kesalahan Umum

| Kesalahan | Dampak | Pencegahan |
|-----------|--------|------------|
| **Skip feasibility karena "sudah jelas"** | Masalah fundamental baru ketahuan di tengah development | Luangkan minimal 2-3 hari untuk assessment formal |
| **Hanya cek kelayakan teknis** | Proyek bisa dibangun tapi tidak ada yang mau pakai atau tidak cukup budget | Selalu evaluasi ke-5 pilar |
| **Estimasi biaya terlalu optimis** | Budget habis sebelum proyek selesai | Tambahkan buffer 20-30% dan hitung Opex, bukan hanya Capex |
| **Tidak mempertimbangkan cost of NOT doing** | Menolak proyek yang sebenarnya menghemat biaya lebih besar | Hitung juga biaya jika masalah dibiarkan (opportunity cost) |
| **Feasibility analysis sebagai formalitas** | Dokumen diisi tapi tidak mempengaruhi keputusan | Pastikan ada gate review resmi dengan keputusan go/no-go/pivot yang terdokumentasi |
| **Mengabaikan kelayakan operasional** | Sistem live tapi tidak ada yang bisa maintain | Identifikasi siapa yang support setelah launch sejak awal |

---

## 7. Dokumen Terkait

- [Problem Discovery](problem-discovery.md) — masalah yang divalidasi menjadi input feasibility analysis.
- [Assumptions and Constraints](assumptions-and-constraints.md) — constraint langsung mempengaruhi penilaian kelayakan.
- [Stakeholder Analysis](stakeholder-analysis.md) — stakeholder kunci terlibat dalam keputusan go/no-go.
- [Discovery Exit Criteria](discovery-exit-criteria.md) — feasibility assessment adalah salah satu exit criteria.
- [User Research](user-research.md) — temuan riset memvalidasi kelayakan operasional (adopsi user).
