# Desain Kasus Uji (Test Case Design Guide)

Merancang kasus uji (*test case design*) secara sistematis adalah keterampilan vital untuk menemukan bug tersembunyi dengan jumlah pengujian minimal. Dokumen ini menjelaskan teknik perancangan *black-box testing* standar industri yang mudah diterapkan oleh solo developer maupun tim pengembang.

---

## 1. Equivalence Partitioning (EP)

Equivalence Partitioning (Partisi Ekivalensi) membagi rentang data masukan menjadi kelompok nilai yang diasumsikan diproses dengan cara yang sama oleh sistem. Jika satu nilai dalam kelompok lolos, nilai lain dalam kelompok tersebut dianggap lolos.

### Contoh Kasus: Diskon Berdasarkan Jumlah Pembelian
Aturan bisnis:
- Beli 1 s.d. 9 barang: Tidak ada diskon.
- Beli 10 s.d. 49 barang: Diskon 10%.
- Beli 50 s.d. 100 barang: Diskon 20%.
- Maksimal order per transaksi: 100 barang.

| Partisi Ekivalensi | Tipe Partisi | Nilai Sampel Uji | Ekspektasi Hasil |
| :--- | :--- | :---: | :--- |
| Jumlah $\le 0$ | Tidak Valid | `-5` | Ditolak (Error invalid quantity) |
| Jumlah 1 - 9 | Valid | `5` | Diterima, diskon 0% |
| Jumlah 10 - 49 | Valid | `25` | Diterima, diskon 10% |
| Jumlah 50 - 100 | Valid | `70` | Diterima, diskon 20% |
| Jumlah $> 100$ | Tidak Valid | `120` | Ditolak (Melebihi kuota maks) |
| Bukan angka | Tidak Valid | `"abc"` | Ditolak (Type error) |

---

## 2. Boundary Value Analysis (BVA)

Sebagian besar kesalahan programmer (*bug*) muncul pada kondisi batas akibat kesalahan operator perbandingan (`<` vs `<=`). BVA berfokus pada titik pergantian partisi:

### Model Nilai Batas (BVA 3-Point)
Untuk batas rentang $[min, max]$:
- Sekitar batas bawah: $min - 1$, $min$, $min + 1$
- Sekitar batas atas: $max - 1$, $max$, $max + 1$

### Contoh Kasus: Validasi Panjang Password (8 s.d. 32 Karakter)
- **Batas Bawah (8 Karakter)**:
  - `7 Karakter` ($min - 1$): Tidak Valid (Ditolak)
  - `8 Karakter` ($min$): Valid (Diterima)
  - `9 Karakter` ($min + 1$): Valid (Diterima)
- **Batas Atas (32 Karakter)**:
  - `31 Karakter` ($max - 1$): Valid (Diterima)
  - `32 Karakter` ($max$): Valid (Diterima)
  - `33 Karakter` ($max + 1$): Tidak Valid (Ditolak)

---

## 3. Decision Table Testing (Tabel Keputusan)

Digunakan ketika perilaku sistem ditentukan oleh kombinasi beberapa kondisi logika bisnis yang kompleks:

### Skenario: Kelayakan Fasilitas Pinjaman Kilat
- Kondisi 1: Usia minimal 21 tahun?
- Kondisi 2: Skor kredit $\ge 650$?
- Kondisi 3: Memiliki bukti penghasilan tetap?

| Kondisi / Aturan | R1 | R2 | R3 | R4 | R5 |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Usia $\ge 21$ tahun | T | Y | Y | Y | Y |
| Skor Kredit $\ge 650$ | - | T | Y | Y | T |
| Penghasilan Tetap | - | - | Y | T | Y |
| **Aksi: Setujui Pinjaman** | **TIDAK** | **TIDAK** | **YA** | **TIDAK** | **REVIEW MANUAL** |

Tabel ini memastikan tidak ada kombinasi kondisi percabangan (*if-else*) yang terlewat oleh developer.

---

## 4. State Transition Testing (Transisi Status)

Banyak aplikasi bisnis bergantung pada mesin status (*finite state machine*), seperti alur siklus hidup pesanan:

```
[ DRAFT ] ---> (Submit) ---------> [ PENDING_PAYMENT ]
                                           |
                    +----------------------+----------------------+
                    | (Payment Success)                           | (Timeout / Cancel)
                    v                                             v
              [ PROCESSING ]                                [ CANCELLED ]
                    |
                    v (Ship Order)
              [ COMPLETED ]
```

