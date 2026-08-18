# NICHES README

Каталог нишевых пакетов, которые подключаются на старте проекта.

## Доступные пакеты

- `NICHE_CRM_ERP.md`
- `NICHE_MOBILE_CONSUMER.md`
- `NICHE_MARKETPLACE.md`
- `NICHE_CONTENT_SOCIAL.md`
- `NICHE_AI_ASSISTANT.md`

## Как использовать

1. @LEAD выбирает пакет по `roles/NICHE_BOOTSTRAP_PROTOCOL.md`.
2. Заполняется `roles/TEMPLATE_PROJECT_PROFILE.md`.
3. Профиль сохраняется в `docs/artifacts/PROJECT_PROFILE.md`.
4. В `docs/digital-trainer/DEVELOPMENT_PLAN.md` фиксируется активный пакет.
5. В первый `DEV_PROMPTS` добавляются инварианты и метрики выбранной ниши.

## Правило структуры пакета

Каждый `NICHE_*` файл обязан содержать:

- Где применять
- Микро-инварианты
- Обязательные шаблоны
- Обязательные секции DEV_PROMPTS
- Метрики ниши
- Риски и анти-паттерны
- Definition of Done
- Готовый COMMAND CENTER шаблон для @LEAD
