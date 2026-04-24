# ZEON AI — ПОЛНЫЙ README_RU_FULL

**Дата сборки:** 2026-04-24  
**Статус:** MVP / Pilot-ready, не production-final  
**Назначение файла:** единый полный README на русском языке, объединяющий `README_RU_FINAL.md` и детализированную документацию из архива `docs(1).zip`.

> Важно: ZEON лучше позиционировать как **API-first AI-модуль подбора и скоринга кандидатов** для интеграции в CRM/ATS/HRIS.  
> Веб-интерфейс в MVP является demo/admin UI для демонстрации, настройки, ручной проверки, аналитики и операционного контроля.
---

## Содержание

1. Исполнительное резюме
2. Позиционирование продукта под запрос заказчика
3. Текущий статус готовности и подтвержденные факты
4. Ключевая бизнес-ценность и ожидаемые KPI
5. Основной функциональный охват
6. Технологический стек и структура репозитория
7. Архитектура высокого уровня
8. Pipeline обработки кандидата
9. AI/ML слой: parser/OCR, NLP, embeddings, LLM
10. Доменная модель
11. Scoring engine и explainability
12. API и интеграционные контракты
13. Интеграции, webhooks, delivery lifecycle
14. Безопасность, RBAC, аудит и compliance
15. Режимы запуска и деплой
16. Demo guide и сценарий показа
17. Operations runbook
18. Pilot readiness, SLA/SLO draft и validation playbook
19. Business impact report template
20. Коммерческая упаковка
21. Traceability: покрытие требований заказчика
22. Roadmap закрытия 100% требований
23. Phase 1 implementation plan
24. Future roadmap
25. Final DoD checklist
26. Карта исходной документации
27. Приложения: нормализированные данные из архива

---
## 1. Исполнительное резюме

**ZEON AI** — интеллектуальный AI-модуль для первичного подбора, сопоставления и скоринга кандидатов.  
Система автоматизирует первичный анализ резюме, извлекает ключевые признаки кандидата, сопоставляет их с требованиями вакансии, рассчитывает объяснимый score и помогает HR/менеджеру принять решение: отправить кандидата в shortlist, на ручную проверку или отклонить.

Ключевая идея ZEON: не просто «AI поставил оценку», а **прозрачная, проверяемая и управляемая оценка**, где пользователь видит:

- какие требования совпали;
- какие must-have критерии отсутствуют;
- какие есть риски;
- насколько система уверена в результате;
- почему кандидат попал в определенную категорию;
- когда кандидата безопаснее отправить на ручную проверку.

ZEON может использоваться в двух форматах:

1. **Встраиваемый AI-модуль** для существующих CRM/ATS/HRIS-систем через API и webhooks.
2. **Самостоятельный MVP/pilot-интерфейс** с HR UI, manager UI, админкой, аналитикой, ручной проверкой и интеграционным мониторингом.

---

## 2. Позиционирование продукта под запрос заказчика

Запрос заказчика на скрине формулируется как: **«Модуль ZEON: AI-подбор и скоринг кандидатов»**.  
Поэтому правильное позиционирование:

> ZEON — это API-first AI-модуль для первичного анализа, сопоставления и скоринга кандидатов, который может быть встроен в текущие CRM/ATS/HRIS-процессы заказчика или использоваться как самостоятельный pilot-ready MVP.

Система не обязана заменять существующую HR-систему заказчика. Основной сценарий внедрения:

```text
Существующая CRM/ATS/HRIS
        ↓
передает резюме + требования вакансии
        ↓
ZEON AI анализирует, нормализует, скорит и объясняет
        ↓
возвращает score + category + confidence + explanation + risks
        ↓
CRM/ATS/HRIS показывает результат в текущем процессе заказчика
```

Веб-интерфейс ZEON в MVP нужен для:

- демонстрации работы модуля;
- ручной загрузки резюме;
- настройки вакансий и scoring profiles;
- просмотра score breakdown;
- ручной проверки спорных кандидатов;
- аналитики;
- администрирования словарей;
- мониторинга интеграций и webhook-доставок.

---

## 3. Текущий статус готовности и подтвержденные факты

**Статус:** MVP / Pilot-ready, не production-final.

Реализовано и подтверждено:

- API/UI, scoring, explainability, review queue, analytics, audit, webhook lifecycle.
- No-DB demo mode для быстрого показа без PostgreSQL/Redis.
- Full-DB mode для локального/пилотного контура с PostgreSQL/Redis.
- Детерминированный scoring в demo-режиме.
- Webhook lifecycle: retries/backoff/idempotency.
- Business-triggered events: `candidate.updated`, `vacancy.updated`.
- API тесты: `6/6` test files passed.
- Тесты: `11/11` passed.
- Integration smoke:
  - `tests/integration-lifecycle.test.ts` — passed;
  - `tests/hr-flow-smoke.test.ts` — passed.

Что требует production-hardening:

- production OCR/parser provider для PDF/DOC/DOCX;
- production embeddings слой для расширенного semantic matching;
- расширенный E2E/load/security hardening;
- фактическая валидация бизнес-KPI на пилоте.

---

## 4. Ключевая бизнес-ценность и ожидаемые KPI

Ключевая бизнес-ценность:

- снижение ручной нагрузки HR на первичном этапе;
- ускорение обработки потока кандидатов;
- повышение прозрачности решений за счет explainable AI;
- масштабируемая интеграция через API и webhooks;
- возможность коммерциализации как SaaS, on-premise или лицензируемый модуль.

Ожидаемые KPI из заявки заказчика:

| KPI | Целевой диапазон | Статус |
|---|---:|---|
| Снижение ручной обработки резюме | 50–80% | требуется подтверждение пилотом |
| Сокращение time-to-hire | 40–70% | требуется подтверждение пилотом |
| Снижение затрат на первичный подбор | 30–50% | требуется подтверждение пилотом |
| Повышение точности первичного подбора | 20–30% | требуется подтверждение пилотом |

Важно: эти проценты нельзя заявлять как уже достигнутый факт до пилота. Корректная формулировка:

> ZEON нацелен на достижение указанных эффектов, а подтверждение KPI проводится через pilot validation: control group vs ZEON-assisted group с фиксацией ручных часов, time-to-hire, затрат и precision shortlist.

---


---

# ЧАСТЬ II. Подробный README из архива документации

# ZEON — подробное техническое и продуктовое описание

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

## 1.1. Соответствие заявке и ожидаемые эффекты

ZEON соответствует запросу на AI-систему первичного отбора, сопоставления и скоринга кандидатов: решение автоматизирует обработку резюме, извлекает ключевые признаки кандидата, сопоставляет их с требованиями вакансии, формирует explainable score и поддерживает human-in-the-loop принятие решений.

Ожидаемые эффекты внедрения:

- снижение ручной обработки резюме на 50-80%;
- сокращение времени закрытия вакансии (time-to-hire) на 40-70%;
- снижение затрат на первичный подбор персонала на 30-50%;
- повышение точности первичного подбора на 20-30%;
- повышение прозрачности решений за счет explainable AI;
- масштабирование решения как SaaS, on-premise или лицензируемого программного продукта.

Технологически система уже включает multilingual NLP-путь для RU/KZ/EN, explainable weighted scoring и API/webhook-интеграции с CRM/ATS/HRIS.

По AI-компонентам текущий MVP использует deterministic baseline для стабильной и предсказуемой работы, при этом архитектура содержит adapter-слои для подключения production OCR/parser, embeddings provider и LLM provider на этапе pilot/production.

Позиционирование продукта: основной домен ZEON — HR Tech и интеллектуальные системы управления подбором; в отраслевых сценариях решение может применяться для подбора персонала в компаниях робототехнического профиля.

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

### Фаза 1 — усиление пилота

- Заменить demo parser на production OCR/parser providers.
- Добавить outbound webhook delivery retries с реальным HTTP execution.
- Добавить contract tests с ATS/CRM mock servers.
- Добавить более богатое duplicate/fuzzy identity detection.

