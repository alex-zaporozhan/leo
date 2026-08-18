# STACK_SELECTION.md
# Global technology stack selection canon
# Decision made by @ARCH; recorded in spine §2 and ADR
# Covers: Backend, Frontend, Mobile, Data, Infrastructure, AI

> **Principle:** the stack is chosen for the task and the team, not for fashion.
> Exotic choices only with ADR. Proven combinations — by default.

---

## PROJECT MODES — starting point

```
SCRIPT:      Python — automation, parsers, one-off tools
SAAS:        Python/FastAPI + TypeScript/React — startup, B2B SaaS, fast iteration
ENTERPRISE:  Java/Spring Boot + TypeScript/React — banks, corporations, strict SLA
HIGHLOAD:    Go / Java + Kafka + Kubernetes — high traffic, event streaming
MOBILE:      React Native (cross-platform) / Swift + Kotlin (native)
AI/ML:       Python + FastAPI + vector DB + LLM provider
```

---

## §1. BACKEND

### Python + FastAPI (SAAS, AI/ML)

**When:**
- B2B SaaS, clinics, marketplaces, education
- AI/RAG component — Python ecosystem
- Team knows Python; fast iteration matters more than raw performance
- < 10,000 RPS on the critical path

**Stack:**
```
Framework:    FastAPI (async, OpenAPI out of the box)
ORM:          SQLAlchemy 2.x (async) + Alembic
DB:           PostgreSQL 16+
Cache:        Redis (aioredis / redis-py)
Queues:       Celery + Redis broker
Auth:         JWT (python-jose) + passlib (bcrypt)
Validation:   Pydantic v2
HTTP client:  httpx (async)
Tests:        pytest + pytest-asyncio + httpx
Linter:       Ruff + mypy
```

**Project structure:**
```
src/
├── api/
│   └── v1/
│       ├── router.py           ← main router
│       └── routers/
│           ├── bookings.py
│           └── payments.py
├── core/
│   ├── config.py               ← Settings (pydantic-settings)
│   ├── database.py             ← async engine + session
│   └── security.py            ← JWT, hashing
├── models/                     ← SQLAlchemy models
├── schemas/                    ← Pydantic schemas (request/response)
├── services/                   ← business logic
├── repositories/               ← DB access layer
├── tasks/                      ← Celery tasks
└── main.py
```

---

### Java + Spring Boot (ENTERPRISE)

**When:**
- Banks, insurance, telecom, public sector
- Strict audit, compliance, SLA 99.9%+
- Java team; long-term support
- Integration with enterprise systems (SAP, 1C, LDAP)

**Stack:**
```
Framework:    Spring Boot 3.x (Java 21, virtual threads)
ORM:          Spring Data JPA + Hibernate + Flyway
DB:           PostgreSQL / Oracle
Cache:        Spring Cache + Redis (Lettuce)
Queues:       Spring AMQP (RabbitMQ) or Spring Kafka
Auth:         Spring Security + OAuth2/OIDC (Keycloak)
Validation:   Jakarta Bean Validation
HTTP client:  WebClient (reactive) / RestTemplate
Tests:        JUnit 5 + Mockito + Testcontainers
Build:        Gradle (preferred) / Maven
```

**Package structure:**
```
com.company.product/
├── api/
│   └── v1/
│       ├── controllers/
│       └── dto/
├── domain/
│   ├── model/
│   ├── repository/
│   └── service/
├── infrastructure/
│   ├── persistence/
│   ├── messaging/
│   └── external/
└── config/
```

---

### Go (HIGHLOAD, microservices)

**When:**
- > 10,000 RPS on the critical path
- Low latency is critical (< 10ms p99)
- Small, isolated services (API Gateway, notification service)
- Team is ready for the Go ecosystem

**Stack:**
```
Framework:    Fiber (high performance) / Chi (standard) / Echo
ORM:          sqlx (raw SQL + mapping) / pgx (pure PostgreSQL driver)
Migrations:   golang-migrate
Cache:        go-redis
Queues:       segmentio/kafka-go / sarama
Auth:         golang-jwt/jwt
HTTP client:  net/http (standard library)
Tests:        testing (standard) + testify + gomock
```

**When Go is NOT needed:**
- Team knows Python/Java and the deadline matters
- Complex business logic with many JOINs
- Product with frequent schema changes

---

### NestJS + TypeScript (Full-stack TypeScript, BFF)