### Matriks Transisi Status (State Transition Matrix)
| Status Saat Ini \ Pemicu Event | Submit | Payment Success | Cancel | Ship Order |
| :--- | :---: | :---: | :---: | :---: |
| **DRAFT** | PENDING_PAYMENT | Error / Invalid | CANCELLED | Error / Invalid |
| **PENDING_PAYMENT** | Error / Invalid | PROCESSING | CANCELLED | Error / Invalid |
| **PROCESSING** | Error / Invalid | Error / Invalid | Error (Refund Flow) | COMPLETED |
| **COMPLETED** | Error / Invalid | Error / Invalid | Error / Invalid | Error / Invalid |

---

## 5. Heuristik Pengujian Praktis: Zero, One, Many

Ketika menguji manipulasi koleksi data (array, list, query database), gunakan heuristik **Zero-One-Many**:
- **Zero (0 Item)**: Halaman atau API menangani daftar kosong (*empty state*) tanpa melempar NullPointerException.
- **One (1 Item)**: Tampilan singular, penomoran urutan tidak rancu, dan kalkulasi total tidak error.
- **Many (Banyak Item)**: Menampilkan 1.000 item; memvalidasi paginasi, lazy loading, dan batas performa memori.

---

## 6. Exploratory Testing Charters

*Exploratory testing* bukan pengujian tanpa rencana, melainkan investigasi terfokus yang dipandu oleh sebuah dokumen singkat bernama *Charter*:

```markdown
### Exploratory Test Charter
- **Target Eksplorasi**: Modul Checkout & Voucher Diskon
- **Tujuan**: Menemukan kerentanan manipulasi nominal voucher saat koneksi internet lambat.
- **Timebox**: 45 Menit
- **Log Temuan**:
  - [10:15] Klik ganda pada tombol voucher memicu diskon terpotong dua kali (BUG-012).
  - [10:35] Input voucher dengan kombinasi huruf kecil/besar berfungsi baik.
```

---

## 7. Standar Penulisan Kasus Uji Formal

Format kasus uji yang efektif harus spesifik, mudah diulang oleh orang lain, dan tidak ambigu:

- **ID Kasus Uji**: Format `TC-[MODUL]-[NOMOR]` (contoh: `TC-AUTH-001`, `TC-CHECKOUT-045`).
- **Judul Singkat**: Menyatakan tujuan uji secara lugas.
- **Preconditions**: Status awal sistem atau akun pengguna sebelum pengujian dimulai.
- **Test Data**: Data spesifik yang digunakan (alamat email, angka, teks).
- **Langkah-Langkah (Steps)**: Urutan tindakan terperinci 1, 2, 3.
- **Hasil yang Diharapkan (Expected Result)**: Respon sistem yang spesifik (UI/DB).

Contoh ringkas:
```
ID: TC-AUTH-003
Judul: Verifikasi penguncian akun setelah 5 kali gagal login berturut-turut
Preconditions: Akun user 'john@example.com' berstatus aktif dan tidak terkunci.
Test Data: Email 'john@example.com', Password salah 'WrongPass123!'
Langkah-langkah:
  1. Buka halaman /login.
  2. Masukkan email dan password salah. Klik 'Masuk'.
  3. Ulangi langkah 2 sebanyak 5 kali berturut-turut.
Expected Result:
  - Pada percobaan ke-5, muncul pesan 'Akun terkunci selama 15 menit'.
  - Kolom input password dinonaktifkan sementara.
  - Field 'locked_until' di tabel database terisi timestamp 15 menit ke depan.
```

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Test Case Terlalu Samar**: Menulis "Coba masukkan data yang salah dan pastikan sistem menampilkan error". Hal ini membuka ruang multi-tafsir bagi penguji lain.
2. **Mengabaikan Karakter Khusus**: Hanya menguji huruf biasa dan angka; lupa menguji emoji, spasi ganda, tag HTML, atau simbol tanda baca pada form input.
3. **Mengabaikan Batas Tipe Data Sistem**: Lupa menguji nilai integer overflow (melebihi 2.147.483.647 pada field 32-bit integer).
4. **Tidak Memelihara Kasus Uji**: Membiarkan dokumen test case usang setelah fitur mengalami perubahan spesifikasi produk.
5. **Menguji Tanpa Verifikasi Database**: Hanya melihat notifikasi sukses di antarmuka web tanpa mengecek apakah baris data benar-benar tersimpan rapi di basis data.

---

## 9. Dokumen Terkait & Template

- [Template Test Case](../templates/testing/test-case-template.md) — Template dokumen kasus uji terstandar.
- [Unit Testing Guide](./unit-testing.md) — Implementasi teknik batas pada level kode fungsi.
- [Bug Reporting Standard](./bug-reporting.md) — Standar dokumentasi bug saat menemukan deviasi hasil.
- [Acceptance Testing Guide](./acceptance-testing.md) — Merancang skenario uji penerimaan bersama user.
