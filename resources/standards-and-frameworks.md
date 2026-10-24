# Standar Internasional & Kerangka Kerja Industri

Dokumen ini memuat daftar standar industri, kerangka kerja formal, dan spesifikasi terbuka yang menjadi acuan teoretis serta praktis dalam Software Development Playbook ini.

> **Catatan Kepatuhan:** Mengacu pada standar di bawah ini tidak secara otomatis memberikan sertifikasi formal (*compliance certification*). Penggunaan standar di repositori ini bertujuan mengadopsi praktik terbaik (*industry best practices*) secara proporsional.

---

## 1. Standar Rekayasa Perangkat Lunak (ISO / IEC / IEEE)

### 1.1 ISO/IEC/IEEE 12207:2017 — Systems and Software Engineering — Software Life Cycle Processes
- **Penerbit**: International Organization for Standardization (ISO) / IEEE Computer Society
- **Cakupan**: Kerangka kerja umum untuk proses siklus hidup perangkat lunak dari konsep, pengembangan, operasi, hingga pensiun (*decommissioning*).
- **Penerapan di Playbook**: Menjadi landasan fase SDLC di [`00-foundations/software-development-lifecycle.md`](../00-foundations/software-development-lifecycle.md) dan proses gerbang rilis di [`08-project-management/stage-gate-process.md`](../08-project-management/stage-gate-process.md).
- **Kapan Digunakan**: Standar rujukan formal untuk pengadaan perangkat lunak skala korporasi, tender instansi, dan audit tata kelola TI.

### 1.2 ISO/IEC/IEEE 29148:2018 — Systems and Software Engineering — Life Cycle Processes — Requirements Engineering
- **Penerbit**: ISO / IEC / IEEE
- **Cakupan**: Proses rekayasa kebutuhan perangkat lunak, struktur dokumen kebutuhan (seperti SyRS/SRS), dan kriteria atribut kebutuhan yang baik (atomik, konsisten, dapat diverifikasi).
- **Penerapan di Playbook**: Diadopsi dalam penyusunan panduan PRD di [`02-product-requirements/prd-guide.md`](../02-product-requirements/prd-guide.md) dan verifikasi kebutuhan di [`checklists/requirements-quality.md`](../checklists/requirements-quality.md).

### 1.3 ISO/IEC 25010:2011 — Systems and Software Engineering — Systems and Software Quality Requirements and Evaluation (SQuaRE) — System and Software Quality Models
- **Penerbit**: ISO / IEC
- **Cakupan**: Model kualitas produk perangkat lunak yang mencakup 8 karakteristik utama: Functional Suitability, Performance Efficiency, Compatibility, Usability, Reliability, Security, Maintainability, dan Portability.
- **Penerapan di Playbook**: Digunakan sebagai taksonomi pengelompokan persyaratan non-fungsional di [`04-technical-design/non-functional-requirements.md`](../04-technical-design/non-functional-requirements.md).

---

## 2. Standar Keamanan Aplikasi & Web

