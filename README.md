# NutriCare AI — Enterprise Healthcare Platform (Phases 1 - 8 Complete)

**NutriCare AI** is an AI-powered healthcare web platform, web app, and installable PWA designed with a mobile-first responsive architecture (360px / 768px / 1280px+), bank-grade authentication, on-device ML vision inference, Mifflin-St Jeor metabolic telemetry, LangGraph AI agent orchestration, verified RAG citations, BullMQ background automation, and full alignment with the **Digital Personal Data Protection (DPDP) Act 2023** of India.

---

## Complete 8-Phase Monorepo Map

```
NUTRICARE AI
├── api/                             # NestJS 10 (TypeScript) Backend
│   ├── prisma/
│   │   ├── schema.prisma            # Models for Users, Profiles, Foods, Plants, Scans, RAG Chunks, Notifications, Feedback
│   │   └── seed.ts                  # Seeds Admin, Dietitian, User accounts with Argon2
│   └── src/
│       ├── common/                  # Guards (JWT, RBAC, Double-Submit CSRF), Zod Pipes, Filters
│       └── modules/
│           ├── auth/                # Phase 1: Argon2, Rotating HttpOnly Cookies, Health ID (NC-YYYY-000123), QR
│           ├── user/                # Phase 1: DPDP Act 2023 Consent, JSON Data Export, Erasure
│           ├── nutrition/           # Phase 2: Mifflin-St Jeor, BMI, IFCT & USDA Database, Food/Water Trackers
│           ├── ml/                  # Phase 3: onnxruntime-node inference for Diabetes, Heart Disease, Obesity
│           ├── plant/               # Phase 4: Local ONNX + Pl@ntNet API, Confidence Gate, Emergency Triage
│           ├── rag/                 # Phase 5: ICMR-NIN & WHO Knowledge Base, Citations, Statutory Refusals
│           ├── agent/               # Phase 6: LangGraph Multi-Tool Agent (11 tools), Audited Execution
│           ├── automation/          # Phase 7: BullMQ Queues, Multi-Channel (Email, WhatsApp, Push), Quiet Hours
│           └── admin/               # Phase 8: Clinical Telemetry, Flagged Scans Review, Feedback System
├── web/                             # Next.js 14 App Router (TypeScript + Tailwind CSS + PWA)
│   ├── public/                      # PWA manifest.json, sw.js (Offline Shell)
│   └── src/
│       ├── app/
│       │   ├── (auth)/login & register
│       │   └── (dashboard)/
│       │       ├── dashboard/       # Health ID Card, DPDP Privacy & Erasure Center
│       │       ├── nutrition/       # Daily Tracker, Mifflin-St Jeor Calculator, 7-Day Progress Charts
│       │       ├── scanner/         # Botanical Camera Lens, Quality Pre-Check, Result Card
│       │       ├── chat/            # NutriCare AI Assistant (LangGraph Tool Badges)
│       │       ├── notifications/   # Delivery Channels & Quiet Hours Settings
│       │       ├── dietitian/       # Guarded Clinical Workspace
│       │       └── admin/           # Guarded Administrative Portal
│       └── components/              # CameraScanner, PlantResultCard, HealthIdCard, ProgressCharts, AgentChat
├── ml/                              # Phase 3 & 4: Machine Learning Pipelines
│   ├── medicinal_plants_classifier.ipynb   # Colab Transfer Learning (MobileNetV3/EfficientNet, 35+ classes, ONNX)
│   ├── chronic_disease_classifiers.ipynb   # Colab Classifiers (XGBoost/RandomForest for Diabetes & Heart Disease)
│   └── export_onnx_pipeline.py             # ONNX Exporter Pipeline & Class Definitions
├── docs/                            # Architecture & Compliance Manuals
└── docker-compose.yml               # PostgreSQL 16 (pgvector) + Redis 7
```

---

## Phase-by-Phase Features & Guarantees

### Phase 1: Auth, Identity & India DPDP 2023
- **Argon2id** password hashing (64MB memory cost, 3 iterations).
- **15-Minute Access Token** + rotating refresh tokens in `HttpOnly`, `Secure`, `SameSite` cookies.
- **Token Reuse Attack Detection**: Revokes entire token family if a previously revoked refresh token is re-submitted.
- **Auto-Logout after Inactivity**: 13-minute warning modal countdown, automatic termination at 15 minutes.
- **Unique Health ID**: Format `NC-YYYY-000123` with dynamic high-resolution QR code.
- **India DPDP 2023**: Informed consent versioning (`v1.0.0-2026`), Section 11 machine-readable JSON data export, and Section 12 irreversible account erasure (`"DELETE MY NUTRICARE DATA"`).