**When:**
- Team is predominantly TypeScript
- BFF (Backend For Frontend) pattern
- GraphQL API
- Small teams without front/back separation

**Stack:**
```
Framework:    NestJS
ORM:          TypeORM / Prisma
Queues:       Bull (Redis) / BullMQ
Auth:         Passport.js + JWT
Tests:        Jest + Supertest
```

---

## §2. FRONTEND

### React + TypeScript (primary choice)

**Stack:**
```
Framework:    React 18+ (or Next.js for SSR/SSG)
Language:     TypeScript (mandatory in production)
State:        TanStack Query (server state) + Zustand / Redux Toolkit (client state)
Routing:      React Router v6 / Next.js App Router
UI:           shadcn/ui + Tailwind CSS (preferred)
             / Ant Design (enterprise)
             / MUI (if required by client)
Forms:        React Hook Form + Zod
HTTP:         Axios / fetch (TanStack Query manages cache)
Tests:        Vitest + Testing Library + Playwright (E2E)
Build:        Vite (dev) / Next.js (prod with SSR)
```

**Structure:**
```
src/
├── api/                    ← query functions (not business logic)
├── components/
│   ├── ui/                 ← atomic components
│   └── features/           ← composite components by feature
├── hooks/                  ← React Query hooks + custom
├── pages/ (or app/)        ← pages / routes
├── store/                  ← Zustand stores
├── types/                  ← TypeScript types
└── utils/
```

### Vue + TypeScript (alternative)

**When:** team prefers Vue; smaller bundle size matters; Nuxt ecosystem needed.

```
Framework:  Vue 3 (Composition API) / Nuxt 3
State:      Pinia
UI:         Quasar / Vuetify / PrimeVue
```

---

## §3. MOBILE

### React Native (cross-platform)

**When:**
- iOS and Android needed from one codebase
- Team knows React/TypeScript
- Time to market matters more than native performance
- Not critical: heavy animation, ARCore/ARKit, complex native APIs

**Stack:**
```
Framework:    React Native (Expo for quick start, or bare workflow)
Navigation:   React Navigation v6
State:        TanStack Query + Zustand
Storage:      AsyncStorage / MMKV (performance)
Auth:         expo-secure-store (tokens)
Push:         Expo Notifications / FCM + APNs directly
Tests:        Jest + React Native Testing Library
CI/CD:        Expo EAS (build + submit) / Fastlane
```

**Expo vs Bare Workflow:**
```
Expo (managed):
  + Quick start, OTA updates, simple CI/CD
  - Limited access to native APIs
  - Suitable: most business apps

Bare Workflow:
  + Full access to native APIs
  - Requires iOS/Android knowledge
  - Suitable: complex native integration (Bluetooth, NFC, AR)
```

---

### Swift (iOS native)

**When:** iOS-only; production-quality animation is critical; ARKit/CoreML; payment apps with high security requirements.

```
Language:       Swift 5.9+
UI Framework:   SwiftUI (new projects) / UIKit (legacy + complex custom UI)
Architecture:   MVVM + Combine / TCA (The Composable Architecture)
Networking:     URLSession + async/await / Alamofire
Storage:        CoreData / SwiftData (iOS 17+) / Realm
Auth:           Keychain for tokens; LocalAuthentication for biometric
Tests:          XCTest + XCUITest
CI/CD:          Xcode Cloud / Fastlane + GitHub Actions
```

---

### Kotlin (Android native)

**When:** Android-only; deep native integration; Wear OS / Android TV / Android Auto.

```
Language:       Kotlin
UI Framework:   Jetpack Compose (new projects) / XML Views (legacy)
Architecture:   MVVM + Clean Architecture + Hilt (DI)
Networking:     Retrofit + OkHttp + Kotlin Coroutines
Storage:        Room (SQLite ORM) / DataStore
Auth:           EncryptedSharedPreferences / Android Keystore
Tests:          JUnit + Espresso + MockK
CI/CD:          GitHub Actions + Fastlane
```

---

### Flutter (cross-platform — React Native alternative)

**When Flutter instead of React Native:**
- Team prefers Dart or has no React experience
- Pixel-perfect custom animation and UI are critical (Flutter renders itself, not through native components)
- Desktop (macOS/Windows/Linux) from one codebase is needed — Flutter supports it
- Maximum visual consistency iOS/Android without platform differences is required

