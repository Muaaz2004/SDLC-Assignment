# 🎓 Smart-Attend — SDLC & DevOps Fundamentals Assignment

> **Course:** SDLC & DevOps Fundamentals  
> **Role:** Junior Project Manager (Release Manager Roleplay)  
> **Project:** Smart-Attend Mobile App — Geo-fence + Biometric Attendance System  
> **Deadline:** 14 March 2026  

---

## 📁 Repository Structure

```
SDLC-Assignment-[YourName]/
├── docs/
│   └── Smart-Attend-SDLC-Analysis.docx   ← Full analysis report (Tasks 1, 2, 4)
├── backlog/
│   └── Smart-Attend-Sprint-Backlog.xlsx   ← Sprint 1 & 2 Backlogs + Product Backlog
├── .github/
│   └── workflows/
│       └── ci-cd.yml                      ← GitHub Actions CI/CD pipeline
└── README.md                              ← This file
```

---

## 📖 Project Summary

**Smart-Attend** is a university startup mobile app that automatically marks student attendance using GPS **Geo-fencing** — when a student enters within 10 metres of a classroom, attendance is marked without any manual action.

**The Pivot:** Mid-development, the Dean required **Biometric (Face ID) Verification** to prevent proxy attendance, forcing a full methodology re-evaluation.

---

## 📋 Task Summaries

### Task 1 — Waterfall Failure Analysis
See `docs/Smart-Attend-SDLC-Analysis.docx` for a detailed breakdown of 3 critical failure points:

| Failure | Description |
|---|---|
| **Structural Rigidity** | Requirements phase is permanently closed once development begins — no formal mechanism to add Face ID mid-project |
| **Cost of Change** | Mid-development changes are exponentially expensive: budget overrun, schedule slippage, full SRS rework |
| **Delayed Testing** | Integration bugs (Face ID ↔ Geo-fence conflicts, device compatibility) discovered only in month 5–6, leaving no time to fix |

---

### Task 2 — Agile Pivot (Scrum Framework)

| Sprint | Duration | Goal | Key Deliverables |
|---|---|---|---|
| **Sprint 1** | Weeks 1–2 | MVP | Login, Geo-fence engine, Teacher dashboard, Notifications |
| **Sprint 2** | Weeks 3–4 | Biometric Update | Face ID (iOS), BiometricPrompt (Android), Privacy controls, Reporting |

---

### Task 3 — Sprint 1 Backlog (Summary)

| Task ID | Story Summary | Priority | Est. Hours |
|---|---|---|---|
| US01 | Student login via university ID | High | 4 hrs |
| US02 | Teacher real-time attendance list | High | 6 hrs |
| US03 | 10m geo-fence detection | High | 8 hrs |
| US04 | Push notification on attendance mark | Medium | 3 hrs |
| US05 | Admin manual override | Low | 4 hrs |
| US06 | Student attendance history by subject | Medium | 5 hrs |
| US07 | Teacher opens/closes attendance window | High | 5 hrs |
| | **Sprint 1 Total** | | **35 hrs** |

Full Sprint 1 + Sprint 2 backlogs available in `backlog/Smart-Attend-Sprint-Backlog.xlsx`

---

## 🔄 Why CI/CD is Used for Smart-Attend

**Continuous Integration / Continuous Deployment (CI/CD)** is the DevOps practice of automatically building, testing, and deploying code every time a change is committed. For Smart-Attend's 2-week sprint release cycle, CI/CD is mission-critical — not optional.

### 🔧 Continuous Integration (CI) — Why?

Every sprint produces new features that must work seamlessly together. Without CI, a developer's geo-fence code might silently break the login flow, and no one would know until manual testing — days later.

**With CI (GitHub Actions):**
- Every `git push` triggers an automated pipeline
- **Linting** (ESLint) catches code style errors immediately
- **Unit Tests** (Jest) verify geo-fence radius calculations, auth logic, notification triggers
- **E2E Tests** (Detox) simulate a real student opening the app, logging in, and triggering geo-fence
- **Blocked Merges:** If any test fails, the pull request cannot be merged — broken code never reaches the main branch