### Фаза 2 — продуктализация

- Multi-tenant isolation для SaaS mode.
- Полная RU/KZ localization UI.
- Scenario-level A/B analytics для scoring profile versions.
- Feature flags для controlled rollout.

### Фаза 3 — Enterprise scale

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

---

## 28. Pilot Readiness and SLA/SLO Draft (2026-04-24)

Текущий статус готовности к пилоту:
- Webhook lifecycle supports retry/backoff and idempotency keys.
- Business-triggered integration events are emitted for `candidate.updated` and `vacancy.updated`.
- No-DB demo mode uses deterministic scoring (stable repeatable output for same input).
- Upload flow includes queue fallback behavior when background infra is unavailable.
- Local validation completed for:
  - `tests/integration-lifecycle.test.ts`
  - `tests/hr-flow-smoke.test.ts`

Черновик SLA/SLO для пилота:
- API availability SLO: >= 99.0% during pilot business hours.
- Resume ingest to first score:
  - P50 <= 60 seconds
  - P95 <= 180 seconds
- Webhook delivery success-rate SLO: >= 98% (excluding partner downtime windows).
- Manual-review routing SLO: 100% of low-confidence cases must be routed to manual review.
- Incident response targets:
  - P1 acknowledgement <= 30 minutes
  - P2 acknowledgement <= 4 hours

Операционные ссылки:
- `docs/operations-runbook.md`
- `docs/integration-runbook.md`
- `docs/final-dod-checklist.md`

## 29. Новые артефакты фаз 3/4 (2026-04-24)
- `docs/pilot-validation-playbook-ru.md` — методика валидации KPI на пилоте (control vs ZEON).
- `docs/business-impact-report-template-ru.md` — шаблон отчета для защиты результатов перед заказчиком.
- `docs/commercial-packaging-ru.md` — модели поставки (SaaS/on-prem/licensing) и продуктовые пакеты.
---

## 26. Карта исходной документации

В единый файл включены данные из:

- `README_RU_FINAL.md` — финальный executive README, текущий статус, тесты, режимы запуска, что отправлять заказчику.
- `docs(1).zip/README_RU.md` — подробное техническое и продуктовое описание.
- `docs(1).zip/architecture.md` — архитектура.
- `docs(1).zip/domain-model.md` — доменная модель.
- `docs(1).zip/api.md` — API.
- `docs(1).zip/scoring-engine.md` — scoring engine.
- `docs(1).zip/deployment.md` — deployment.
- `docs(1).zip/security-and-compliance.md` — безопасность и compliance.
- `docs(1).zip/demo-guide.md` — demo guide.
- `docs(1).zip/future-roadmap.md` — roadmap.
- `docs(1).zip/operations-runbook.md` — operations runbook.
- `docs(1).zip/integration-runbook.md` — integration runbook.
- `docs(1).zip/final-dod-checklist.md` — финальный DoD checklist.
- `docs(1).zip/customer-requirements-traceability-ru.md` — матрица покрытия требований заказчика.
- `docs(1).zip/customer-coverage-roadmap-ru.md` — roadmap до 100% покрытия требований.
- `docs(1).zip/phase-1-implementation-plan-ru.md` — план production OCR/parser, embeddings и LLM hardening.
- `docs(1).zip/provider-contracts-ru.md` — контракты внешних AI-провайдеров.
- `docs(1).zip/pilot-validation-playbook-ru.md` — методика проверки KPI на пилоте.
- `docs(1).zip/business-impact-report-template-ru.md` — шаблон отчета о бизнес-эффекте.
- `docs(1).zip/commercial-packaging-ru.md` — модели поставки и коммерческая упаковка.

---

---

# ЧАСТЬ III. Детализированные приложения из архива


## Приложение A. Traceability-чеклист покрытия требований заказчика

### ZEON: Чеклист покрытия требований заказчика

## Как читать документ
- `Статус сейчас`:
  - `Закрыто` — уже реализовано и подтверждается текущими артефактами.
  - `Частично` — покрыто архитектурно/в MVP, но нужно усиление по roadmap.
  - `Открыто` — требуется отдельная реализация/подтверждение.
- `Финальное доказательство` — что показываем заказчику как подтверждение 100% покрытия.

## Матрица: Требование -> Покрытие -> Доказательство

| Требование заказчика | Статус сейчас | Что уже есть | Что делаем по roadmap | Финальное доказательство |
|---|---|---|---|---|
| AI-подбор и скоринг кандидатов | Закрыто | Pipeline parse/normalize/score, weighted scoring, ranking | Тонкая калибровка в пилоте | Демо + API ответы + отчёт по качеству подбора |
| Автоматизация первичного отбора | Закрыто | Upload/import, автоматический скоринг, manual review queue | Доп. quality gates | Воронка обработки кандидатов до/после |
| Анализ резюме относительно вакансии | Закрыто | Vacancy requirements, scoring profile, match breakdown | Уточнение весов по пилотным ролям | Карточка кандидата с breakdown |
| Explainable AI (прозрачность решения) | Закрыто | Factors, risks, must-have gaps, confidence | Стандартизация explanation templates | Набор explainability-кейсов + audit trail |
| Ранжированный список кандидатов | Закрыто | Ranking/categories/recommendations/comparison | A/B калибровка порогов | Сравнение shortlist до/после |
| API доступ для интеграций | Частично | REST API + webhooks реализованы | Явно оформить CRM/ATS/HRIS сценарии и контрактные тесты | Integration runbook + протоколы тестов |
| Интеграция с CRM/ATS/HRIS | Частично | Webhook lifecycle и health есть | Контрактные тесты + retries/backoff/idempotency | Успешные интеграционные прогоны (pilot) |
| NLP для RU/KZ/EN | Частично | Multilingual path задекларирован и работает в MVP-сценариях | Расширенный пилотный набор резюме и регрессионные тесты | Отчёт по качеству обработки RU/KZ/EN |
| Работа с неструктурированными резюме (PDF/DOC/DOCX) | Частично | Adapter path в MVP | Подключение production OCR/parser provider | E2E-прогоны на реальных документах |
| Embeddings для semantic matching | Частично | Embeddings provider как архитектурный слой | Включение embeddings в pilot pipeline | Метрики релевантности (baseline vs embeddings) |
| LLM-слой для расширенной интерпретации | Частично | Optional LLM explanation + deterministic fallback | Guardrails, шаблоны, версионирование моделей | Стабильные explainability-выводы в пилоте |
| Снижение ручной обработки резюме на 50-80% | Открыто (требует валидации) | Потенциал заложен архитектурно | Измерение на пилоте (контроль/эксперимент) | Business Impact Report с методикой и цифрами |
| Сокращение time-to-hire на 40-70% | Открыто (требует валидации) | Pipeline ускоряет первичный отбор | Пилотные замеры по вакансиям | Business Impact Report + сырые данные |
| Снижение затрат на подбор на 30-50% | Открыто (требует валидации) | Есть операционный потенциал | Финмодель эффекта на пилоте | Экономический расчёт с допущениями |
| Повышение точности подбора на 20-30% | Открыто (требует валидации) | Explainable scoring и manual review уже есть | A/B сравнение качества shortlist | Отчёт по precision/acceptance на пилоте |
| Коммерциализация (SaaS/licensing/on-prem) | Частично | В roadmap есть SaaS направление | Продуктовые пакеты и модель поставки | Коммерческий пакет и deployment blueprint |
| Соответствие формулировкам заявки (без demo-диссонанса) | Частично | Текущий README сильный, но с явным demo-акцентом | Фаза 0: обновление позиционирования и блока эффектов | Обновлённый README_RU + заявочный one-pager |
| Сфера применения сформулирована корректно | Частично | Сейчас есть риск несостыковки с “робототехникой” | Уточнить как HR Tech + отраслевой кейс | Согласованный текст в заявке/README |

