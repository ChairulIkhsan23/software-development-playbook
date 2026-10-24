# Jenis-Jenis Proyek Software

Panduan ini membantu developer memilih pendekatan yang tepat berdasarkan jenis proyek. Setiap jenis punya karakteristik, arsitektur, dan risiko yang berbeda — keputusan awal di sini menentukan arah seluruh development lifecycle.

## Tabel Perbandingan

| Jenis Proyek | Kompleksitas Deploy | Kedalaman Dokumentasi | Strategi Testing | Kebutuhan Security |
|---|---|---|---|---|
| Web App (SPA/MPA/SSR) | Sedang | Sedang — API contract, component docs | Unit + E2E + visual regression | XSS, CSRF, CSP, auth |
| REST API / Backend Service | Rendah–Sedang | Tinggi — OpenAPI spec wajib | Unit + integration + contract test | AuthZ, rate limiting, input validation |
| Mobile App | Tinggi | Sedang — platform-specific guides | Unit + UI + device matrix | Data encryption at rest, cert pinning, store compliance |
| SaaS Multi-tenant | Tinggi | Tinggi — tenant isolation, SLA docs | Full pyramid + load test + chaos | Tenant isolation, compliance (GDPR/SOC2), encryption |
| Internal Tools / Dashboard | Rendah | Rendah — README cukup | Smoke test + integration | RBAC, network-level access |
| CLI Tools | Rendah | Sedang — `--help` + man page / README | Unit + integration + snapshot | Input sanitization, minimal attack surface |
| Static Website / Landing Page | Rendah | Rendah — deployment note saja | Visual regression + link check | CSP headers, dependency audit |

## 1. Web Application

### Varian

- **SPA (Single Page Application)** — satu HTML, rendering di browser, navigasi tanpa full reload. Cocok untuk app interaktif (dashboard, editor, chat).
- **MPA (Multi Page Application)** — setiap route = request baru ke server. Cocok untuk content-heavy site dengan SEO penting.
- **SSR/SSG (Server-Side Rendering / Static Site Generation)** — HTML di-render di server atau build time. Gabungan performa SPA dengan SEO MPA.

### Arsitektur Tipikal

```
Browser → CDN → Web Server / Edge Function → API Layer → Database
```

SPA: thick client + thin API. SSR: server render + hydration. SSG: pre-built HTML + optional API.

### Pertimbangan Utama

- **Performance**: Core Web Vitals (LCP, CLS, INP). Code splitting, lazy loading, image optimization.
- **Auth**: Session-based (MPA) atau token-based (SPA). Pertimbangkan OAuth2 / OIDC untuk third-party login.
- **Deployment**: Static files ke CDN (SPA/SSG). Container atau serverless untuk SSR. Environment variables untuk config.
- **State management**: Lokal state vs global store — pilih berdasarkan kompleksitas, bukan popularitas framework.

### Tech Stack Options

Frontend: React, Vue, Svelte, Angular. Meta-framework: Next.js, Nuxt, SvelteKit, Astro. Styling: Tailwind CSS, CSS Modules.

### Bagian Playbook Terkait

- [UX/UI Design](../03-ux-ui-design/) — user flows, responsive design, accessibility
- [Non-Functional Requirements](../04-technical-design/non-functional-requirements.md) — performance targets
- [Deployment Guide](../07-delivery-operations/deployment-guide.md)

## 2. REST API / Backend Service

### Karakteristik

Headless service yang melayani client (web, mobile, third-party). Fokus pada contract stability, throughput, dan backward compatibility.

### Arsitektur Tipikal

```
Client → API Gateway / Load Balancer → App Server → Database / Cache / Message Queue
```

Monolith dulu. Pecah ke microservices hanya ketika ada alasan konkret (scaling independen, tim terpisah).

### Pertimbangan Utama

- **Performance**: Connection pooling, query optimization, caching (Redis/Memcached). Pagination wajib untuk list endpoint.
- **Auth**: JWT atau API key. Rate limiting per client. Scope-based authorization.
- **Deployment**: Containerized (Docker). Blue-green atau rolling update. Database migration terpisah dari app deploy.
- **Versioning**: URL path (`/v1/`) atau header. Jangan break existing client.
- **Idempotency**: POST/PUT harus idempotent untuk retry safety.

### Tech Stack Options

Runtime: Node.js (Express/Fastify), Python (FastAPI/Django), Go (net/http, Gin), Java (Spring Boot), .NET. Database: PostgreSQL, MySQL, MongoDB. Message queue: RabbitMQ, Redis Streams, Kafka.

### Bagian Playbook Terkait

- [API Design](../04-technical-design/api-design.md) — naming, pagination, versioning
- [API Error Handling](../04-technical-design/api-error-handling.md)
- [Authentication & Authorization](../04-technical-design/authentication-and-authorization.md)
- [API Testing](../06-quality-assurance/api-testing.md)

