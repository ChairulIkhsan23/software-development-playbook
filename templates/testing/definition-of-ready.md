---
title: "Template Definition of Ready (DoR)"
document_id: "DOR-TMPL-001"
version: "1.0.0"
last_updated: "[YYYY-MM-DD]"
owner: "[Nama Scrum Master / Product Owner / Tech Lead]"
team_name: "[Nama Tim Rekayasa Perangkat Lunak]"
scope: "Sprint Planning & Backlog Refinement"
---

# Definition of Ready (DoR) Standard

> **Panduan Pengisian:** Gunakan checklist ini selama sesi Backlog Refinement atau Sprint Planning. Suatu User Story atau tiket pekerjaan TIDAK BOLEH ditarik ke dalam Sprint Backlog yang aktif sebelum seluruh kriteria wajib DoR ini terpenuhi.

---

## 1. Metadata Dokumen & Cakupan [Wajib]

> **Panduan Pengisian:** Definisikan identitas tim dan batas implementasi aturan DoR ini.

- **Nama Tim Rekayasa [Wajib]:** `[Nama Tim / Skuad]`
- **Penanggung Jawab Kualitas Cerita [Wajib]:** `[Nama Product Owner & Tech Lead]`
- **Platform / Aplikasi Target [Wajib]:** `[Web Portal / Mobile Apps / Backend API]`
- **Siklus Evaluasi [Wajib]:** Dievaluasi setiap Sprint Retrospective.

---

## 2. Checklist Kriteria Masuk Sprint (DoR Checklist) [Wajib]

> **Panduan Pengisian:** Centang setiap item saat meninjau User Story pada sesi refinement. Jika ada satu item wajib yang tidak terpenuhi, kembalikan tiket ke Product Backlog.

### A. Format Cerita Pengguna & Nilai Bisnis (User Story & Value) [Wajib]
- [ ] [Wajib] Mengikuti format baku: *"Sebagai [peran pengguna], saya ingin [tindakan yang dilakukan], sehingga [nilai manfaat bisnis yang didapatkan]"*.
- [ ] [Wajib] Nilai bisnis (*business value*) dinyatakan secara eksplisit dan dipahami oleh pengembang.
- [ ] [Wajib] Tiket memiliki judul deskriptif dan ID referensi yang valid (misal: `US-102`).

### B. Kriteria Penerimaan Terdefinisi (Acceptance Criteria) [Wajib]
- [ ] [Wajib] Acceptance Criteria ditulis menggunakan format skenario teruji (*Given-When-Then*).
- [ ] [Wajib] Mencakup skenario alur utama (*happy path*) dan skenario penanganan galat (*negative path*).
- [ ] [Wajib] Validasi batas nilai masukan (*boundary values*) telah disepakati oleh QA dan PO.

### C. Aset Desain & Antarmuka Pengguna (UI/UX Assets) [Wajib]
- [ ] [Wajib] Tautan desain final Figma atau Adobe XD terlampir pada deskripsi tiket.
- [ ] [Wajib] Status status interaksi antarmuka telah lengkap (Normal, Hover, Pressed, Loading, Empty State, Error State).
- [ ] [Kondisional] Panduan responsif mobile dan desktop telah tersedia jika tiket menyentuh frontend.

### D. Ketergantungan Eksternal & Teknis (Dependencies) [Wajib]
- [ ] [Wajib] Seluruh API kontrak pihak ketiga atau tim lain telah siap di staging atau memiliki mock server yang disepakati.
- [ ] [Wajib] Izin akses sistem, kredensial sandbox, dan token integrasi telah tersedia bagi pengembang.
- [ ] [Wajib] Tidak ada blocker tiket lain yang berstatus belum selesai.

### E. Batasan Non-Fungsional (Non-Functional Requirements) [Wajib]
- [ ] [Wajib] Batasan performa dicantumkan (contoh: respons API < 300ms, beban maksimal RPS).
- [ ] [Wajib] Persyaratan keamanan dan privasi data (contoh: sanitasi PII, hashing password) ditegaskan.

### F. Estimasi Beban Kerja (Story Points Estimation) [Wajib]
- [ ] [Wajib] Tim engineering telah mendiskusikan kompleksitas dan mencapai konsensus estimasi (*Story Points*).
- [ ] [Wajib] Ukuran story tidak melebihi kapasitas paruh sprint tim (maksimal `[5 / 8]` story points; pecah story jika lebih besar).

---

## 3. Aturan Penegakan & Pengecualian (Enforcement & Exceptions) [Wajib]

> **Panduan Pengisian:** Tentukan prosedur ketat penolakan tiket serta syarat darurat untuk membypass aturan DoR.

### Aturan Penegakan (Enforcement Policy) [Wajib]:
1. **Hak Veto Tim Dev:** Pengembang dan QA berhak penuh menolak story masuk sprint jika belum memenuhi kriteria wajib di atas.
2. **Batas Waktu DoR (Cut-off):** Seluruh tiket sprint target harus sudah berstatus "Ready for Sprint" minimal 24 jam sebelum Sprint Planning dimulai.

### Kebijakan Pengecualian Darurat (Emergency Exceptions) [Wajib]:
- Tiket perbaikan cacat produksi kritis (*Hotfix / SEV-1 Bug*) dikecualikan dari siklus DoR formal, namun wajib memiliki langkah reproduksi minimal dan persetujuan tertulis dari Tech Lead atau Engineering Manager.

---

*(Seksi checklist riset teknis di bawah ini opsional: hapus jika tim tidak memakai Spike tiket)*
## 4. Kriteria Tambahan untuk Tiket Eksplorasi (Spike Stories) [Opsional]
- [ ] Batasan waktu (*timebox*) telah ditentukan secara tegas (contoh: maksimal 2 hari kerja).
- [ ] Output luaran terdefinisi jelas (dokumen ADR, diagram arsitektur, atau bukti konsep repositori terpisah).