## Критерии “100% покрыто”
1. Все строки в матрице имеют статус `Закрыто` или `Закрыто (подтверждено пилотом)`.
2. KPI-строки подтверждены численно в `Business Impact Report`.
3. Интеграционные требования подтверждены протоколами pilot-тестов.
4. README/заявка/презентация не содержат противоречий по OCR/embeddings/LLM уровню готовности.

## Пакет артефактов для защиты перед заказчиком
- Обновлённый `README_RU` с блоком “Соответствие заявке и ожидаемые эффекты”.
- Технический roadmap: `docs/customer-coverage-roadmap-ru.md`.
- Настоящий traceability-чеклист.
- Integration runbook + результаты контрактных тестов.
- Business Impact Report (пилотные метрики и экономика).


## Приложение B. Roadmap полного покрытия требований заказчика

### Roadmap: Полное покрытие требований заказчика

## 1) Цель
Довести текущее решение ZEON от уровня demo-ready MVP до уровня pilot/production-ready, чтобы закрыть требования заказчика на 100% по функционалу, интеграциям и ожидаемым бизнес-эффектам.

## 2) Текущая оценка
- Текущее покрытие: 80-85% (по сравнению `README_RU_customer_comparison.docx`).
- Сильная сторона: скоринг, explainability, pipeline, API/webhook, manual review, RBAC, аналитика.
- Основные разрывы: явные бизнес-KPI в документации, production OCR/parser, embeddings/LLM как рабочий слой (не только adapter-формулировка), формализация коммерциализации (SaaS/licensing), уточнение позиционирования (HR Tech vs робототехника).

## 3) Приоритеты (P0/P1/P2)
- P0: Закрыть критичные разрывы для заявки и пилота (бизнес-эффект, production parser/OCR, embeddings, CRM/ATS/HRIS формулировки, обновление позиционирования).
- P1: Усилить эксплуатационную зрелость (наблюдаемость, SLA/SLO, тестовые контуры интеграций, безопасность поставки).
- P2: Коммерциализация и масштабирование (SaaS multi-tenant, лицензирование, enterprise-пакеты).

## 4) План по фазам (12 недель)

## Фаза 0 (Неделя 1): Выравнивание заявки и документации
Цель: закрыть текстовые/позиционные разрывы, чтобы уже сейчас корректно коммуницировать решение заказчику.

Задачи:
- Добавить в `README_RU.md` отдельный раздел `Соответствие заявке и ожидаемые эффекты`.
- Зафиксировать KPI из заявки:
  - снижение ручной обработки: 50-80%;
  - снижение time-to-hire: 40-70%;
  - снижение затрат: 30-50%;
  - повышение точности подбора: 20-30%.
- Уточнить формулировку по embeddings/LLM/OCR:
  - в MVP deterministic baseline;
  - в pilot подключаются production providers.
- Явно прописать интеграции с CRM/ATS/HRIS через API/webhooks.
- справить позиционирование: основной домен HR Tech / интеллектуальные системы управления; робототехнику оставить как отраслевой кейс (если требуется заявкой).

Критерий завершения:
- Обновленные README/презентационные материалы без противоречий с заявкой.

## Фаза 1 (Недели 2-5): Product AI Core (production-путь)
Цель: закрыть функциональные разрывы по работе с неструктурированными данными и semantic matching.

Задачи:
- нтегрировать production OCR/parser provider для PDF/DOC/DOCX.
- Включить embeddings provider для semantic matching (vacancy <-> candidate).
- Усилить LLM-layer:
  - нормализованный prompt/template;
  - guardrails и deterministic fallback;
  - версионирование моделей.
- Добавить quality-gates:
  - parse quality score;
  - confidence thresholds;
  - auto-routing в manual review.
- Расширить тесты:
  - контрактные тесты parser/OCR;
  - регрессионные тесты scoring+explainability;
  - smoke-тесты multi-language RU/KZ/EN.

Критерий завершения:
- End-to-end pipeline в pilot-режиме работает с production parser/OCR + embeddings.
- Ошибки/неуверенные случаи корректно уходят в manual review.

## Фаза 2 (Недели 6-8): нтеграции и эксплуатационная готовность
Цель: перейти от “работает в демо” к “стабильно работает в пилоте”.

Задачи:
- Довести webhook lifecycle до production-паттерна:
  - retries/backoff;
  - dead-letter сценарии;
  - idempotency keys.
- Добавить мониторинг и наблюдаемость:
  - метрики pipeline (ingest->parse->score->review);
  - error budget по интеграциям;
  - dashboard по delivery success rate.
- Ввести SLA/SLO для пилота:
  - время обработки резюме;
  - uptime API;
  - доля успешных интеграционных доставок.
- Закрыть security-hardening:
  - secret management;
  - доступы по ролям для integration/admin зон;
  - аудит критичных действий.

Критерий завершения:
- Есть измеримая стабильность pilot-среды и управляемость инцидентов.

## Фаза 3 (Недели 9-10): Доказательство бизнес-эффектов
Цель: подтвердить обещанные KPI заказчика реальными метриками пилота.

Задачи:
- Запустить baseline-vs-AI сравнение:
  - контрольная группа (ручной отбор);
  - экспериментальная группа (ZEON pipeline).
- Замерить и зафиксировать:
  - время закрытия вакансии;
  - трудозатраты HR;
  - точность shortlisting;
  - conversion на этапах funnel.
- Подготовить отчет `Business Impact Report`:
  - методика расчета;
  - источники данных;
  - диапазоны эффекта по ролям/вакансиям.

Критерий завершения:
- Есть верифицируемый отчет с цифрами для защиты перед заказчиком.

## Фаза 4 (Недели 11-12): Коммерциализация и упаковка
Цель: оформить решение как продукт, готовый к масштабированию и продаже.

Задачи:
- Подготовить 3 варианта поставки:
  - SaaS;
  - on-premise;
  - лицензируемый модуль.
- Уточнить продуктовые пакеты:
  - MVP/Pilot/Enterprise;
  - ограничения и SLA по пакетам.
- Подготовить внедренческий пакет:
  - deployment blueprint;
  - integration playbook;
  - security/compliance one-pager;
  - roadmap развития на 2 квартала.

Критерий завершения:
- Готов коммерческий и технический пакет для масштабирования.

## 5) Матрица “требование -> что делаем”
- Бизнес-эффект (30-50 / 40-70 / 20-30 / 50-80): Фаза 0 + Фаза 3.
- OCR/parser для неструктурированных резюме: Фаза 1.
- Embeddings/LLM как часть решения: Фаза 1.
- нтеграция с CRM/ATS/HRIS: Фаза 0 + Фаза 2.
- Explainable AI и прозрачность: уже есть, усиливаем quality-gates в Фазе 1.
- Коммерциализация SaaS/licensing: Фаза 4.
- Снижение операционной нагрузки HR: подтверждаем в Фазе 3 метриками.

## 6) Риски и контрмеры
- Риск: слабое качество OCR на сложных резюме.
  - Контрмера: ensemble parser + fallback + manual review routing.
- Риск: нестабильность внешних интеграций ATS/CRM.
  - Контрмера: retries/backoff/idempotency + integration health monitoring.
- Риск: LLM-объяснения дают непоследовательный стиль.
  - Контрмера: template-based generation + deterministic fallback.
- Риск: KPI не подтверждаются на пилоте.
  - Контрмера: корректная baseline-методика, сегментация по типам вакансий, итеративная настройка scoring profile.

## 7) Definition of Success (на конец 12-й недели)
- Покрытие требований заказчика: 100% по матрице сравнения.
- Подтвержденные бизнес-эффекты: отчет с измерениями пилота.
- Техническая готовность: production parser/OCR + embeddings + стабильные интеграции.
- Продуктовая готовность: упакованные модели коммерциализации (SaaS/on-prem/licensing).

## 8) Текущий статус выполнения (обновлено: 2026-04-24)

- Статус фазы 0: DONE
- Статус фазы 1: IN PROGRESS
- Статус фазы 2: IN PROGRESS
- Статус фазы 3: TODO
- Статус фазы 4: TODO

