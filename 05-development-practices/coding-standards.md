# Coding Standards

Standar penulisan kode untuk memastikan basis kode tetap mudah dibaca, diprediksi, dan dirawat oleh seluruh anggota tim teknis.

---

## 1. Mengapa Coding Standards Penting?

Standar koding bukan masalah preferensi estetika pribadi, melainkan fondasi efisiensi tim:
- **Mengurangi Beban Kognitif:** Format dan pola yang seragam membuat developer dapat fokus pada penyelesaian logika bisnis, bukan terdistraksi gaya penulisan sintaksis yang berbeda-beda.
- **Mempercepat Review Kode:** Diskusi pada Pull Request tidak terbuang untuk memperdebatkan gaya penulisan spasi, kurung kurawal, atau penamaan variabel.
- **Mencegah Bug Dini:** Konfigurasi linter dan type checker yang ketat menangkap potensi *runtime error* sebelum kode menyentuh branch utama.
- **Onboarding Lebih Cepat:** Developer baru dapat langsung beradaptasi karena pola kode konsisten di setiap modul.

---

## 2. Aturan TypeScript Strict Mode

Proyek berbasis TypeScript wajib mengaktifkan mode strict (`strict: true` di `tsconfig.json`).

### Aturan Inti:
1. **Dilarang Menggunakan `any`:**
   - Gunakan `unknown` bila tipe data benar-benar belum diketahui (misal payload eksternal). Lakukan type narrowing / validation dengan library seperti Zod atau type guard native.
   - Hindari type assertion (`as MyType`) kecuali pada integrasi library pihak ketiga yang cacat tipe.
2. **`noImplicitAny: true`:** Semua parameter fungsi dan properti wajib memiliki definisi tipe eksplisit.
3. **`strictNullChecks: true`:** Tangani kemungkinan nilai `null` atau `undefined` secara eksplisit menggunakan optional chaining (`?.`) atau nullish coalescing (`??`).

```typescript
// ❌ Buruk: Penggunaan any membunuh proteksi compiler
function parseData(input: any) {
  return input.profile.name;
}

// ✅ Baik: Validasi tipe eksplisit atau gunakan type narrowing
interface UserProfile {
  name: string;
}

function parseData(input: unknown): string {
  if (typeof input === "object" && input !== null && "profile" in input) {
    const profile = (input as { profile: UserProfile }).profile;
    return profile.name;
  }
  throw new Error("Format payload tidak valid");
}
```

---

## 3. Konvensi Penamaan (Naming Conventions)

Konsistensi penamaan wajib diikuti pada seluruh file dalam repositori proyek:

| Entitas | Gaya Penamaan | Contoh | Aturan Tambahan |
| :--- | :--- | :--- | :--- |
| **Variabel & Properti** | `camelCase` | `userId`, `isActive`, `totalAmount` | Boolean diawali kata penanya: `is`, `has`, `can`, `should`. |
| **Fungsi & Metode** | `camelCase` | `getUserById()`, `calculateTax()` | Wajib diawali kata kerja aktif deskriptif. |
| **Class, Interface, Type** | `PascalCase` | `UserProfile`, `PaymentGateway` | Jangan pakai prefix `I` atau `T` (bukan `IUser`). |
| **Konstanta Global** | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT` | Hanya untuk konstanta immutable tingkat modul. |
| **Nama File & Folder** | `kebab-case` | `auth-service.ts`, `user-profile.tsx` | Gunakan ekstensi `.ts` atau `.tsx` (untuk komponen UI). |
| **Enums & Enum Members** | `PascalCase` | `enum UserRole { Admin = "ADMIN" }` | Nama enum dan member-nya menggunakan PascalCase. |

---

## 4. Format Kode Otomatis (Prettier & ESLint)

Formatting harus sepenuhnya diotomasi oleh mesin, bukan dinilai secara manual saat code review:

### Contoh Konfigurasi Prettier (`.prettierrc`):
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "printWidth": 100,
  "trailingComma": "all"
}
```

### Prinsip Konfigurasi ESLint:
- `@typescript-eslint/no-floating-promises`: Mencegah promise async yang lupa di-`await`.
- `@typescript-eslint/no-explicit-any`: Memblokir penggunaan tipe `any`.
- `no-unused-vars`: Menolak variabel yang dideklarasikan tetapi tidak pernah dipakai.
- **Integrasi Git Hooks:** Gunakan Husky dan `lint-staged` untuk memformat dan mengecek file yang di-stage sebelum di-commit.

Prinsip dasar tim: *Jika mesin bisa memformat dan memeriksanya, jangan pernah mendebatkannya di kolom review PR.*

---

## 5. Pedoman Clean Code

