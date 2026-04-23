# ZEON — подробный README_RU

**ZEON** — MVP/демо-готовый enterprise-модуль для AI-сопоставления и объяснимой оценки кандидатов на вакансии.  
Система помогает HR-командам быстрее обрабатывать поток резюме, ранжировать кандидатов, видеть причины оценки и сохранять контроль человека при принятии решений.

Файл объединяет основной README и русифицированную информацию из документации проекта: архитектура, API, доменная модель, скоринг, деплой, безопасность, демо-сценарий, операционные инструкции, интеграции, roadmap и финальный DoD-чеклист.

---

## 1. Краткое описание продукта

ZEON предназначен для первичного скрининга кандидатов. Он принимает вакансии и резюме, обрабатывает документы, нормализует данные, рассчитывает weighted score, формирует объяснение результата и помогает HR/менеджеру принять решение: добавить кандидата в shortlist, отправить на ручную проверку или отклонить.

Главная идея продукта: не просто «AI поставил оценку», а **прозрачная, проверяемая и управляемая оценка**, где человек видит:

- какие требования совпали;
- какие must-have критерии отсутствуют;
- какие есть риски;
- насколько система уверена в результате;
- почему кандидат попал в определенную категорию;
- когда кандидата безопаснее отправить на ручную проверку.

---

## 2. Основной стек

- **Frontend:** Next.js 15 + TypeScript + Tailwind + Recharts
- **Backend:** Fastify + TypeScript + Prisma + JWT + RBAC
- **Worker:** BullMQ + Redis
- **База данных:** PostgreSQL
- **Монорепозиторий:** npm workspaces (`apps/*`, `packages/*`)
- **Документация:** `docs/*`
- **Демо-режим без БД:** `ZEON_NO_DB=true`

---

## 3. Структура монорепозитория

```text
apps/
  web/       # HR UI, Manager UI, dashboard, вакансии, кандидаты, аналитика, админка, интеграции
  api/       # REST API, auth, RBAC, audit, admin, analytics, integrations
  worker/    # Асинхронная обработка: парсинг, нормализация, scoring jobs

packages/
  shared/    # Общие типы, DTO, нормализация, shared-логика
  scoring/   # Объяснимый движок weighted scoring

docs/
  architecture.md
  api.md
  domain-model.md
  scoring-engine.md
  deployment.md
  security-and-compliance.md
  demo-guide.md
  future-roadmap.md
  operations-runbook.md
  integration-runbook.md
  final-dod-checklist.md
```

---

## 4. Архитектура высокого уровня

### 4.1. Основные компоненты

1. **Web UI (`apps/web`)**  
   Интерфейс HR, менеджера и администратора. Позволяет работать с вакансиями, кандидатами, очередью ручной проверки, аналитикой, справочниками, аудитом и интеграциями.

2. **API (`apps/api`)**  
   REST API для авторизации, RBAC, вакансий, кандидатов, matching/scoring, review-процессов, аналитики, интеграций, admin-инструментов и системных health-check эндпоинтов.

3. **Worker (`apps/worker`)**  
   Асинхронный обработчик задач: парсинг резюме, определение языка, нормализация, скоринг и генерация объяснений.

4. **Scoring package (`packages/scoring`)**  
   Отдельный пакет с логикой объяснимого weighted scoring.

5. **Shared package (`packages/shared`)**  
   Общие типы, нормализация и переиспользуемые структуры данных.

6. **PostgreSQL**  
   Основное хранилище данных через Prisma models.

7. **Redis + BullMQ**  
   Очередь для тяжелых фоновых задач обработки резюме и scoring pipeline.

---

## 5. Pipeline обработки кандидата

Типовой процесс выглядит так:

1. Создается или импортируется вакансия.
2. Загружается или импортируется резюме кандидата.
3. Документ кандидата сохраняется в системе.
4. Создается processing job и отправляется в очередь.
5. Worker извлекает текст и определяет язык по пути RU/KZ/EN.
6. Нормализация применяет словари, canonical aliases и унифицированные значения.
7. Scoring engine рассчитывает weighted score и confidence.
8. Генерируется блок объяснимости: факторы, совпадения, пробелы, риски, summary.
9. Случаи с низкой уверенностью или спорными признаками отправляются в manual review.
10. Обновляются аналитика, audit log и связанные сущности.