Выполнено по факту:
- справлен CORS для PATCH (bulk actions в UI работают корректно).
- справлен fallback в upload/queue path (убран зависающий сценарий при недоступной очереди).
- В no-db demo включен детерминированный scoring (без случайного разброса при одинаковом вводе).
- Реализован webhook lifecycle: retries/backoff/idempotency (full-db и no-db).
- Добавлены business-triggered events (candidate.updated / vacancy.updated).
- справлен boolean query parsing для integration endpoints (success=false и includePaused=false).
- Локально подтверждено: integration-lifecycle.test.ts и hr-flow-smoke.test.ts проходят.

Что осталось для закрытия фазы 2:
- Финальный end-to-end smoke по демо-сценарию в UI (no-db-demo): login -> upload -> shortlist -> analytics -> integrations.
- Единый прогон полного набора API тестов в локальной среде без spawn EPERM ограничений.
- Обновить README_RU.md секцией "pilot readiness" + SLA/SLO draft.


## 9) Status Refresh (2026-04-24)

     :
-  0: DONE
-  1: IN PROGRESS
-  2: DONE
-  3: IN PROGRESS
-  4: IN PROGRESS

 :
- API : 6/6   11/11     .
- Integration lifecycle  HR smoke   .
- Pilot readiness + SLA/SLO draft   `docs/README_RU.md`.

   3/4:
- `docs/pilot-validation-playbook-ru.md`
- `docs/business-impact-report-template-ru.md`
- `docs/commercial-packaging-ru.md`


## Приложение C. Phase 1 implementation plan: OCR/parser + embeddings + LLM hardening

### Phase 1: План реализации (OCR/parser + embeddings + LLM hardening)

## Цель фазы
Закрыть разрыв между demo adapters и pilot-ready AI core: production ingestion, semantic matching и предсказуемая explainability.

## Область применения и владельцы модулей
- `apps/api/src/services/providers.ts`: провайдеры OCR/parser, embeddings, LLM.
- `apps/api/src/services/pipeline.service.ts`: встраивание новых сигналов в scoring/explanation pipeline.
- `apps/worker/src/main.ts`: синхронизация worker-пайплайна с API-логикой.
- `packages/scoring/*`: добавление embeddings-сигнала в итоговый score.
- `apps/api/tests/*` и `packages/*/*.test.ts`: регрессия и контрактные тесты.

## Workstream A: Production OCR/parser
1. Ввести параметризацию parser provider:
   - `PARSER_PROVIDER=demo|production`.
2. Добавить production adapter c единым интерфейсом `ResumeParserAdapter`.
3. Нормализовать parser output к `ParsedResume`.
4. Добавить parse quality telemetry:
   - parse confidence;
   - missing critical fields;
   - parse errors taxonomy.
5. Добавить graceful fallback:
   - при ошибке production parser -> demo parser;
   - обязательный audit event о fallback.

Критерии завершения:
- Документы PDF/DOC/DOCX обрабатываются production-путем.
- При отказе внешнего parser процесс не падает и уходит в контролируемый fallback.

## Workstream B: Embeddings semantic matching
1. Расширить `EmbeddingsProvider`:
   - текущая similarity + production backend.
2. Добавить конфиг:
   - `EMBEDDINGS_PROVIDER=demo|production`;
   - `EMBEDDINGS_MODEL=...`.
3. В `pipeline.service.ts` считать semantic similarity:
   - между требованиями вакансии и профилем кандидата;
   - с логированием значения и confidence.
4. Пробросить фактор в scoring:
   - новый фактор `semanticMatch`.
5. В explanation включить short reason по semantic signal.

Критерии завершения:
- Semantic factor участвует в итоговой оценке.
- Видна трассировка источника semantic score в explanation/traceability.

## Workstream C: LLM explanation hardening
1. Унифицировать шаблон prompt:
   - нейтральность;
   - запрет на домысливание;
   - ограничение длины.
2. Добавить policy checks:
   - пустой или слишком длинный ответ;
   - несоответствие входным фактам.
3. Поддержать deterministic fallback как default-safe режим.
4. Добавить версионирование используемой модели и шаблона в traceability.

Критерии завершения:
- Explainability стабильна, воспроизводима и безопасно деградирует в deterministic режим.

## Workstream D: Worker/API consistency
1. Убрать дублирование логики parse/score между API и worker:
   - вынести общий processing use-case в общий сервис.
2. Использовать единый providers registry в обоих потоках.
3. Выровнять статусы `MANUAL_REVIEW/PROCESSED/FAILED`.

Критерии завершения:
- Нет расхождения результатов при обработке через API path и worker path.

## Workstream E: Tests and validation
1. Контрактные тесты parser adapter.
2. Тесты embeddings-фактора:
   - влияние на score;
   - отсутствие деградации baseline.
3. E2E smoke для RU/KZ/EN сценариев.
4. Негативные тесты:
   - provider timeout;
   - provider invalid payload;
   - forced fallback.

Критерии завершения:
- Пакет тестов проходит стабильно.
- Ключевые сценарии деградации покрыты.

## Workstream F: Rollout strategy
1. Feature flags:
   - `FF_PRODUCTION_PARSER`;
   - `FF_EMBEDDINGS_MATCH`.
2. Канареечный rollout:
   - сначала 10% вакансий, затем 50%, затем 100%.
3. Мониторинг в rollout:
   - parse fail rate;
   - manual review rate;
   - score drift vs baseline.

Критерии завершения:
- Управляемый rollout без резкой деградации операционных метрик.

## Риски фазы
- Нестабильный внешний parser/OCR.
- Дрейф score из-за embeddings.
- Непредсказуемый стиль LLM-объяснений.

## Контрольные точки (по неделям)
- Week 2: provider scaffolding + feature flags.
- Week 3: parser integration + fallback.
- Week 4: embeddings in scoring + explanation traceability.
- Week 5: tests, rollout gates, pilot readiness sign-off.


## Приложение D. Контракты внешних AI-провайдеров

### Контракты внешних AI-провайдеров (Pilot)

Документ фиксирует API-контракты для интеграции production parser/OCR и embeddings-сервиса в ZEON.

## 1) Resume Parser / OCR Provider

Используется при `PARSER_PROVIDER=production`.

### Endpoint
- URL: `PARSER_HTTP_URL`
- Method: `POST`
- Headers:
  - `Content-Type: application/json`
  - `Authorization: Bearer <PARSER_HTTP_TOKEN>` (опционально)

### Request body
```json
{
  "text": "raw resume text content"
}
```

### Response body (успешный)
Допускаются два формата: объект напрямую или объект в поле `result`.

```json
{
  "result": {
    "fullName": "Jane Doe",
    "email": "jane@example.com",
    "phone": "+77001234567",
    "skills": ["TypeScript", "Node.js", "PostgreSQL"],
    "experienceYears": 6,
    "relevantYears": 4,
    "seniority": "senior",
    "domains": ["fintech"],
    "languages": ["EN", "RU"],
    "educationLevel": "master",
    "certifications": ["AWS"],
    "location": "Almaty",
    "summary": "Backend engineer with fintech experience",
    "confidence": 86
  }
}
```

### Обязательные поля
- `fullName` (string)
- `skills` (string[])
- `experienceYears` (number)
- `relevantYears` (number)
- `confidence` (number, 0..100)

### Fallback поведение
- При timeout/5xx/невалидном ответе ZEON переключается на demo parser.
- Пишется audit событие `PARSER_PROVIDER_FALLBACK`.

## 2) Embeddings Similarity Provider

Используется при `EMBEDDINGS_PROVIDER=production`.

### Endpoint
- URL: `EMBEDDINGS_HTTP_URL`
- Method: `POST`
- Headers:
  - `Content-Type: application/json`
  - `Authorization: Bearer <EMBEDDINGS_HTTP_TOKEN>` (опционально)

### Request body
```json
{
  "a": ["required skill A", "required skill B"],
  "b": ["candidate skill A", "candidate summary fragment"]
}
```

