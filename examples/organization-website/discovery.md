# Catatan Discovery: Situs Web Yayasan Bina Karya

```yaml
document_id: DOC-DISC-002
project: Organization Website
organization: Yayasan Bina Karya Nusantara
author: "Lead Business Analyst & Volunteer Tech Lead"
date: 2026-03-10
status: Completed
```

---

## 1. Latar Belakang & Identifikasi Masalah

Yayasan Bina Karya Nusantara telah menyelenggarakan pelatihan keterampilan vokasi kerja untuk lebih dari 1.200 anak muda pra-sejahtera selama 5 tahun terakhir. Namun, eksistensi digital yayasan saat ini mengalami sejumlah kendala:

1. **Situs Lama Usang**: Website lama menggunakan template WordPress tidak terawat yang sering terkena malware, lambat dimuat (> 7 detik di jaringan 3G), dan memiliki skor Google Lighthouse hanya 32.
2. **Hambatan Pendanaan**: Calon donatur institusi dan korporasi kesulitan memverifikasi legalitas dan laporan transparansi program yayasan karena dokumen tersebar dalam bentuk file PDF yang rusak.
3. **Ketergantungan Teknis**: Setiap kali staf program ingin mengunggah dokumentasi pelatihan baru, mereka harus menunggu bantuan sukarelawan IT eksternal.

---

## 2. Analisis Stakeholder

| Stakeholder | Peran / Kepentingan | Strategi Komunikasi |
|---|---|---|
| **Ketua Yayasan** | Sponsor utama; menginginkan citra yayasan yang profesional dan kredibel untuk menjalin kemitraan BUMN/CSR. | Laporan mingguan progress, persetujuan tata letak halaman utama (Homepage). |
| **Tim Humas & Program** | Pengguna admin utama; membutuhkan antarmuka penulisan berita kegiatan yang mudah dipahami tanpa coding. | Workshop uji coba CMS, panduan tertulis editorial. |
| **Calon Donatur / Mitra** | Pembaca eksternal; mencari laporan kegiatan, nomor rekening resmi yayasan, dan legalitas izin Kemenkumham. | Evaluasi kenyamanan navigasi dan kecepatan muat halaman. |
| **Calon Siswa Pelatihan** | Mengakses jadwal pendaftaran pelatihan melalui smartphone berspesifikasi rendah dengan kuota terbatas. | Optimasi ukuran payload halaman (< 500 KB) dan responsive layout. |

---

## 3. Persona Pengguna Kunci

### Persona 1: Ibu Ratna (48 Tahun, Perwakilan CSR Perusahaan)
- **Karakteristik**: Mengakses website melalui laptop kantor saat melakukan uji kelayakan (*due diligence*) program bantuan sosial.
- **Tujuan**: Menemukan laporan audit keuangan tahunan yayasan dan legalitas akta notaris dalam waktu kurang dari 1 menit.

### Persona 2: Bayu (19 Tahun, Calon Peserta Pelatihan)
- **Karakteristik**: Mengakses via smartphone Android lama di area dengan sinyal internet 4G lemah.
- **Tujuan**: Mengetahui persyaratan program pelatihan montir gratis dan cara mendaftar secara online.

---

## 4. Analisis Kelayakan (Feasibility Analysis)

- **Kelayakan Teknis**: Sangat Layak. Menggunakan framework Next.js Static Site Generation (SSG) yang menghasilkan file HTML/CSS statis. Tidak memerlukan server komputasi aktif terus-menerus.
- **Kelayakan Finansial**: Sangat Layak. Hosting static di Cloudflare Pages / Vercel memanfaatkan tier gratis untuk organisasi nirlaba. Biaya operasional tahunan hanya domain `.or.id` (kurang dari Rp 100.000 / tahun).
- **Kelayakan Operasional**: Layak. Tim humas dilatih menggunakan CMS berbasis web sederhana yang terhubung otomatis ke repository GitHub.

---

## 5. Kriteria Keluar Discovery (Gate 1 Exit Criteria)

- [x] Masalah utama tervalidasi dan disepakati oleh dewan pengurus yayasan.
- [x] Persona donatur dan calon siswa telah dipetakan secara jelas.
- [x] Batasan scope non-profit dan biaya hosting nol rupiah telah diverifikasi.
- [x] Stakeholder sepakat untuk melanjutkan ke penyusunan PRD.
