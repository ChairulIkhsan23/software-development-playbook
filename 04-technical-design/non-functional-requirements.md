# Non-Functional Requirements (NFR)

## Apa Itu NFR?

Non-Functional Requirements menentukan **bagaimana** sistem bekerja, bukan **apa** yang sistem lakukan.
Functional requirement: "User bisa checkout pesanan." NFR: "Checkout selesai dalam < 2 detik pada 95% request."

NFR yang tidak ditentukan sejak awal = keputusan arsitektur yang salah, dan mahal diperbaiki nanti.

---

## Kategori NFR

| Kategori          | Pertanyaan Utama                                    |
|-------------------|------------------------------------------------------|
| **Performance**   | Seberapa cepat sistem merespons?                     |
| **Scalability**   | Seberapa besar beban yang bisa ditangani?            |
| **Availability**  | Seberapa sering sistem bisa diakses?                 |
| **Reliability**   | Seberapa jarang sistem gagal?                        |
| **Security**      | Seberapa tahan terhadap serangan?                    |
| **Privacy**       | Seberapa baik data pengguna dilindungi?              |
| **Maintainability** | Seberapa mudah sistem diubah atau diperbaiki?      |
| **Portability**   | Seberapa mudah dipindah ke environment lain?         |
| **Compatibility** | Seberapa baik bekerja dengan sistem/browser lain?    |
| **Observability** | Seberapa mudah memahami kondisi sistem saat runtime? |
| **Accessibility** | Seberapa mudah digunakan oleh semua orang?           |

---

## Cara Menulis NFR yang Terukur

NFR yang baik punya 4 komponen:

```
[Metrik] + [Target] + [Kondisi] + [Metode Pengukuran]
```

**❌ NFR buruk:** "Sistem harus cepat."
**✅ NFR baik:** "95th percentile API response time < 200ms pada kondisi beban normal (100 concurrent users), diukur menggunakan APM tool."

---

## Tabel Template NFR

| Kategori | Metrik | Target | Kondisi | Metode Pengukuran |
|----------|--------|--------|---------|-------------------|
| **Performance** | p95 API response time | < 200ms (read), < 500ms (write) | 100 concurrent users | APM tracing (Datadog/New Relic) |
| **Performance** | Halaman web load time | < 3 detik (First Contentful Paint) | Koneksi 4G, perangkat mid-range | Lighthouse / WebPageTest |
| **Scalability** | Horizontal scaling time | < 5 menit dari trigger ke ready | Traffic spike 3x normal | Auto-scaling policy + load test |
| **Scalability** | Max concurrent users | 1.000 concurrent tanpa degradasi | Semua fitur aktif | Stress test (k6/Locust) |
| **Availability** | Uptime | 99.9% (max ~43 menit downtime/bulan) | Termasuk planned maintenance | External uptime monitoring |
| **Availability** | Recovery Time Objective (RTO) | < 2 jam | Kegagalan total satu zona | Disaster recovery drill berkala |
| **Reliability** | Recovery Point Objective (RPO) | < 15 menit data loss | Database failure | Replikasi + snapshot interval |
| **Reliability** | Error rate | < 0.1% dari total request | Operasi normal | Error tracking (Sentry) |
| **Security** | Vulnerability scan | 0 critical/high findings | Sebelum setiap release | SAST + dependency scanning |
| **Security** | Penetration test | Lulus tanpa temuan critical | Per kuartal atau major release | Third-party pentest |
| **Privacy** | Data retention | Hapus data inaktif setelah 2 tahun | Akun yang tidak login | Automated cleanup job |
| **Maintainability** | Code coverage | > 80% pada business logic | Setiap merge ke main | CI pipeline |
| **Portability** | Container support | Berjalan di Docker tanpa modifikasi | Semua environment | Dockerfile + CI build test |
| **Compatibility** | Browser support | Chrome, Firefox, Safari, Edge (2 versi terakhir) | Desktop + mobile | Cross-browser test suite |
| **Observability** | Log completeness | Setiap request punya trace ID end-to-end | Semua service | Distributed tracing |
| **Accessibility** | WCAG compliance | Level AA | Semua halaman publik | Axe / manual audit |

