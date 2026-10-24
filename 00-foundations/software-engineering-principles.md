# Software Engineering Principles

Prinsip dasar yang wajib diterapkan dalam penulisan kode dan perancangan arsitektur perangkat lunak.
Panduan ini ditujukan untuk junior developer, solo developer, dan tim kecil.

---

## Ringkasan Prinsip

| Prinsip | Inti | Kapan Langgar |
|---|---|---|
| **SRP** | Satu modul = satu alasan berubah | Prototype/script sekali pakai |
| **OCP** | Tambah fitur tanpa ubah kode lama | Belum ada variasi nyata |
| **LSP** | Subtype harus substitutable | — (jangan dilanggar) |
| **ISP** | Interface kecil, spesifik | Hanya 1–2 consumer |
| **DIP** | Depend on abstractions | Aplikasi kecil tanpa layer |
| **DRY** | Satu sumber kebenaran | Duplikasi premature abstraction lebih buruk |
| **KISS** | Solusi paling sederhana yang benar | Performa kritis butuh optimasi |
| **YAGNI** | Jangan bangun yang belum perlu | Keputusan arsitektur yang mahal diubah nanti |
| **SoC** | Pisahkan tanggung jawab | — (jangan dilanggar) |
| **High Cohesion / Low Coupling** | Modul fokus, dependensi minimal | — (jangan dilanggar) |
| **Least Surprise** | Perilaku sesuai ekspektasi | — (jangan dilanggar) |
| **Composition > Inheritance** | Gabungkan behavior, jangan wariskan | Hirarki alami yang dangkal (1–2 level) |
| **Fail Fast** | Gagal segera, gagal jelas | Hot path performa kritis |

---

## 1. SOLID

### 1.1 Single Responsibility Principle (SRP)

**Apa:** Satu class/modul/fungsi hanya punya satu alasan untuk berubah.

**Kenapa:** Kalau satu modul handle banyak hal, perubahan di satu area bisa merusak area lain. Testing jadi sulit. Reuse jadi mustahil.

```typescript
// ❌ Pelanggaran: UserService handle bisnis logic DAN kirim email
class UserService {
  createUser(data: UserData) { /* simpan ke DB */ }
  sendWelcomeEmail(user: User) { /* kirim email */ }
  generateReport(users: User[]) { /* buat PDF */ }
}

// ✅ Benar: Pisahkan tanggung jawab
class UserRepository { save(data: UserData): User { /* ... */ } }
class EmailService { sendWelcome(user: User) { /* ... */ } }
class UserReportGenerator { generate(users: User[]) { /* ... */ } }
```

**Pelanggaran umum:** "God class" yang punya 20+ method dengan tanggung jawab campur aduk.

### 1.2 Open/Closed Principle (OCP)

**Apa:** Terbuka untuk ekstensi, tertutup untuk modifikasi. Tambah behavior baru tanpa ubah kode yang sudah jalan.

**Kenapa:** Kode lama sudah di-test dan berjalan di production. Modifikasi = risiko regresi.

```typescript
// ❌ Pelanggaran: Harus ubah fungsi tiap ada tipe baru
function calculateDiscount(type: string, price: number): number {
  if (type === "student") return price * 0.2;
  if (type === "veteran") return price * 0.3;
  // tambah tipe baru = ubah fungsi ini
}

// ✅ Benar: Ekstensi via interface
interface DiscountStrategy {
  calculate(price: number): number;
}
class StudentDiscount implements DiscountStrategy {
  calculate(price: number) { return price * 0.2; }
}
// Tambah tipe baru = tambah class baru, fungsi lama tidak berubah
```

**Pelanggaran umum:** Switch/if-else panjang yang terus bertambah tiap ada requirement baru.

### 1.3 Liskov Substitution Principle (LSP)

**Apa:** Subtype harus bisa menggantikan parent type tanpa merusak perilaku program.

**Kenapa:** Kalau substitusi rusak, polymorphism tidak bisa dipercaya. Kode yang depend pada base type akan break secara tidak terduga.

