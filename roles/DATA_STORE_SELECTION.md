# DATA_STORE_SELECTION.md
# Data store selection canon
# Decision made by @ARCH; recorded in the project's ADR spine

> **Principle:** the right storage is the one that solves the task with the minimum operational complexity.

---

## DECISION TREE

```
What is the nature of the data and the access pattern?
│
├── Structured data + transactions + JOIN + ACID
│   └── PostgreSQL (95% of cases)
│
├── Data in PostgreSQL but full-text search needed?
│   ├── < 1M documents, simple search → pg_trgm + tsvector
│   └── > 1M documents, facets, relevance → Elasticsearch / Meilisearch
│
├── Cache / sessions / queues / real-time counters?
│   └── Redis
│
├── Analytics on large volumes (OLAP)?
│   ├── < 100M rows → materialized views in PostgreSQL
│   └── > 100M rows → ClickHouse
│
├── Documents with an unpredictable schema?
│   ├── Data is isolated, no JOIN → MongoDB
│   └── Data is linked with relational data → JSONB in PostgreSQL
│
├── High-throughput events + replay + guarantees?
│   ├── < 100K msg/sec, no replay → Celery + Redis or Redis Streams
│   └── > 100K msg/sec or replay needed → Kafka
│
└── Graph data (social connections, recommendations)?
    └── Neo4j (specialised; ADR mandatory)
```

---

## §1. PostgreSQL — primary storage

**Always the first choice** if there are no explicit reasons for another storage.
Multi-tenant SaaS, finance, transactions, ACID — PostgreSQL.

### When to scale up

| Signal | Solution |
|--------|---------|
| > 100 concurrent connections | PgBouncer (transaction pooling) |
| > 70% read traffic | Read replica |
| Slow analytical queries > 500ms | Materialized views or ClickHouse |
| Full-text search > 1M documents | Elasticsearch / Meilisearch |
| > 500M rows in one table | Partitioning by date / tenant_id |

### Mandatory patterns

```sql
-- Multi-tenancy: composite index is mandatory
CREATE INDEX idx_bookings_tenant_date
  ON bookings(tenant_id, created_at DESC);

-- Soft delete: partial index
CREATE INDEX idx_bookings_active
  ON bookings(tenant_id, status)
  WHERE deleted_at IS NULL;

-- Locking competing resources
SELECT * FROM slots
  WHERE id = $1 AND tenant_id = $2
  FOR UPDATE;

-- Uniqueness within the tenant
CREATE UNIQUE INDEX idx_slots_unique
  ON slots(tenant_id, resource_id, start_time)
  WHERE deleted_at IS NULL;

-- Partitioning for large tables
CREATE TABLE events (
  id UUID,
  tenant_id UUID,
  created_at TIMESTAMPTZ
) PARTITION BY RANGE (created_at);
```

### Useful extensions
- `pg_trgm` — fuzzy search, LIKE with index
- `uuid-ossp` / `gen_random_uuid()` — UUID generation
- `pgcrypto` — encryption at rest
- `pg_stat_statements` — slow query analysis
- `pgvector` — vector embeddings (RAG)

---

## §2. Redis — cache, queues, real-time

**Never:** primary storage without PostgreSQL as source of truth.

### Data structures

| Structure | Use case | Example key |
|-----------|---------|-------------|
| String | Counters, simple cache, locks | rate_limit:user:123 → 47 |
| Hash | Sessions, objects with fields | session:abc → {user_id, role} |
| List | Simple queue, last N log | recent:clinic:123 |
| Set | Unique elements, online users | online_users → {u1, u2} |
| Sorted Set | Rankings, priority queues | leaderboard → [(u1,100),(u2,90)] |
| Stream | Reliable queue with consumer groups | events:bookings |
| Pub/Sub | Real-time notifications (fire-and-forget) | WebSocket fan-out |

### Redis Streams vs Celery vs Kafka

