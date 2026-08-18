# NICHE_AI_ASSISTANT

Нишевой пакет для AI-first приложений и ассистентов.

## Где применять

- AI copilot/assistant в вебе и мобильных приложениях.
- Инструменты генерации текста/кода/аналитики.
- Продукты с RAG, tool-calling и агентными сценариями.

## Приоритеты

- Качество ответов и управляемость контекста.
- Стоимость inference и лимиты использования.
- Безопасность промптов и данных.
- Наблюдаемость качества (hallucination, refusal, latency).

## Микро-инварианты (обязательные)

- Ответ без источника в RAG-контуре маркируется как uncertain.
- Запросы с чувствительными данными проходят policy-фильтр.
- Tool-calling имеет allowlist и валидацию входов.
- Retry не должен дублировать side effects.
- Пользователь видит явное сообщение при fallback-модели.

## Критичные доменные контуры

- Context lifecycle (ingest -> index -> retrieve -> respond).
- Guardrails lifecycle (detect -> block -> fallback -> log).
- Cost lifecycle (quota -> consume -> alert -> throttle).

## Обязательные шаблоны

- `roles/RAG_CANON.md`
- `roles/METRICS_PROTOCOL.md`
- `roles/ROLE_SEC.md`
- `roles/TEMPLATE_DOCUMENTATION_ARCHITECTURE.md`

## Обязательные разделы в DEV_PROMPTS

- Источники истины и границы RAG-контекста.
- Политика fallback (модель/режим/сообщение пользователю).
- Ограничения tool execution и permission boundary.
- Тесты на hallucination/unsafe output/latency budget.

## Метрики уровня ниши (минимум)

- Answer grounded rate.
- Hallucination report rate.
- Refusal correctness rate.
- p50/p95 latency per model.
- Cost per successful session.

## Критичные проверки

- Жесткий список источников истины для RAG.
- Контроль доступа к данным в запросах к моделям.
- Явные fallback-сценарии при ошибках провайдера LLM.

## Риски и анти-паттерны

- Свободный RAG без allowlist и приоритета источников.
- Tool execution без sandbox/permission checks.
- Экономия на safety-фильтрах в пользу скорости релиза.
- Отсутствие telemetry по качеству ответов.

## Definition of Done (ниша)

- RAG-контур верифицирован и проходит проверку целостности якорей.
- Guardrails покрывают критичные unsafe-сценарии.
- Cost/latency budgets зафиксированы и мониторятся.
- Пользовательский fallback понятен и повторяем.

## COMMAND CENTER (готовый шаблон)

```
***
COMMAND CENTER:
> Фаза: [Старт / RAG / Архитектура / Разработка / QA_ARCH]
> Ниша: AI_ASSISTANT
> Сделано: [что закрыто по качеству ответов]
> Grounding/safety: [ok / риск]
> Cost/latency: [текущее значение vs бюджет]
> Следующий шаг: @[РОЛЬ] → [конкретная задача]
> Промпт для копирования: [готовый промпт или "не нужен"]
***
```
