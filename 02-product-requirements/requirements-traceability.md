# Requirements Traceability

## Apa Itu Traceability?

Requirements traceability adalah kemampuan untuk **melacak setiap requirement** dari asal-usulnya (tujuan bisnis) hingga implementasi (kode) dan verifikasi (test case) — dan sebaliknya. Setiap baris kode yang ditulis harus bisa dijawab: "Ini untuk memenuhi requirement yang mana?"

## Mengapa Traceability Penting?

### 1. Impact Analysis

Saat requirement berubah, kamu bisa langsung menjawab:
- Modul kode mana yang terdampak?
- Test case mana yang harus diperbarui?
- Tim mana yang perlu diberitahu?

**Tanpa traceability:** Perubahan satu requirement bisa menyebabkan bug di tempat yang tidak terduga, karena tidak ada yang tahu kode mana yang terhubung.

### 2. Test Coverage

- Memastikan setiap requirement punya minimal satu test case
- Mengidentifikasi requirement yang belum diuji (gap)
- Menghindari test case yang tidak terhubung ke requirement manapun (test tanpa tujuan)

### 3. Audit dan Compliance

- Regulasi tertentu (ISO, SOC2, HIPAA) **mewajibkan** traceability
- Membuktikan bahwa fitur yang di-deploy sesuai dengan yang disetujui
- Mempermudah audit internal maupun eksternal

### 4. Mencegah Gold Plating

Fitur atau kode yang tidak bisa di-trace ke requirement bisnis = kemungkinan gold plating (menambah hal yang tidak diminta). Traceability membantu mendeteksi ini lebih awal.

## Rantai Traceability (Traceability Chain)

Setiap requirement mengalir dari level bisnis ke level teknis:

```
Tujuan Bisnis (Business Goal)
    │
    ▼
Kebutuhan di PRD (Product Requirement)
    │
    ▼
User Story / Use Case
    │
    ▼
Design (UI/UX Mockup, API Spec)
    │
    ▼
Kode (Module, Service, Component)
    │
    ▼
Test Case (Unit, Integration, E2E)
```

### Contoh Rantai Lengkap

```
BG-01: "Meningkatkan konversi checkout sebesar 15% di Q3"
  └─ PRD-CHECKOUT-01: "Pengguna bisa checkout tanpa registrasi (guest checkout)"
       └─ US-301: "Sebagai pembeli, saya ingin checkout tanpa membuat akun"
            ├─ DESIGN: Mockup halaman guest checkout (Figma link)
            ├─ CODE: checkout-service/guest-checkout.ts, OrderController.ts
            └─ TEST: TC-CHK-001 (happy path), TC-CHK-002 (validasi email),
                     TC-CHK-003 (payment failure)
```

## Requirements Traceability Matrix (RTM)

RTM adalah tabel yang memetakan hubungan antar artefak di setiap level.

### Format RTM

| ID Requirement | Deskripsi | User Story | Design Artifact | Modul / Service | Test Case | Status |
|---|---|---|---|---|---|---|
| FR-AUTH-01 | Login dengan email & password | US-101 | auth-login-mockup.fig | auth-service/login.ts | TC-AUTH-001, TC-AUTH-002, TC-AUTH-003 | ✅ Verified |
| FR-AUTH-02 | Reset password via email | US-102 | auth-reset-mockup.fig | auth-service/reset.ts, email-service/send.ts | TC-AUTH-010, TC-AUTH-011 | ✅ Verified |
| FR-PAY-01 | Pembayaran via transfer bank | US-201 | payment-flow.fig | payment-service/bank-transfer.ts | TC-PAY-001, TC-PAY-002 | 🔄 In Progress |
| FR-PAY-02 | Refund otomatis untuk pembatalan | US-205 | refund-flow.fig | payment-service/refund.ts | TC-PAY-020 | ⚠️ Not Tested |
| NFR-PERF-01 | Response time API < 500ms | - | - | Semua endpoint API | TC-PERF-001 | ✅ Verified |

### Status yang Digunakan

| Status | Arti |
|---|---|
| ✅ Verified | Requirement sudah diimplementasikan dan semua test case pass |
| 🔄 In Progress | Sedang dalam pengembangan atau pengujian |
| ⚠️ Not Tested | Sudah diimplementasikan tapi belum ada test case atau belum diuji |
| ❌ Failed | Test case gagal, butuh perbaikan |
| 📋 Planned | Belum dikerjakan, masih di backlog |

> Template lengkap: [Traceability Matrix Template](../templates/governance/traceability-matrix-template.md)

## Bidirectional Traceability

Traceability yang efektif bekerja **dua arah**:

### Forward Traceability (Maju)

```
Requirement → Design → Code → Test
```

Menjawab: "Requirement ini sudah diimplementasikan dan diuji belum?"

Berguna untuk:
- Memastikan semua requirement ter-cover
- Mengidentifikasi requirement yang belum diimplementasikan
- Melacak progress development

### Backward Traceability (Mundur)

```
Test → Code → Design → Requirement → Business Goal
```

Menjawab: "Kode ini ditulis untuk memenuhi kebutuhan bisnis yang mana?"