### Response body (успешный)
```json
{
  "similarity": 0.73
}
```

### Ограничения
- `similarity` ожидается в диапазоне `0..1`.

### Fallback поведение
- При timeout/5xx/невалидном ответе ZEON переключается на demo similarity.
- Пишется audit событие `EMBEDDINGS_PROVIDER_FALLBACK`.

## 3) Quality Gate и audit события

При обработке кандидата ZEON пишет:
- `PARSER_PROVIDER_FALLBACK` — parser fallback с production на demo.
- `EMBEDDINGS_PROVIDER_FALLBACK` — embeddings fallback с production на demo.
- `QUALITY_GATE_MANUAL_REVIEW` — кандидат отправлен в ручную проверку из-за:
  - низкого parse confidence;
  - низкой semantic similarity;
  - scoring-based manual review условий.

## 4) Pilot acceptance checks

Перед запуском пилота проверить:
1. Parser endpoint отвечает по контракту минимум на 20 тестовых резюме.
2. Embeddings endpoint стабильно отдает `similarity` в диапазоне `0..1`.
3. При искусственном сбое endpoint-ов фиксируются fallback audit события.
4. Кандидаты с низкой semantic similarity попадают в `MANUAL_REVIEW`.

## 5) Локальный stub-режим (для разработки и проверки пайплайна)

В репозитории есть встроенный локальный провайдер-стаб:
- `scripts/provider-stub.mjs`
- Endpoints:
  - `GET /health`
  - `POST /parse`
  - `POST /similarity`

Быстрый старт:
1. Скопировать `.env.providers.stub.example` в `.env`.
2. Запустить все сервисы вместе со stub:
   - `npm run dev:all:stub`

Отдельный запуск только stub:
- `npm run dev:providers:stub`

Smoke-check:
- `GET http://localhost:5055/health`
- `POST http://localhost:5055/parse` с `Authorization: Bearer stub-parser-token`
- `POST http://localhost:5055/similarity` с `Authorization: Bearer stub-embeddings-token`


## Приложение E. Playbook валидации пилота

### Playbook валидации пилота ZEON

## 1. Цель
Подтвердить KPI заказчика на пилоте с прозрачной методикой и воспроизводимыми расчетами.

Целевые KPI:
- снижение ручной обработки резюме: 50-80%;
- снижение time-to-hire: 40-70%;
- снижение затрат на первичный подбор: 30-50%;
- рост точности первичного подбора: 20-30%.

## 2. Дизайн пилота
- Период: 4-6 недель.
- Группы:
  - Control: текущий ручной процесс.
  - ZEON: процесс с ZEON (auto-score + manual review).
- Разделение: вакансии одного типа и схожего уровня в обеих группах.
- Минимальный объем: не менее 5 вакансий и не менее 100 кандидатов на группу.

## 3. Источники данных
- ATS/CRM:
  - дата открытия вакансии;
  - дата закрытия вакансии;
  - этапы воронки;
  - финальные решения.
- ZEON:
  - audit log;
  - pipeline analytics;
  - manual review события;
  - webhook deliveries (если интеграции в scope).

## 4. Формулы KPI
1. Снижение ручной обработки
- `ManualHoursReduction% = (ManualHoursBaseline - ManualHoursZeon) / ManualHoursBaseline * 100`

2. Снижение time-to-hire
- `TimeToHireReduction% = (TTHBaseline - TTHZeon) / TTHBaseline * 100`

3. Снижение затрат
- `CostReduction% = (CostBaseline - CostZeon) / CostBaseline * 100`

4. Рост точности первичного подбора
- `PrecisionGrowth% = (PrecisionZeon - PrecisionBaseline) / PrecisionBaseline * 100`
- где `Precision = Успешные_кандидаты_после_shortlist / Все_кандидаты_в_shortlist`

## 5. Еженедельный ритм
- Неделя 1:
  - зафиксировать baseline;
  - зафиксировать scoring profile и пороги.
- Недели 2-4:
  - еженедельная выгрузка KPI;
  - разбор ложноположительных/ложноотрицательных кейсов;
  - точечная калибровка threshold/weights.
- Неделя 5-6:
  - финальная сверка control vs ZEON;
  - выпуск Business Impact Report.

## 6. Критерии валидности
- Сопоставимые вакансии между группами.
- Единые правила учета времени/затрат.
- Фиксированный scoring profile внутри одного цикла сравнения.
- Все исключения (праздники, freeze найма, внешние ограничения) отражены в отчете.

## 7. Выходные артефакты
- Заполненный: `docs/business-impact-report-template-ru.md`.
- Таблица расчета KPI (CSV/XLSX) с формулами.
- Краткая защита для заказчика:
  - достигнутые диапазоны;
  - ограничения;
  - план расширения rollout.


## Приложение F. Шаблон отчета о бизнес-эффекте

### Отчет о бизнес-эффекте ZEON — pre-pilot шаблон

Дата: 2026-04-24  
Статус: Pre-Pilot (без фактических KPI-данных)

## 1. Важно
Этот документ не содержит фактических бизнес-результатов пилота.  
Заполнять числовые KPI только по реальным данным ATS/CRM/финучета после периода наблюдения.

## 2. Контекст пилота
- Период пилота: `TBD`
- Заказчик/подразделение: `TBD`
- Типы вакансий: `TBD`
- Объем кандидатов: `TBD`
- Конфигурация ZEON: `TBD`

## 3. Методика
- Дизайн: Control (ручной процесс) vs ZEON-assisted.
- Источники:
  - ATS/CRM: этапы, даты открытия/закрытия, найм.
  - ZEON: audit, pipeline analytics, manual review, integration logs.
  - Финансы/HR: часы, ставки, внешние расходы.
- Формулы:
  - `ManualHoursReduction% = (Baseline - ZEON) / Baseline * 100`
  - `TimeToHireReduction% = (Baseline - ZEON) / Baseline * 100`
  - `CostReduction% = (Baseline - ZEON) / Baseline * 100`
  - `PrecisionGrowth% = (PrecisionZEON - PrecisionBaseline) / PrecisionBaseline * 100`

## 4. KPI заказчика (для заполнения фактами)
| KPI | Целевой диапазон | Baseline | Pilot (ZEON) | Delta | Статус |
|---|---|---:|---:|---:|---|
| Снижение ручной обработки резюме | 50-80% | TBD | TBD | TBD | TBD |
| Снижение time-to-hire | 40-70% | TBD | TBD | TBD | TBD |
| Снижение затрат на первичный подбор | 30-50% | TBD | TBD | TBD | TBD |
| Рост точности первичного подбора | 20-30% | TBD | TBD | TBD | TBD |

## 5. Интеграции и стабильность (что уже подтверждено)
- API тесты пройдены: `6/6` файлов, `11/11` тестов.
- Integration smoke пройден:
  - `tests/integration-lifecycle.test.ts`
  - `tests/hr-flow-smoke.test.ts`

## 6. Что можно отправлять заказчику сейчас
- Технический статус готовности.
- Подход к измерению KPI.
- План пилотной валидации и список требуемых данных.

## 7. Что нельзя утверждать до пилота
- Любые проценты экономии/ускорения/роста точности как “факт”.
- Финансовый эффект в деньгах без реальных исходных данных.

## 8. Минимальный набор данных для финального отчета
1. Часы HR на первичный скрининг (до/после).
2. Средний time-to-hire (до/после).
3. Стоимость первичного подбора (до/после).
4. Precision shortlist (до/после).
5. Объем вакансий/кандидатов в обеих группах.
6. Webhook метрики: success/failure/latency/retry.


## Приложение G. Коммерческая упаковка

### Коммерческая упаковка ZEON

## 1. Модели поставки

### 1.1 SaaS
- Размещение: облачный контур ZEON.
- Подходит: быстрый запуск пилота/масштабирование.
- Плюсы:
  - минимальный time-to-value;
  - централизованные обновления;
  - ниже входной порог по инфраструктуре.

