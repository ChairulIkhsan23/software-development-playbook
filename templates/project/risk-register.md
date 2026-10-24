---
id: DOC-RSK-001
title: Project Risk Register
project: "[Nama Proyek]"
version: 1.0.0
author: "[Nama Project Lead / Risk Owner]"
date: "[YYYY-MM-DD]"
status: Active # Pilihan: Draft / Active / Archived
---

# Project Risk Register: [Nama Proyek]

> **Panduan Pengisian:**
> Dokumen ini digunakan untuk mencatat, mengevaluasi, memitigasi, dan memantau seluruh potensi risiko teknis, operasional, dan bisnis selama siklus hidup proyek. Format ID risiko: `RSK-MOD-NNN` (berdasarkan modul) atau `RSK-NNN`. Nilai Skor Risiko dihitung dari perkalian Probabilitas x Dampak (1 - 25).

---

## 1. Panduan Penilaian Matriks Risiko 5x5 [Wajib]

> **Panduan Pengisian:** Gunakan acuan skala berikut untuk menjaga konsistensi penilaian antar anggota tim.

| Nilai | Skala Probabilitas (P) | Skala Dampak / Severity (D) |
|:---:|---|---|
| **1** | Sangat Jarang (< 10% kemungkinan terjadi) | Sangat Rendah: Gangguan minor, tidak berdampak pada jadwal/biaya |
| **2** | Jarang (10% - 30% kemungkinan terjadi) | Rendah: Penundaan kecil (< 2 hari), dapat diatasi internal tim |
| **3** | Sedang (31% - 60% kemungkinan terjadi) | Sedang: Jadwal sprint bergeser 3-5 hari, butuh penyesuaian fitur |
| **4** | Sering (61% - 80% kemungkinan terjadi) | Tinggi: Penundaan rilis > 1 minggu, biaya membengkak > 20% |
| **5** | Sangat Sering (> 80% hampir pasti terjadi) | Kritis: Proyek terancam gagal total, isu regulasi/keamanan data fatal |

### Klasifikasi Skor Risiko:
- **1 - 4 (Low / Hijau):** Dapat diterima (*Accept*), pantau berkala tanpa alokasi anggaran khusus.
- **5 - 9 (Medium / Kuning):** Butuh mitigasi preventif terencana dan penanggung jawab aktif.
- **10 - 15 (High / Oranye):** Risiko serius; membutuhkan intervensi kepemimpinan dan rencana kontinjensi matang.
- **16 - 25 (Critical / Merah):** Risiko fatal; wajib eskalasi segera ke Project Sponsor, blocker proyek.

### Kategori Risiko Standar:
- **Teknis:** Bug arsitektur, integrasi API pihak ketiga, kinerja database, reliabilitas infrastruktur.
- **Operasional & Tim:** Kehilangan talenta kunci, bottleneck proses persetujuan, keterbatasan perangkat.
- **Bisnis & Scope:** Perubahan regulasi hukum/kebijakan, scope creep dari stakeholder, pemangkasan anggaran.
- **Keamanan & Kepatuhan:** Kebocoran data pribadi (UU PDP), kerentanan dependensi open-source, kegagalan audit.

---

## 2. Tabel Risk Register [Wajib]

> **Panduan Pengisian:** Isi tabel di bawah untuk setiap risiko yang teridentifikasi. Pilihan strategi penanganan: *Avoid* (Hindari), *Mitigate* (Kurangi), *Transfer* (Alihkan ke pihak ketiga), *Accept* (Terima).

| ID Risiko | Kategori | Deskripsi Risiko | P (1-5) | D (1-5) | Skor (PxD) | Strategi | Rencana Aksi Preventif (Action Plan) | Rencana Kontinjensi (Contingency Plan) | Pemilik (Owner) | Indikator Pemicu (Trigger Sign) | Status |
|---|---|---|:---:|:---:|:---:|---|---|---|---|---|---|
| RSK-001 | Teknis | API payment gateway eksternal lambat / sering timeout | 3 | 4 | 12 | Mitigate | Pasang circuit breaker, asynchronous queue, dan fallback retry | Alihkan otomatis ke channel pembayaran alternatif (QRIS statis) | [Nama Backend Lead] | Error rate HTTP 504 > 5% dalam 10 menit | Open |
| RSK-002 | Scope | Perubahan regulasi format laporan audit di tengah sprint | 2 | 4 | 8 | Mitigate | Desain modul pelaporan berbasis konfigurasi dinamis (JSON schema) | Tambah buffer sprint 1 minggu dan negosiasi ulang tanggal audit | [Nama PM] | Notifikasi resmi dari tim Legal / Regulator | Open |
| RSK-003 | Sumber Daya | Engineer kunci mengundurkan diri saat fase pengembangan | 2 | 5 | 10 | Mitigate | Dokumentasikan ADR, wajibkan pair programming & code review ketat | Sewa konsultan kontraktor jangka pendek (staff augmentation) | [Nama Project Lead] | Pengajuan notice resign resmi | Open |
| RSK-004 | Keamanan | Potensi kebocoran kredensial API secret ke repositori | 2 | 5 | 10 | Avoid | Pasang pre-commit hook secret scanner (Trufflehog/GitLeaks) | Revoke secret langsung, rotasi API key, dan audit log akses | [Nama DevOps] | Alert security pipeline CI/CD gagal | Open |

---

## 3. Detail Rencana Mitigasi Risiko Kritis [Kondisional]
*(Seksi ini kondisional: dapat dihapus jika tidak ada risiko dengan skor >= 10)*

> **Panduan Pengisian:** Uraikan rincian eksekusi kontinjensi untuk risiko berkategori High dan Critical.

### Prosedur Eskalasi RSK-001 (Kegagalan Gateway Pembayaran)
- **Kondisi Aktivasi:** Jika transaksi gagal berturut-turut selama 15 menit.
- **Langkah 1:** Engineer on-call mengaktifkan feature flag `PAYMENT_FALLBACK_MODE=true`.
- **Langkah 2:** Tampilkan banner pemeliharaan halus (*graceful degradation*) pada frontend checkout.
- **Langkah 3:** Kirim notifikasi status ke channel Slack `#war-room-incident`.

---

## 4. Pedoman Irama Tinjauan Risiko (Review Cadence) [Wajib]

> **Panduan Pengisian:** Tetapkan jadwal berkala untuk meninjau status risiko dan mendeteksi risiko baru.

- **Tinjauan Mingguan (Weekly Risk Sync):** Dilakukan 15 menit setiap awal sesi sprint planning oleh Tech Lead dan PM.
- **Kriteria Penutupan Risiko (Closed):** Risiko dinyatakan `Closed` jika penyebab utama telah dihilangkan atau fase proyek terkait telah terlewati secara sukses.
- **Arsip Risiko:** Risiko berstatus `Closed` dipindahkan ke lembar catatan retrospektif proyek sebagai basis pengetahuan (*lessons learned*).