### A. Batasan Ukuran Fungsi (< 30 Baris) & File (< 300 Baris)
Fungsi yang panjang adalah indikator kuat bahwa fungsi tersebut memiliki terlalu banyak tanggung jawab (*Multiple Responsibilities*). File yang melebihi 300 baris sebaiknya dipecah menjadi modul atau sub-komponen terpisah.

### B. Hindari Deep Nesting (Maksimal 2-3 Level)
Gunakan pola **Early Return** / **Guard Clauses** untuk mengurangi percabangan if-else bersarang:

```typescript
// ❌ Buruk: Indentasi bertingkat sulit dipahami
function processPayment(user: User, amount: number) {
  if (user) {
    if (user.isActive) {
      if (amount > 0) {
        return charge(user, amount);
      }
    }
  }
  return null;
}

// ✅ Baik: Guard clauses menjaga struktur kode tetap datar
function processPayment(user: User, amount: number) {
  if (!user || !user.isActive) return null;
  if (amount <= 0) throw new Error("Nominal pembayaran harus lebih dari nol");

  return charge(user, amount);
}
```

### C. Single Level of Abstraction (SLA)
Dalam satu fungsi, jangan mencampur logika bisnis tingkat tinggi (orkestrasi alur) dengan detail implementasi tingkat rendah (manipulasi string, parsing regex, kueri SQL manual).

---

## 6. Defensive Programming vs Fail Fast

- **Fail Fast di Batas Sistem (Trust Boundary):** Ketika menerima input dari luar (API request, form submission, upload file, environment variables), validasi secepat mungkin dan lempar error segera bila data tidak valid.
- **Defensive Programming di Internal Core:** Pastikan fungsi internal menangani kondisi batas seperti array kosong atau nilai null tanpa menyebabkan runtime crash yang tidak tertangani.
- **Hindari Defensive Berlebihan:** Jangan membungkus setiap baris kode dengan blok `try-catch` kosong yang menelan (*swallow*) error dan menyulitkan pelacakan bug.

---

## 7. Kebijakan Komentar Kode (Code Comments & Anti-Slop)

### Aturan Utama: Tulis "Mengapa", Bukan "Apa"
Kode yang bersih dan ekspresif menjelaskan *apa* yang dilakukannya melalui penamaan fungsi dan variabel yang tepat. Komentar hanya ditulis untuk menjelaskan *alasan* (konteks bisnis, batasan teknis vendor pihak ketiga, atau algoritma rumit).

| Jenis Komentar | Contoh Penulisan | Status |
| :--- | :--- | :--- |
| **Redundan (Slop)** | `// Simpan data user ke database`<br>`await userRepository.save(user);` | ❌ Hapus (kode sudah jelas) |
| **AI Boilerplate** | `// Function to calculate sum of two numbers`<br>`function add(a, b) { ... }` | ❌ Hapus (AI slop) |
| **Konteks Bisnis** | `// Regulasi OJK mensyaratkan retensi data audit minimum 5 tahun`<br>`const RETENTION_DAYS = 365 * 5;` | ✅ Wajib (menjelaskan alasan regulasi) |
| **Batasan Vendor** | `// Jeda 250ms karena API vendor memiliki rate-limit ketat 4 req/detik`<br>`await delay(250);` | ✅ Wajib (menjelaskan batasan teknis pihak ke-3) |

---

## 8. Kesalahan Umum (Common Mistakes)

1. **Magic Numbers / Magic Strings:** Menulis angka atau string acak di tengah kode tanpa nama konstanta yang jelas (misal: `if (status === 3)` alih-alih `if (status === Status.ACTIVE)`).
2. **Mutasi State Langsung:** Mengubah objek atau array parameter secara langsung tanpa menerapkan immutability, memicu efek samping (*side effects*).
3. **Mengabaikan Promise Rejection:** Menjalankan operasi async tanpa menangani kegagalan dengan `.catch()` atau blok `try-catch`.
4. **Premature Optimization:** Membuat abstraksi atau mekanisme caching rumit untuk performa sebelum ada bukti profiling nyata.
5. **Over-abstraction:** Membuat interface dengan satu implementasi atau factory pattern untuk satu objek sederhana yang tidak pernah bervariasi.

---

## 9. Penanganan Deprecated Code

Saat menghentikan fungsi atau modul usang:
1. Tandai dengan anotasi JSDoc `@deprecated` disertai alasan dan alternatif pengganti.
2. Berikan rentang waktu minimal 1 siklus rilis sebelum fungsi benar-benar dihapus.
3. Hindari membiarkan dead code tersimpan tanpa penanda resmi.

---

## 10. Dokumen Terkait

- [Repository Structure](repository-structure.md)
- [Code Review Guide](code-review.md)
- [Logging and Error Handling](logging-and-error-handling.md)
- [Branching and Commits](branching-and-commits.md)
