# Kursus Rekomendasi Software Engineering

Daftar kursus terbuka, materi universitas, dan program pembelajaran mandiri berkualitas tinggi yang dapat diakses secara gratis atau terjangkau untuk memperdalam keahlian rekayasa perangkat lunak.

---

## 1. Fondasi Ilmu Komputer & Pemrograman

### 1.1 CS50x: Introduction to Computer Science
- **Penyelenggara**: Harvard University (via edX)
- **Akses**: Gratis (Audit mode) di [cs50.harvard.edu/x](https://cs50.harvard.edu/x/)
- **Cakupan**: Algoritma, struktur data, manajemen memori (C), pemrograman berorientasi objek (Python), SQL, dan dasar web development.
- **Cocok Untuk**: Mahasiswa IT, pemula, dan self-taught developer yang membutuhkan pemahaman komputasi tingkat rendah yang kuat.

### 1.2 The Missing Semester of Your CS Education
- **Penyelenggara**: Massachusetts Institute of Technology (MIT)
- **Akses**: Gratis di [missing.csail.mit.edu](https://missing.csail.mit.edu/)
- **Cakupan**: Keterampilan praktis yang jarang diajarkan di kelas formal: Shell scripting (Bash), Git version control, editor Vim, SSH, build systems, dan data wrangling.
- **Relevansi di Playbook**: Sangat sejalan dengan [`00-foundations/git-and-github-workflow.md`](../00-foundations/git-and-github-workflow.md) dan efisiensi lingkungan kerja developer.

---

## 2. Full-Stack Web Development & Modern Practices

### 2.1 Full Stack Open
- **Penyelenggara**: University of Helsinki
- **Akses**: Gratis di [fullstackopen.com](https://fullstackopen.com/)
- **Cakupan**: React, Redux, Node.js, Express, REST API, GraphQL, TypeScript, React Native, CI/CD, dan containerization dengan Docker.
- **Keunggulan**: Menekankan pengujian otomatis (Jest, Cypress, Playwright) dan standar coding modern berbasis TypeScript.
- **Relevansi di Playbook**: Materi testing dan arsitektur web modern pada folder [`05-development-practices`](../05-development-practices/README.md) dan [`06-quality-assurance`](../06-quality-assurance/README.md).

### 2.2 Testing JavaScript with Kent C. Dodds
- **Penyelenggara**: Epic Web / Kent C. Dodds
- **Akses**: Berbayar (dengan beberapa materi pengantar gratis) di [testingjavascript.com](https://testingjavascript.com/)
- **Cakupan**: Pengujian piramida dan "Testing Trophy": Unit testing, Integration testing, Mock Service Worker (MSW), dan End-to-End testing dengan Cypress/Playwright.
- **Relevansi di Playbook**: Memperkuat konsep pada [`06-quality-assurance/test-strategy.md`](../06-quality-assurance/test-strategy.md).

---

## 3. Desain Sistem & Arsitektur Perangkat Lunak

### 3.1 System Design Primer (Open Source Curriculum)
- **Pencetus**: Donne Martin
- **Akses**: Gratis di [github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
- **Cakupan**: Skalabilitas sistem, DNS, CDN, Load Balancer, Caching, Sharding database, komunikasi asinkron (Message Queues), dan analisis bottleneck performa.
- **Relevansi di Playbook**: Rujukan pelengkap untuk [`04-technical-design/system-architecture.md`](../04-technical-design/system-architecture.md) dan NFR.

### 3.2 Distributed Systems Course (MIT 6.824 / 6.5840)
- **Penyelenggara**: MIT (Prof. Robert Morris)
- **Akses**: Video kuliah dan lab terbuka di [pdos.csail.mit.edu/6.824/](https://pdos.csail.mit.edu/6.824/)
- **Cakupan**: Konsensus terdistribusi (Raft, Paxos), replikasi data, toleransi kesalahan (*fault tolerance*), dan sistem penyimpanan terdistribusi.
- **Cocok Untuk**: Senior engineer yang membangun sistem multi-node berkeandalan tinggi.

---

## 4. Keamanan & Cloud Native

### 4.1 Web Security Academy
- **Penyelenggara**: PortSwigger (Pembuat Burp Suite)
- **Akses**: Gratis di [portswigger.net/web-security](https://portswigger.net/web-security)
- **Cakupan**: Laboratorium interaktif gratis untuk mempelajari kerentanan web: SQL Injection, XSS, CSRF, SSRF, broken authentication, dan insecure direct object references.
- **Relevansi di Playbook**: Panduan praktis mitigasi OWASP Top 10 di [`04-technical-design/security-and-privacy.md`](../04-technical-design/security-and-privacy.md).