**When React Native is preferred:**
- Team knows React/TypeScript — less onboarding
- Deep native library integration needed (React Native bridge is more mature)
- npm ecosystem matters (many ready packages)

```
Language:       Dart
Framework:      Flutter
State:          BLoC / Riverpod / Provider
Storage:        Hive / Isar (local) / sqflite
Network:        Dio + Retrofit
Push:           FCM + flutter_local_notifications
CI/CD:          Codemagic / Fastlane
Tests:          flutter_test + integration_test
```

---

## §4. AI / ML

### LLM and RAG stack

```
LLM providers:
  Cloud:        Anthropic Claude (generation + analysis)
                OpenAI GPT-4o (alternative)
                Google Gemini (if Google Workspace needed)
  Self-hosted:  Ollama + Mistral / LLaMA (data residency)

Embeddings:
  Cloud:        text-embedding-3-large (OpenAI)
  Self-hosted:  BGE-M3 (multilingual)

Vector Store:
  < 10M vectors:   pgvector (if PostgreSQL already)
  Production RAG:  Qdrant (self-hosted) / Pinecone (managed)

Orchestration:
  LangGraph (StateGraph) — agentic RAG, complex graphs
  LlamaIndex — document-heavy workflows
  Haystack — enterprise, production-grade

Evaluation:
  RAGAS — RAG quality metrics
  DeepEval — regression testing for LLM

Details: roles/ROLE_AI_ENGINEER.md + roles/RAG_ARCHITECTURE_STACK_2026.md
```

---

## §5. DATA

### Full data stack

```
OLTP (transactions):
  PostgreSQL 16+                ← primary DB for everything

OLAP (analytics):
  < 100M rows → materialized views in PostgreSQL
  > 100M rows → ClickHouse

Search:
  < 1M documents → pg_trgm / tsvector
  > 1M documents → Meilisearch (startup) / Elasticsearch (enterprise)

Queues and streaming:
  Background jobs → Celery + Redis
  < 100K msg/sec, replay → Redis Streams
  > 100K msg/sec, event sourcing → Apache Kafka

Cache:
  Redis (universal)
  Memcached (if only simple key-value, no Redis)

Data Pipeline (if ETL needed):
  Apache Airflow (complex DAGs)
  dbt (SQL transformations)
  Airbyte (source connectors)

Details: roles/DATA_STORE_SELECTION.md
```

---

## §6. INFRASTRUCTURE

### Containers and orchestration

```
Development:  Docker + Docker Compose
CI/CD:        Jenkins + GHCR (primary project canon)
              GitHub Actions (auxiliary PR gates)
Production:   Kubernetes (at > 1 instance or HA requirements)
Registry:     GHCR (ghcr.io) — primary
              Docker Hub — only if required by client

Kubernetes operators:
  Strimzi     ← Kafka
  Redis Operator ← Redis Sentinel/Cluster
  KEDA        ← event-driven autoscaling

Ingress:
  nginx-ingress-controller + cert-manager (Let's Encrypt)

Monitoring stack:
  Prometheus + Grafana + AlertManager
  Loki (logs) + Tempo / Jaeger (traces)
  OpenTelemetry (instrumentation)

Details: roles/DOCKER_INFRA_PASSPORT.md + roles/SYSTEM_DESIGN_PROTOCOL.md
```

### Observability — stack selection

The decision is made at the start: changing it later is expensive. @ARCH records in spine §10.

```
Sentry (error tracking only):
  + Quick start, minimal setup
  + Convenient UI for errors, traces, performance
  + Managed, no ops overhead
  - Errors + basic performance only; no custom metrics
  When: startup, MVP, team < 5 people, no DevOps

Prometheus + Grafana (self-hosted, recommended):
  + Full control of metrics, alerts, dashboards
  + kube-prometheus-stack = everything out of the box for Kubernetes
  + Integration with any service
  - Requires ops knowledge; metrics storage on own side
  When: Kubernetes, production SaaS, DevOps available

Datadog (managed, enterprise):
  + APM, logs, traces, metrics in one place
  + Minimal setup, powerful alerts
  - $$$: ~$15-30/host/month grows fast
  When: enterprise, compliance, no resources for self-hosted ops

OpenTelemetry (instrumentation standard):
  + Vendor-neutral: one SDK → any backend (Grafana/Datadog/Jaeger)
  + No lock-in to a specific provider
  Rule: always instrument code via OpenTelemetry SDK;
        backend choice is a separate decision

Recommended path:
  Start → Sentry (errors) + basic Prometheus metrics
  Production → kube-prometheus-stack + Loki + Tempo
  Enterprise → Datadog or Grafana Cloud (managed)
```