## 3. Mobile Application

### Varian

- **Native**: Swift/Kotlin — performa maksimal, akses penuh ke API platform. Butuh dua codebase.
- **Cross-platform**: Flutter, React Native, Kotlin Multiplatform — satu codebase, trade-off di performa dan akses native API.

### Arsitektur Tipikal

```
Mobile App → API Layer → Backend Services → Database
         └→ Local Storage (SQLite / Realm) untuk offline support
```

### Pertimbangan Utama

- **Performance**: Startup time, memori, konsumsi baterai. Lazy load heavy modules.
- **Auth**: Secure token storage (Keychain / Keystore). Biometric auth. Session refresh tanpa re-login.
- **Deployment**: App Store / Play Store review cycle (1–7 hari). OTA update untuk fix cepat (CodePush, Shorebird). Forced update mechanism untuk breaking change.
- **Offline**: Tentukan fitur mana yang harus jalan offline. Sync strategy (conflict resolution).
- **Device fragmentation**: Test di berbagai ukuran layar dan versi OS.

### Tech Stack Options

Native: Swift (iOS), Kotlin (Android). Cross-platform: Flutter (Dart), React Native, .NET MAUI. Backend: sama seperti REST API di atas.

### Bagian Playbook Terkait

- [System Architecture](../04-technical-design/system-architecture.md)
- [Test Strategy](../06-quality-assurance/test-strategy.md) — device matrix testing
- [Release Management](../07-delivery-operations/release-management.md)

## 4. SaaS / Multi-tenant

### Karakteristik

Aplikasi yang diakses banyak tenant (organisasi/user) dengan data terisolasi, billing terintegrasi, dan ekspektasi uptime tinggi (SLA 99.9%+).

### Arsitektur Tipikal

```
Tenant → Load Balancer → App Cluster → Shared DB (RLS) atau DB per tenant
                                     → Job Queue (billing, email, reports)
                                     → Object Storage (uploads)
```

Dua model utama: **shared database** dengan Row-Level Security (RLS) untuk cost efficiency, atau **database per tenant** untuk isolasi ketat.

### Pertimbangan Utama

- **Tenant isolation**: Data satu tenant tidak boleh bocor ke tenant lain. RLS di database level, bukan hanya application level.
- **Auth**: Multi-tenant identity. Tenant-specific SSO/SAML. Role hierarchy per tenant.
- **Billing**: Subscription management, usage metering, invoice. Integrasikan Stripe/Paddle/LemonSqueezy.
- **Deployment**: Zero-downtime deploy wajib. Feature flags untuk gradual rollout. Canary deployment.
- **Compliance**: GDPR (data deletion per tenant), SOC 2, data residency requirements.
- **Observability**: Per-tenant metrics, alerting, cost tracking.

### Tech Stack Options

Backend: sama seperti REST API. Database: PostgreSQL (RLS), CockroachDB. Infrastructure: Kubernetes, Terraform. Billing: Stripe, Paddle. Auth: Auth0, Clerk, Keycloak.

### Bagian Playbook Terkait

- [Security & Privacy](../04-technical-design/security-and-privacy.md)
- [Database Design](../04-technical-design/database-design.md) — multi-tenant schema
- [Monitoring & Observability](../07-delivery-operations/monitoring-and-observability.md)
- [Incident Response](../07-delivery-operations/incident-response.md)

## 5. Internal Tools / Admin Dashboard

### Karakteristik

Dipakai tim internal untuk operasional: CRUD data, report, approval workflow. Pengguna terbatas dan terpercaya. Prioritas: kecepatan development > polish UI.

### Arsitektur Tipikal

```
Browser → Internal App → Database langsung (atau via existing API)
```

Sering langsung query database tanpa API layer terpisah. Acceptable untuk scope internal.

### Pertimbangan Utama

- **Auth**: SSO via corporate identity (Google Workspace, Azure AD). RBAC — siapa boleh lihat/edit apa.
- **Performance**: Bukan prioritas utama, tapi query berat harus async atau paginated.
- **Deployment**: Internal network atau VPN-gated. CI/CD sederhana cukup.
- **UX**: Functional > pretty. Tapi tetap usable — internal user juga manusia.

### Tech Stack Options

Low-code: Retool, Appsmith, Tooljet. Custom: Next.js, Laravel, Django Admin. Visualisasi: Metabase, Grafana.

### Bagian Playbook Terkait

- [Authentication & Authorization](../04-technical-design/authentication-and-authorization.md)
- [Coding Standards](../05-development-practices/coding-standards.md)

## 6. CLI Tools

### Karakteristik

