# Software Development Glossary

Daftar istilah standar yang digunakan di seluruh playbook ini.

| Term | Definisi |
|------|----------|
| **Acceptance Criteria** | Kondisi spesifik yang harus dipenuhi agar sebuah user story atau fitur dianggap selesai dan diterima oleh stakeholder. |
| **ADR (Architecture Decision Record)** | Dokumen pendek yang mencatat keputusan arsitektural penting beserta konteks, opsi, dan konsekuensinya. |
| **Agile** | Pendekatan pengembangan perangkat lunak yang iteratif dan inkremental, menekankan kolaborasi, adaptasi, dan pengiriman nilai secara berkelanjutan. |
| **API (Application Programming Interface)** | Kontrak antarmuka yang mendefinisikan cara komponen perangkat lunak berkomunikasi satu sama lain. |
| **Authentication** | Proses verifikasi identitas pengguna atau sistem, memastikan bahwa entitas adalah benar siapa yang diklaim. |
| **Authorization** | Proses penentuan hak akses dan izin yang dimiliki entitas yang sudah ter-autentikasi terhadap sumber daya tertentu. |
| **Availability** | Persentase waktu sistem dapat diakses dan berfungsi normal, biasanya dinyatakan dalam angka seperti 99.9%. |
| **Backlog** | Daftar terurut berisi seluruh pekerjaan (fitur, bug, technical debt) yang perlu dikerjakan oleh tim. |
| **Blue-Green Deployment** | Strategi deployment menggunakan dua lingkungan identik (blue dan green) untuk meminimalkan downtime saat rilis. |
| **Boundary Value Analysis** | Teknik pengujian yang fokus pada nilai-nilai di batas rentang input (minimum, maksimum, dan sekitarnya) untuk menemukan bug. |
| **Business Rule** | Aturan atau kebijakan bisnis yang harus diterjemahkan menjadi logika dalam sistem perangkat lunak. |
| **Canary Release** | Strategi rilis bertahap di mana versi baru di-deploy ke sebagian kecil pengguna terlebih dahulu sebelum rollout penuh. |
| **CI/CD (Continuous Integration / Continuous Delivery)** | Praktik otomasi build, pengujian, dan pengiriman kode dari repository hingga ke lingkungan produksi. |
| **Circuit Breaker** | Pola desain yang mencegah kegagalan berantai dengan menghentikan sementara panggilan ke layanan yang sedang bermasalah. |
| **Code Review** | Proses pemeriksaan kode oleh rekan tim untuk menemukan bug, memastikan kualitas, dan berbagi pengetahuan. |
| **Conventional Commits** | Konvensi penulisan pesan commit yang terstruktur (misalnya `feat:`, `fix:`, `chore:`) untuk memudahkan otomasi changelog dan versioning. |
| **CRUD (Create, Read, Update, Delete)** | Empat operasi dasar pada penyimpanan data yang menjadi fondasi sebagian besar aplikasi. |
| **Data Dictionary** | Dokumen yang mendefinisikan struktur, tipe, format, dan aturan validasi setiap elemen data dalam sistem. |
| **Definition of Done (DoD)** | Daftar kriteria yang harus dipenuhi agar sebuah item pekerjaan dapat dinyatakan selesai sepenuhnya. |
| **Definition of Ready (DoR)** | Kriteria kesiapan sebuah item sebelum dapat dimasukkan ke dalam sprint backlog untuk dikerjakan. |
| **Dependency Injection** | Teknik desain di mana dependensi sebuah komponen disuplai dari luar, bukan dibuat di dalam komponen itu sendiri, untuk meningkatkan testability. |
| **Deployment** | Proses menempatkan dan mengaktifkan versi perangkat lunak ke lingkungan target (staging, produksi, dsb.). |
| **Design System** | Kumpulan komponen UI, pola, dan panduan yang terstandardisasi untuk menjaga konsistensi tampilan dan perilaku produk. |
| **Design Token** | Nilai-nilai desain atomik (warna, spacing, tipografi) yang disimpan sebagai variabel untuk digunakan lintas platform secara konsisten. |
| **E2E Testing (End-to-End Testing)** | Pengujian yang memvalidasi alur kerja pengguna secara menyeluruh dari awal hingga akhir melalui seluruh lapisan sistem. |
| **Equivalence Partitioning** | Teknik pengujian yang membagi input menjadi kelas-kelas ekuivalen sehingga cukup satu test case per kelas. |
| **Feature Flag** | Mekanisme untuk mengaktifkan atau menonaktifkan fitur tertentu secara dinamis tanpa melakukan deployment ulang. |
| **Functional Requirement** | Persyaratan yang mendeskripsikan perilaku atau fungsi spesifik yang harus dimiliki sistem. |
| **Gherkin** | Bahasa terstruktur berbasis kata kunci (Given/When/Then) untuk menulis skenario pengujian yang dapat dibaca oleh non-teknis. |
| **Idempotency** | Sifat operasi di mana eksekusi berulang dengan parameter yang sama menghasilkan status akhir yang sama tanpa efek samping tambahan. |
| **Integration Testing** | Pengujian yang memverifikasi interaksi antar komponen atau modul untuk memastikan mereka bekerja dengan benar saat digabungkan. |
| **INVEST** | Kriteria kualitas user story: Independent, Negotiable, Valuable, Estimable, Small, Testable. |
| **JWT (JSON Web Token)** | Format token berbasis JSON yang ditandatangani secara kriptografis untuk pertukaran klaim autentikasi dan otorisasi antar pihak. |
| **Latency** | Waktu yang dibutuhkan dari pengiriman request hingga diterimanya response pertama, biasanya diukur dalam milidetik. |
| **Load Balancing** | Teknik mendistribusikan lalu lintas jaringan secara merata ke beberapa server untuk meningkatkan ketersediaan dan kinerja. |
| **MFA (Multi-Factor Authentication)** | Metode autentikasi yang mengharuskan pengguna membuktikan identitas melalui dua atau lebih faktor berbeda (misal: password + OTP). |
| **Microservices** | Arsitektur di mana aplikasi dibangun sebagai kumpulan layanan kecil yang independen, masing-masing berjalan dalam prosesnya sendiri dan berkomunikasi via API. |
| **Migration** | Proses perubahan terstruktur pada skema database atau perpindahan data/sistem dari satu state ke state lain secara terkontrol. |
| **Monolith** | Arsitektur di mana seluruh komponen aplikasi digabung dalam satu unit deployment tunggal. |
| **MoSCoW** | Metode prioritisasi kebutuhan: Must have, Should have, Could have, Won't have (for now). |
| **MVP (Minimum Viable Product)** | Versi paling awal dari produk dengan fitur esensial minimum untuk memvalidasi hipotesis di pasar nyata. |
| **NFR (Non-Functional Requirement)** | Persyaratan kualitas sistem seperti performa, keamanan, reliabilitas, skalabilitas, dan maintainability. |
| **OKR (Objectives and Key Results)** | Framework penetapan tujuan yang menghubungkan objektif kualitatif dengan hasil kunci yang terukur. |
| **OWASP (Open Worldwide Application Security Project)** | Organisasi dan referensi standar untuk keamanan aplikasi web, termasuk daftar OWASP Top 10 risiko keamanan paling kritis. |
| **Pagination** | Teknik membagi dataset besar menjadi halaman-halaman kecil untuk mengurangi beban response dan meningkatkan UX. |
| **PRD (Product Requirements Document)** | Dokumen spesifikasi kebutuhan produk yang menjelaskan tujuan, fitur, batasan, dan kriteria keberhasilan. |
| **RACI (Responsible, Accountable, Consulted, Informed)** | Matriks penugasan peran yang memperjelas tanggung jawab setiap pihak dalam suatu aktivitas atau keputusan. |
| **RBAC (Role-Based Access Control)** | Model otorisasi yang memberikan hak akses berdasarkan peran (role) yang diberikan kepada pengguna, bukan langsung ke individu. |
| **Regression Testing** | Pengujian ulang terhadap fungsionalitas yang sudah ada untuk memastikan perubahan baru tidak merusak fitur yang sebelumnya berfungsi. |
| **REST (Representational State Transfer)** | Gaya arsitektur API yang menggunakan HTTP method standar dan resource-based URL untuk komunikasi antar sistem. |
| **RICE (Reach, Impact, Confidence, Effort)** | Framework scoring untuk prioritisasi fitur berdasarkan jangkauan, dampak, tingkat keyakinan, dan usaha yang dibutuhkan. |
| **Rollback** | Proses mengembalikan sistem ke versi atau state sebelumnya ketika deployment baru mengalami masalah. |
| **RPO (Recovery Point Objective)** | Batas maksimal data yang diperbolehkan hilang (dalam satuan waktu) jika terjadi bencana atau kegagalan sistem. |
| **RTO (Recovery Time Objective)** | Target waktu maksimal pemulihan sistem kembali beroperasi normal setelah terjadi gangguan. |
| **SaaS (Software as a Service)** | Model distribusi perangkat lunak di mana aplikasi di-host secara terpusat dan diakses pelanggan melalui internet, biasanya berbasis langganan. |
| **Semantic Versioning** | Sistem penomoran versi format `MAJOR.MINOR.PATCH` yang mengkomunikasikan tingkat perubahan: breaking, fitur baru, atau perbaikan bug. |
| **SLA (Service Level Agreement)** | Perjanjian resmi tingkat layanan yang dijanjikan penyedia kepada pelanggan (misal: uptime 99.9%). |
| **SLO (Service Level Objective)** | Target internal keandalan sistem yang menjadi tolok ukur operasional tim teknis, biasanya lebih ketat dari SLA. |
| **Smoke Test** | Pengujian ringan dan cepat untuk memverifikasi bahwa fungsi-fungsi utama sistem berjalan setelah deployment. |
| **SOLID** | Lima prinsip desain OOP: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion. |
| **Sprint** | Iterasi kerja berdurasi tetap (biasanya 1–4 minggu) dalam framework Scrum di mana tim menghasilkan increment yang potensial untuk dirilis. |
| **Stakeholder** | Individu atau kelompok yang memiliki kepentingan, pengaruh, atau terdampak oleh hasil dari proyek atau produk. |
| **Technical Debt** | Biaya implisit dari pekerjaan tambahan di masa depan yang timbul akibat memilih solusi cepat daripada pendekatan yang lebih baik. |
| **Test Pyramid** | Strategi pengujian berlapis: banyak unit test di dasar, lebih sedikit integration test di tengah, dan paling sedikit E2E test di puncak. |
| **Throughput** | Jumlah request, transaksi, atau unit kerja yang dapat diproses sistem dalam satuan waktu tertentu. |
| **Traceability** | Kemampuan menelusuri hubungan antara kebutuhan, desain, kode, dan test case untuk memastikan cakupan dan kepatuhan. |
| **UAT (User Acceptance Testing)** | Pengujian penerimaan akhir yang dilakukan oleh pengguna bisnis untuk memvalidasi sistem sesuai kebutuhan sebelum rilis. |
| **Unit Testing** | Pengujian terhadap unit terkecil kode (fungsi, method) secara terisolasi untuk memverifikasi kebenaran logika. |
| **User Story** | Deskripsi singkat kebutuhan dari perspektif pengguna, biasanya berformat: "Sebagai [peran], saya ingin [aksi], agar [manfaat]." |
| **Wireframe** | Sketsa atau prototipe visual berketelitian rendah yang menggambarkan tata letak dan struktur halaman tanpa detail visual akhir. |
| **YAGNI (You Aren't Gonna Need It)** | Prinsip yang menyatakan bahwa fitur atau abstraksi tidak boleh dibangun sampai benar-benar dibutuhkan. |