Berguna untuk:
- Memvalidasi bahwa semua kode punya justifikasi bisnis
- Mendeteksi orphan code (kode tanpa requirement)
- Impact analysis saat refactoring: "Kalau kode ini dihapus, requirement mana yang terdampak?"

## Tools dan Praktik

Pilih level yang sesuai dengan ukuran tim dan kompleksitas proyek.

### Level 1: Spreadsheet (Solo Dev / Tim 1-3 Orang)

**Tool:** Google Sheets, Excel, atau Markdown table di repo.

```markdown
<!-- docs/traceability.md di root repo -->
| Req ID | Story | Code | Test | Status |
|--------|-------|------|------|--------|
| FR-01  | #12   | src/auth/login.ts | tests/auth/login.test.ts | Done |
| FR-02  | #15   | src/payment/charge.ts | tests/payment/charge.test.ts | In Progress |
```

**Kelebihan:** Zero cost, mudah dimulai.
**Kekurangan:** Manual update, mudah stale.

**Tips:** Review traceability matrix setiap sprint review. Jadikan bagian dari Definition of Done: "RTM diperbarui."

### Level 2: Issue Tracker Labels (Tim 3-8 Orang)

**Tool:** GitHub Issues/Projects, Jira, Linear.

**Praktik:**
1. Buat label per requirement ID: `req:FR-AUTH-01`, `req:FR-PAY-01`
2. Setiap issue/PR yang terkait diberi label requirement
3. Test case di-link ke issue via mention atau custom field
4. Gunakan fitur "linked issues" untuk hubungkan story → task → bug

```
GitHub Issue #45: "Implementasi guest checkout"
  Labels: req:FR-CHK-01, type:feature, priority:high
  Linked to: PR #67, PR #72
  Test: Disebutkan di test plan issue #50
```

**Kelebihan:** Terintegrasi dengan workflow development, semi-otomatis.
**Kekurangan:** Butuh disiplin labeling, bisa berantakan tanpa konvensi.

### Level 3: Dedicated Tools (Tim >8 Orang / Regulated Industry)

**Tool:** Jira + Xray/Zephyr, Azure DevOps, Jama Connect, IBM DOORS.

**Fitur:**
- Traceability otomatis dari requirement → test case → defect
- Coverage report: persentase requirement yang sudah di-test
- Impact analysis otomatis saat requirement berubah
- Audit trail lengkap

**Kelebihan:** Komprehensif, cocok untuk compliance.
**Kekurangan:** Cost tinggi, learning curve, overhead setup.

## Memelihara Traceability Tanpa Overhead Berlebihan

### Prinsip: Cukup Baik > Sempurna

Traceability yang 80% akurat dan di-maintain > traceability 100% detail tapi tidak pernah diupdate.

### Praktik yang Realistis

1. **Embed di workflow, bukan tambahan** — Konvensi commit message atau PR template yang otomatis link ke requirement

   ```
   # PR Template
   ## Related Requirement
   - Req ID: FR-AUTH-01
   - User Story: #45
   ```

2. **Update saat ada perubahan, bukan berkala** — Jangan jadwalkan "traceability update meeting". Update saat:
   - Story selesai → update status di RTM
   - Requirement berubah → update impacted items
   - Test case gagal → update status

3. **Automasi yang murah** — Gunakan naming convention yang bisa di-grep

   ```
   // Di code comment
   // REQ: FR-AUTH-01 - Login dengan email & password

   // Di test file
   describe('FR-AUTH-01: Login dengan email & password', () => { ... });
   ```

4. **Review di sprint review** — Tampilkan RTM sebagai bagian dari demo. Stakeholder bisa lihat progress per requirement.

5. **Jangan trace semua** — Fokus pada critical path dan regulated requirements. Bug fix minor tidak perlu di-trace sampai business goal.

## Kesalahan Umum

| Kesalahan | Dampak | Cara Menghindari |
|---|---|---|
| **Tidak mulai dari awal** | Retroaktif membuat RTM sangat mahal | Mulai simple (spreadsheet), tingkatkan gradual |
| **Terlalu detail** | Overhead maintenance membunuh adopsi | Trace di level requirement, bukan di level sub-task |
| **RTM tidak pernah di-update** | Data stale → tidak dipercaya → tidak dipakai | Embed update di workflow (DoD, PR template) |
| **Hanya forward traceability** | Tidak bisa jawab "kode ini untuk apa?" | Tambahkan backward trace minimal via code comment |
| **Trace semuanya** | Effort tidak proporsional dengan value | Prioritaskan: legal/compliance, core business logic, high-risk areas |
| **Satu orang bertanggung jawab** | Bottleneck, single point of failure | Setiap developer update trace untuk kode yang mereka tulis |

## Dokumen Terkait

- [PRD Guide](./prd-guide.md) — sumber requirement yang di-trace
- [Functional Requirements](./functional-requirements.md) — daftar requirement ID yang masuk RTM
- [Acceptance Criteria](./acceptance-criteria.md) — kriteria yang menjadi basis test case di RTM
- [Requirements Change Management](./requirements-change-management.md) — proses saat requirement berubah dan RTM harus di-update
- [Traceability Matrix Template](../templates/governance/traceability-matrix-template.md) — template siap pakai