Program berbasis terminal untuk automasi, developer tooling, atau system administration. Dijalankan manual atau di CI/CD pipeline.

### Arsitektur Tipikal

```
stdin/args → Parser → Command Handler → Output (stdout/stderr) + Exit Code
```

### Pertimbangan Utama

- **UX**: Help text yang jelas (`--help`). Consistent flag naming (`--verbose`, `--output`). Progress indicator untuk operasi lama.
- **Performance**: Startup time cepat. Minimal dependency. Distribusi sebagai single binary ideal.
- **Error handling**: Exit code yang benar (0 = sukses, non-zero = error). Error message ke stderr, output ke stdout.
- **Deployment**: Distribusi via package manager (npm, pip, brew, cargo) atau binary download.
- **Composability**: Bisa di-pipe dengan tools lain. Support `--json` output untuk scripting.

### Tech Stack Options

Compiled: Go, Rust. Scripted: Python (Click/Typer), Node.js (Commander/yargs), Bash. Parser: cobra (Go), clap (Rust).

### Bagian Playbook Terkait

- [Coding Standards](../05-development-practices/coding-standards.md)
- [Unit Testing](../06-quality-assurance/unit-testing.md)
- [CI/CD](../07-delivery-operations/ci-cd.md) — automated release

## 7. Static Website / Landing Page

### Karakteristik

Content statis atau semi-statis: marketing page, dokumentasi, blog, portfolio. Minim interaktivitas. Deploy sekali, serve dari CDN.

### Arsitektur Tipikal

```
Markdown/CMS → Static Site Generator → HTML/CSS/JS → CDN
```

### Pertimbangan Utama

- **Performance**: Target Lighthouse 95+. Minimal JavaScript. Optimasi gambar (WebP/AVIF, lazy load).
- **SEO**: Meta tags, Open Graph, structured data, sitemap.xml, robots.txt.
- **Deployment**: Push ke CDN (Vercel, Netlify, Cloudflare Pages). Otomatis dari git push.
- **Auth**: Biasanya tidak perlu. Kalau perlu gated content, pakai third-party (Auth0, Supabase Auth).
- **Content update**: Headless CMS untuk non-developer editor. Atau langsung edit Markdown di repo.

### Tech Stack Options

Generator: Astro, Hugo, 11ty, Jekyll, Docusaurus. CMS: Sanity, Contentful, Strapi. Hosting: Vercel, Netlify, Cloudflare Pages, GitHub Pages.

### Bagian Playbook Terkait

- [Accessibility & Responsive Design](../03-ux-ui-design/accessibility-and-responsive-design.md)
- [Deployment Guide](../07-delivery-operations/deployment-guide.md)

## Bagaimana Jenis Proyek Mempengaruhi Proses

| Aspek | Proyek Sederhana (Static, CLI) | Proyek Menengah (Web App, API) | Proyek Kompleks (SaaS, Mobile) |
|---|---|---|---|
| **Dokumentasi** | README + deployment note | API spec, architecture doc, developer guide | Full documentation set termasuk runbook, SLA, compliance |
| **Testing** | Unit test + smoke test | Test pyramid lengkap | + load test, chaos engineering, device matrix |
| **Deployment** | `git push` ke CDN atau binary release | CI/CD pipeline, staging environment | Multi-stage pipeline, canary, feature flags |
| **Security** | CSP headers, dependency audit | Auth, input validation, rate limiting | + tenant isolation, encryption, compliance audit |
| **Monitoring** | Uptime check cukup | Error tracking, basic metrics | Full observability: traces, metrics, logs, alerting |

## Panduan Memilih

1. **Mulai dari masalah**, bukan tech stack. Tentukan siapa user dan apa yang mereka butuhkan.
2. **Pilih arsitektur paling sederhana** yang memenuhi kebutuhan. Monolith > microservices untuk tim kecil.
3. **Jenis proyek bisa berubah** — landing page bisa berkembang jadi web app, internal tool bisa jadi SaaS. Rancang untuk hari ini, refactor ketika perlu.
4. **Baca bagian playbook yang relevan** sesuai jenis proyek, bukan semuanya sekaligus.

## Dokumen Terkait

- [Software Development Lifecycle](software-development-lifecycle.md) — fase-fase development
- [Software Engineering Principles](software-engineering-principles.md) — prinsip dasar
- [Architecture Patterns](../04-technical-design/architecture-patterns.md) — monolith, microservices, serverless
- [System Architecture](../04-technical-design/system-architecture.md) — cara mendokumentasikan arsitektur
- [Test Strategy](../06-quality-assurance/test-strategy.md) — menentukan testing approach
- [Deployment Guide](../07-delivery-operations/deployment-guide.md) — strategi deployment
- [Security & Privacy](../04-technical-design/security-and-privacy.md) — keamanan per jenis proyek
