# ZEON — MVP AI-сопоставления и оценки кандидатов

ZEON — enterprise-ready и объяснимый AI-модуль для первичного скрининга кандидатов.  
Он помогает HR-командам быстрее обрабатывать поток резюме, сохраняя контроль человека в процессе принятия решений.

## Стек

- Frontend: Next.js 15 + TypeScript + Tailwind + Recharts
- Backend: Fastify + TypeScript + Prisma + JWT + RBAC
- Worker: BullMQ + Redis
- База данных: PostgreSQL
- Monorepo: npm workspaces (`apps/*`, `packages/*`)

## Архитектура высокого уровня

- `apps/web`: интерфейс HR и интерфейс менеджера
- `apps/api`: REST API, авторизация, RBAC, аудит, администрирование, аналитика, интеграции
- `apps/worker`: асинхронный парсинг и задачи скоринга
- `packages/shared`: общие типы и логика нормализации
- `packages/scoring`: объяснимый движок взвешенной оценки
- `docs/*`: документация по архитектуре, API, безопасности/соответствию требованиям и развертыванию

## Быстрый старт локально

1. Скопируйте `.env.example` в `.env` и при необходимости измените значения.
2. Установите зависимости:

   ```bash
   npm install
   ```

3. Сгенерируйте Prisma Client и выполните миграцию:

   ```bash
   npm run db:generate --workspace @zeon/api
   npm run db:migrate --workspace @zeon/api
   ```

4. Заполните базу демо-данными:

   ```bash
   npm run db:seed --workspace @zeon/api
   ```

5. Запустите все сервисы:

   ```bash
   npm run dev:all
   ```

6. Откройте:

   - Web: `http://localhost:3010`
   - API: `http://localhost:4000/api/v1`
   - Swagger: `http://localhost:4000/docs`

## Быстрый старт в режиме без базы данных

Используйте этот режим, если PostgreSQL/Redis недоступны.

1. В `.env` установите:

   ```env
   ZEON_NO_DB=true
   ```

2. Запустите:

   ```bash
   npm run dev --workspace @zeon/api
   npm run dev --workspace @zeon/web
   ```

3. Войдите под демо-пользователями:

   - `admin@zeon.local` / `Admin123!`
   - `hr@zeon.local` / `Hr123456!`
   - `manager@zeon.local` / `Manager123!`

## Запуск через Docker

```bash
docker compose up --build
```

## Демо-аккаунты

- `admin@zeon.local` / `Admin123!`
- `hr@zeon.local` / `Hr123456!`
- `manager@zeon.local` / `Manager123!`

## Основной функциональный охват

- Создание, редактирование, публикация и версионирование вакансий
- Загрузка резюме: одиночная/массовая загрузка и импорт, обнаружение дублей
- Асинхронный pipeline парсинга, нормализации и скоринга через BullMQ
- Поддержка языкового пути RU/KZ/EN
- Объяснимый breakdown оценки и рекомендации
- Маршрутизация на ручную проверку и фиксация решения
- Сравнение кандидатов от 2 до 5 человек
- Аналитические дашборды
- Административные справочники и журнал аудита
- Настройка и тестирование интеграционных webhook-ов, а также жизненный цикл доставки

## Переменные окружения

Смотрите `.env.example`.

Переменные, связанные с LLM:

- `LLM_PROVIDER=deterministic|openai|gemini`
- `OPENAI_API_KEY`, `OPENAI_MODEL`
- `GEMINI_API_KEY`, `GEMINI_MODEL`

Если ключи провайдера отсутствуют или провайдер не отвечает, ZEON автоматически переключается на deterministic explainability.

## Команды для тестов

```bash
npm run test --workspace @zeon/api
npm run test --workspace @zeon/web
```

## Карта документации

- Архитектура: `docs/architecture.md`
- Доменная модель: `docs/domain-model.md`
- API: `docs/api.md`
- Движок скоринга: `docs/scoring-engine.md`
- Развертывание: `docs/deployment.md`
- Безопасность и соответствие требованиям: `docs/security-and-compliance.md`
- Демо-гайд: `docs/demo-guide.md`
- Roadmap будущего развития: `docs/future-roadmap.md`
- Operations runbook: `docs/operations-runbook.md`
- Integration runbook: `docs/integration-runbook.md`
- Финальный DoD-чеклист: `docs/final-dod-checklist.md`

## Известные ограничения MVP

- Провайдеры парсинга являются демо-адаптерами deterministic-типа, а не внешними OCR/LLM-интеграциями.
- Извлечение данных из PDF/DOC/DOCX смоделировано через adapter path; надежное бинарное извлечение можно подключить отдельно.
- Доставка webhook-ов в no-db режиме использует контролируемую симуляцию; полноценное выполнение outbound HTTP требует настройки production-коннектора.
- Провайдеры LLM-объяснений опциональны и работают по принципу best-effort; deterministic fallback всегда остается активным.

## Следующие шаги

- Подключить production-парсеры документов и OCR-pipeline.
- Добавить провайдера semantic embeddings для глубокого сопоставления релевантности.
- Добавить полноценную i18n-локализацию RU/KZ для UI-контента.
- Расширить frontend E2E-набор и тесты нагрузки/производительности.
