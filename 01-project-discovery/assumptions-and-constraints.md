# Assumptions and Constraints

## 1. Definisi

Empat konsep ini sering tercampur. Penting untuk membedakan karena masing-masing butuh penanganan berbeda:

| Konsep | Definisi | Contoh | Penanganan |
|--------|----------|--------|------------|
| **Assumption (Asumsi)** | Hal yang **diyakini benar** saat ini tapi **belum diverifikasi**. Jika salah, bisa jadi risiko. | "User mau pakai SSO perusahaan untuk login." | Validasi sesegera mungkin. |
| **Constraint (Batasan)** | Pembatas yang **tidak bisa dinegosiasikan**. Harus diterima sebagai fakta. | "Budget maksimal Rp 200 juta." "Harus comply UU PDP." | Desain solusi dalam batas ini. |
| **Dependency (Ketergantungan)** | Sesuatu yang **harus tersedia** atau **selesai terlebih dahulu** agar proyek bisa berjalan. | "API payment gateway harus ready sebelum fitur checkout bisa dibangun." | Track dan monitor progress-nya. |
| **Risk (Risiko)** | Kejadian yang **mungkin terjadi** dan berdampak negatif pada proyek. | "Vendor API mungkin mengubah pricing model di Q3." | Mitigasi — buat plan B. |

### Hubungan Antar Konsep

```
Assumption yang TIDAK divalidasi → berubah jadi Risk
Dependency yang GAGAL terpenuhi → berubah jadi Blocker
Constraint yang DILANGGAR → berubah jadi masalah legal/bisnis/teknis
```

---

## 2. Mengapa Asumsi Harus Eksplisit?

Setiap proyek berjalan di atas asumsi — masalahnya, kebanyakan asumsi **tidak pernah dituliskan** dan tidak pernah divalidasi. Dampaknya:

- **Masing-masing orang punya asumsi berbeda** — developer asumsi A, product owner asumsi B. Tidak ada yang sadar sampai konflik muncul.
- **Asumsi yang salah baru ketahuan terlambat** — saat development sudah berjalan 3 bulan, ternyata asumsi fundamental salah.
- **Tidak ada mekanisme validasi** — karena tidak tertulis, tidak ada yang bertanggung jawab memverifikasinya.

> **Aturan sederhana:** Jika kamu pakai kata "pasti", "harusnya", "kayaknya", atau "asumsi saya" dalam diskusi — itu sinyal untuk menuliskan asumsi dan merencakan validasinya.

---

## 3. Assumption Mapping Matrix

Peta semua asumsi proyek dengan detail berikut:

| ID | Pernyataan Asumsi | Confidence (H/M/L) | Impact jika Salah (H/M/L) | Rencana Validasi | Target Validasi | Status |
|----|-------------------|:---:|:---:|---|---|---|
| ASM-01 | User bersedia login pakai SSO perusahaan | Medium | High | Survei 20 calon user | Minggu ke-2 | ✅ Validated |
| ASM-02 | API vendor X bisa handle 500 RPS | Low | High | Stress test di sandbox | Minggu ke-3 | ⏳ Pending |
| ASM-03 | Tim finance bisa migrasi data dari Excel ke sistem baru sendiri | Medium | Medium | Wawancara 3 staff finance | Minggu ke-2 | ❌ Invalid — perlu bantuan |
| ASM-04 | Mobile browser cukup, tidak perlu native app | High | Medium | Cek device analytics existing system | Minggu ke-1 | ✅ Validated |
| ASM-05 | Tidak ada perubahan regulasi pelaporan di Q4 | Low | High | Cek ke tim legal | Minggu ke-4 | ⏳ Pending |

### Prioritaskan Validasi Berdasarkan Risiko

```
                    IMPACT jika salah
                 Rendah          Tinggi
            ┌─────────────┬─────────────┐
    Rendah  │ Monitor     │ VALIDASI    │
CONFIDENCE  │ saja        │ SEGERA! ⚠️  │
            ├─────────────┼─────────────┤
    Tinggi  │ Skip atau   │ Validasi    │
            │ monitor     │ saat ada    │
            │             │ kesempatan  │
            └─────────────┴─────────────┘
```

**Fokus utama:** Asumsi dengan **Confidence rendah + Impact tinggi**. Ini bom waktu yang harus segera divalidasi.

---

## 4. Kategori Constraint

### 4.1 Batasan Teknis

- Stack teknologi yang harus digunakan (misal: harus .NET karena standar perusahaan)
- Browser/device yang harus disupport (misal: IE11 masih dipakai oleh client)
- Sistem legacy yang harus diintegrasikan
- Limitasi infrastruktur (misal: on-premise only, tidak boleh cloud)
- Batasan performance (misal: response time < 2 detik untuk 99th percentile)

### 4.2 Batasan Bisnis

- Budget total proyek
- Timeline dan deadline yang sudah dikomunikasikan ke stakeholder
- Jumlah dan ketersediaan tim
- Prioritas relatif terhadap proyek lain di organisasi
- Brand guidelines atau standar UI perusahaan

### 4.3 Batasan Regulasi & Legal