```typescript
// ❌ Pelanggaran klasik: Square "is-a" Rectangle, tapi behavior beda
class Rectangle {
  setWidth(w: number) { this.width = w; }
  setHeight(h: number) { this.height = h; }
  area(): number { return this.width * this.height; }
}
class Square extends Rectangle {
  setWidth(w: number) { this.width = w; this.height = w; } // surprise!
  setHeight(h: number) { this.width = h; this.height = h; }
}
// rect.setWidth(5); rect.setHeight(3); rect.area() → harusnya 15, Square kasih 9

// ✅ Benar: Pisahkan, jangan paksa inheritance
interface Shape { area(): number; }
class Rectangle implements Shape { /* width × height */ }
class Square implements Shape { /* side × side */ }
```

**Pelanggaran umum:** Override method yang throw `NotImplementedException` atau ubah postcondition parent.

### 1.4 Interface Segregation Principle (ISP)

**Apa:** Jangan paksa client depend pada method yang tidak mereka pakai.

**Kenapa:** Interface besar = coupling besar. Implementor dipaksa implement method kosong. Perubahan interface memaksa semua implementor berubah.

```typescript
// ❌ Pelanggaran: Interface terlalu gemuk
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}
class Robot implements Worker {
  work() { /* OK */ }
  eat() { /* Robot tidak makan — dipaksa implement */ }
  sleep() { /* Robot tidak tidur */ }
}

// ✅ Benar: Interface kecil dan fokus
interface Workable { work(): void; }
interface Feedable { eat(): void; }
class Robot implements Workable { work() { /* ... */ } }
class Human implements Workable, Feedable { work() { /* ... */ } eat() { /* ... */ } }
```

**Pelanggaran umum:** Interface "catch-all" dengan 10+ method di mana kebanyakan implementor cuma pakai 2–3.

### 1.5 Dependency Inversion Principle (DIP)

**Apa:** Modul high-level tidak boleh depend pada modul low-level. Keduanya depend pada abstraksi.

**Kenapa:** Tanpa DIP, perubahan di database layer bisa memaksa perubahan di business logic. Testing jadi sulit karena tidak bisa mock.

```typescript
// ❌ Pelanggaran: Business logic depend langsung pada implementasi
class OrderService {
  private db = new MySQLDatabase(); // hard dependency
  save(order: Order) { this.db.insert("orders", order); }
}

// ✅ Benar: Depend pada abstraksi
interface OrderRepository { save(order: Order): void; }

class OrderService {
  constructor(private repo: OrderRepository) {} // injected
  save(order: Order) { this.repo.save(order); }
}
```

**Pelanggaran umum:** `new` concrete class di dalam business logic. Import langsung ke implementation detail.

---

## 2. DRY — Don't Repeat Yourself

**Apa:** Setiap pengetahuan (*knowledge*) dalam sistem punya satu representasi yang otoritatif.

**Kenapa:** Duplikasi = bug menunggu terjadi. Ubah di satu tempat, lupa di tempat lain.

```typescript
// ❌ Validasi email di-copy paste di 3 file berbeda
// Ubah regex di satu file, lupa di file lain

// ✅ Satu sumber kebenaran
function isValidEmail(email: string): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}
```

**Pelanggaran umum:** Copy-paste logic antar file. Konstanta magic number tersebar di mana-mana.

**Peringatan:** DRY tentang *knowledge*, bukan tentang kode yang kebetulan mirip. Dua fungsi yang kebetulan sama tapi punya alasan berubah yang berbeda → **biarkan duplikat**. Abstraksi prematur lebih buruk dari duplikasi.

---

## 3. KISS — Keep It Simple, Stupid

**Apa:** Pilih solusi paling sederhana yang benar-benar menyelesaikan masalah.

**Kenapa:** Kode yang kompleks sulit dibaca, di-debug, dan di-maintain. Kompleksitas adalah musuh utama software.

```typescript
// ❌ Over-engineering: Factory + Strategy + Observer untuk toggle feature
class FeatureToggleFactory {
  createStrategy(config: Config): FeatureToggleStrategy { /* ... */ }
}

// ✅ Cukup:
const FEATURE_FLAGS = { darkMode: true, betaSearch: false };
```

**Pelanggaran umum:** Design pattern tanpa masalah nyata. Abstraksi berlapis untuk "fleksibilitas masa depan" yang tidak pernah datang.

---

## 4. YAGNI — You Aren't Gonna Need It