---

## 6. AI/ML абстракции и точки замены

В MVP предусмотрены явные adapter-слои, чтобы позже заменить демо-логику на production-провайдеров:

- `ResumeParserAdapter` — парсинг резюме;
- `LanguageDetectionProvider` — определение языка;
- `EmbeddingsProvider` — embeddings/semantic relevance;
- `LlmExplanationProvider` — генерация/улучшение объяснений через LLM.

В текущем MVP используются deterministic demo adapters. Это значит, что система работает стабильно и предсказуемо для демо, но production OCR/parser/LLM/embeddings-провайдеры можно подключить отдельно.

---

## 7. Быстрый старт локально, полный режим с БД

### 7.1. Предварительные требования

Нужно, чтобы были доступны:

- Node.js/npm;
- PostgreSQL;
- Redis;
- корректный `.env` на базе `.env.example`.

### 7.2. Запуск

1. Скопируйте `.env.example` в `.env` и при необходимости измените значения.

2. Установите зависимости:

```bash
npm install
```

3. Сгенерируйте Prisma Client:

```bash
npm run db:generate --workspace @zeon/api
```

4. Выполните миграции:

```bash
npm run db:migrate --workspace @zeon/api
```

5. Заполните базу демо-данными:

```bash
npm run db:seed --workspace @zeon/api
```

6. Запустите все сервисы:

```bash
npm run dev:all
```

7. Откройте сервисы:

- Web: `http://localhost:3010`
- API: `http://localhost:4000/api/v1`
- Swagger: `http://localhost:4000/docs`

---

## 8. Быстрый старт в No-DB режиме

No-DB режим нужен, если PostgreSQL/Redis недоступны или нужно быстро показать демо без полноценной инфраструктуры.

1. В `.env` установите:

```env
ZEON_NO_DB=true
```

2. Запустите API:

```bash
npm run dev --workspace @zeon/api
```

3. Запустите Web:

```bash
npm run dev --workspace @zeon/web
```

4. Войдите под демо-пользователями:

- `admin@zeon.local` / `Admin123!`
- `hr@zeon.local` / `Hr123456!`
- `manager@zeon.local` / `Manager123!`

В No-DB режиме API остается запускаемым и использует demo adapters + in-memory persistence для основных бизнес-сценариев.

---

## 9. Запуск через Docker

```bash
docker compose up --build
```

Docker-подход подходит для локального комплексного запуска, когда нужно поднять окружение ближе к полноценному режиму.

---

## 10. Демо-аккаунты

| Роль | Логин | Пароль |
|---|---|---|
| Admin | `admin@zeon.local` | `Admin123!` |
| HR | `hr@zeon.local` | `Hr123456!` |
| Manager | `manager@zeon.local` | `Manager123!` |

---

## 11. Демо-данные

В проект включены демо-данные для демонстрации полного сценария:

- 6 вакансий;
- 60 кандидатов;
- смешанные языковые пути RU/KZ/EN;
- кандидаты разных категорий: strongly fit, fit, borderline, weak;
- кейсы для manual review;
- примеры дублей;
- примеры low confidence.

---

## 12. Основной функциональный охват

ZEON MVP покрывает следующие сценарии:

- создание, редактирование, публикация и версионирование вакансий;
- одиночная и массовая загрузка резюме;
- импорт кандидатов;
- обнаружение дублей;
- асинхронный pipeline parse → normalize → score;
- обработка RU/KZ/EN языковых путей;
- объяснимый score breakdown;
- рекомендации по кандидату;
- выявление must-have gaps;
- отображение рисков;
- ручная проверка спорных кандидатов;
- сравнение 2–5 кандидатов;
- аналитические dashboard-ы;
- административные справочники;
- audit log;
- настройка webhook-интеграций;
- тестирование интеграционных webhook-ов;
- просмотр delivery lifecycle по webhook-ам;
- health/readiness endpoints.

---

## 13. Доменная модель

В Prisma реализованы следующие сущности:

### 13.1. Пользователи и доступы

