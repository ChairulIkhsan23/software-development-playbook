# UI States and Error Messages

## 1. Mengapa UI State Penting?

Setiap halaman dan komponen tidak hanya punya satu tampilan. Pengguna mengalami berbagai **kondisi data dan sistem** — dan jika kita hanya mendesain "happy state", pengguna akan menemukan tampilan kosong, spinner tanpa akhir, atau error tanpa penjelasan.

> **Aturan:** Tidak ada halaman yang boleh go-live tanpa semua 7 state dipertimbangkan.

## 2. Tujuh State Wajib

### 2.1 Ideal State (Filled / Populated)

Tampilan optimal saat semua data tersedia dan sistem berjalan lancar.

**Apa yang ditampilkan:** Data lengkap, layout penuh, semua elemen terisi.

**Tips:**
- Ini yang biasanya didesain pertama — tapi jangan berhenti di sini
- Gunakan data realistis (bukan "John Doe" dan "Lorem ipsum")
- Test dengan data edge case: nama sangat panjang, angka sangat besar, deskripsi kosong

### 2.2 Empty State (Zero Data)

Kondisi saat belum ada data — pengguna baru, daftar kosong, hasil pencarian nol.

**Apa yang ditampilkan:**
- Ilustrasi atau ikon sederhana (jangan terlalu besar)
- Pesan singkat yang menjelaskan kenapa kosong
- **Call to Action (CTA)** — beri langkah berikutnya

**Contoh:**

| Baik ✅ | Buruk ❌ |
|---|---|
| "Belum ada pesanan. Mulai belanja sekarang!" [Tombol: Lihat Produk] | "No data." |
| "Pencarian tidak menemukan hasil. Coba kata kunci lain." | "0 results" |
| "Belum ada anggota tim. Undang rekan kerja Anda." [Tombol: Undang] | Halaman putih kosong |

### 2.3 Loading State (Skeleton / Shimmer)

Tampilan selama data sedang diambil dari server.

**Apa yang ditampilkan:**
- **Skeleton screen** — kotak abu-abu yang bentuknya mirip layout final, dengan shimmer animation
- **BUKAN** spinner full-screen yang menutupi semua konten

**Guidelines:**
- Tampilkan skeleton yang bentuknya mirip konten yang akan muncul
- Jika ada bagian halaman yang datanya sudah ready, tampilkan dulu (progressive loading)
- Untuk aksi cepat (<300ms), tidak perlu loading indicator — langsung tampilkan hasil
- Untuk aksi 300ms-1s, gunakan subtle indicator (progress bar, skeleton)
- Untuk aksi >1s, skeleton screen + pesan "Memuat data..."

### 2.4 Error State

Tampilan saat terjadi kegagalan — network error, server error, data tidak ditemukan.

**Apa yang ditampilkan:**
- Pesan error yang jelas (lihat bagian Error Messages di bawah)
- **Tombol retry** atau instruksi langkah berikutnya
- Jangan hapus konten yang sudah ada — jika sebagian data berhasil dimuat, pertahankan

**Jenis error:**

| Error | Contoh | Yang Ditampilkan |
|---|---|---|
| Network error | Koneksi putus | "Tidak ada koneksi internet. Periksa jaringan Anda." + Retry |
| Server error (500) | Backend down | "Terjadi gangguan pada sistem kami. Coba lagi nanti." + Retry |
| Not found (404) | URL salah / data dihapus | "Halaman tidak ditemukan." + Link ke beranda |
| Forbidden (403) | Tidak punya akses | "Anda tidak memiliki akses ke halaman ini." + Hubungi admin |
| Timeout | Respons terlalu lama | "Permintaan terlalu lama. Coba lagi." + Retry |

### 2.5 Partial State (Degraded)

Tampilan saat **sebagian** data berhasil dimuat, tapi sebagian gagal.

**Apa yang ditampilkan:**
- Tampilkan data yang berhasil
- Bagian yang gagal: tampilkan placeholder dengan pesan error kecil + retry
- Jangan tampilkan halaman error penuh hanya karena satu section gagal

**Contoh:** Dashboard — grafik utama berhasil dimuat, tapi widget "penjualan hari ini" gagal. Tampilkan grafik, widget menampilkan "Gagal memuat. Klik untuk coba lagi."

### 2.6 Disabled State

Elemen yang ada tapi belum bisa diinteraksi karena kondisi belum terpenuhi.

**Guidelines:**
- Tampilkan elemen dengan **opacity rendah** (50-60%) dan **cursor not-allowed**
- Jika memungkinkan, jelaskan **kenapa** disabled (tooltip atau teks kecil di bawah)
- Jangan hide elemen — disabled lebih baik dari hidden karena pengguna tahu fitur itu ada

**Contoh:**
- Tombol "Bayar" disabled → tooltip: "Pilih metode pembayaran terlebih dahulu"
- Input tanggal akhir disabled → teks: "Pilih tanggal mulai dulu"

### 2.7 Success State (Confirmation)

Tampilan setelah aksi berhasil dilakukan.

**Apa yang ditampilkan:**
- Konfirmasi singkat bahwa aksi berhasil
- **Langkah berikutnya** yang jelas
- Detail ringkasan (untuk aksi penting seperti pembayaran)

**Contoh:**
- Form tersimpan → toast: "Data berhasil disimpan" (auto-dismiss 5 detik)
- Pesanan dibuat → halaman konfirmasi: nomor pesanan, estimasi pengiriman, tombol "Lihat Pesanan"
- Akun dibuat → redirect ke dashboard + welcome message

## 3. Pedoman Penulisan Error Messages

### Prinsip Utama