```
Celery + Redis broker:
  + Background jobs, retry out of the box, Flower monitoring
  + Simplicity for Python stack
  - No replay

Redis Streams:
  + Replay within retention
  + Consumer groups
  + < 100K msg/sec
  - No exactly-once

Kafka:
  + > 100K msg/sec
  + Long-term replay (audit, event sourcing)
  + Multiple independent consumer groups
  - High operational complexity
```

### Redis Sentinel vs Cluster
```
Sentinel (recommended for most cases):
  - 3 nodes: 1 primary + 1 replica + 1 sentinel
  - Suitable: < 100GB data, < 100K ops/sec
  - Automatic failover

Cluster (for large scale):
  - 6 nodes minimum: 3 primary + 3 replica
  - Suitable: > 100GB data or > 100K ops/sec
  - Horizontal sharding
```

### Eviction Policy
- Response cache: `allkeys-lru`
- Celery broker: `noeviction` (task loss is critical)
- Sessions: `volatile-lru`

---

## §3. MongoDB — document database

**Justified use cases:**
- CMS / content with different block structures
- Product catalogue with different attributes per category
- Logs and events with a flexible payload schema
- IoT data with different schemas per device type
- Offline-first mobile sync with nested structures

**When MongoDB is a mistake:**
- Financial data with ACID requirements
- Data is actively JOIN-ed with other collections
- Multi-tenancy with isolation (PostgreSQL RLS is simpler)

### Hybrid approach (recommended)
```
PostgreSQL: users, tenants, bookings, payments (transactional)
MongoDB: content, catalog, preferences, event_log (document)

Connection: MongoDB documents store tenant_id and entity_id from PostgreSQL
Rule: MongoDB is not source of truth for financial data
```

---

## §4. Elasticsearch / Meilisearch — full-text search

### Comparison

| | PostgreSQL FTS | Meilisearch | Elasticsearch |
|--|--------------|-------------|--------------|
| Documents | < 1M | < 10M | Unlimited |
| Typo tolerance | No | Yes | Yes |
| Facets | Limited | Yes | Yes |
| Ops complexity | None | Low | High |
| When | Simple search | SaaS, startup | Large scale |

### Synchronisation pattern
```
PostgreSQL (source of truth)
  → on change → Celery task
    → Elasticsearch/Meilisearch index update

Rule: never write to the search index from business logic directly.
On restart: full reindex from PostgreSQL.
Eventual consistency is acceptable: 1–5 sec delay.
```

---

## §5. TimescaleDB — time-series (middle ground)

**When:** time-series data (metrics, IoT, financial ticks, logs with aggregates) without the need for full OLAP. Works as a PostgreSQL extension — no stack change.

**Why a middle ground between PostgreSQL and ClickHouse is needed:**
```
PostgreSQL plain:
  + Simplicity
  - Slow at > 10M time-series rows without special optimisation
  - No automatic continuous aggregates

TimescaleDB (PostgreSQL + extension):
  + Full PostgreSQL compatibility (same queries, ORM, migrations)
  + Automatic partitioning by time (chunks)
  + Continuous aggregates: pre-aggregates updated automatically
  + Compression: up to 90% storage savings on historical data
  - Not suitable for cross-tenant analytics across all tenants at once

ClickHouse:
  + > 100M rows, complex OLAP analytics
  - Separate service, separate stack, ETL pipeline
```

**Justified use cases:**
- Application metrics (latency, RPS, error rate) — if not using Prometheus
- Financial transactions with period aggregates (revenue per hour/day/month)
- IoT: sensor readings, telemetry
- User activity logs with aggregates (sessions, events)

**Example:**
```sql
-- Enable extension (one line in migration)
CREATE EXTENSION IF NOT EXISTS timescaledb;

-- Regular PostgreSQL table
CREATE TABLE metrics (
  time        TIMESTAMPTZ NOT NULL,
  tenant_id   UUID NOT NULL,
  metric_name TEXT NOT NULL,
  value       DOUBLE PRECISION
);

-- Convert to hypertable (automatic partitioning)
SELECT create_hypertable('metrics', 'time');

-- Continuous aggregate: aggregation cache, updated automatically
CREATE MATERIALIZED VIEW metrics_hourly
WITH (timescaledb.continuous) AS
SELECT time_bucket('1 hour', time) AS bucket,
       tenant_id,
       metric_name,
       avg(value) AS avg_value,
       max(value) AS max_value
FROM metrics
GROUP BY bucket, tenant_id, metric_name;
```