```yaml
# .github/workflows/ci-cd.yml
name: Smart-Attend CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm install
      - name: Lint
        run: npm run lint
      - name: Unit Tests
        run: npm test -- --coverage
      - name: E2E Tests
        run: npm run test:e2e

  build-and-deploy:
    needs: test
    runs-on: macos-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      - name: Build iOS app
        run: npx react-native build-ios --mode Release
      - name: Deploy to TestFlight
        uses: apple-actions/upload-testflight-build@v1
        with:
          app-path: SmartAttend.ipa
          issuer-id: ${{ secrets.APP_STORE_ISSUER_ID }}
          api-key-id: ${{ secrets.APP_STORE_API_KEY_ID }}
          api-private-key: ${{ secrets.APP_STORE_API_PRIVATE_KEY }}
```

---

### 🚀 Continuous Deployment (CD) — How Sprint Updates Reach Students Automatically

When the team completes a sprint and the Sprint Review is approved:

1. **Sprint 1 merge** → CI pipeline runs all tests → **TestFlight build auto-uploaded**
   - Students enrolled as beta testers receive an **automatic OTA update notification**
   - Faculty can test the MVP (geo-fence + login) immediately — no manual APK distribution

2. **Sprint 2 merge** → Same pipeline → **Biometric update pushed automatically**
   - Face ID verification appears in the next app update for ALL test users
   - Zero manual work from the PM or dev team

**Branch strategy:**

| Branch | Deploys To | Audience |
|---|---|---|
| `develop` | Staging environment | Dev team internal testing |
| `main` | TestFlight + Play Internal | Faculty & beta student testers |
| `release/v1.0` | App Store + Play Store | All students (full rollout) |

---

### 📊 CI/CD Pipeline Stages

```
Developer pushes code
        ↓
[Stage 1] GitHub Actions triggered
        ↓
[Stage 2] ESLint — code style checks
        ↓
[Stage 3] Jest — 200+ unit tests (geo-fence, auth, notifications)
        ↓
[Stage 4] Detox — end-to-end UI tests on simulated devices
        ↓
[Stage 5] Fastlane — build signed .ipa (iOS) + .apk (Android)
        ↓
[Stage 6] TestFlight / Play Internal — auto-deploy to beta students
        ↓
Students receive OTA update notification ✅
```

---

### 🎯 Summary: Why CI/CD + Agile = The Right Choice

| Without CI/CD (Waterfall) | With CI/CD (Agile/Scrum) |
|---|---|
| Manual testing at end of 6-month cycle | Automated tests on every push |
| Manual APK distribution via email | Automatic OTA push to all beta students |
| Bugs discovered in week 23 | Bugs caught in minutes |
| 1 big-bang release per project | 1 polished release per 2-week sprint |
| Face ID requirement = project crisis | Face ID requirement = Sprint 2 story |

---

## 👥 Team

| Role | Responsibility |
|---|---|
| Junior PM (You) | Backlog management, sprint planning, stakeholder communication |
| Dev – Arjun | Geo-fence engine, Biometric module |
| Dev – Rahul | Authentication, Admin controls, Privacy |
| Dev – Priya | UI, Notifications, Reporting |
| QA – Divya | Test plans, UAT pilot |

---

## 📅 Timeline

```
Week 1–2:  Sprint 1 — MVP (Login + Geo-fence + Teacher Dashboard)
Week 3–4:  Sprint 2 — Biometric Update (Face ID + Reporting Refinement)
Week 5:    UAT Pilot — 30 students
Week 6:    Final Sprint Review → Dean Approval → Production Release
```

---

*Submitted as part of SDLC & DevOps Fundamentals coursework. All code and documentation prepared by the Junior Project Manager.*