| Prinsip | Penjelasan |
|---|---|
| **Jelaskan apa yang terjadi** | Pengguna harus paham masalahnya |
| **Sarankan solusi** | Beri tahu apa yang bisa dilakukan |
| **Jangan salahkan pengguna** | Bukan "Anda salah memasukkan...", tapi "Format email belum sesuai" |
| **Jangan pakai jargon teknis** | Bukan "Error 500 Internal Server", tapi "Terjadi gangguan pada sistem kami" |
| **Singkat dan spesifik** | Bukan "Terjadi kesalahan", tapi "Password harus minimal 8 karakter" |

### Contoh Error Messages

#### Form Validation

| Buruk ❌ | Baik ✅ |
|---|---|
| "Invalid input" | "Format email belum sesuai. Contoh: nama@email.com" |
| "Field required" | "Nama lengkap harus diisi" |
| "Password error" | "Password harus minimal 8 karakter dengan 1 huruf besar dan 1 angka" |
| "Invalid date" | "Tanggal akhir harus setelah tanggal mulai" |
| "Error" | "Nomor telepon hanya boleh berisi angka (10-13 digit)" |

#### System Error

| Buruk ❌ | Baik ✅ |
|---|---|
| "Error 500" | "Terjadi gangguan pada sistem kami. Tim kami sedang memperbaiki. Coba lagi dalam beberapa menit." |
| "Network error" | "Tidak ada koneksi internet. Periksa jaringan Anda dan coba lagi." |
| "Timeout" | "Server tidak merespons. Coba lagi." |
| "null reference exception" | "Terjadi kesalahan yang tidak terduga. Coba muat ulang halaman." |
| "Forbidden" | "Anda tidak memiliki akses. Hubungi administrator untuk mendapatkan izin." |

## 4. Error Placement: Toast vs Inline vs Page-Level

### Inline Error (di samping/bawah field)

**Kapan:** Validasi field form — pengguna perlu tahu **field mana** yang salah.

```
Email
┌──────────────────────────┐
│ john@                    │  ← border merah
└──────────────────────────┘
⚠️ Format email belum sesuai. Contoh: nama@email.com
```

**Rules:**
- Tampilkan saat field kehilangan focus (on blur) atau saat submit
- Warna merah untuk border dan pesan
- Posisi di bawah field, bukan di atas
- Scroll ke error pertama jika form panjang

### Toast Notification (popup sementara)

**Kapan:** Feedback singkat atas aksi yang berhasil atau gagal, **tidak perlu input pengguna**.

```
┌─────────────────────────────────────┐
│ ✅  Data berhasil disimpan          │
└─────────────────────────────────────┘
```

**Rules:**
- Auto-dismiss setelah 5 detik (untuk success)
- Error toast harus bisa di-dismiss manual + auto-dismiss lebih lama (8-10 detik)
- Posisi: top-right atau top-center
- Jangan stack lebih dari 3 toast sekaligus
- Bisa diklik untuk dismiss

### Page-Level Error (banner / full-page)

**Kapan:** Error yang mempengaruhi seluruh halaman — server down, unauthorized, 404.

**Rules:**
- Banner di atas konten untuk error non-fatal (warning, maintenance notice)
- Full-page error untuk error fatal (404, 500, no permission)
- Full-page error harus punya CTA: "Kembali ke Beranda", "Coba Lagi", "Hubungi Support"

## 5. Form Validation States

Setiap field form punya 5 visual state:

| State | Visual | Keterangan |
|---|---|---|
| **Default** | Border netral | Belum disentuh |
| **Focus** | Border primary + focus ring | Sedang diisi |
| **Filled (valid)** | Border netral atau hijau + check icon | Sudah diisi dan valid |
| **Error** | Border merah + error message di bawah | Validasi gagal |
| **Disabled** | Background abu-abu, teks pudar | Tidak bisa diisi |

### Kapan Validasi Terjadi?

| Timing | Kapan Pakai |
|---|---|
| **On blur** (saat keluar field) | Validasi format (email, phone, password strength) |
| **On submit** | Validasi semua field sekaligus, scroll ke error pertama |
| **Real-time** (saat mengetik) | Hanya untuk: password strength meter, character counter, username availability |

> **Jangan** validasi real-time untuk semua field — mengganggu pengguna yang belum selesai mengetik.

## 6. Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|---|---|---|
| Hanya desain ideal state | Pengguna lihat halaman kosong / spinner tanpa akhir | Desain semua 7 state |
| Empty state tanpa CTA | Pengguna tidak tahu langkah selanjutnya | Selalu beri next action |
| Spinner full-screen untuk semua loading | UX terasa lambat, tidak ada konteks | Pakai skeleton screen |
| Error message pakai jargon teknis | Pengguna awam bingung | Tulis dalam bahasa pengguna |
| Error message tanpa solusi | Pengguna tidak tahu harus apa | Selalu sarankan langkah berikutnya |
| Toast untuk error yang butuh tindakan | Toast hilang, pengguna miss errornya | Pakai inline error atau page-level |
| Semua field validasi real-time | Mengganggu, error muncul belum selesai ketik | Real-time hanya untuk password meter, char count |
| Disabled button tanpa penjelasan | Pengguna tidak tahu kenapa tidak bisa klik | Tooltip atau teks penjelasan |

## 7. Dokumen Terkait

- [Design System](design-system.md) — Komponen untuk setiap state (skeleton, toast, badge error)
- [User Flows](user-flows.md) — State muncul di setiap node flow
- [UI Specification](ui-specification.md) — Detail teknis implementasi setiap state
- [Wireframes and Prototypes](wireframes-and-prototypes.md) — State harus ada di prototype
- [Accessibility and Responsive Design](accessibility-and-responsive-design.md) — Error messages harus accessible