### Hosting (by project type)

```
RF projects (personal data of RF residents — FZ-152):
  Selectel, Timeweb Cloud, Yandex Cloud, MTS Cloud

International projects:
  AWS (EKS + RDS + ElastiCache)
  GCP (GKE + Cloud SQL + Memorystore)
  Azure (AKS + Azure Database)

Managed DB (recommended for production):
  PostgreSQL: Supabase / Neon / RDS / Yandex Managed PostgreSQL
  Redis: Upstash / ElastiCache / Yandex Managed Redis

Serverless (for simple cases):
  Vercel (Next.js frontend)
  Railway (simple backend services)
```

---

## §7. INTER-SERVICE COMMUNICATION

```
Synchronous:
  REST + JSON    ← public APIs, simple services
  gRPC           ← internal microservices, high throughput
  GraphQL        ← BFF pattern, flexible client queries

Asynchronous:
  Celery + Redis ← Python background jobs
  Kafka          ← event streaming, > 100K msg/sec
  Redis Streams  ← < 100K msg/sec, replay needed

Real-time:
  WebSocket      ← bidirectional exchange (chat)
  SSE            ← one-directional stream (notifications)
  FCM/APNs       ← mobile push notifications

Service Mesh (at > 10 microservices):
  Istio / Linkerd ← mTLS, traffic management, observability
```

---

## §8. SECURITY

```
Auth:
  JWT (access + refresh token)
  OAuth2 + OIDC (Keycloak / Auth0 for enterprise)
  API Keys (for service-to-service)

Secrets:
  HashiCorp Vault (enterprise)
  Kubernetes Secrets + External Secrets Operator
  Never: in code, in git, in docker image

TLS:
  Let's Encrypt (automatic via cert-manager)
  Corporate CAs (enterprise)

Scanning:
  Trivy (images + dependencies)
  Snyk / Dependabot (dependencies)
  SAST: Semgrep / SonarQube
```

---

## §9. TESTS

```
Python:
  pytest + pytest-asyncio       ← unit + integration
  httpx (AsyncClient)           ← API tests
  Testcontainers                ← real DB in tests
  Locust                        ← load tests

Java:
  JUnit 5 + Mockito             ← unit
  Testcontainers                ← integration
  Spring Boot Test              ← integration
  JMeter / Gatling              ← load tests

Frontend:
  Vitest + Testing Library      ← unit + component
  Playwright                    ← E2E

Mobile:
  XCTest + XCUITest (iOS)
  JUnit + Espresso (Android)
  Detox (React Native E2E)

CI tests are mandatory: never merge without a green pipeline
```

---

## SELECTION MATRIX — quick answer

| Task | Recommendation | Alternative |
|------|--------------|------------|
| B2B SaaS backend | Python + FastAPI | NestJS |
| Enterprise backend | Java + Spring Boot | — |
| High-throughput service | Go | Java (reactive) |
| Admin panel / dashboard | React + TypeScript | Vue |
| Cross-platform mobile | React Native (Expo) | Flutter |
| iOS native | Swift + SwiftUI | — |
| Android native | Kotlin + Compose | — |
| Primary DB | PostgreSQL | — |
| Cache | Redis | — |
| Background jobs | Celery + Redis | — |
| Event streaming | Kafka | Redis Streams |
| Full-text search | Meilisearch (startup) | Elasticsearch |
| Analytics large volumes | ClickHouse | — |
| Container orchestration | Kubernetes | Docker Compose (dev) |
| LLM generation | Claude Sonnet | GPT-4o |
| Embeddings | text-embedding-3-large | BGE-M3 (self-hosted) |
| Vector store | Qdrant | pgvector |

---

Reference: roles/DATA_STORE_SELECTION.md · roles/SYSTEM_DESIGN_PROTOCOL.md · roles/ROLE_ARCH.md · roles/ROLE_AI_ENGINEER.md · roles/RAG_ARCHITECTURE_STACK_2026.md · roles/DOCKER_INFRA_PASSPORT.md
Version: 2.0 | 2026-05-22