### Phase 2: Core Health Features
- **Mifflin-St Jeor Equation**: Precise Basal Metabolic Rate (BMR) and Total Daily Energy Expenditure (TDEE) based on age, gender, height, weight, and physical activity level.
- **Body Mass Index (BMI)**: Classified under modified WHO Asian cohort criteria.
- **Nutrition Database (IFCT / USDA)**: Seeded with authentic Indian and international items (Brown rice, Ragi, Moong dal, Moringa, Amla, Paneer, Curd, etc.) with calories, protein, carbs, fats, fiber, and micronutrients.
- **Daily Trackers**: Food and water loggers (+250ml quick add) with remaining calorie countdown and responsive 7-day progress charts.

### Phase 3: ML Models & ONNX Runtime
- **Colab Notebooks** in `/ml` for:
  - Medicinal Plants Classifier (Transfer Learning via MobileNetV3 / EfficientNet with evaluation metrics: Accuracy, Precision, Recall, Macro F1, Confusion Matrix, and ONNX export).
  - Chronic Disease Classifiers (RandomForest & XGBoost models for Diabetes, Cardiovascular Risk, and Obesity Stage).
- Backend service loads ONNX models via `onnxruntime-node` with confidence thresholding and unknown class detection.

### Phase 4: Plant Scanner & Remedy Guide
- **Camera Component**: `getUserMedia` stream with fallback to gallery file upload, on-device canvas compression, and luminance/blur pre-checks.
- **Scan Fusion**: Local ONNX model + Pl@ntNet API for combined confidence scoring.
- **75% Confidence Gate**: If confidence < 0.75 or ambiguous, displays **ONLY possible matches** with **NO herbal remedy advice**.
- **Mandatory Red-Flag Triage**: Deep wounds, heavy bleeding, large or facial burns, snake/animal bites, suspected fractures, child under 12, or pregnancy trigger immediate emergency advice: *"Seek medical care now. Call 108 / 112"*; herbal advice is strictly blocked.
- **DB Grounding**: All facts, evidence levels, and cautions derive strictly from the database; LLM never invents medical facts or numbers.

### Phase 5: Verified RAG & Citations
- Curated knowledge base derived from ICMR-NIN Dietary Guidelines, WHO Monographs, and Red Cross First Aid.
- Similarity threshold gate (0.65): If confidence is low or query is out of domain, replies exactly:
  > *"I don't have verified information on this."*
- Every verified answer includes an official citation (e.g. `[ICMR-NIN Dietary Guidelines for Indians 2024, Chapter 4]`).
- Includes a 50 Q&A evaluation benchmark set (`api/src/modules/rag/evaluation/qa-evaluation-set.json`).

### Phase 6: AI Agent (LangGraph Architecture)
- 11 Registered Agent Tools: `identify_plant`, `get_plant_profile`, `injury_remedy_lookup`, `check_toxicity`, `nutrition_lookup`, `calorie_calculator`, `risk_predictor`, `meal_planner`, `rag_search`, `schedule_reminder`, `send_message`.
- Every tool execution is cryptographically logged in the `AuditLog` table.
- System prompt enforcing safety boundaries: never diagnose, never prescribe pharmaceuticals, and immediately escalate emergencies.

### Phase 7: Automation & Multi-Channel Pipeline
- BullMQ Redis queues for scheduled tasks: daily water reminders, meal logging nudges, and weekly health progress reports.
- Gateways configured for: Email (Brevo/Resend), WhatsApp Sandbox (Twilio), SMS, Telegram, and Web Push.
- Respects user-defined **Quiet Hours** (e.g. 22:00 to 07:00), includes unsubscribe headers, and retries on transient failure with exponential backoff.

### Phase 8: PWA, Clinician Portal & Quality
- Installable PWA with Web App Manifest and Service Worker offline caching.
- Clinician & Admin Portal with flagged low-confidence scan review, patient feedback sheets, and system telemetry.
- Comprehensive Jest unit tests (13/13 passing tests across all modules).

---

## How to Run & Verify

### 1. Run Unit Tests (100% Pass)
```powershell
cd api
npx jest --config jest.config.js
```
*Result: 5 test suites passed, 13 unit tests passed.*

### 2. Start PostgreSQL & Redis
```powershell
docker compose up -d
```

### 3. Start NestJS Backend API
```powershell
cd api
npx prisma db push
npm run prisma:seed
npm run start:dev
```
- API starts at: `http://localhost:4000`
- Swagger OpenAPI: `http://localhost:4000/api/docs`
- Health check: `http://localhost:4000/api/v1/health`

### 4. Start Next.js 14 Frontend Web Application
```powershell
cd web
npm run dev
```
- Web Application starts at: `http://localhost:3000`
- Test responsive viewports: `360px` (Mobile), `768px` (Tablet), `1280px` (Desktop).
#   N U T R I T I O N _ A I  
 