- `User`
- `Role`
- `Permission`
- `UserRole`
- `RolePermission`

### 13.2. Вакансии

- `Vacancy`
- `VacancyVersion`
- `VacancyRequirement`
- `VacancyScoringProfile`

### 13.3. Кандидаты

- `Candidate`
- `CandidateDocument`
- `CandidateProfile`
- `CandidateExperience`
- `CandidateSkill`
- `CandidateEducation`
- `CandidateLanguage`
- `CandidateCertification`
- `CandidateTag`

### 13.4. Matching и scoring

- `MatchResult`
- `ScoreBreakdown`
- `Explanation`

### 13.5. Review и комментарии

- `ReviewAction`
- `CandidateComment`

### 13.6. Интеграции

- `IntegrationEndpoint`
- `WebhookDelivery`

### 13.7. Администрирование и служебные сущности

- `DictionaryEntry`
- `ModelVersion`
- `ProcessingJob`
- `AuditLog`
- `FeedbackEntry`

### 13.8. Индексы и ограничения

Реализованы:

- уникальный email пользователя;
- уникальный code роли;
- уникальный code permission;
- уникальная пара vacancy/candidate для match results;
- helper-индексы по email/phone для обнаружения дублей кандидатов;
- soft-delete поля `deletedAt` на ключевых сущностях;
- timestamps и traceability metadata на основных таблицах.

---

## 14. Scoring Engine

`packages/scoring` реализует объяснимый weighted scoring engine.

### 14.1. Измерения оценки

Оценка кандидата может учитывать:

- совпадение must-have требований;
- совпадение hard skills;
- релевантный опыт;
- соответствие seniority;
- domain fit;
- language fit;
- education fit;
- certification fit;
- location/work-format fit;
- career stability;
- дополнительные факторы.

### 14.2. Результат scoring engine

На выходе формируется:

- `finalScore` от 0 до 100;
- `category`: `STRONGLY_FIT`, `FIT`, `BORDERLINE`, `WEAK_FIT`;
- `confidence`;
- `recommendation`: `SHORTLIST`, `REVIEW`, `REJECT`;
- breakdown по факторам;
- сильные совпадения;
- отсутствующие критерии;
- must-have gaps;
- риски;
- summary;
- рекомендация по ручной проверке.

### 14.3. Дефолтные thresholds

Пороговые значения настраиваемые, но по умолчанию:

| Score | Категория |
|---:|---|
| 85–100 | `STRONGLY_FIT` |
| 70–84 | `FIT` |
| 55–69 | `BORDERLINE` |
| 0–54 | `WEAK_FIT` |

### 14.4. Safety-логика

- Отсутствующие must-have критерии приводят к штрафам и/или ручной проверке.
- Hard reject по умолчанию отключен, чтобы сохранить безопасный human-in-the-loop процесс.
- Спорные и low-confidence кейсы должны попадать в manual review, а не автоматически отклоняться.

### 14.5. LLM explainability layer

LLM-слой является опциональным:

- deterministic scoring остается источником истины;
- LLM может использоваться только для улучшения текста объяснения;
- выбор провайдера управляется через env-переменную;
- при timeout, error или отсутствии ключа система автоматически возвращается к deterministic summary.

Поддерживаемые значения:

```env
LLM_PROVIDER=deterministic|openai|gemini
```

---

## 15. Переменные окружения

Основной список смотрите в `.env.example`.

LLM-related переменные:

```env
LLM_PROVIDER=deterministic|openai|gemini
OPENAI_API_KEY=
OPENAI_MODEL=
GEMINI_API_KEY=
GEMINI_MODEL=
```

Если ключи провайдера отсутствуют или провайдер недоступен, ZEON автоматически использует deterministic explainability.

---

## 16. API

### 16.1. Базовые адреса

- Base URL: `http://localhost:4000/api/v1`
- Swagger: `http://localhost:4000/docs`

---

### 16.2. Auth

- `POST /auth/login`
- `POST /auth/refresh`
- `POST /auth/logout`
- `GET /auth/me`

---

### 16.3. Vacancies

