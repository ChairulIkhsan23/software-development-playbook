# Deprecation & System Migration Lifecycle

Panduan terstruktur untuk memensiunkan (*deprecate*) API, fitur aplikasi, atau subsistem usang secara aman tanpa merusak ekosistem pengguna dan layanan hilir.

---

## 1. Mengapa Deprecation Butuh Proses Formal?

Menghapus fitur atau mengubah skema API secara mendadak (*breaking changes*) memicu downtime tak terduga pada klien eksternal maupun service internal.

Tujuan dari proses deprecation formal:
1. Memberikan waktu yang memadai bagi konsumen API untuk merencanakan dan menguji migrasi mereka.
2. Meminimalisir beban pemeliharaan kode warisan (*legacy code*) yang menghambat kelincahan tim engineering.
3. Menjamin transparansi komunikasi dan menjaga reputasi profesional penyedia layanan.

---

## 2. Lima Tahap Siklus Deprecation (5-Stage Process)

```
[1. Announcement] ---> [2. Soft Deprecation] ---> [3. Brownout Testing] ---> [4. Hard Deprecation] ---> [5. Decommissioning]
  (H-180 hari)             (Headers & Logs)            (Chaos / Latihan)           (Read-Only / 410)           (Code Removal)
```

### Tahap 1: Assessment & Announcement (H - 180 Hari)
- **Analisis Dampak (Telemetry Audit):** Periksa log akses untuk melihat siapa saja konsumen aktif yang masih memanggil endpoint/fitur tersebut (Client ID, IP, frekuensi request).
- **Penetapan Sunset Date:** Tentukan tanggal resmi penonaktifan. Standar industri untuk public API adalah minimal **6 bulan (180 hari)**, sedangkan untuk internal service tim kecil minimal **30-60 hari**.
- **Pengumuman Resmi:** Publikasikan melalui release notes, email broadcast ke developer terdaftar, dan banner dokumentasi pengembang.

### Tahap 2: Soft Deprecation (H - 120 Hari)
Sistem tetap melayani traffic secara normal, tetapi mulai memancarkan sinyal peringatan:
- **Standar HTTP Header (RFC 8594 / RFC 9213):**
  ```http
  HTTP/1.1 200 OK
  Content-Type: application/json
  Deprecation: @1735689600
  Sunset: Wed, 01 Jul 2026 00:00:00 GMT
  Link: <https://api.example.com/docs/v2-migration>; rel="deprecation"; type="text/html"
  ```
- **Runtime Logger Warnings:** Catat peringatan dengan tag `[DEPRECATED_ENDPOINT_CALLED]` lengkap dengan identitas pemanggil untuk memudahkan tracing.
- **In-App Notices:** Jika fitur visual di UI, tampilkan peringatan halus (*subtle banner*) kepada end-user bahwa alur tersebut akan digantikan oleh halaman baru.

### Tahap 3: Brownout Testing (H - 30 Hari & H - 14 Hari)
Banyak konsumen tidak membaca dokumentasi atau mengabaikan header sampai aplikasinya benar-benar gagal. 
- **Simulasi Gangguan Terencana:** Nonaktifkan endpoint lama secara berkala selama durasi terbatas di jam kerja (misal: 1 jam pada hari Selasa minggu ke-1, lalu 4 jam pada minggu ke-3).
- **Response Khusus Brownout:** Kembalikan response HTTP `503 Service Unavailable` atau `429 Too Many Requests` dengan response payload informatif:
  ```json
  {
    "error": "BROWNOUT_TESTING",
    "message": "Endpoint ini sedang dalam uji coba brownout. Fitur akan dinonaktifkan permanen pada 01 Juli 2026. Silakan migrasi ke /api/v2/orders.",
    "migration_guide": "https://api.example.com/docs/v2-migration"
  }
  ```
- Brownout memaksa tim yang lalai untuk segera menginvestigasi log kegagalan mereka sebelum *sunset date* tiba.

### Tahap 4: Hard Deprecation (Hari H Sunset Date)
- Endpoint resmi dinonaktifkan dari traffic produksi normal.
- **HTTP 410 Gone:** Kembalikan status HTTP `410 Gone` (bukan sekadar `404 Not Found`) untuk menegaskan kepada web crawler dan client bahwa resource ini sengaja dihapus selamanya.
- **Read-Only Fallback:** Jika berupa modul database/data store, kunci tabel menjadi *read-only* agar tidak ada mutasi data baru yang masuk.

