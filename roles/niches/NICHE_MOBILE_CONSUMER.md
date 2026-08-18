# NICHE_MOBILE_CONSUMER

Нишевой пакет для мобильных приложений (Google Play / Apple App Store).

## Где применять

- B2C mobile-first продукты.
- Super-app companion для существующего web/backend.
- Проекты с жесткими требованиями App Store / Google Play.

## Приоритеты

- Онбординг и быстрый первый успех пользователя.
- Устойчивость на слабой сети и офлайн-сценарии.
- Crash/ANR дисциплина и стабильные релизы.
- Соответствие store policy и приватности данных.

## Микро-инварианты (обязательные)

- Каждый экран имеет 4 состояния: Loading / Empty / Error / Success.
- Любая мутация блокирует повторный submit до завершения запроса.
- Ошибки API на клиенте отображаются в едином формате `detail + code`.
- Для критичных действий есть явный fallback при сетевой деградации.
- Push и фоновая синхронизация не должны ломать целостность локальных данных.
- Идемпотентность событий синхронизации обязательна.

## Обязательные разделы в архитектуре

- Управление версиями и релиз-каналы (alpha/beta/prod).
- Push-уведомления и разрешения.
- Offline-first / синхронизация.
- Telemetry (crash, startup time, retention events).

## Обязательные разделы в DEV_PROMPTS

- Нефункциональные ограничения по мобильному UX (latency, crash-budget).
- Матрица сетевых сценариев (online/intermittent/offline).
- Тест-план для upgrade path (обновление приложения без потери состояния).
- Чеклист store compliance перед релизом.

## Обязательные шаблоны

- `roles/TESTING_CANON.md`
- `roles/METRICS_PROTOCOL.md`
- `roles/DOCKER_INFRA_PASSPORT.md` (backend/mobile API контур)
- `roles/TEMPLATE_DESIGN_UX.md` (если есть витринные экраны)

## Метрики уровня ниши (минимум)

- Crash-free users (%).
- ANR rate (%).
- Cold start p50/p95.
- Onboarding completion.
- Day-1 / Day-7 retention.
- Sync failure rate.

## Store readiness checklist

- Подписывание и bundle IDs настроены.
- Privacy policy и data usage задокументированы.
- Crash rate, ANR и startup budget определены.
- План rollback/hotfix зафиксирован.

## Риски и анти-паттерны

- Выпуск без canary-группы и crash-мониторинга.
- Offline cache без стратегии инвалидирования.
- Push-логика без дедупликации событий.
- Логирование персональных данных в telemetry.

## Definition of Done (ниша)

- Пройден функциональный smoke на iOS и Android.
- Подтверждена корректность offline -> online sync.
- Store checklist закрыт без критичных блокеров.
- Метрики релизного качества заведены в `METRICS_REGISTRY`.

## Передача @LEAD (шаблон)

```
ПЕРЕДАЧА @[РОЛЬ] → @LEAD

Ниша: MOBILE_CONSUMER
Что сделано: [кратко]
Store readiness: [готово / не готово + блокеры]
Crash/ANR budget: [факт]
Следующий шаг: @[РОЛЬ] → [задача]
```

## COMMAND CENTER (готовый шаблон)

```
***
COMMAND CENTER:
> Фаза: [Старт / Архитектура / Разработка / QA_ARCH / Релиз]
> Ниша: MOBILE_CONSUMER
> Сделано: [кратко: что готово по iOS/Android]
> Store readiness: [готово / блокер]
> Crash/ANR: [текущее значение vs бюджет]
> Следующий шаг: @[РОЛЬ] → [конкретная задача]
> Промпт для копирования: [готовый промпт или "не нужен"]
***
```