**Apa:** Jangan bangun fungsionalitas sampai benar-benar dibutuhkan *sekarang*.

**Kenapa:** Fitur spekulatif = kode yang harus di-maintain tanpa memberikan value. Requirement berubah — kemungkinan besar fitur spekulatif akan salah arah.

```typescript
// ❌ "Nanti kita mungkin butuh multi-currency"
class Price {
  constructor(
    public amount: number,
    public currency: string,  // belum ada requirement
    public exchangeRate: number,  // belum ada requirement
  ) {}
}

// ✅ Bangun yang dibutuhkan sekarang
class Price {
  constructor(public amount: number) {} // ponytail: tambah currency saat ada requirement multi-currency
}
```

**Pelanggaran umum:** Config system untuk value yang tidak pernah berubah. Abstract base class dengan satu implementasi.

---

## 5. Separation of Concerns (SoC)

**Apa:** Pisahkan kode berdasarkan tanggung jawab. Setiap bagian handle satu aspek.

**Kenapa:** Perubahan di satu concern tidak seharusnya memaksa perubahan di concern lain.

```
Contoh arsitektur berlapis:
┌─────────────────────┐
│  Presentation Layer  │  → UI, input/output
├─────────────────────┤
│  Business Logic      │  → Aturan bisnis, validasi domain
├─────────────────────┤
│  Data Access Layer   │  → Database, API eksternal
└─────────────────────┘
```

**Pelanggaran umum:** SQL query di dalam handler HTTP. Business rule di dalam template HTML. Formatting logic di dalam database model.

---

## 6. High Cohesion & Low Coupling

**Apa:**
- **High Cohesion:** Elemen dalam satu modul saling terkait erat dan bekerja menuju tujuan yang sama.
- **Low Coupling:** Modul seminimal mungkin bergantung pada modul lain.

**Kenapa:** High cohesion = mudah dipahami. Low coupling = mudah diubah tanpa efek samping.

```typescript
// ❌ Low cohesion: satu modul handle hal yang tidak berhubungan
// utils.ts → formatDate(), calculateTax(), sendEmail(), resizeImage()

// ✅ High cohesion: kelompokkan berdasarkan domain
// date-utils.ts → formatDate(), parseDate(), diffDays()
// tax.ts → calculateTax(), getTaxRate()
```

**Pelanggaran umum:** File `utils.ts` atau `helpers.ts` yang jadi tempat buang semua fungsi. Module A yang import 15 hal dari Module B.

---

## 7. Principle of Least Surprise

**Apa:** Kode harus berperilaku sesuai ekspektasi wajar dari pembaca/pengguna.

**Kenapa:** Surprise = bug. Developer yang baca kode akan membuat asumsi berdasarkan nama dan konvensi. Kalau asumsi salah, mereka akan salah pakai.

```typescript
// ❌ Surprise: fungsi "get" tapi punya side effect
function getUser(id: string): User {
  const user = db.find(id);
  db.updateLastAccess(id, Date.now()); // side effect tidak terduga
  return user;
}

// ✅ Nama jelas, behavior sesuai ekspektasi
function getUser(id: string): User { return db.find(id); }
function getUserAndRecordAccess(id: string): User { /* ... */ }
```

**Pelanggaran umum:** Setter yang melakukan validasi + transformasi diam-diam. `delete()` yang sebenarnya soft-delete tanpa indikasi. Boolean parameter yang mengubah perilaku fungsi secara drastis.

---

## 8. Composition over Inheritance

**Apa:** Bangun behavior dengan menggabungkan objek kecil (*has-a*), bukan mewariskan dari parent (*is-a*).

**Kenapa:** Inheritance membuat hierarki kaku. Perubahan di parent bisa merusak semua child. Multiple inheritance menambah kompleksitas. Composition lebih fleksibel dan mudah di-test.

```typescript
// ❌ Inheritance yang dalam dan kaku
class Animal { }
class FlyingAnimal extends Animal { fly() {} }
class SwimmingFlyingAnimal extends FlyingAnimal { swim() {} } // diamond problem incoming

// ✅ Composition: gabungkan capability
interface CanFly { fly(): void; }
interface CanSwim { swim(): void; }

class Duck implements CanFly, CanSwim {
  private flyer = new FlightBehavior();
  private swimmer = new SwimBehavior();
  fly() { this.flyer.fly(); }
  swim() { this.swimmer.swim(); }
}
```

