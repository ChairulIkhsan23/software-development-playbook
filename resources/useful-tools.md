# Rekomendasi Tools & Utilitas Software Engineering

Daftar perangkat lunak, utilitas baris perintah (*CLI*), ekstensi, dan layanan terkelola yang direkomendasikan untuk menunjang produktivitas dan kualitas pada setiap fase pengembangan.

---

## 1. Pemodelan & Diagram As Code

| Tool | Kategori | Keunggulan | Penggunaan Utama |
|---|---|---|---|
| **Mermaid.js** | Diagram as Code | Didukung langsung oleh GitHub, GitLab, dan Notion tanpa build step tambahan. | Flowchart, Sequence diagram, ERD database, GitGraph. |
| **PlantUML** | Diagram as Code | Sangat kuat untuk diagram komponen UML dan diagram deployment C4 yang kompleks. | Dokumentasi arsitektur enterprise. |
| **Excalidraw** | Sketsa Bebas | Tampilan bergaya hand-drawn yang intuitif untuk kolaborasi real-time. | Sesi brainstorming penemuan masalah dan arsitektur awal. |
| **Figma** | Desain UI/UX | Standar industri untuk wireframing, high-fidelity mockups, dan handoff developer. | Desain antarmuka di [`03-ux-ui-design`](../03-ux-ui-design/README.md). |

---

## 2. Pengujian & Eksplorasi API

| Tool | Kategori | Keunggulan | Penggunaan Utama |
|---|---|---|---|
| **Bruno** / **Hoppscotch** | API Client Open Source | Alternatif Postman yang menyimpan koleksi API sebagai file Git-friendly di repositori lokal. | Pengujian endpoint REST API harian tanpa vendor lock-in. |
| **Scalar** / **Swagger UI** | API Documentation | Menghasilkan antarmuka dokumentasi interaktif yang cantik langsung dari OpenAPI YAML/JSON. | Dokumentasi kontrak API publik dan internal. |
| **k6 (Grafana)** | Load Testing | Scripting berbasis JavaScript modern, penggunaan memori sangat ringan, ramah CI/CD. | Uji performa throughput dan latensi batas NFR. |
| **Mock Service Worker (MSW)** | Network Mocking | Mencegat request di tingkat jaringan (Service Worker) tanpa mengotori kode aplikasi. | Unit testing dan integration testing frontend. |

---

## 3. Kualitas Kode, Linter, & Keamanan Statis

| Tool | Kategori | Keunggulan | Penggunaan Utama |
|---|---|---|---|
| **Biome** / **ESLint + Prettier** | Linter & Formatter | Standar de-facto untuk formatting dan analisis statis kode JavaScript/TypeScript. | Otomasi gaya penulisan kode di [`05-development-practices`](../05-development-practices/README.md). |
| **Gitleaks** / **TruffleHog** | Secret Detection | Memindai histori commit Git untuk mencegah kebocoran API key dan password sebelum push. | Pre-commit hook via Husky untuk proteksi kredensial. |
| **Trivy** / **Snyk** | Vulnerability Scanner | Memindai kerentanan CVE pada dependensi aplikasi dan Docker container image. | Gerbang verifikasi keamanan pada pipeline CI/CD. |
| **SonarQube** | Static Analysis | Menghitung technical debt, code smells, dan duplikasi kode pada basis data besar. | Audit berkala kesehatan codebase. |

---

## 4. Observabilitas, Logging, & Pemantauan

| Tool | Kategori | Keunggulan | Penggunaan Utama |
|---|---|---|---|
| **Prometheus + Grafana** | Metrik & Visualisasi | Kombinasi standar open source untuk mengumpulkan time-series metrics dan dashboard visual. | Pemantauan RED (Rate, Errors, Duration) dan USE metrics server. |
| **Loki** / **Vector** | Log Aggregation | Agregasi log terstruktur hemat biaya tanpa indeks teks lengkap berat seperti Elasticsearch. | Pelacakan error di production via correlation ID. |
| **OpenTelemetry (OTel)** | Distributed Tracing | Standar vendor-agnostik untuk instrumentasi tracing request lintas layanan backend. | Investigasi bottleneck latensi pada arsitektur terdistribusi. |
| **Sentry** / **GlitchTip** | Error Tracking | Pelacakan real-time exception aplikasi lengkap dengan stack trace, release version, dan context user. | Notifikasi dini insiden SEV-1/SEV-2 di production. |

---

## 5. Manajemen Lingkungan & Delivery

| Tool | Kategori | Keunggulan | Penggunaan Utama |
|---|---|---|---|
| **Docker & Docker Compose** | Kontainerisasi | Menjamin kesamaan lingkungan pengembangan lokal dengan lingkungan produksi (*parity*). | Menjalankan database PostgreSQL, Redis, dan mock services lokal. |
| **Act (nektos/act)** | Local CI Runner | Menjalankan workflow GitHub Actions secara lokal menggunakan Docker tanpa perlu push ke remote. | Debugging pipeline CI/CD sebelum merge ke main branch. |
| **direnv** / **dotenvx** | Environment Manager | Menginjeksi environment variables secara aman dan otomatis saat berpindah direktori terminal. | Pengelolaan variabel lingkungan lokal tanpa risiko commit secret. |