- `POST /vacancies`
- `GET /vacancies`
- `GET /vacancies/:id`
- `PATCH /vacancies/:id`
- `POST /vacancies/:id/publish`
- `POST /vacancies/:id/version`
- `GET /vacancies/:id/candidates`

`GET /vacancies/:id/candidates` поддерживает query params:

- `status`
- `language`
- `minScore`
- `search`
- `sortBy=score|confidence|uploadDate`
- `order=asc|desc`

---

### 16.4. Candidates

- `POST /candidates/upload`
- `POST /candidates/import`
- `GET /candidates/:id`
- `GET /candidates/:id/profile`
- `GET /candidates/:id/document`
- `PATCH /candidates/:id/status`
- `POST /candidates/:id/comment`
- `POST /candidates/compare`

---

### 16.5. Matching

- `GET /matching/:vacancyId/:candidateId`
- `POST /matching/recalculate`
- `POST /matching/bulk-recalculate`

---

### 16.6. Manual Review

- `GET /manual-review`
- `GET /manual-review/:candidateId/history`
- `POST /manual-review/:candidateId/resolve`

`GET /manual-review` поддерживает query params:

- `vacancyId`
- `search`
- `reason`
- `from`
- `to`
- `page`
- `pageSize`

---

### 16.7. Analytics

- `GET /analytics/summary`
- `GET /analytics/vacancies/:id`
- `GET /analytics/pipeline`

---

### 16.8. Feedback

- `POST /feedback`

---

### 16.9. Integrations

- `POST /integrations/webhooks`
- `GET /integrations/webhooks`
- `POST /integrations/webhooks/test`
- `PATCH /integrations/webhooks/:id/status`
- `GET /integrations/webhooks/:id/deliveries`
- `POST /integrations/webhooks/:id/retry/:deliveryId`
- `GET /integrations/health`

`GET /integrations/webhooks/:id/deliveries` поддерживает query params:

- `page`
- `pageSize`
- `success`
- `eventType`

---

### 16.10. Admin

- `GET /admin/dictionaries`
- `POST /admin/dictionaries`
- `PATCH /admin/dictionaries/:id`
- `GET /admin/model-versions`
- `GET /admin/scoring-profiles`
- `PATCH /admin/scoring-profiles/:vacancyId`
- `GET /admin/audit`
- `GET /admin/audit/related`

`GET /admin/audit` поддерживает query params:

- `page`
- `pageSize`
- `action`
- `entityType`
- `entityId`
- `userId`
- `requestId`
- `from`
- `to`

`GET /admin/audit/related` требует:

- `entityType=Candidate|Vacancy`
- `entityId`

---

### 16.11. System

- `GET /health`
- `GET /ready`

---

## 17. Команды для тестов

```bash
npm run test --workspace @zeon/api
npm run test --workspace @zeon/web
```

MVP-level тестовое покрытие включает:

- RBAC/scoring tests;
- lifecycle tests;
- smoke API tests.

Для production-hardening отдельно рекомендуется расширить browser E2E coverage.

---

## 18. Демо-сценарий для презентации

Рекомендуемый порядок демонстрации:

1. Войти как HR: `hr@zeon.local`.
2. Открыть Dashboard и показать базовые KPI.
3. Перейти в Vacancies.
4. Создать, отредактировать или опубликовать вакансию.
5. Открыть детали вакансии.
6. Импортировать резюме, например bulk import через API.
7. Показать асинхронную обработку и ранжирование кандидатов.
8. Открыть карточку кандидата.
9. Объяснить score breakdown.
10. Показать must-have gaps и risk visibility.
11. Открыть Manual Review queue.
12. Разрешить один спорный кейс.
13. Сравнить 2–3 кандидатов из shortlist.
14. Показать Analytics.
15. Показать Admin dictionaries.
16. Показать Audit log.
17. Показать Integrations webhook test.

---

## 19. Безопасность и соответствие требованиям

### 19.1. Security controls

Реализованы следующие меры:

- JWT authentication;
- role-based authorization;
- password hashing через `bcrypt`;
- rate limiting на auth и общий трафик;
- request ID correlation для диагностики инцидентов;
- audit logs для ключевых бизнес-действий;
- manual review fallback для low-confidence и ambiguous cases.

