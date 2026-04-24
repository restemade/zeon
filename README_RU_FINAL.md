# ZEON AI - Финальный README (RU)

Дата: 2026-04-24
Статус: MVP / Pilot-ready (не production-final)

## 1. Что это за проект
ZEON AI - интеллектуальная система первичного подбора и скоринга кандидатов.
Система автоматизирует первичный скрининг резюме, объясняет результаты оценки (explainable AI),
поддерживает ручную валидацию (human-in-the-loop) и интеграции с внешними HR-системами.

## 2. Ключевая бизнес-ценность
- Снижение ручной нагрузки HR на первичном этапе.
- Ускорение обработки потока кандидатов.
- Повышение прозрачности решений за счет explainability.
- Масштабируемая интеграция через API и webhooks.

Важно:
- KPI-эффекты (экономия, time-to-hire, precision) должны подтверждаться только на фактических пилотных данных.
- В репозитории есть pre-pilot шаблон отчета: `docs/business-impact-report-template-ru.md`.

## 3. Что реализовано сейчас
- Управление вакансиями: создание, редактирование, публикация, версии.
- Инжест кандидатов: upload/import, карточка кандидата, список по вакансии.
- Scoring pipeline: parse/normalize/score/explain.
- Explainability: факторы оценки, риски, must-have gaps, confidence.
- Manual review: очередь, разбор кейсов, резолюция статусов.
- Candidate comparison: сравнение кандидатов.
- Analytics: summary/pipeline/vacancy analytics.
- Admin: словари, профили скоринга, аудит.
- Integrations: webhook endpoints, test/retry, deliveries, health.
- RBAC/Auth: роли, JWT, защищенные зоны.

## 4. Подтвержденные факты качества
- API тесты: 6/6 test files passed.
- Тесты: 11/11 passed.
- Интеграционные smoke-сценарии:
  - `tests/integration-lifecycle.test.ts` - passed
  - `tests/hr-flow-smoke.test.ts` - passed

## 5. Технологический стек
- Frontend: Next.js 15, TypeScript, Tailwind, Recharts.
- Backend: Fastify, TypeScript, Prisma, JWT.
- Worker/Queue: BullMQ, Redis.
- DB: PostgreSQL.
- Monorepo: npm workspaces (`apps/*`, `packages/*`).

## 6. Структура репозитория
```text
apps/
  web/      # UI
  api/      # REST API
  worker/   # фоновые задачи

packages/
  scoring/  # scoring engine
  shared/   # общие типы и утилиты

docs/       # документация, roadmap, runbooks
```

## 7. Режимы запуска

### 7.1 Full-DB режим
Для полноценного локального контура с PostgreSQL/Redis.

Основные команды:
```bash
npm install
npm run db:generate --workspace @zeon/api
npm run db:migrate --workspace @zeon/api
npm run db:seed --workspace @zeon/api
npm run dev:all
```

### 7.2 No-DB demo режим
Для демо без PostgreSQL/Redis.

```bash
# .env
ZEON_NO_DB=true

# запуск
npm run dev --workspace @zeon/api
npm run dev --workspace @zeon/web
```

Особенности no-db:
- In-memory данные.
- Детерминированный scoring (без случайного разброса на одинаковом вводе).
- Подходит для демонстрации продуктового потока.

## 8. Демо-аккаунты
- admin@zeon.local / Admin123!
- hr@zeon.local / Hr123456!
- manager@zeon.local / Manager123!

## 9. Интеграции (Webhook lifecycle)
Реализовано:
- Создание/пауза endpoint.
- Тестовые отправки.
- Retry с backoff.
- Idempotency keys.
- Delivery history и health.
- Business-triggered события:
  - `candidate.updated`
  - `vacancy.updated`

## 10. Безопасность и управление доступом
- JWT auth.
- Role-based access control (RBAC).
- Разделение доступов для HR/Manager/Admin зон.
- Аудит критичных действий.

## 11. Что реально, а что pilot/production roadmap
Реально уже работает (MVP/pilot-ready):
- API/UI, scoring, explainability, review queue, analytics, audit, webhook lifecycle.

Требует отдельного production-hardening:
- Production OCR/parser provider для DOC/PDF контуров.
- Production embeddings слой для расширенного semantic matching.
- Расширенный E2E/load/security hardening.

## 12. Документы проекта (must-read)
- Архитектура: `docs/architecture.md`
- Доменная модель: `docs/domain-model.md`
- API: `docs/api.md`
- Движок скоринга: `docs/scoring-engine.md`
- Деплой: `docs/deployment.md`
- Безопасность: `docs/security-and-compliance.md`
- Demo guide: `docs/demo-guide.md`
- Operations runbook: `docs/operations-runbook.md`
- Integration runbook: `docs/integration-runbook.md`
- DoD checklist: `docs/final-dod-checklist.md`
- Traceability (заказчик): `docs/customer-requirements-traceability-ru.md`
- Coverage roadmap (заказчик): `docs/customer-coverage-roadmap-ru.md`
- KPI playbook: `docs/pilot-validation-playbook-ru.md`
- Business impact template (pre-pilot): `docs/business-impact-report-template-ru.md`
- Commercial packaging: `docs/commercial-packaging-ru.md`

## 13. Что отправлять заказчику сейчас
Без риска ложных утверждений:
1. Этот README (техническая и продуктовая картина).
2. `customer-coverage-roadmap-ru.md` (что закрыто/что в работе).
3. `customer-requirements-traceability-ru.md` (матрица покрытия).
4. `business-impact-report-template-ru.md` как pre-pilot шаблон.

## 14. Текущий итог готовности
- Техническая готовность: высокая для MVP/pilot.
- Демо-готовность: подтверждена.
- Интеграционная готовность: подтверждена тестами lifecycle/smoke.
- Бизнес-KPI готовность: методика готова, фактические цифры - после пилота.