**Pelanggaran umum:** Hierarki inheritance lebih dari 2 level. Extend class hanya untuk reuse 1 method.

---

## 9. Fail Fast

**Apa:** Ketika ada kondisi error, gagal segera dan gagal dengan pesan yang jelas. Jangan lanjut dalam keadaan tidak valid.

**Kenapa:** Semakin jauh error dari sumbernya, semakin sulit di-debug. Data corrupt yang menyebar diam-diam jauh lebih berbahaya dari crash.

```typescript
// ❌ Gagal lambat: lanjut dalam keadaan invalid
function processOrder(order: Order) {
  const total = order.items?.reduce((sum, i) => sum + (i.price ?? 0), 0) ?? 0;
  // Kalau order.items null, kita proses order kosong tanpa peringatan
}

// ✅ Fail fast: validasi di awal
function processOrder(order: Order) {
  if (!order.items || order.items.length === 0) {
    throw new Error("Order harus punya minimal satu item");
  }
  if (order.items.some(i => i.price == null || i.price < 0)) {
    throw new Error("Semua item harus punya harga valid");
  }
  const total = order.items.reduce((sum, i) => sum + i.price, 0);
}
```

**Pelanggaran umum:** Catch exception lalu return null/default tanpa logging. Optional chaining berlebihan yang menutupi bug. Silent fallback yang membuat data tidak konsisten.

---

## Ketika Prinsip Berkonflik

Prinsip-prinsip ini sering tarik-menarik. Panduan pengambilan keputusan:

| Konflik | Pilih | Alasan |
|---|---|---|
| DRY vs KISS | KISS | Abstraksi prematur lebih mahal dari duplikasi. Tunggu sampai ada 3 duplikasi nyata (*Rule of Three*). |
| DRY vs YAGNI | YAGNI | Jangan buat abstraksi untuk kasus yang belum ada. |
| OCP vs KISS | KISS | Jangan buat strategy pattern untuk satu varian. Refactor saat varian kedua muncul. |
| SRP vs KISS | Konteks | Script kecil boleh punya satu file. Aplikasi production butuh pemisahan. |
| DIP vs YAGNI | YAGNI | Jangan inject interface kalau hanya ada satu implementasi dan belum perlu mock di test. |

**Aturan praktis:** Untuk kode yang hidup singkat (prototype, script), KISS dan YAGNI menang. Untuk kode production yang hidup lama, SRP, SoC, dan DIP investasinya layak.

---

## Kesalahan Umum

1. **Menerapkan semua prinsip sekaligus di awal.** Mulai dari KISS dan YAGNI. Tambahkan abstraksi saat kompleksitas nyata memaksanya.

2. **DRY berlebihan.** Menggabungkan kode yang kebetulan mirip tapi punya alasan berubah yang berbeda. Hasilnya: fungsi dengan 5 parameter boolean yang mengatur perilaku.

3. **Cargo cult design patterns.** Pakai Factory, Observer, Strategy tanpa memahami masalah yang mereka selesaikan. Pattern yang salah konteks = kompleksitas gratis.

4. **Inheritance sebagai default reuse.** Inheritance bukan tool reuse — inheritance adalah tool *polimorfisme*. Untuk reuse, pakai composition atau fungsi biasa.

5. **Mengabaikan Fail Fast.** Catch-all error handling yang menelan exception. Data korup menyebar tanpa terdeteksi selama berhari-hari.

6. **Over-abstraction "untuk masa depan".** Interface dengan satu implementasi, config untuk satu value, layer yang hanya meneruskan panggilan. Setiap abstraksi punya biaya kognitif — pastikan biaya itu terbayar.

---

## Dokumen Terkait

- [Coding Standards](../05-development-practices/coding-standards.md) — Standar penulisan kode
- [Code Review](../05-development-practices/code-review.md) — Panduan review kode
- [Logging and Error Handling](../05-development-practices/logging-and-error-handling.md) — Penanganan error dan logging
- [Software Development Lifecycle](software-development-lifecycle.md) — Siklus hidup pengembangan
- [Glossary](glossary.md) — Daftar istilah