> Gunakan template lengkap di [NFR Template](../templates/design/nfr-template.md) untuk proyek Anda.

---

## Prioritisasi NFR

Tidak semua NFR sama pentingnya. Prioritaskan berdasarkan dampak bisnis:

| Prioritas | Kriteria | Contoh |
|-----------|----------|--------|
| **P0 — Critical** | Jika gagal = bisnis berhenti atau data hilang | Availability, data integrity, security |
| **P1 — High** | Jika gagal = user experience sangat buruk | Performance, reliability |
| **P2 — Medium** | Jika gagal = friction tapi masih bisa dipakai | Scalability, maintainability |
| **P3 — Low** | Nice to have, improve over time | Portability, accessibility enhancement |

**Tips:** Diskusikan prioritas NFR dengan stakeholder. Developer sering over-prioritize performance, bisnis sering over-prioritize fitur baru. Keduanya perlu diseimbangkan.

---

## Metode Verifikasi NFR

| Metode | NFR yang Diverifikasi | Tools |
|--------|----------------------|-------|
| **Load testing** | Performance, scalability | k6, Locust, Artillery |
| **Stress testing** | Reliability, availability | k6 (soak test), Chaos Monkey |
| **Security scanning** | Security | OWASP ZAP, Snyk, Trivy |
| **Static code analysis** | Maintainability, security | SonarQube, ESLint, Semgrep |
| **Dependency audit** | Security, compatibility | `npm audit`, Dependabot |
| **Manual audit** | Accessibility, privacy | WCAG checklist, privacy review |
| **Synthetic monitoring** | Availability, performance | Uptime Robot, Pingdom |
| **Chaos engineering** | Reliability, recovery | Chaos Monkey, Litmus |

---

## Dampak NFR terhadap Arsitektur

NFR bukan dokumen yang hanya dibaca — NFR menentukan keputusan arsitektur:

| NFR | Keputusan Arsitektur yang Terpengaruh |
|-----|---------------------------------------|
| High availability (99.99%) | Multi-region deployment, database replication |
| Low latency (< 50ms) | Caching layer (Redis), CDN, read replicas |
| High scalability | Microservices atau event-driven, horizontal scaling |
| Strong security | Zero trust network, encryption at rest, WAF |
| High maintainability | Clean architecture, modular monolith, CI/CD |
| Data privacy compliance | Data residency, audit log, consent management |

---

## NFR untuk Tipe Proyek yang Berbeda

| NFR | Web App | REST API | Mobile App |
|-----|---------|----------|------------|
| Performance focus | Page load time, FCP | Response time, throughput | App launch time, frame rate |
| Availability | CDN + failover | Multi-instance + load balancer | Offline-first capability |
| Security | CSP, XSS prevention | Rate limiting, input validation | Certificate pinning, secure storage |
| Scalability metric | Concurrent sessions | Requests per second | Sync queue size |
| Compatibility | Cross-browser | API versioning | OS version support matrix |

---

## Kesalahan Umum

| Kesalahan | Dampak | Solusi |
|-----------|--------|--------|
| NFR tidak terukur ("harus cepat") | Tidak bisa diverifikasi, jadi tidak pernah tercapai | Tulis dengan format: metrik + target + kondisi |
| NFR baru dipikirkan saat production | Refactor arsitektur yang mahal | Definisikan NFR di fase desain |
| Target NFR terlalu ambisius | Over-engineering, biaya tinggi | Sesuaikan dengan kebutuhan bisnis nyata |
| Tidak ada monitoring untuk NFR | Tidak tahu apakah NFR terpenuhi | Pasang monitoring sesuai metode verifikasi |
| Semua NFR prioritas sama | Tidak ada fokus, semuanya setengah jadi | Gunakan prioritisasi P0–P3 |

---

## Dokumen Terkait

- [System Architecture](system-architecture.md) — Keputusan arsitektur yang dipengaruhi NFR.
- [Security and Privacy](security-and-privacy.md) — Detail NFR keamanan dan privasi.
- [API Design](api-design.md) — Performance NFR untuk API.
- [NFR Template](../templates/design/nfr-template.md) — Template siap pakai untuk mendokumentasikan NFR proyek.