### 19.2. Работа с персональными данными

- PII кандидатов обрабатывается в рамках контролируемого доступа.
- Логи спроектированы так, чтобы избегать лишнего раскрытия sensitive payload.
- Retention/deletion hooks доступны через soft-delete и timestamps сущностей.

### 19.3. Fairness & Safety principles

- Нет скрытого black-box-only decisioning.
- Требуются объяснимые результаты на уровне факторов.
- Must-have gaps и confidence отображаются пользователю.
- Human-in-the-loop решения обязательны для uncertain cases.
- Дискриминационные факторы исключены из scoring logic.

---

## 20. Интеграции и webhook lifecycle

### 20.1. Назначение

Integration runbook описывает операционную работу с webhook-интеграциями для ATS/CRM/HRIS систем.

### 20.2. Поддерживаемые endpoint-ы

- `POST /api/v1/integrations/webhooks`
- `GET /api/v1/integrations/webhooks`
- `POST /api/v1/integrations/webhooks/test`
- `PATCH /api/v1/integrations/webhooks/:id/status`
- `GET /api/v1/integrations/webhooks/:id/deliveries`
- `POST /api/v1/integrations/webhooks/:id/retry/:deliveryId`
- `GET /api/v1/integrations/health`

### 20.3. Lifecycle интеграционного endpoint-а

1. Создать endpoint с URL, secret и event types.
2. Держать endpoint в статусе `ACTIVE` для доставки событий.
3. Запустить test delivery и проверить status code.
4. Мониторить delivery history и failure rate.
5. При необходимости повторить failed deliveries.
6. При стабильных сбоях перевести endpoint в pause.

### 20.4. Типовые failure scenarios

#### 401/403 на стороне receiver

Возможные причины:

- secret mismatch;
- auth failure во внешней системе.

Что проверить:

- корректность secret rotation;
- receiving auth middleware;
- совпадение ожидаемой схемы авторизации.

#### 5xx ошибки receiver-а

Возможные причины:

- внешняя система недоступна;
- receiver нестабилен;
- temporary downtime.

Рекомендуемые действия:

- поставить endpoint на pause;
- согласовать восстановление receiver-а;
- после восстановления повторить deliveries.

#### Всплеск высокого failure rate

Что проверить:

- `GET /integrations/health`;
- deliveries с фильтром `success=false`;
- группировку по `eventType`, чтобы определить проблемный тип payload.

### 20.5. Retry strategy в MVP

- Retry выполняется вручную из delivery history UI или через retry endpoint.
- Retry рекомендуется выполнять только когда endpoint находится в `ACTIVE`.
- Если проблема в payload class, сначала нужно исправить receiver.

### 20.6. Security notes по интеграциям

- Не логировать полные secrets в клиентском UI.
- Хранить webhook secret только на серверной стороне.
- Ограничить интеграционные endpoint-ы ролью `integration:manage`.

### 20.7. No-DB demo notes

В No-DB режиме можно симулировать сбои:

- URL endpoint-а содержит `fail`; или
- test event type содержит `fail`.

Health и delivery history остаются доступны даже в no-db режиме.

---

## 21. Operations Runbook

### 21.1. Scope

Operations runbook покрывает ежедневную эксплуатацию ZEON MVP в local, pilot и pre-production окружениях.

### 21.2. Сервисы

- Web (`@zeon/web`) на порту `3010`;
- API (`@zeon/api`) на порту `4000`;
- Worker (`@zeon/worker`) для async parse/score jobs;
- PostgreSQL и Redis для full mode.

### 21.3. Full mode startup

1. Убедиться, что PostgreSQL и Redis доступны.
2. Установить:

```env
ZEON_NO_DB=false
```

3. Выполнить:

```bash
npm install
npm run db:generate --workspace @zeon/api
npm run db:migrate --workspace @zeon/api
npm run db:seed --workspace @zeon/api
npm run dev:all
```

### 21.4. No-DB mode startup

1. Установить:

```env
ZEON_NO_DB=true
```

2. Выполнить:

```bash
npm run dev --workspace @zeon/api
npm run dev --workspace @zeon/web
```

### 21.5. Health checks