- UU Pelindungan Data Pribadi (UU PDP) — jika mengelola data personal
- Standar industri (ISO 27001, PCI-DSS untuk payment, HIPAA untuk kesehatan)
- Regulasi sektoral (OJK untuk fintech, Kominfo untuk telekomunikasi)
- Lisensi software — open source license compatibility
- Data residency — data harus disimpan di wilayah tertentu

### 4.4 Batasan Waktu

- Hard deadline (misal: harus live sebelum regulasi baru berlaku 1 Januari)
- Event-driven deadline (misal: harus ready untuk pameran bulan depan)
- Seasonal constraint (misal: tidak boleh deploy saat peak season e-commerce)

### 4.5 Batasan Budget

- Capex — biaya development (orang, tools, lisensi)
- Opex — biaya operasional bulanan (hosting, SaaS subscription, support)
- Batasan per-item (misal: "tidak mau bayar lebih dari $500/bulan untuk cloud")

---

## 5. Dependency Tracking

Dependency yang tidak di-track akan menjadi blocker mendadak. Format tracking:

| ID | Dependency | Tipe | Pemilik | Target Date | Status | Dampak jika Terlambat |
|----|-----------|------|---------|-------------|--------|----------------------|
| DEP-01 | API payment gateway ready | External | Vendor Y | 15 Mar | 🟢 On track | Fitur checkout tertunda |
| DEP-02 | Design system v2 selesai | Internal | Tim Design | 1 Mar | 🟡 At risk | UI development pakai component lama |
| DEP-03 | Server production provisioned | Internal | Tim Infra | 20 Mar | 🟢 On track | Tidak bisa deploy ke production |
| DEP-04 | Data migrasi dari sistem lama | Internal | Tim Data | 10 Mar | 🔴 Delayed | UAT tidak bisa pakai data real |

### Tipe Dependency

- **Internal** — bergantung pada tim atau resource di dalam organisasi.
- **External** — bergantung pada vendor, partner, atau pihak ketiga.
- **Regulatory** — bergantung pada keputusan regulator atau kebijakan baru.

> **Tips:** External dependency lebih berisiko karena kamu punya kontrol lebih sedikit. Berikan buffer waktu lebih besar.

---

## 6. Bagaimana Asumsi Berubah Jadi Risiko

```
ASM-02: "API vendor X bisa handle 500 RPS"
  ↓ (stress test dilakukan)
  ↓ Hasil: API hanya handle 200 RPS

Asumsi INVALID → menjadi RISK:
  - Risiko: Sistem tidak bisa handle load production
  - Probabilitas: High (sudah terbukti)
  - Impact: High (core functionality terganggu)
  - Mitigasi: Negosiasi dengan vendor untuk upgrade tier,
              atau implementasi caching + rate limiting
```

**Proses formal:**

1. Validasi asumsi sesuai rencana.
2. Jika asumsi terbukti salah → pindahkan ke risk register.
3. Tentukan probabilitas dan impact.
4. Buat rencana mitigasi.
5. Assign pemilik risiko (risk owner).

Lihat panduan risk management lebih lengkap di [08-project-management](../08-project-management/).

---

## 7. Kesalahan Umum

| Kesalahan | Dampak | Pencegahan |
|-----------|--------|------------|
| **Tidak membedakan asumsi dan fakta** | Tim berjalan berdasarkan keyakinan yang belum terbukti | Setiap claim yang belum diverifikasi = asumsi, bukan fakta |
| **Menulis asumsi tapi tidak punya rencana validasi** | Dokumen jadi formalitas tanpa value | Setiap asumsi di matrix HARUS punya kolom "Rencana Validasi" dan "Target Date" |
| **Menganggap constraint bisa dinegosiasikan** | Konflik dengan legal/compliance/budget di kemudian hari | Konfirmasi setiap constraint dengan pemiliknya (misal: budget dengan finance, regulasi dengan legal) |
| **Tidak track dependency** | Blocker muncul mendadak dan menghentikan development | Buat dependency list sejak awal, review mingguan |
| **Asumsi hanya di kepala, tidak tertulis** | Setiap orang berasumsi hal berbeda tanpa sadar | Wajibkan sesi "assumption surfacing" di awal proyek — 30 menit brainstorm semua asumsi |
| **Terlalu banyak asumsi high-risk** | Proyek dibangun di atas fondasi yang rapuh | Jika ada >5 asumsi high-impact low-confidence, pertanyakan apakah proyek siap dilanjutkan |

---

## 8. Dokumen Terkait

- [Problem Discovery](problem-discovery.md) — sumber utama asumsi awal tentang masalah dan user.
- [Feasibility Analysis](feasibility-analysis.md) — constraint mempengaruhi penilaian kelayakan.
- [Stakeholder Analysis](stakeholder-analysis.md) — stakeholder kunci bisa mengkonfirmasi atau membantah asumsi.
- [User Research](user-research.md) — riset user adalah cara utama memvalidasi asumsi tentang kebutuhan pengguna.
- [Discovery Exit Criteria](discovery-exit-criteria.md) — asumsi high-risk harus tervalidasi sebelum exit discovery.