### 1.2 On-Premise
- Размещение: инфраструктура заказчика.
- Подходит: строгие требования по данным/комплаенсу.
- Плюсы:
  - полный контроль инфраструктуры;
  - соответствие внутренним политикам безопасности.

### 1.3 Licensing (модуль)
- Формат: лицензируемый модуль API/engine для встраивания.
- Подходит: интеграторы и продуктовые команды заказчика.
- Плюсы:
  - гибкость интеграции;
  - контроль UI/процессов на стороне заказчика.

## 2. Продуктовые пакеты

## 2.1 MVP
- Scope:
  - базовый ingestion/import/upload;
  - explainable scoring;
  - manual review;
  - базовые аналитики;
  - webhook lifecycle.
- Ограничения:
  - ограниченный объем и SLA best-effort;
  - адаптерные parser/LLM пути допускаются.

## 2.2 Pilot
- Scope:
  - все из MVP;
  - pilot SLO/SLA;
  - integration runbook;
  - валидация KPI по playbook.
- Ограничения:
  - ограниченный набор вакансий/подразделений;
  - controlled rollout.

## 2.3 Enterprise
- Scope:
  - multi-tenant/enterprise-ready governance;
  - расширенные интеграции ATS/CRM/HRIS;
  - расширенная observability и security controls;
  - formalized support model.
- Ограничения:
  - проектная оценка и поэтапный rollout по доменам.

## 3. SLA/SLO рамка (черновик)
- API availability: >= 99.0% в бизнес-часы пилота.
- Ingest-to-first-score:
  - P50 <= 60s;
  - P95 <= 180s.
- Webhook success rate: >= 98% (исключая внешние downtime окна).
- Incident response:
  - P1 <= 30 мин на подтверждение;
  - P2 <= 4 часа на подтверждение.

## 4. Коммерческий пакет для защиты
- 1-page value proposition под заказчика.
- Business Impact Report (заполненный по пилоту).
- Traceability matrix покрытия требований.
- Deployment blueprint + security/compliance one-pager.
- План развития на 2 квартала (roadmap).


## Приложение H. Детальный integration runbook

### Интеграционный runbook

## Назначение
Operational guide for webhook integrations with ATS/CRM/HRIS systems.

## Supported Endpoints
- `POST /api/v1/integrations/webhooks`
- `GET /api/v1/integrations/webhooks`
- `POST /api/v1/integrations/webhooks/test`
- `PATCH /api/v1/integrations/webhooks/:id/status`
- `GET /api/v1/integrations/webhooks/:id/deliveries`
- `POST /api/v1/integrations/webhooks/:id/retry/:deliveryId`
- `GET /api/v1/integrations/health`

## Lifecycle
1. Create endpoint with URL, secret, event types.
2. Keep endpoint `ACTIVE` for delivery.
3. Run test delivery and validate status code.
4. Monitor delivery history and failure rate.
5. Retry failed deliveries if needed.
6. Pause endpoint on persistent failures.

## Business-triggered outbound events
ZEON now emits integration deliveries not only via manual test/retry endpoints, but also from core business actions:
- `candidate.updated`:
  - candidate status update (`PATCH /candidates/:id/status`)
  - manual review resolution (`POST /manual-review/:candidateId/resolve`)
- `vacancy.updated`:
  - vacancy update (`PATCH /vacancies/:id`)
  - vacancy publish (`POST /vacancies/:id/publish`)

All business-triggered deliveries use idempotency keys derived from entity id + action + request id.

## Типовые сценарии сбоев
### 401/403 on receiver side
- Secret mismatch or auth failure on external system.
- Validate secret rotation and receiving auth middleware.

### 5xx receiver errors
- Receiver unavailable or unstable.
- Pause endpoint, coordinate recovery, then retry deliveries.

### High failure rate spike
- Check `GET /integrations/health` for failing endpoint count.
- Filter endpoint deliveries by `success=false`.
- Group by event type to isolate problematic payloads.

## Retry-стратегия MVP
- Manual retry from delivery history UI or retry endpoint.
- Retry only when endpoint is `ACTIVE`.
- For known-bad payload classes, fix receiver first.
- Test and retry endpoints support retry policy tuning:
  - `maxRetries` (0..5, default 2)
  - `initialBackoffMs` (50..5000, default 300)
- Backoff policy is exponential per attempt.

## Idempotency / идемпотентность
- Test and retry endpoints support `idempotencyKey` (optional).
- If the same `idempotencyKey` is reused for the same endpoint/event pair, the API returns the existing delivery result instead of creating duplicates.
- Delivery metadata stores:
  - attempt number,
  - max attempts,
  - initial backoff,
  - idempotency key,
  - `retriedFromId` for retry flows.

## Безопасность Notes
- Do not log full secrets in client-side UI.
- Store webhook secret server-side only.
- Restrict integration endpoints to `integration:manage` role.

## Demo notes в No-DB режиме
- To simulate failures, use endpoint URL containing `fail` or test event type containing `fail`.
- Health and delivery history are still available in no-db mode.


## Приложение I. Детальный operations runbook

### Операционный runbook

## Область применения
This runbook covers day-to-day operations for ZEON MVP in local, pilot, and pre-production setups.

## Сервисы
- Web (`@zeon/web`) on `3010`
- API (`@zeon/api`) on `4000`
- Worker (`@zeon/worker`) for async parse/score jobs
- PostgreSQL and Redis for full mode

## Режимы запуска
### Full mode
1. Ensure PostgreSQL and Redis are reachable.
2. Set `ZEON_NO_DB=false`.
3. Run:
   ```bash
   npm install
   npm run db:generate --workspace @zeon/api
   npm run db:migrate --workspace @zeon/api
   npm run db:seed --workspace @zeon/api
   npm run dev:all
   ```

### No-DB режим (безопасный для демо)
1. Set `ZEON_NO_DB=true`.
2. Run:
   ```bash
   npm run dev --workspace @zeon/api
   npm run dev --workspace @zeon/web
   ```

## Health-checks
- API health: `GET /api/v1/health`
- API readiness: `GET /api/v1/ready`
- Integration health: `GET /api/v1/integrations/health`

## Ежедневный ops-checklist
1. Verify login for admin and HR demo accounts.
2. Verify vacancy list and candidate pages load.
3. Verify manual-review queue opens and resolves items.
4. Verify integration health has expected values.
5. Verify analytics summary endpoint responds.

## Разбор инцидентов
### 1) Login fails
- Check API is running (`/api/v1/health`).
- Verify `NEXT_PUBLIC_API_URL` points to API (`http://localhost:4000/api/v1`).
- Verify JWT secrets are set.

### 2) Candidates not appearing after upload/import
- In full mode: inspect `processing_jobs` status and worker logs.
- In no-db mode: verify upload/import response has `candidateId` and `queued`.
- Check manual review queue for low-confidence routing.

### 3) Integration deliveries failing
- Open Integrations UI and inspect delivery history.
- Use endpoint status toggle (`ACTIVE/PAUSED`) as needed.
- Retry failed delivery from history.
- Confirm URL/secret correctness.

## Действия по восстановлению
- Restart API and Worker.
- Re-run seed in a clean local demo if state is inconsistent.
- Pause unstable webhook endpoints to prevent noisy retries.

## Evidence и audit
- Use Audit page filters by `action/entityType/entityId`.
- Use drilldown endpoint `/admin/audit/related` for entity timeline.
- Preserve `requestId` values from API responses/logs for diagnostics.

## Safe rollback strategy (MVP)
- Revert scoring profile to previous threshold/weights via admin profile editor.
- Pause integration endpoints while investigating delivery issues.
- Route uncertain candidates to manual review instead of rejection.


## Приложение J. Полная карта API

### API

Base URL: `http://localhost:4000/api/v1`  
Swagger: `http://localhost:4000/docs`

## Auth
- `POST /auth/login`
- `POST /auth/refresh`
- `POST /auth/logout`
- `GET /auth/me`