- API health: `GET /api/v1/health`
- API readiness: `GET /api/v1/ready`
- Integration health: `GET /api/v1/integrations/health`

### 21.6. Daily ops checklist

1. Проверить login для admin и HR demo accounts.
2. Проверить, что vacancy list и candidate pages открываются.
3. Проверить, что manual-review queue открывается и позволяет resolve.
4. Проверить, что integration health показывает ожидаемые значения.
5. Проверить, что analytics summary endpoint отвечает.

### 21.7. Incident triage

#### 1) Login fails

Проверить:

- запущен ли API: `/api/v1/health`;
- указывает ли `NEXT_PUBLIC_API_URL` на API: `http://localhost:4000/api/v1`;
- заданы ли JWT secrets.

#### 2) Candidates не появляются после upload/import

Проверить:

- в full mode: `processing_jobs` и worker logs;
- в no-db mode: есть ли в upload/import response `candidateId` и `queued`;
- manual review queue на предмет low-confidence routing.

#### 3) Integration deliveries failing

Проверить:

- Integrations UI;
- delivery history;
- endpoint status toggle: `ACTIVE/PAUSED`;
- retry failed delivery;
- корректность URL/secret.

### 21.8. Recovery actions

- Перезапустить API и Worker.
- Перезапустить seed в чистом local demo, если состояние неконсистентно.
- Поставить нестабильные webhook endpoint-ы на pause, чтобы избежать noisy retries.

### 21.9. Evidence and audit

- Использовать Audit page filters по `action/entityType/entityId`.
- Использовать drilldown endpoint `/admin/audit/related` для timeline конкретной сущности.
- Сохранять `requestId` из API responses/logs для диагностики.

### 21.10. Safe rollback strategy в MVP

- Откатить scoring profile к предыдущим threshold/weights через admin profile editor.
- Поставить integration endpoints на pause во время расследования delivery issues.
- Направлять uncertain candidates на manual review вместо rejection.

---

## 22. Deployment

### 22.1. Local Docker

```bash
docker compose up --build
```

### 22.2. Local Native

```bash
npm install
npm run db:generate --workspace @zeon/api
npm run db:migrate --workspace @zeon/api
npm run db:seed --workspace @zeon/api
npm run dev:all
```

### 22.3. Production recommendations

Для production/pilot рекомендуется:

- использовать managed PostgreSQL и Redis;
- запускать API и worker в отдельных autoscaled deployments;
- использовать object storage через S3-compatible storage adapter;
- добавить secret manager для JWT и webhook secrets;
- добавить centralized logs и tracing;
- добавить WAF / reverse proxy / TLS termination.

---

## 23. Известные ограничения MVP

Текущие ограничения:

- Parser providers являются demo adapters deterministic-типа, а не внешними OCR/LLM-интеграциями.
- Извлечение данных из PDF/DOC/DOCX смоделировано через adapter path.
- Надежное binary extraction можно подключить отдельно.
- Webhook delivery в no-db режиме использует controlled simulation.
- Полноценное outbound HTTP execution требует production connector setup.
- LLM explanation providers опциональны и работают best-effort.
- Deterministic fallback всегда остается активным.

---

## 24. Future Roadmap

### Phase 1 — Pilot hardening

- Заменить demo parser на production OCR/parser providers.
- Добавить outbound webhook delivery retries с реальным HTTP execution.
- Добавить contract tests с ATS/CRM mock servers.
- Добавить более богатое duplicate/fuzzy identity detection.

### Phase 2 — Productization

- Multi-tenant isolation для SaaS mode.
- Полная RU/KZ localization UI.
- Scenario-level A/B analytics для scoring profile versions.
- Feature flags для controlled rollout.

### Phase 3 — Enterprise scale

- Advanced semantic search с embeddings index.
- Feedback-driven calibration assistant.
- SSO/SAML integration.
- Policy packs по региону и job category.

---

## 25. Final DoD Checklist

Ниже — финальная сверка MVP по Definition of Done.

### 25.1. Real codebase, не docs-only

- **Status:** DONE
- **Evidence:** `apps/*`, `packages/*`, runnable services and tests.

### 25.2. Working backend