**Decision: TimescaleDB vs ClickHouse:**
```
TimescaleDB — if:
  ✓ PostgreSQL already in the stack
  ✓ < 500M time-series rows
  ✓ Team does not want a separate service
  ✓ JOINs with relational data needed

ClickHouse — if:
  ✓ > 500M rows OR OLAP analytics across all dimensions needed
  ✓ Team is ready for a separate service and ETL
```

---

## §6. ClickHouse — analytical storage (OLAP)

**When to move from PostgreSQL:**
- Analytical queries > 500ms with correct indexes
- Analytical data volume > 100M rows
- > 10 concurrent analytical queries

### Architecture
```
PostgreSQL (OLTP) → replication → ClickHouse (OLAP)
  Via: Kafka + consumer / Airbyte / Debezium CDC

ClickHouse — read analytics only.
All writes go through PostgreSQL.
Replication lag: 1 min – 1 hour (acceptable for analytics).
```

```sql
CREATE TABLE events (
  tenant_id UUID,
  event_type String,
  created_at DateTime
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(created_at)
ORDER BY (tenant_id, created_at);
```

---

## §6. Kafka — event streaming

**When Kafka and not Redis Streams:**
- Throughput > 100K msg/sec
- Long-term replay (weeks, months)
- Multiple independent consumer groups on one topic
- Event sourcing as a pattern
- Integration between multiple services / teams

### Kafka in Kubernetes (Strimzi Operator)
```yaml
apiVersion: kafka.strimzi.io/v1beta2
kind: Kafka
metadata:
  name: kafka-cluster
spec:
  kafka:
    replicas: 3
    storage:
      type: persistent-claim
      size: 100Gi
  zookeeper:
    replicas: 3
```

### Topic Design
```
Naming: [domain].[entity].[event_type]
  payments.invoice.created
  bookings.appointment.cancelled

Partition key: entity_id (for ordering within one entity)
Partitions = planned number of consumers × 2

Retention:
  Business events: 7–30 days
  Analytics events: 90+ days
```

---

## §7. Kubernetes — orchestration

**When to move from Docker Compose:**
- > 1 instance of any service
- Zero-downtime deployment
- Auto-scaling by load
- > 5 services in production

### Minimum SaaS architecture
```yaml
Deployments (stateless, HPA):
  api:          replicas: 2–N (CPU/RPS)
  celery:       replicas: 2–N (queue lag, KEDA)
  celery-beat:  replicas: 1 (singleton)
  websocket:    replicas: 2–N (connections)

Managed (externalise in production):
  PostgreSQL: RDS / Supabase / Neon
  Redis:      ElastiCache / Upstash

Ingress: nginx-ingress + cert-manager (Let's Encrypt)
Monitoring: kube-prometheus-stack (Prometheus + Grafana)
Tracing: Jaeger or Tempo
```

### KEDA — Celery autoscaling by queue
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: celery-scaler
spec:
  scaleTargetRef:
    name: celery-deployment
  minReplicaCount: 2
  maxReplicaCount: 20
  triggers:
  - type: redis
    metadata:
      address: redis:6379
      listName: celery
      listLength: "10"
```

---

## §8. gRPC — inter-service communication

**When:** internal microservices, high throughput, strict contracts.

### REST vs gRPC
| | REST/HTTP | gRPC |
|--|-----------|------|
| Protocol | HTTP/1.1 + JSON | HTTP/2 + Protobuf |
| Performance | Basic | 5–10x faster |
| Streaming | No | Bidirectional out of the box |
| Browser | Yes | grpc-web (with proxy) |
| When | Public API | Internal microservices |

```protobuf
syntax = "proto3";
package booking.v1;