## Vacancies
- `POST /vacancies`
- `GET /vacancies`
- `GET /vacancies/:id`
- `PATCH /vacancies/:id`
- `POST /vacancies/:id/publish`
- `POST /vacancies/:id/version`
- `GET /vacancies/:id/candidates`

`GET /vacancies/:id/candidates` поддерживает query-параметры:
- `status`
- `language`
- `minScore`
- `search`
- `sortBy=score|confidence|uploadDate`
- `order=asc|desc`

## Candidates
- `POST /candidates/upload`
- `POST /candidates/import`
- `GET /candidates/:id`
- `GET /candidates/:id/profile`
- `GET /candidates/:id/document`
- `PATCH /candidates/:id/status`
- `POST /candidates/:id/comment`
- `POST /candidates/compare`

## Matching
- `GET /matching/:vacancyId/:candidateId`
- `POST /matching/recalculate`
- `POST /matching/bulk-recalculate`

## Review
- `GET /manual-review`
- `GET /manual-review/:candidateId/history`
- `POST /manual-review/:candidateId/resolve`

`GET /manual-review` поддерживает query-параметры:
- `vacancyId`
- `search`
- `reason`
- `from`
- `to`
- `page`
- `pageSize`

## Analytics
- `GET /analytics/summary`
- `GET /analytics/vacancies/:id`
- `GET /analytics/pipeline`

## Feedback
- `POST /feedback`

## Integrations
- `POST /integrations/webhooks`
- `GET /integrations/webhooks`
- `POST /integrations/webhooks/test`
- `PATCH /integrations/webhooks/:id/status`
- `GET /integrations/webhooks/:id/deliveries`
- `POST /integrations/webhooks/:id/retry/:deliveryId`
- `GET /integrations/health`

`GET /integrations/webhooks/:id/deliveries` поддерживает query-параметры:
- `page`
- `pageSize`
- `success`
- `eventType`

## External AI provider contracts (Pilot)
- Parser/OCR and embeddings contracts are documented in:
  - `docs/provider-contracts-ru.md`
- Runtime provider selection is controlled via:
  - `PARSER_PROVIDER=demo|production`
  - `EMBEDDINGS_PROVIDER=demo|production`
- Pilot environment template:
  - `.env.pilot.example`
- Local stub environment template:
  - `.env.providers.stub.example`

## Admin
- `GET /admin/dictionaries`
- `POST /admin/dictionaries`
- `PATCH /admin/dictionaries/:id`
- `GET /admin/model-versions`
- `GET /admin/scoring-profiles`
- `PATCH /admin/scoring-profiles/:vacancyId`
- `GET /admin/audit`
- `GET /admin/audit/related`

`GET /admin/audit` поддерживает query-параметры:
- `page`
- `pageSize`
- `action`
- `entityType`
- `entityId`
- `userId`
- `requestId`
- `from`
- `to`

`GET /admin/audit/related` requires:
- `entityType=Candidate|Vacancy`
- `entityId`

## System
- `GET /health`
- `GET /ready`

## No-DB Mode Notes
When `ZEON_NO_DB=true`, API remains fully runnable with demo adapters and in-memory persistence for core business flows.


## Приложение K. Архитектура

### Архитектура

## Компоненты
1. Web UI (`apps/web`)
2. API (`apps/api`)
3. Worker (`apps/worker`)
4. Scoring package (`packages/scoring`)
5. Shared types/normalization (`packages/shared`)
6. PostgreSQL (Prisma models)
7. Redis queue (BullMQ)

## Pipeline обработки
1. Vacancy created/imported.
2. Candidate resume uploaded/imported.
3. Candidate document persisted.
4. Processing job persisted and queued.
5. Worker parses text and detects language (RU/KZ/EN path).
6. Normalization applies dictionaries and canonical aliases.
7. Scoring engine calculates weighted score + confidence.
8. Explainability block generated (factors, matches, gaps, risks).
9. Low-confidence cases routed to manual review.
10. Analytics and audit logs updated.

## Абстракции AI-провайдеров
- `ResumeParserAdapter`
- `LanguageDetectionProvider`
- `EmbeddingsProvider`
- `LlmExplanationProvider`

В MVP используются deterministic demo adapters с явными точками замены на production-провайдеров.

## Безопасность
- JWT auth + RBAC
- Password hashing (`bcrypt`)
- Rate limiting
- Request ID propagation
- Audit logs for key operations
- Sensitive process behavior routed with manual review fallback

## Масштабируемость
- Тяжелая обработка вынесена в worker
- Очередь поддерживает retries и backoff
- Stateless API
- Горизонтальное масштабирование через реплики worker-а


## Приложение L. Доменная модель

### Доменная модель

Implemented entities (Prisma):
- User, Role, Permission, UserRole, RolePermission
- Vacancy, VacancyVersion, VacancyRequirement, VacancyScoringProfile
- Candidate, CandidateDocument, CandidateProfile
- CandidateExperience, CandidateSkill, CandidateEducation, CandidateLanguage, CandidateCertification, CandidateTag
- MatchResult, ScoreBreakdown, Explanation
- ReviewAction
- IntegrationEndpoint, WebhookDelivery
- DictionaryEntry, ModelVersion
- ProcessingJob, AuditLog, FeedbackEntry, CandidateComment

Indexes and constraints:
- Unique user email, role code, permission code.
- Unique pair vacancy/candidate for match results.
- Candidate duplicate detection helpers via email/phone indexes.
- Soft-delete fields (`deletedAt`) on core entities.
- Timestamps and traceability metadata on all key tables.


## Приложение M. Движок скоринга

### Движок скоринга

`packages/scoring` implements an explainable weighted scoring engine.

## Dimensions
- must-have match
- hard skills match
- relevant experience
- seniority fit
- domain fit
- language fit
- education fit
- certification fit
- location/work-format fit
- career stability
- additional factors

## Выходные данные
- `finalScore` (0-100)
- `category` (`STRONGLY_FIT`, `FIT`, `BORDERLINE`, `WEAK_FIT`)
- `confidence`
- `recommendation` (`SHORTLIST`, `REVIEW`, `REJECT`)
- factor-level breakdown
- strong matches
- missing criteria
- must-have gaps
- risks
- summary
- manual review recommendation

## Thresholds (default, configurable)
- 85-100: Strongly Fit
- 70-84: Fit
- 55-69: Borderline
- 0-54: Weak Fit

## Safety
- Missing must-have criteria triggers penalties and/or manual review.
- Hard reject is disabled by default to support safer human-in-the-loop operation.

## Optional LLM Explainability Layer
- Deterministic scoring remains the source of truth.
- LLM can be enabled only for explanation text refinement.
- Provider selection is env-driven (`LLM_PROVIDER=openai|gemini|deterministic`).
- On provider timeout/error/missing key, system falls back to deterministic summary automatically.


## Приложение N. Безопасность и compliance

### Безопасность и соответствие требованиям

## Безопасность Controls
- JWT authentication and role-based authorization.
- Password hashing with bcrypt.
- API rate limiting on auth and general traffic.
- Request ID correlation for incident diagnostics.
- Audit logs for key business actions.
- Manual review fallback for low confidence and ambiguity.

## Personal Data Handling
- Candidate PII processed with controlled access boundaries.
- Logs designed to avoid unnecessary sensitive payload exposure.
- Retention/deletion hooks available via soft-delete and entity timestamps.

## Fairness & Safety Principles
- No hidden black-box-only decisioning.
- Explainable factor-level outcomes required.
- Must-have gaps and confidence exposed to users.
- Human-in-the-loop decisions remain mandatory for uncertain cases.
- Discriminatory factors are excluded from scoring logic.


## Приложение O. Deployment

### Деплой

## Локальный запуск через Docker
```bash
docker compose up --build
```

## Локальный native-запуск
```bash
npm install
npm run db:generate --workspace @zeon/api
npm run db:migrate --workspace @zeon/api
npm run db:seed --workspace @zeon/api
npm run dev:all
```

