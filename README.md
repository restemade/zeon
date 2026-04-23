# ZEON - AI Candidate Matching and Scoring MVP

ZEON is an enterprise-ready, explainable AI module for primary candidate screening.
It helps HR teams process resume streams faster while preserving human-in-the-loop control.

## Stack
- Frontend: Next.js 15 + TypeScript + Tailwind + Recharts
- Backend: Fastify + TypeScript + Prisma + JWT + RBAC
- Worker: BullMQ + Redis
- Database: PostgreSQL
- Monorepo: npm workspaces (`apps/*`, `packages/*`)

## Architecture (high level)
- `apps/web`: HR UI and manager UI
- `apps/api`: REST API, auth, RBAC, audit, admin, analytics, integrations
- `apps/worker`: async parsing and scoring jobs
- `packages/shared`: common types and normalization logic
- `packages/scoring`: explainable weighted scoring engine
- `docs/*`: architecture, API, security/compliance and deployment docs

## Quick Start (local)
1. Copy `.env.example` into `.env` and adjust values if needed.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Generate prisma client and run migration:
   ```bash
   npm run db:generate --workspace @zeon/api
   npm run db:migrate --workspace @zeon/api
   ```
4. Seed demo data:
   ```bash
   npm run db:seed --workspace @zeon/api
   ```
5. Start all services:
   ```bash
   npm run dev:all
   ```
6. Open:
  - Web: `http://localhost:3010`
  - API: `http://localhost:4000/api/v1`
  - Swagger: `http://localhost:4000/docs`

## Quick Start (No-DB mode)
Use this mode when PostgreSQL/Redis are unavailable.
1. In `.env` set `ZEON_NO_DB=true`.
2. Run:
   ```bash
   npm run dev --workspace @zeon/api
   npm run dev --workspace @zeon/web
   ```
3. Login with demo users:
   - `admin@zeon.local` / `Admin123!`
   - `hr@zeon.local` / `Hr123456!`
   - `manager@zeon.local` / `Manager123!`

## Docker Run
```bash
docker compose up --build
```

## Demo Accounts
- `admin@zeon.local` / `Admin123!`
- `hr@zeon.local` / `Hr123456!`
- `manager@zeon.local` / `Manager123!`

## Core Functional Coverage
- Vacancy creation/edit/publish/versioning
- Resume ingestion (single/bulk upload + import), duplicate detection
- Async parse-normalize-score pipeline (BullMQ)
- RU/KZ/EN language path
- Explainable score breakdown and recommendations
- Manual review routing and resolution
- Candidate comparison (2-5)
- Analytics dashboards
- Admin dictionaries and audit log
- Integration webhook config/testing + delivery lifecycle

## Environment Variables
See `.env.example`.

LLM-related variables:
- `LLM_PROVIDER=deterministic|openai|gemini`
- `OPENAI_API_KEY`, `OPENAI_MODEL`
- `GEMINI_API_KEY`, `GEMINI_MODEL`

If provider keys are missing or the provider fails, ZEON automatically falls back to deterministic explainability.

## Test Commands
```bash
npm run test --workspace @zeon/api
npm run test --workspace @zeon/web
```

## Documentation Map
- Architecture: `docs/architecture.md`
- Domain model: `docs/domain-model.md`
- API: `docs/api.md`
- Scoring engine: `docs/scoring-engine.md`
- Deployment: `docs/deployment.md`
- Security/compliance: `docs/security-and-compliance.md`
- Demo guide: `docs/demo-guide.md`
- Future roadmap: `docs/future-roadmap.md`
- Operations runbook: `docs/operations-runbook.md`
- Integration runbook: `docs/integration-runbook.md`
- Final DoD checklist: `docs/final-dod-checklist.md`

## Known Limitations (MVP)
- Parser providers are demo adapters (deterministic), not external OCR/LLM integrations.
- File extraction for PDF/DOC/DOCX is modeled with adapter path; robust binary extraction can be plugged in.
- Webhook delivery uses controlled simulation in no-db mode; full outbound HTTP execution requires production connector setup.
- LLM explanation providers are optional and best-effort; deterministic fallback always remains active.

## Next Steps
- Plug in production document parsers and OCR pipelines.
- Add semantic embeddings provider for deep relevance matching.
- Add full i18n for RU/KZ UI content.
- Expand frontend E2E suite and load/performance tests.