### 2.1 OWASP Top 10 (2021)
- **Penerbit**: Open Worldwide Application Security Project (OWASP)
- **Cakupan**: Daftar 10 risiko keamanan paling kritis pada aplikasi web (Broken Access Control, Cryptographic Failures, Injection, Insecure Design, Security Misconfiguration, dll.).
- **Tautan Resmi**: [owasp.org/Top10](https://owasp.org/Top10/)
- **Penerapan di Playbook**: Panduan mitigasi keamanan di [`04-technical-design/security-and-privacy.md`](../04-technical-design/security-and-privacy.md) dan checklist audit di [`checklists/security-review.md`](../checklists/security-review.md).

### 2.2 OWASP Application Security Verification Standard (ASVS) v4.0.3
- **Penerbit**: OWASP
- **Cakupan**: Kerangka kerja pengujian dan verifikasi kontrol keamanan teknis aplikasi web berjenjang (Level 1: Opportunistic, Level 2: Standard, Level 3: Advanced).
- **Penerapan di Playbook**: Digunakan sebagai dasar butir pemeriksaan checklist keamanan pra-rilis.

---

## 3. Standar Aksesibilitas & Dokumentasi

### 3.1 Web Content Accessibility Guidelines (WCAG) 2.1 Level AA
- **Penerbit**: World Wide Web Consortium (W3C) Web Accessibility Initiative (WAI)
- **Cakupan**: Standar internasional untuk membuat konten web dapat diakses oleh penyandang disabilitas dengan 4 prinsip: Perceivable, Operable, Understandable, Robust.
- **Tautan Resmi**: [w3.org/TR/WCAG21](https://www.w3.org/TR/WCAG21/)
- **Penerapan di Playbook**: Panduan aksesibilitas di [`03-ux-ui-design/accessibility-and-responsive-design.md`](../03-ux-ui-design/accessibility-and-responsive-design.md).

### 3.2 Diátaxis Documentation Framework
- **Pencetus**: Daniele Procida
- **Cakupan**: Arsitektur penyusunan dokumentasi teknis yang membagi dokumen ke dalam 4 kuadran: Tutorials (belajar praktis), How-To Guides (menyelesaikan masalah), Reference (informasi teknis), dan Explanation (penjelasan konsep).
- **Tautan Resmi**: [diataxis.fr](https://diataxis.fr/)
- **Penerapan di Playbook**: Landasan penulisan dokumentasi di [`00-foundations/documentation-principles.md`](../00-foundations/documentation-principles.md).

---

## 4. Spesifikasi Teknis & Metodologi Arsitektur

### 4.1 RFC 7807 — Problem Details for HTTP APIs
- **Penerbit**: Internet Engineering Task Force (IETF)
- **Cakupan**: Standar format payload respons error pada REST API berbasis JSON/XML (`type`, `title`, `status`, `detail`, `instance`).
- **Tautan Resmi**: [datatracker.ietf.org/doc/html/rfc7807](https://datatracker.ietf.org/doc/html/rfc7807)
- **Penerapan di Playbook**: Diadopsi penuh pada [`04-technical-design/api-error-handling.md`](../04-technical-design/api-error-handling.md) dan template spesifikasi API.

### 4.2 The Twelve-Factor App
- **Pencetus**: Adam Wiggins (Heroku)
- **Cakupan**: 12 metodologi perancangan aplikasi perangkat lunak cloud-native modern (Codebase, Dependencies, Config, Backing Services, Build-Release-Run, Processes, Port Binding, Concurrency, Disposability, Dev/Prod Parity, Logs, Admin Processes).
- **Tautan Resmi**: [12factor.net](https://12factor.net/)
- **Penerapan di Playbook**: Prinsip environment di [`05-development-practices/environment-configuration.md`](../05-development-practices/environment-configuration.md) dan [`07-delivery-operations/environment-strategy.md`](../07-delivery-operations/environment-strategy.md).

### 4.3 Semantic Versioning (SemVer) 2.0.0
- **Pencetus**: Tom Preston-Werner
- **Cakupan**: Aturan penomoran rilis formal `MAJOR.MINOR.PATCH` untuk mengkomunikasikan dampak perubahan secara deterministik kepada pengguna dependensi.
- **Tautan Resmi**: [semver.org](https://semver.org/)
- **Penerapan di Playbook**: Standar rilis di [`07-delivery-operations/release-management.md`](../07-delivery-operations/release-management.md).

### 4.4 Conventional Commits 1.0.0
- **Cakupan**: Konvensi penulisan pesan commit Git terstruktur (`type(scope): subject`) yang memudahkan otomatisasi changelog dan penentuan versi rilis.
- **Tautan Resmi**: [conventionalcommits.org](https://www.conventionalcommits.org/)
- **Penerapan di Playbook**: Diuraikan pada [`00-foundations/git-and-github-workflow.md`](../00-foundations/git-and-github-workflow.md) dan [`05-development-practices/branching-and-commits.md`](../05-development-practices/branching-and-commits.md).

---

## 5. Ringkasan Kapan Mengadopsi Standar

| Kategori Proyek | Standar Wajib Diterapkan | Standar Opsional / Situasional |
|---|---|---|
| **Proyek Pribadi / Solo Dev** | SemVer, Conventional Commits, 12-Factor (Config) | ISO 12207, ISO 29148 |
| **Aplikasi Web Publik / Startup** | OWASP Top 10, WCAG 2.1 AA, RFC 7807, 12-Factor | ISO 25010, OWASP ASVS Level 2 |
| **Sistem FinTech / Enterprise** | OWASP ASVS Level 2/3, ISO 25010, ISO 29148, RFC 7807 | ISO 12207 formal audit |