### Tahap 5: Complete Decommissioning (Hari H + 30 Hari)
Setelah masa tenggang pasca-sunset date berlalu tanpa eskalasi darurat:
- **Code Removal:** Hapus route handlers, model, DTO, helper functions terkait dari codebase (manfaatkan git history jika butuh referensi lama).
- **Database Cleanup:** Buat skrip migrasi untuk menghapus kolom atau tabel usang (*drop table/column* setelah backup).
- **Infrastructure Teardown:** Hentikan VM, container pods, DNS records, load balancer rules, atau serverless functions yang terdedikasi untuk sistem lama.

---

## 3. Pola Transisi Arsitektur (Adapter / Facade Pattern)

Saat melakukan refactor backend tanpa ingin langsung merusak API v1, gunakan pola adapter untuk meneruskan request v1 ke service v2:

```
[Client v1] ---> [API v1 Route (Adapter Layer)] ---> [Core Business Logic v2]
[Client v2] ----------------------------------------^
```

- **Keuntungan:** Logika bisnis tidak terduplikasi. Perubahan core logic otomatis dirasakan oleh kedua endpoint.
- **Kapan Dihapus:** Adapter layer dihapus total pada Tahap 5 (Complete Decommissioning).

---

## 4. Format Dokumen Panduan Migrasi (Migration Guide)

Panduan migrasi yang baik harus meminimalkan friksi tim pengembang konsumen:

| Bagian Wajib | Tujuan | Contoh Isi |
|---|---|---|
| **Ringkasan Perubahan** | Menjelaskan motivasi migrasi | "v2 beralih ke UUID v7 dan skema pagination berbasis cursor untuk efisiensi kueri." |
| **Field Mapping Table** | Kamus pemetaan skema data | `old_field: user_name` digantikan menjadi `new_field: account.displayName`. |
| **Code Comparison** | Perbandingan langsung pemanggilan | Menampilkan request JSON & cURL v1 vs v2 secara berdampingan. |
| **Breaking Behavior** | Perubahan logika atau validasi | "Field `phone_number` kini wajib diawali kode negara format E.164 (`+62...`)." |
| **Dukungan & FAQ** | Jalur eskalasi bantuan | Kanal Slack `#api-support` atau email `dev-support@example.com`. |

---

## 5. Checklist Pelaksanaan Deprecation

Gunakan checklist ini sebelum mencabut fitur apa pun:
- [ ] Telemetri membuktikan traffic ke endpoint lama sudah `0` atau hanya berasal dari bot/crawler yang tidak terdaftar.
- [ ] Pengumuman resmi telah dikirim minimal 6 bulan (public) atau 30 hari (internal) sebelumnya.
- [ ] Header HTTP `Deprecation` dan `Sunset` telah dipasang minimal selama 90 hari.
- [ ] Brownout test telah dijalankan minimal dua kali dengan pemberitahuan sebelumnya.
- [ ] Migration guide telah direview dan diuji oleh engineer independen.
- [ ] Snapshot backup database skema lama telah disimpan ke cold storage sebelum proses *drop table*.

---

## 6. Kesalahan Umum (Common Mistakes)

1. **Silent Deprecation:** Mengubah atau mematikan API tanpa pengumuman sebelumnya, menyebabkan klien crash mendadak di production.
2. **Abadi di Tahap Soft Deprecation:** Memasang banner "Fitur ini akan segera dipensiunkan" selama bertahun-tahun tanpa pernah berani menetapkan *sunset date* final.
3. **Melewatkan Brownout Testing:** Langsung melakukan hard shutdown di hari H, lalu panik ketika ada klien besar internal yang sistemnya lumpuh karena mereka tidak pernah membaca email notifikasi.
4. **Meninggalkan Zombie Code:** Mematikan route API di gateway, tetapi membiarkan 5.000 baris kode controller, model, dan unit test usang tetap bersarang di codebase utama.

---

## 7. Dokumen Terkait

- [Technical Debt](technical-debt.md) — Menghapus kode peninggalan deprecation untuk menekan architectural debt.
- [Dependency Updates](dependency-updates.md) — Mengelola siklus deprecation pada library dependensi pihak ketiga.
- [Post-Release Review](post-release-review.md) — Evaluasi dampak terhadap kepuasan pengguna setelah penghapusan fitur lama.