- **Status:** DONE
- **Evidence:** Fastify API with auth, RBAC, business modules, Swagger.

### 25.3. Working frontend

- **Status:** DONE
- **Evidence:** Next.js UI with auth flow, dashboard, vacancies, candidates, review, analytics, admin, integrations.

### 25.4. DB schema + migrations

- **Status:** DONE
- **Evidence:** Prisma schema and migrations in `apps/api/prisma`.
- **Note:** no-db demo mode available when DB is unavailable.

### 25.5. Resume ingestion and processing flow

- **Status:** DONE
- **Evidence:** upload/import endpoints, duplicate checks, parse quality routing, processing jobs.

### 25.6. Candidate scoring flow

- **Status:** DONE
- **Evidence:** weighted scoring engine in `packages/scoring`, category mapping, ranking path.

### 25.7. Explainability flow

- **Status:** DONE
- **Evidence:** factor breakdown, summaries, missing criteria, risks, manual review signals.
- **Note:** optional LLM provider supported; deterministic fallback always active.

### 25.8. Manual review queue

- **Status:** DONE
- **Evidence:** queue endpoint, filters, resolve actions, candidate review history.

### 25.9. Analytics dashboard

- **Status:** DONE
- **Evidence:** summary/pipeline endpoints + UI charts and KPI cards.

### 25.10. Admin configuration surface

- **Status:** DONE
- **Evidence:** dictionaries, scoring profiles, thresholds/weights update, model versions, audit tools.

### 25.11. API documentation

- **Status:** DONE
- **Evidence:** `docs/api.md` updated to include Wave 1/2 endpoints.

### 25.12. Demo data

- **Status:** DONE
- **Evidence:** seed scripts + no-db in-memory demo dataset and demo users.

### 25.13. Tests

- **Status:** DONE на уровне MVP
- **Evidence:** RBAC/scoring tests + lifecycle and smoke API tests.
- **Remaining for production-hardening:** broader browser E2E coverage.

### 25.14. Docker/local environment

- **Status:** DONE
- **Evidence:** `docker-compose.yml`, `.env.example`, local run paths documented.

### 25.15. Documentation

- **Status:** DONE
- **Evidence:** architecture, domain model, scoring, API, deployment, security, roadmap, runbooks.

### 25.16. Final implementation summary

- **Status:** DONE
- **Evidence:** delivery summaries provided in development thread.

### 25.17. Clear real vs adapter/mock statement

- **Status:** DONE

Реальное:

- API/UI modules;
- scoring logic;
- RBAC;
- audit;
- review queue;
- integration lifecycle APIs;
- analytics.

Adapter/demo mode:

- deterministic resume parsing adapters;
- no-db in-memory mode;
- simulated webhook failure behavior.

Optional providers:

- LLM explanation adapters for OpenAI/Gemini with deterministic fallback.

### 25.18. Final verdict

- MVP demo-ready.
- MVP operationally runnable.
- Для полной production readiness в первую очередь нужно усилить:
  - browser E2E tests;
  - external parser/OCR provider integration;
  - infrastructure hardening.

---

## 26. Карта исходной документации

После объединения вся ключевая информация из этих документов перенесена в данный README:

- `docs/architecture.md` — архитектура;
- `docs/domain-model.md` — доменная модель;
- `docs/api.md` — API;
- `docs/scoring-engine.md` — scoring engine;
- `docs/deployment.md` — deployment;
- `docs/security-and-compliance.md` — безопасность и compliance;
- `docs/demo-guide.md` — demo guide;
- `docs/future-roadmap.md` — roadmap;
- `docs/operations-runbook.md` — operations runbook;
- `docs/integration-runbook.md` — integration runbook;
- `docs/final-dod-checklist.md` — финальный DoD checklist.

---

## 27. Краткий итог

ZEON MVP уже покрывает полноценный demo-ready сценарий: от загрузки вакансии и резюме до объяснимого скоринга, ручной проверки, аналитики, аудита и webhook-интеграций.

Ключевая ценность системы — не только в автоматизации HR-скрининга, а в прозрачной AI-оценке, где каждый score сопровождается объяснением, рисками, confidence и возможностью ручного контроля.