## Рекомендации для production
- Use managed PostgreSQL and Redis.
- Run API and worker in separate autoscaled deployments.
- Use object storage (S3-compatible) via storage adapter.
- Add secret manager for JWT and webhook secrets.
- Add centralized logs + tracing.
- Add WAF / reverse proxy / TLS termination.


## Приложение P. Demo guide

### Демо-сценарий

## Suggested Demo Story
1. Login as HR (`hr@zeon.local`).
2. Open Dashboard and show KPI baseline.
3. Open Vacancies and create/edit/publish a vacancy.
4. Open vacancy details and import resumes (bulk via API import).
5. Show async processing behavior and candidate ranking.
6. Open candidate card and explain score breakdown.
7. Show must-have gaps and risk visibility.
8. Open Manual Review queue and resolve one case.
9. Compare 2-3 shortlisted candidates.
10. Show Analytics and Admin dictionaries.
11. Show Audit log and Integrations webhook test.

## Demo Data Included
- 6 vacancies
- 60 candidates
- Mixed RU/KZ/EN language paths
- distribution across strongly fit / fit / borderline / weak + manual review cases
- duplicates and low confidence examples


## Приложение Q. Future roadmap

### Roadmap развития

## Planning window
- Horizon: 12 weeks
- Goal: move from demo-ready MVP to pilot/production-ready solution with measurable business impact

## Phase 0 (Week 1): Request alignment
- Add explicit "matching to customer request" section to RU materials.
- Lock KPI targets in documentation:
  - manual processing reduction 50-80%;
  - time-to-hire reduction 40-70%;
  - hiring cost reduction 30-50%;
  - primary screening precision increase 20-30%.
- Clarify MVP vs pilot wording for OCR/parser, embeddings, and LLM layers.
- Align positioning for customer-facing language (HR Tech core domain, industry-specific usage).

## Phase 1 (Weeks 2-5): Product AI core
- Integrate production OCR/parser provider for PDF/DOC/DOCX ingestion.
- Enable embeddings-based semantic matching in the scoring pipeline.
- Harden LLM explanation layer with guardrails and deterministic fallback.
- Introduce parsing/scoring quality gates and confidence routing to manual review.
- Add regression/contract tests for parser, scoring, and multilingual paths RU/KZ/EN.

## Phase 2 (Weeks 6-8): Integrations and operations
- Harden webhooks with retries/backoff/idempotency.
- Add ATS/CRM/HRIS contract tests and integration validation flow.
- Define pilot SLO/SLA for pipeline latency, API availability, and delivery success rate.
- Add observability dashboards for ingest/parse/score/review and integration health.

## Phase 3 (Weeks 9-10): Business impact validation
- Run baseline-vs-AI pilot measurement with control and experiment groups.
- Measure and report:
  - time-to-hire;
  - HR effort reduction;
  - shortlist precision/quality;
  - funnel conversion improvements.
- Publish Business Impact Report with methods, assumptions, and measured ranges.

## Phase 4 (Weeks 11-12): Productization and commercial packaging
- Prepare delivery models: SaaS, on-premise, licensing.
- Define MVP/Pilot/Enterprise packages with capability and SLA boundaries.
- Finalize implementation kit:
  - deployment blueprint;
  - integration playbook;
  - security/compliance summary;
  - 2-quarter extension roadmap.

## Exit criteria
- All customer-request lines mapped to shipped features or measured pilot evidence.
- KPI targets either validated with pilot data or explicitly tracked with agreed assumptions.
- Customer-facing docs and internal implementation status are consistent.


## Приложение R. Финальный DoD checklist

### Финальный DoD checklist

This checklist maps the implemented ZEON MVP against the requested Definition of Done.

## 1) Real codebase (not docs-only)
- Status: DONE
- Evidence: `apps/*`, `packages/*`, runnable services and tests.

## 2) Working backend
- Status: DONE
- Evidence: Fastify API with auth, RBAC, business modules, Swagger.

## 3) Working frontend
- Status: DONE
- Evidence: Next.js UI with auth flow, dashboard, vacancies, candidates, review, analytics, admin, integrations.

## 4) DB schema + migrations
- Status: DONE
- Evidence: Prisma schema and migrations in `apps/api/prisma`.
- Note: no-db demo mode available when DB is unavailable.

## 5) Resume ingestion and processing flow
- Status: DONE
- Evidence: upload/import endpoints, duplicate checks, parse quality routing, processing jobs.

## 6) Candidate scoring flow
- Status: DONE
- Evidence: weighted scoring engine in `packages/scoring`, category mapping, ranking path.

## 7) Explainability flow
- Status: DONE
- Evidence: factor breakdown, summaries, missing criteria, risks, manual review signals.
- Note: optional LLM provider supported; deterministic fallback always active.

## 8) Manual review queue
- Status: DONE
- Evidence: queue endpoint, filters, resolve actions, candidate review history.

## 9) Analytics dashboard
- Status: DONE
- Evidence: summary/pipeline endpoints + UI charts and KPI cards.

## 10) Admin configuration surface
- Status: DONE
- Evidence: dictionaries, scoring profiles, thresholds/weights update, model versions, audit tools.

## 11) API documentation
- Status: DONE
- Evidence: `docs/api.md` updated to include Wave 1/2 endpoints.

## 12) Demo data
- Status: DONE
- Evidence: seed scripts + no-db in-memory demo dataset and demo users.

## 13) Tests
- Status: DONE (MVP level)
- Evidence: RBAC/scoring tests + lifecycle and smoke API tests.
- Remaining for production-hardening: broader browser E2E coverage.

## 14) Docker/local environment
- Status: DONE
- Evidence: `docker-compose.yml`, `.env.example`, local run paths documented.

## 15) Documentation
- Status: DONE
- Evidence: architecture, domain model, scoring, API, deployment, security, roadmap, runbooks.

## 16) Final implementation summary
- Status: DONE
- Evidence: delivery summaries provided in development thread.

## 17) Clear real vs adapter/mock statement
- Status: DONE
- Real:
  - API/UI modules, scoring logic, RBAC, audit, review queue, integration lifecycle APIs, analytics.
- Adapter/Demo mode:
  - Resume parsing adapters (deterministic), no-db in-memory mode, simulated webhook failure behavior.
- Optional providers:
  - LLM explanation adapters for OpenAI/Gemini with deterministic fallback.

## Final Verdict
- MVP is demo-ready and operationally runnable.
- For full production readiness, prioritize: expanded browser E2E, external parser/OCR provider integration, and infra hardening.

## 18) Pilot/Packaging Progress (2026-04-24)
- Status: IN PROGRESS
- Evidence:
  - Full API tests green in local environment: 6/6 files, 11/11 tests.
  - Pilot KPI validation playbook added: `docs/pilot-validation-playbook-ru.md`.
  - Commercial packaging document added: `docs/commercial-packaging-ru.md`.
  - Business Impact report template available: `docs/business-impact-report-template-ru.md`.
- Remaining for closure:
  - Fill Business Impact report with pilot measurements.
  - Finalize customer-facing package (SaaS/on-prem/licensing + agreed SLA/SLO).


---

# Финальный итог

ZEON AI в текущем виде закрывает demo-ready / pilot-ready сценарий и содержит все ключевые элементы запроса заказчика:

- AI-подбор и скоринг кандидатов;
- анализ резюме относительно требований вакансии;
- explainable AI;
- ручную проверку спорных кейсов;
- RU/KZ/EN путь обработки;
- API и webhooks для интеграции с CRM/ATS/HRIS;
- analytics, audit, RBAC;
- readiness-план для production parser/OCR, embeddings и LLM hardening;
- методику подтверждения бизнес-KPI на пилоте;
- коммерческие варианты поставки: SaaS, on-premise, licensing.

Главная формулировка для заказчика:

> ZEON AI — это не просто HR-интерфейс, а API-first AI-модуль первичного подбора и скоринга кандидатов, который можно встроить в существующие HR-процессы заказчика и масштабировать до SaaS/on-prem/licensing модели.
