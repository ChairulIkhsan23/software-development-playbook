# Panduan Kontribusi (Contributing Guide)

Terima kasih atas minat Anda untuk berkontribusi pada **Software Development Playbook**! Repositori ini adalah basis pengetahuan jangka panjang (*long-term knowledge base*) dan panduan kerja praktis rekayasa perangkat lunak yang dirancang untuk dapat digunakan berulang kali oleh mahasiswa, solo developer, software engineer, maupun tim rekayasa kecil.

---

## 1. Prinsip Utama Kontribusi

Sebelum mengusulkan perubahan atau materi baru, pastikan kontribusi Anda berpegang pada prinsip berikut:

1. **Akurat dan Berorientasi Praktik**: Utamakan penjelasan yang realistis, dapat langsung diterapkan, dan mencantumkan trade-off (kelebihan dan kekurangan). Hindari klaim mutlak bahwa satu framework atau arsitektur selalu terbaik.
2. **Anti-Duplikasi (Single Source of Truth)**: Jangan membuat dokumen baru jika topik tersebut sudah dibahas secara memadai. Rujuklah berkas kanonik yang sudah ada menggunakan relative link (misalnya `[Glossary](00-foundations/glossary.md)` atau `[PRD Guide](02-product-requirements/prd-guide.md)`).
3. **Bebas Konten Fiktif**: Jangan mengarang judul buku, edisi standar, tautan URL, atau sertifikasi yang tidak dapat diverifikasi secara faktual.
4. **Bukan Source Code Aplikasi**: Repositori ini adalah repositori panduan dokumentasi, bukan kode sumber aplikasi baru. Contoh kode hanya disertakan sebagai ilustrasi ringkas.
5. **Skalabilitas & Proporsionalitas**: Selalu jelaskan bagaimana suatu konsep disederhanakan untuk developer tunggal (solo dev) atau proyek kecil, dan kapan perlu ditingkatkan ke level formal enterprise.

---

## 2. Alur Pengusulan Perubahan

### 2.1 Memperbaiki Materi yang Ada
Jika Anda menemukan kesalahan ketik (*typo*), tautan rusak, atau informasi teknis yang usang:
1. Buat branch baru dari `main` dengan konvensi: `fix/nama-topik` atau `docs/nama-topik`.
2. Lakukan perubahan spesifik dan periksa kembali keutuhan tautan internal (*relative link*).
3. Buat Pull Request dengan penjelasan singkat mengenai apa yang diperbaiki dan alasannya.

### 2.2 Menambahkan Topik Panduan Baru
Jika ingin menambahkan topik baru yang belum tercover di fase 00–09:
1. Buka *Issue* terlebih dahulu untuk mendiskusikan relevansi topik dengan struktur playbook.
2. Pastikan file ditempatkan pada folder fase SDLC yang tepat.
3. Ikuti struktur panduan standar:
   - Ikhtisar & Definisi
   - Mengapa Ini Penting (Why It Matters)
   - Kapan Menggunakan & Kapan Tidak (When to Use / Trade-offs)
   - Alur Kerja atau Prosedur Bertahap
   - Kesalahan Umum & Solusinya (Common Mistakes)
   - Dokumen Terkait (Relative Links)

### 2.3 Mengubah atau Menambah Template
Jika mengusulkan template dokumen baru di folder `templates/`:
- Pisahkan instruksi pengisian dari isi dokumen menggunakan blok kutipan `> **Panduan Pengisian:**`.
- Gunakan placeholder terstandarisasi dalam kurung siku, misalnya `[Nama Proyek]`, `[YYYY-MM-DD]`, `[Nama Pemilik]`.
- Tandai status field dengan `[Wajib]`, `[Opsional]`, atau `[Kondisional]`.
- Sertakan metadata frontmatter di bagian atas berkas.
- Berikan format ID standar (seperti `DOC-PRD-NNN` atau `TC-MOD-NNN`).

### 2.4 Menambahkan Studi Kasus / Contoh
Jika memperkaya folder `examples/`:
- Pastikan seluruh dokumen dalam contoh studi kasus saling konsisten (ID requirement, model data, dan kontrak API tidak boleh saling bertentangan).
- Jangan menggunakan data finansial atau informasi rahasia yang nyata.

---

## 3. Konvensi Bahasa & Penulisan Markdown

- **Bahasa Pengantar**: Gunakan Bahasa Indonesia yang baku, profesional, dan lugas.
- **Istilah Teknis**: Pertahankan istilah teknis industri dalam bahasa Inggris jika itu merupakan konvensi baku (contoh: *pull request*, *circuit breaker*, *idempotency*, *rate limiting*, *branching*).
- **Format Heading**: Gunakan satu heading `#` untuk judul dokumen. Gunakan `##`, `###`, `####` secara bertingkat tanpa melompati level.
- **Tautan Internal**: Selalu gunakan path relatif terhadap berkas aktif, misalnya `00-foundations/glossary.md` dari root atau `../00-foundations/glossary.md` dari subdirektori.
- **Diagram**: Gunakan sintaks Mermaid terstruktur (`graph`, `sequenceDiagram`, `erDiagram`, `gitGraph`). Hindari gambar biner (`.png`/`.jpg`) jika diagram teks dapat merepresentasikannya secara bersih.
- **Fenced Code Blocks**: Selalu cantumkan identifier bahasa pada blok kode (misalnya ` ```typescript `, ` ```bash `, ` ```json `, ` ```yaml `).

---

## 4. Konvensi Commit & Branch

Gunakan konvensi [Conventional Commits](https://www.conventionalcommits.org/):

- `docs: update deployment guide with rollback steps`
- `fix(templates): add missing validation column in data dictionary`
- `feat(examples): add search endpoint in personal finance api spec`
- `refactor(checklists): reorder pre-development items by priority`

---

## 5. Checklist Verifikasi Sebelum Mengirimkan Pull Request

Sebelum membuat PR, pastikan Anda telah memeriksa poin-poin berikut:

- [ ] Seluruh relative links telah diuji dan mengarah ke file/heading yang benar.
- [ ] Format Markdown valid (tabel rapi, code block memiliki language identifier).
- [ ] Tidak ada API key, token rahasia, atau data pribadi nyata yang tidak sengaja ter-commit.
- [ ] Istilah teknis konsisten dengan [Glossary](00-foundations/glossary.md).
- [ ] Konten baru tidak menduplikasi dokumen utama yang sudah tersedia.
- [ ] Pesan commit mematuhi format Conventional Commits.