service BookingService {
  rpc CreateBooking(CreateBookingRequest) returns (BookingResponse);
  rpc StreamBookings(StreamRequest) returns (stream BookingEvent);
}

// Versioning: booking.v1, booking.v2
// Backward compat: add fields, do not delete/rename
```

---

## §9. Vector Store — for AI/RAG systems

**When:** storing and searching vector embeddings in AI applications (RAG, semantic search, recommendations).

### pgvector vs Qdrant vs Pinecone

| | pgvector | Qdrant | Pinecone |
|--|---------|--------|---------|
| Setup | Extension in PostgreSQL | Separate service | Managed cloud |
| Vectors | < 5M | Unlimited | Unlimited |
| Search speed | Slower at > 1M | High | High |
| Filtering | SQL (full power) | Payload filters | Metadata filters |
| Joint queries | JOIN with relational data | No | No |
| Ops complexity | None (already PostgreSQL) | Medium | None (managed) |
| Cost | Free | Self-hosted / managed | $$$ at large volumes |
| When | Startup, < 5M vectors, JOINs needed | Production RAG, > 5M vectors | Managed without ops overhead |

### Decision tree for vector storage

```
How many vectors are planned in 12 months?
│
├── < 1M → pgvector (PostgreSQL extension, no new service)
│
├── 1M – 10M → evaluate:
│   ├── JOINs with relational data needed → pgvector with HNSW index
│   └── No JOINs, speed needed → Qdrant (self-hosted)
│
└── > 10M → Qdrant or Pinecone
    ├── Kubernetes available, team ready for ops → Qdrant self-hosted
    └── No ops resources → Pinecone managed
```

### pgvector — when it is sufficient

```sql
-- Enable extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Table with embeddings
CREATE TABLE documents (
  id         UUID PRIMARY KEY,
  tenant_id  UUID NOT NULL,
  content    TEXT,
  embedding  vector(1536),  -- size depends on model
  created_at TIMESTAMPTZ DEFAULT now()
);

-- HNSW index (recommended for > 100K vectors)
CREATE INDEX ON documents
  USING hnsw (embedding vector_cosine_ops)
  WITH (m = 16, ef_construction = 64);

-- Semantic search with tenant isolation
SELECT id, content,
       1 - (embedding <=> $1::vector) AS similarity
FROM documents
WHERE tenant_id = $2          -- tenant isolation is mandatory
  AND deleted_at IS NULL
ORDER BY embedding <=> $1::vector
LIMIT 20;
```

### Qdrant — when more is needed

```python
# Creating a collection with tenant isolation via payload
from qdrant_client import QdrantClient
from qdrant_client.models import VectorParams, Distance, Filter, FieldCondition, MatchValue

client = QdrantClient(host="qdrant", port=6333)

# Tenant isolation via payload filter — mandatory (Pillar X1)
results = client.search(
    collection_name="documents",
    query_vector=query_embedding,
    query_filter=Filter(
        must=[FieldCondition(
            key="tenant_id",
            match=MatchValue(value=str(tenant_id))
        )]
    ),
    limit=20
)
```

**Important:** for both solutions tenant isolation is implemented in the search code — do not rely on the user "not knowing" someone else's IDs. This is Pillar X1 from `roles/ROLE_AI_ENGINEER.md`.

---

## ADR TEMPLATE — storage selection

```markdown
## ADR: Storage selection for [module]

**Context:** [task and requirements]

**Considered options:**
1. PostgreSQL: [pros/cons]
2. [Alternative]: [pros/cons]

**Decision:** [chosen option]

**Justification:** [specific reasons]

**Consequences:**
- Ops complexity: low/medium/high
- At 10x load: [what will happen]

**Condition for review:** [at what signal to return to this decision]
```

---

Reference: roles/SYSTEM_DESIGN_PROTOCOL.md · roles/CACHE_STRATEGY.md · roles/ROLE_ARCH.md · roles/STACK_SELECTION.md
Version: 1.0 | 2026-05-22
