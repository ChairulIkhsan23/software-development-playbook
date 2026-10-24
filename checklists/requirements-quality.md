# Requirements Quality Verification Checklist

Checklist evaluasi kualitas setiap item kebutuhan individual dan user story sebelum masuk ke sprint backlog. Memastikan spesifikasi siap dieksekusi tanpa kebingungan.

---

## 1. Clarity & Unambiguity
- [ ] **Active & Deterministic Voice**: Kebutuhan ditulis dengan subjek aktif dan kata kerja spesifik (contoh: "Sistem memvalidasi", bukan "Data akan divalidasi").
- [ ] **No Subjective Adjectives**: Tidak memuat kata multitafsir ("ramah pengguna", "fleksibel", "efisien", "cepat").
- [ ] **Domain Glossary Conformance**: Istilah bisnis konsisten dengan kamus istilah (contoh: beda jelas antara *User*, *Customer*, dan *Member*).
- [ ] **Explicit Actor Definition**: Aktor pemicu dan penerima aksi dinyatakan eksplisit di setiap skenario.
- [ ] **Precise Calculation Formulas**: Rumus perhitungan angka (pajak, diskon, poin) dijabarkan langkah demi langkah.

---

## 2. Testability & Verifiability
- [ ] **Structured Acceptance Criteria**: Setiap user story memiliki Acceptance Criteria dalam format *Given-When-Then* (Gherkin).
- [ ] **Binary Outcome**: Kondisi pengujian hanya memiliki 2 hasil tegas: *Pass* (Lolos) atau *Fail* (Gagal).
- [ ] **Objective Verification Method**: Terdapat cara konkret menguji kebutuhan (unit test, assertion API, atau inspeksi UI manual).
- [ ] **Deterministic Mock Data**: Contoh input payload dan respons yang diharapkan disertakan.
- [ ] **No Hidden Dependencies**: Tidak bergantung pada kondisi pengujian misterius yang tak bisa direproduksi di staging.

---

## 3. Completeness (Edge Cases & Boundaries)
- [ ] **Happy Path Mapped**: Skenario ideal pengguna menyelesaikan aksi dari awal hingga akhir terdokumentasi.
- [ ] **Boundary Value Analysis**: Nilai batas input diuji (karakter 0, panjang maksimal, nilai negatif, angka nol).
- [ ] **Error & Exception Handling**: Pesan kesalahan spesifik, penanganan kegagalan koneksi, dan status code HTTP ditentukan.
- [ ] **Network & Timeout Cases**: Perilaku sistem saat koneksi lambat, timeout API eksternal, atau koneksi terputus dijelaskan.
- [ ] **Concurrency & Race Conditions**: Antisipasi klik ganda pengguna (*double submit*) atau benturan transaksi konkuren tercatat.

---

## 4. Feasibility & Realism
- [ ] **Technical Constraint Alignment**: Kebutuhan dapat direalisasikan dengan stack teknologi dan pustaka yang ada.
- [ ] **Infrastructure Reality**: Kebutuhan tidak menuntut spesifikasi server di luar budget operasional tim.
- [ ] **Data Model Consistency**: Relasi data selaras dengan arsitektur database tanpa butuh denormalisasi ekstrem.
- [ ] **Security Boundaries**: Kebutuhan tidak membuka celah keamanan atau melanggar hak akses peran lain.
- [ ] **Realistic Effort Limit**: Beban kerja implementasi kebutuhan masuk akal untuk dikerjakan dalam 1 iterasi sprint.

---

## 5. Traceability
- [ ] **Business Goal Linkage**: Setiap kebutuhan tertaut langsung ke nomor sasaran bisnis / PRD.
- [ ] **Test Case Mapping**: Nomor kebutuhan terhubung dengan ID test case di test management tool.
- [ ] **Code Commit Association**: Format branch Git dan commit message dapat mengacu ke ID kebutuhan (`PROJ-123`).
- [ ] **Backward & Forward Traceability**: Perubahan kebutuhan dapat dilacak dampaknya ke komponen terkait.

---

## 6. INVEST Compliance (User Stories)
- [ ] **[I] Independent**: User story tidak memiliki ketergantungan blokir dengan story lain dalam sprint yang sama.
- [ ] **[N] Negotiable**: Menyisakan ruang kolaborasi solusi antara developer dan product owner, bukan kontrak kaku.
- [ ] **[V] Valuable**: Memberikan nilai fungsional nyata yang dirasakan oleh end-user atau bisnis.
- [ ] **[E] Estimable**: Tim engineering memiliki pemahaman cukup untuk memberikan estimasi poin kesulitan.
- [ ] **[S] Small**: Ukuran story cukup kecil untuk diselesaikan dan diuji tuntas dalam waktu 1-3 hari kerja.
- [ ] **[T] Testable**: Memiliki kriteria lulus uji yang tidak meninggalkan keraguan bagi QA.

---

## Panduan Evaluasi & Batas Kelulusan
Setiap kebutuhan dinilai secara biner per bagian:

| Kondisi Kebutuhan | Status | Tindakan Tim |
|---|---|---|
| **Semua Bagian Centang Lengkap** | **Ready for Dev** | Masukkan story ke sprint planning; siap diestimasi. |
| **Gagal pada Bagian 2 (Testability)** | **Blocked** | Kembalikan ke author; wajib buat skenario *Given-When-Then*. |
| **Gagal pada Kriteria [S] (Small)** | **Split Required** | Pecah story menjadi 2 atau lebih sub-story yang lebih atomik. |
| **Gagal pada Bagian 3 (Completeness)** | **Refine Required** | Tambahkan spesifikasi batas input dan pesan error. |

---

## Tindakan Remediasi (Remediation Actions)
- **Story Terlalu Besar (Ukuran > 5 Story Points)**:
  Pecah berdasarkan variasi alur (Happy Path vs Edge Cases), tipe platform (Web vs Mobile), atau operasi data (Read vs Write).
- **Acceptance Criteria Mengambang**:
  Tulis ulang dengan template baku:
  *Given [prasyarat awal], When [aksi pengguna], Then [hasil yang diharapkan dari sistem]*.
- **Ketergantungan Kuat Antar Story**:
  Gunakan mock interface atau dummy data stubbing sehingga frontend dan backend dapat bekerja paralel tanpa saling tunggu.
- **Kebutuhan Mengandung Bias Solusi**:
  Hapus referensi implementasi internal (contoh: ganti "Simpan di tabel PostgreSQL" menjadi "Persistensikan data profil pengguna").
- **Edge Cases Terlupakan**:
  Gunakan rumus cepat *CRUD + BVA*: periksa Create, Read, Update, Delete ditambah Boundary Values (Null, Max, Duplicate).