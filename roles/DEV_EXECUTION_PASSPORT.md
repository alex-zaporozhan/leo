# DEV_EXECUTION_PASSPORT.md
# Development execution passport — checkpoint map, pattern catalogue, diagnostics
# Analogous to ARCHITECTURE_EXCELLENCE_PASSPORT but for the moment of writing code

> **Principle:** @DEV errors do not occur because the developer doesn't know the rules,
> but because the rules are not activated at the right moment.
> This file is a system of activation checkpoints built into the work process.
>
> Difference from ROLE_DEV:
> ROLE_DEV — laws and principles (permanent, read once at onboarding).
> DEV_EXECUTION_PASSPORT — operational tool (used on every task).

---

## HOW TO USE

```
Before code:           §1 Pre-implementation Scan
During:                §2 Anti-pattern Detector — keep in mind
Before submission:     §3 Exit Criteria + §5 Code Review Self-Check
Non-standard task:     §4 Pattern Catalogue — ready solutions
Performance:           §6 Performance Diagnostics
Mobile task:           §7 Mobile Patterns
AI/RAG task:           §8 AI/RAG Patterns
Error found:           §9 REFLEX-LINK — update the passport
```

@QA_ARCH checks by the same principles. The better @DEV ran through the passport — the fewer iterations.

---

## §1. PRE-IMPLEMENTATION SCAN

### 1.1 Doppelganger Check

```
Goal: do not create what already exists.

Backend (Python):
  grep -r "def [similar_name]" src/
  grep -r "class [similar_name]" src/
  grep -r "async def [similar_name]" src/

Backend (Java):
  grep -r "public.*[similarName]" src/
  Search by @Service, @Repository with similar purpose

Frontend:
  Search for useXxx (hooks), [ComponentName] (components)
  Check api/ folder — is there already a function for this endpoint
  Check types/ — is there already the needed type

DB schema:
  Check the latest migration — is there already the needed column/table

Result:
  Found an analogue → reuse/extend
  Record: "Reused X from Y"
  Not found → create, keep the style of neighbouring files
  Record: "Created new, no analogues found"
```

### 1.2 Context Check

```
Open DEV_PROMPTS and find:
  □ ## NFR (passport) — §§ ARCHITECTURE_EXCELLENCE_PASSPORT
  □ Performance Budget — target p95 for this endpoint
  □ Failure Mode — behaviour when dependencies are unavailable
  □ Domain Checklist — business rules for the page type
  □ LPA findings — triggers from @LEAD (take into account before writing)

If NFR block is absent → universal minimum:
  - No N+1
  - No stack trace in response
  - Tenant isolation
  - Unified error format: {"detail": "...", "code": "SNAKE_CASE"}
  - Pagination for lists > 100 items
```

### 1.3 Contract Check

```
Verify against spine and passports:
  □ API contract (method, path, status codes) = spine/ARCH_MODULE?
  □ Response schema (fields, types) = Pydantic schema / DTO?
  □ tenant_id present in DB queries?
  □ Migration exists if DB schema is changing?

Discrepancy → Blocker: [what exactly] → @LEAD. Do not bypass.
```

---

## §2. ANTI-PATTERN DETECTOR

Keep active while writing code. Each one — a real error found by @QA_ARCH.

### 2.1 Database Anti-patterns

```
🔴 N+1 — query inside a loop
   Sign: for item in items: db.query(Related).filter(id=item.related_id)
   Solution: selectinload(Entity.related) / joinedload / EntityGraph
   Java: @EntityGraph(attributePaths={"related"}) or JPQL JOIN FETCH

🔴 Missing tenant filter
   Sign: SELECT * FROM bookings WHERE id = $1
   Solution: WHERE id = $1 AND tenant_id = $2

🔴 Physical deletion instead of soft delete
   Sign: session.delete(entity) / DELETE FROM table WHERE id = $1
   Solution: entity.deleted_at = datetime.utcnow()
   And: add WHERE deleted_at IS NULL to all SELECTs

🔴 Float for money
   Sign: amount: float = 99.99 / balance = 0.1 + 0.2  # = 0.30000000000000004
   Solution: amount: Decimal = Decimal("99.99") / integer minor units in DB

🔴 FOR UPDATE missing on concurrency
   Sign: SELECT balance FROM cashbox WHERE id = $1 (without FOR UPDATE)
   Solution: select(Cashbox).where(...).with_for_update()

🔴 Transaction does not cover all atomic operations
   Sign: create_transaction(); update_balance() — separately
   Solution: async with session.begin(): both operations inside

🔴 deleted_at IS NULL missing in SELECT
   Sign: query(Entity).filter(Entity.id == id)
   Solution: query(Entity).filter(Entity.id == id, Entity.deleted_at.is_(None))

🟡 SELECT * instead of explicit fields on large tables
   Sign: SELECT * FROM events (table with 10+ fields and millions of rows)
   Solution: SELECT needed fields; especially if TEXT/JSONB columns exist

🟡 No index on filtered field
   Sign: WHERE tenant_id = $1 AND status = $2 without a composite index
   Solution: CREATE INDEX ... ON table(tenant_id, status)
```

### 2.2 API / HTTP Anti-patterns

```
🔴 Arbitrary error format
   Sign: return {"error": "something went wrong"}
   Solution: raise HTTPException(404, {"detail": "Not found", "code": "NOT_FOUND"})

🔴 500 where the error is expected
   Sign: ObjectNotFound → unhandled exception → 500
   Solution: except ObjectNotFound: raise HTTPException(404, ...)

🔴 Stack trace in response
   Sign: traceback in JSON response body on production
   Solution: global exception handler in main.py; traceback → server logs only

🔴 Wrong HTTP code
   422 invalid data (not 400)
   409 conflict / duplicate (not 400)
   404 not found (not 200 with null)
   503 external service unavailable (not 500)

🔴 IDOR reveals existence of someone else's resource
   Sign: entity.tenant_id != current → return 403 Forbidden
   Solution: entity.tenant_id != current → raise HTTPException(404)
   (403 says "resource exists but you have no rights")
```

### 2.3 Frontend Anti-patterns

```
🔴 No invalidateQueries after mutation
   Sign: useMutation({ mutationFn: createBooking }) — without onSuccess
   Solution: onSuccess: () => { queryClient.invalidateQueries({queryKey: ['bookings']}) }

🔴 Button not disabled during mutation
   Sign: <Button onClick={handleSubmit}>Save</Button>
   Solution: <Button disabled={isPending} onClick={handleSubmit}>

🔴 UUID in UI
   Sign: <td>{booking.doctor_id}</td>
   Solution: <td>{booking.doctor.full_name}</td>

🔴 null instead of [] breaks .map()
   Sign: {items.map(item => ...)} when items can be null/undefined
   Solution: {(items ?? []).map(item => ...)} or guaranteed [] from server

🔴 No Loading state
   Sign: component renders data or nothing while loading
   Solution: if (isLoading) return <SkeletonTable rows={5} />

🔴 No Empty state
   Sign: {items.length === 0 && null} or an empty div
   Solution: <EmptyState icon={...} title="..." action={<Button>Create</Button>} />

🔴 No Error state
   Sign: on query error UI hangs or shows emptiness without explanation
   Solution: if (isError) return <Alert>...<Button onClick={refetch}>Retry</Button></Alert>

🟡 Promise.all with mutations
   Sign: await Promise.all([createA(), updateB()])
   Solution: await createA(); await updateB() — sequentially
             or Promise.allSettled with checking each result.status

🟡 Missing optimistic UI rollback handling
   Sign: drag-and-drop changes UI → onError does nothing
   Solution: onError → queryClient.setQueryData([...], previousData) (rollback)
```

### 2.4 Async / Background Anti-patterns

```
🔴 Blocking I/O in async function
   Sign: time.sleep(1) or requests.get(url) inside async def
   Solution: await asyncio.sleep(1) or httpx.AsyncClient

🔴 Silent exception swallowing
   Sign: try: ... except Exception: pass
   Solution: except Exception as e: logger.error("...", exc_info=True); raise (or graceful fallback)

🔴 Celery task without idempotency
   Sign: repeated run creates a duplicate record/notification
   Solution: check existence before creating + unique constraint in DB

🔴 No timeout on external call
   Sign: httpx.get(url) without timeout / requests.get(url)
   Solution: httpx.AsyncClient(timeout=httpx.Timeout(30.0, connect=5.0))

🟡 Retry without jitter — thundering herd
   Sign: retry at fixed 5, 10, 15 seconds
   Solution: 2^attempt * base + random.uniform(0, 1) * jitter_factor

🟡 No DLQ at max_retries
   Sign: task is simply deleted after max_retries
   Solution: acks_late=True + dead letter queue for analysis and replay
```

### 2.5 Security Anti-patterns

```
🔴 Secret in code
   Sign: API_KEY = "sk-abc123..." in any .py/.ts/.java file
   Solution: os.getenv("API_KEY") / process.env.API_KEY

🔴 Raw SQL with concatenation
   Sign: f"SELECT * FROM users WHERE email = '{email}'"
   Solution: parameterised queries; never f-string in SQL

🔴 No webhook signature verification
   Sign: processing webhook before verifying X-Signature / X-Hub-Signature
   Solution: verify HMAC signature as the first step of the handler

🟡 PII in logs and metrics
   Sign: logger.info(f"User {user.email}...") / labels(email=email)
   Solution: logger.info(f"User {user.id}...") / labels(user_id=str(user.id))
```

### 2.6 WebSocket / Real-time Anti-patterns

```
🔴 Memory leak on client disconnect
   Sign: connection added to registry on connect but not removed on disconnect
   Solution: try/finally in connection handler; cleanup on WebSocketDisconnect

🔴 No tenant isolation in subscription
   Sign: client subscribes to channel without checking tenant_id
   Solution: on subscribe, verify the channel belongs to the current tenant

🔴 In-process fan-out with multiple instances
   Sign: broadcast only to connections on the current instance
   Solution: Redis Pub/Sub for broadcast across instances

🟡 No reconnect logic on client
   Sign: on WS disconnection — blank screen without reconnection attempt
   Solution: exponential backoff reconnect + connection state indicator
```

### 2.7 AI/RAG Anti-patterns

```
🔴 No tenant isolation in vector search
   Sign: similarity_search(query) without filter_by(tenant_id=...)
   Solution: vector_store.search(query, filter={"tenant_id": tenant_id})

🔴 No fallback on LLM unavailability
   Sign: LLM timeout → unhandled exception → 500
   Solution: except LLMException: return graceful_fallback_response()

🔴 LLM call in synchronous user-facing path
   Sign: POST /api/message → await llm.generate() (10-60 sec) → response
   Solution: return 202 + task_id; async Celery task; polling or SSE for result

🟡 Prompt without version
   Sign: system prompt changed in code without versioning
   Solution: SYSTEM_PROMPT_V = "2.1"; prompt_version logged with the request

🟡 No empty retrieval handling
   Sign: if chunks == [] → LLM hallucinates without context
   Solution: explicit branch: if not chunks: return "Information not found"
```

---

## §3. EXIT CRITERIA — submission standard

### What "task complete" means

```
Task is NOT complete if:
  □ At least one DEV_PROMPTS item is not closed with proof
  □ Task matrix not passed for the corresponding type
  □ Level 1 tests not written and not run
  □ Open questions to @ARCH/@LEAD without an answer
  □ Stubs not explicitly marked [STUB]
```

### Proof format per item

```
✅ Item 1: endpoint POST /api/v1/bookings created
   file: src/api/v1/routers/bookings.py
   test: tests/test_bookings.py::test_create_booking — ✅ passed
   test: tests/test_bookings.py::test_create_booking_idor — ✅ passed
   matrix: Type A passed

✅ Item 2: CreateBookingDrawer form (frontend)
   file: frontend/src/components/features/bookings/CreateBookingDrawer.tsx
   matrix: Type C + Type D passed
   invalidateQueries: bookings, schedule

⚠️ Item 3: payment system integration
   status: STUB — awaiting API keys from @LEAD
   marked in code: # [STUB] — replace with real call
   not included in this phase's completion criterion
```

---

## §4. PATTERN CATALOGUE — ready solutions

### 4.1 FastAPI: full CRUD with tenant isolation

```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from app.core.database import get_session
from app.core.security import get_current_tenant_id
from app.models import Booking
from app.schemas import BookingCreate, BookingResponse
import logging

router = APIRouter(prefix="/api/v1/bookings", tags=["bookings"])
logger = logging.getLogger(__name__)


@router.get("/{booking_id}", response_model=BookingResponse)
async def get_booking(
    booking_id: str,
    session: AsyncSession = Depends(get_session),
    tenant_id: str = Depends(get_current_tenant_id),
):
    result = await session.execute(
        select(Booking).where(
            Booking.id == booking_id,
            Booking.tenant_id == tenant_id,    # tenant isolation
            Booking.deleted_at.is_(None),       # soft delete
        )
    )
    booking = result.scalar_one_or_none()
    if not booking:
        # 404 hides the fact of existence (not 403)
        raise HTTPException(404, {"detail": "Booking not found", "code": "NOT_FOUND"})
    return booking


@router.post("/", response_model=BookingResponse, status_code=201)
async def create_booking(
    data: BookingCreate,
    session: AsyncSession = Depends(get_session),
    tenant_id: str = Depends(get_current_tenant_id),
):
    booking = Booking(**data.model_dump(), tenant_id=tenant_id)
    session.add(booking)
    try:
        await session.flush()
        await session.refresh(booking)   # mandatory after add()
        logger.info(
            "Booking created",
            extra={"booking_id": str(booking.id), "tenant_id": tenant_id}
        )
        return booking
    except Exception:
        await session.rollback()
        logger.error("Failed to create booking", exc_info=True,
                     extra={"tenant_id": tenant_id})
        raise
```

### 4.2 FastAPI: financial operation with FOR UPDATE

```python
from decimal import Decimal

async def transfer_between_cashboxes(
    from_id: str, to_id: str,
    amount: Decimal, tenant_id: str,
    session: AsyncSession,
) -> None:
    """Transfer. FOR UPDATE + transaction = no race condition."""
    async with session.begin():
        # FOR UPDATE — lock rows from parallel changes
        from_box = await session.scalar(
            select(Cashbox)
            .where(Cashbox.id == from_id, Cashbox.tenant_id == tenant_id)
            .with_for_update()
        )
        to_box = await session.scalar(
            select(Cashbox)
            .where(Cashbox.id == to_id, Cashbox.tenant_id == tenant_id)
            .with_for_update()
        )

        if not from_box or not to_box:
            raise HTTPException(404, {"detail": "Cashbox not found", "code": "NOT_FOUND"})

        if from_box.balance < amount:
            raise HTTPException(409, {
                "detail": "Insufficient balance",
                "code": "INSUFFICIENT_BALANCE"
            })

        from_box.balance -= amount  # Decimal — not float
        to_box.balance += amount

        # Append-only log: INSERT, not UPDATE
        session.add(Transaction(
            from_cashbox_id=from_id, to_cashbox_id=to_id,
            amount=amount, tenant_id=tenant_id, type="transfer",
        ))
        # session.begin() auto-commits on exit without exception
```

### 4.3 React Query: mutation with full state set

```typescript
import { useMutation, useQueryClient } from '@tanstack/react-query'
import { toast } from 'sonner'
import { api } from '@/api/client'

export function useCreateBooking() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: (data: CreateBookingData) =>
      api.post<BookingResponse>('/api/v1/bookings', data),

    onSuccess: () => {
      // Invalidate ALL related keys
      queryClient.invalidateQueries({ queryKey: ['bookings'] })
      queryClient.invalidateQueries({ queryKey: ['schedule'] })
      toast.success('Booking created')
    },

    onError: (error: ApiError) => {
      const message = error?.response?.data?.detail ?? 'Failed to create booking'
      toast.error(message)
    },
  })
}

// In component:
function CreateBookingButton() {
  const { mutate, isPending } = useCreateBooking()

  return (
    // disabled={isPending} MANDATORY — protection against double-submit
    <Button disabled={isPending} onClick={() => mutate(data)}>
      {isPending ? 'Creating...' : 'Create booking'}
    </Button>
  )
}
```

### 4.4 React: full State Matrix (4 states)

```typescript
function BookingList() {
  const { data, isLoading, isError, refetch } = useQuery({
    queryKey: ['bookings'],
    queryFn: fetchBookings,
  })

  // 1. LOADING — Skeleton, not Spinner
  if (isLoading) return <SkeletonTable rows={5} />

  // 2. ERROR — with retry option
  if (isError) return (
    <Alert variant="destructive">
      <AlertDescription>
        Failed to load bookings.
        <Button variant="link" onClick={() => refetch()}>Retry</Button>
      </AlertDescription>
    </Alert>
  )

  // 3. EMPTY — with CTA (not null!)
  if (!data?.length) return (
    <EmptyState
      icon={<CalendarIcon />}
      title="No bookings"
      description="Create the first booking for the client"
      action={<Button onClick={onCreateClick}>Create booking</Button>}
    />
  )

  // 4. SUCCESS — data displayed
  return (
    <Table>
      {data.map(booking => (
        <TableRow key={booking.id}>
          {/* NEVER booking.doctor_id — human-readable only */}
          <TableCell>{booking.doctor.full_name}</TableCell>
          <TableCell>{booking.service.name}</TableCell>
          <TableCell>
            {/* Date localised, not ISO string */}
            {formatDate(booking.start_time)}
          </TableCell>
        </TableRow>
      ))}
    </Table>
  )
}
```

### 4.5 Optimistic UI with rollback on error

```typescript
function useUpdateBookingStatus() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: ({ id, status }: { id: string; status: BookingStatus }) =>
      api.patch(`/api/v1/bookings/${id}`, { status }),

    // Optimistic update — UI changes before server response
    onMutate: async ({ id, status }) => {
      await queryClient.cancelQueries({ queryKey: ['bookings'] })
      const previousData = queryClient.getQueryData(['bookings'])

      queryClient.setQueryData(['bookings'], (old: Booking[]) =>
        old.map(b => b.id === id ? { ...b, status } : b)
      )

      return { previousData } // save for rollback
    },

    // Rollback on error
    onError: (err, variables, context) => {
      if (context?.previousData) {
        queryClient.setQueryData(['bookings'], context.previousData)
      }
      toast.error('Failed to change status')
    },

    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['bookings'] })
    },
  })
}
```

### 4.6 Cursor-based pagination (not offset)

```python
# Offset pagination breaks with parallel inserts: page 2 can skip a record
# Cursor-based — stable pagination

from pydantic import BaseModel
from typing import Optional
import base64

class PaginatedResponse(BaseModel):
    items: list
    next_cursor: Optional[str] = None
    has_more: bool


@router.get("/", response_model=PaginatedResponse)
async def list_bookings(
    cursor: Optional[str] = None,   # base64 encoded last_id
    limit: int = 20,
    tenant_id: str = Depends(get_current_tenant_id),
    session: AsyncSession = Depends(get_session),
):
    query = (
        select(Booking)
        .where(
            Booking.tenant_id == tenant_id,
            Booking.deleted_at.is_(None),
        )
        .order_by(Booking.created_at.desc(), Booking.id)
        .limit(limit + 1)  # fetch one extra to determine has_more
    )

    if cursor:
        cursor_id = base64.b64decode(cursor).decode()
        query = query.where(Booking.id < cursor_id)

    result = await session.execute(query)
    items = result.scalars().all()

    has_more = len(items) > limit
    items = items[:limit]

    next_cursor = None
    if has_more and items:
        next_cursor = base64.b64encode(str(items[-1].id).encode()).decode()

    return PaginatedResponse(items=items, next_cursor=next_cursor, has_more=has_more)
```

### 4.7 Celery: idempotent task with retry

```python
from celery import shared_task
from celery.utils.log import get_task_logger
import random

logger = get_task_logger(__name__)


@shared_task(
    bind=True,
    max_retries=3,
    acks_late=True,           # acknowledge only after successful execution
    reject_on_worker_lost=True,  # retry if worker crashed mid-task
    name="notifications.send_reminder",
)
def send_reminder(self, booking_id: str, tenant_id: str) -> dict:
    """Idempotent: repeated call = skip if already sent."""
    try:
        # Idempotency check first
        if NotificationLog.exists(booking_id=booking_id, type="reminder"):
            logger.info("Already sent, skipping", extra={"booking_id": booking_id})
            return {"status": "skipped"}

        notification_service.send(booking_id=booking_id)
        NotificationLog.create(booking_id=booking_id, type="reminder")

        logger.info("Reminder sent",
                    extra={"booking_id": booking_id, "tenant_id": tenant_id})
        return {"status": "sent"}

    except ExternalServiceUnavailable as exc:
        # Exponential backoff + jitter
        delay = (2 ** self.request.retries) * 30 + random.randint(0, 15)
        raise self.retry(exc=exc, countdown=delay)

    except Exception:
        logger.error("Failed to send reminder", exc_info=True,
                     extra={"booking_id": booking_id})
        raise
```

### 4.8 Alembic: expand-deploy-contract migration

```python
"""Add subscription_id to bookings — Phase 1: expand (nullable)

Revision ID: a1b2c3d4e5f6
"""
# STRATEGY: expand → deploy → contract
# Phase 1 (this migration): add nullable field
# Phase 2 (deploy): code writes to both fields (old and new)
# Phase 3 (next migration): NOT NULL constraint + remove old field

from alembic import op
import sqlalchemy as sa


def upgrade() -> None:
    # Nullable — backward compatibility: old code will not break
    op.add_column(
        "bookings",
        sa.Column(
            "subscription_id",
            sa.UUID(),
            nullable=True,
            comment="Phase 1: nullable. Make NOT NULL in Phase 3 migration."
        )
    )
    # Index on FK — mandatory
    op.create_index("ix_bookings_subscription_id", "bookings", ["subscription_id"])


def downgrade() -> None:
    op.drop_index("ix_bookings_subscription_id", table_name="bookings")
    op.drop_column("bookings", "subscription_id")
```

### 4.9 FastAPI: external API with circuit breaker

```python
import httpx
from tenacity import (
    retry, stop_after_attempt,
    wait_exponential, retry_if_exception_type,
    CircuitBreaker
)

# Circuit breaker: opens after 5 errors, closes after 60 sec
circuit_breaker = CircuitBreaker(
    failure_threshold=5,
    recovery_timeout=60,
    expected_exception=httpx.HTTPStatusError,
)


@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=2, max=10),
    retry=retry_if_exception_type((httpx.TimeoutException, httpx.ConnectError)),
    reraise=True,
)
async def call_external_api(
    payload: dict,
    idempotency_key: str,
    tenant_id: str,
) -> dict:
    async with httpx.AsyncClient(
        timeout=httpx.Timeout(30.0, connect=5.0)  # separate timeouts
    ) as client:
        try:
            response = await client.post(
                f"{settings.EXTERNAL_API_URL}/endpoint",
                json=payload,
                headers={
                    "Idempotency-Key": idempotency_key,
                    "Authorization": f"Bearer {settings.EXTERNAL_API_KEY}",
                    # DO NOT log Authorization header
                },
            )
            response.raise_for_status()
            return response.json()

        except httpx.TimeoutException:
            logger.error("External API timeout",
                         extra={"tenant_id": tenant_id, "key": idempotency_key})
            raise HTTPException(503, {
                "detail": "Service temporarily unavailable",
                "code": "EXTERNAL_TIMEOUT"
            })

        except httpx.HTTPStatusError as e:
            logger.error(
                f"External API error: {e.response.status_code}",
                extra={"tenant_id": tenant_id}
                # DO NOT include headers (they contain keys)
            )
            raise
```

### 4.10 WebSocket: tenant-safe real-time with Redis Pub/Sub

```python
from fastapi import WebSocket, WebSocketDisconnect, Depends
import redis.asyncio as aioredis
import asyncio, json

class ConnectionRegistry:
    """Thread-safe registry of WebSocket connections."""
    def __init__(self):
        self._connections: dict[str, set[WebSocket]] = {}

    async def connect(self, tenant_id: str, ws: WebSocket):
        await ws.accept()
        self._connections.setdefault(tenant_id, set()).add(ws)

    async def disconnect(self, tenant_id: str, ws: WebSocket):
        if tenant_id in self._connections:
            self._connections[tenant_id].discard(ws)

    async def broadcast_to_tenant(self, tenant_id: str, message: dict):
        dead = set()
        for ws in self._connections.get(tenant_id, set()):
            try:
                await ws.send_json(message)
            except Exception:
                dead.add(ws)
        # Remove dead connections
        for ws in dead:
            self._connections[tenant_id].discard(ws)


registry = ConnectionRegistry()


@router.websocket("/ws")
async def websocket_endpoint(
    ws: WebSocket,
    tenant_id: str = Depends(get_current_tenant_id_from_ws),
):
    await registry.connect(tenant_id, ws)
    # Redis Pub/Sub for cross-instance broadcast
    redis = aioredis.from_url(settings.REDIS_URL)
    pubsub = redis.pubsub()
    await pubsub.subscribe(f"tenant:{tenant_id}:events")

    try:
        # Listen to Redis and WS simultaneously
        async def listen_redis():
            async for msg in pubsub.listen():
                if msg["type"] == "message":
                    data = json.loads(msg["data"])
                    await registry.broadcast_to_tenant(tenant_id, data)

        await asyncio.gather(
            listen_redis(),
            ws.receive_text(),  # keep connection alive
        )
    except WebSocketDisconnect:
        pass  # normal disconnection
    finally:
        # MANDATORY: cleanup on any outcome
        await registry.disconnect(tenant_id, ws)
        await pubsub.unsubscribe(f"tenant:{tenant_id}:events")
        await redis.aclose()
```

### 4.11 Spring Boot: correct relationship loading (no N+1)

```java
// ❌ N+1 — never do this
List<Booking> bookings = bookingRepository.findAll();
bookings.forEach(b -> b.getDoctor().getFullName()); // N SQL queries

// ✅ EntityGraph — one SQL with JOIN
@EntityGraph(attributePaths = {"doctor", "service", "patient"})
List<Booking> findAllByTenantId(String tenantId);

// ✅ JPQL with JOIN FETCH — explicit control
@Query("""
    SELECT b FROM Booking b
    LEFT JOIN FETCH b.doctor
    LEFT JOIN FETCH b.service
    WHERE b.tenantId = :tenantId
      AND b.deletedAt IS NULL
    ORDER BY b.startTime DESC
    """)
List<Booking> findWithRelations(@Param("tenantId") String tenantId);

// ✅ For collections — @BatchSize (N queries → ceil(N/size) queries)
@OneToMany(mappedBy = "booking", fetch = FetchType.LAZY)
@BatchSize(size = 30)
private List<BookingNote> notes;
```

---

## §5. CODE REVIEW SELF-CHECK

Final pass before submission. This is what @QA_ARCH will check first.

```
BACKEND
□ All endpoints from DEV_PROMPTS implemented and match the contract
□ Tenant isolation: tenant_id in EVERY DB query
□ Soft delete: deleted_at IS NULL in EVERY SELECT
□ No N+1 (verify via SQL logs or EXPLAIN)
□ Unified error format: {"detail": "...", "code": "SNAKE_CASE"}
□ No stack trace in response body
□ Transactions: atomic operations in one begin/commit
□ FOR UPDATE on competing resources
□ Decimal for money (no float)
□ Logging: INFO on success with tenant_id; ERROR with traceback
□ No PII/secrets in logs

FRONTEND
□ Loading: Skeleton for every async component
□ Empty: EmptyState with CTA for every list
□ Error: Alert/Toast with "Retry" button
□ Success: invalidateQueries + form reset + Drawer/Modal closed
□ disabled={isPending} on mutation buttons
□ UUID nowhere in UI — human-readable only
□ null checked before .map() — no runtime crash
□ Long text: truncate + title={full text}

MIGRATIONS
□ upgrade() and downgrade() implemented (or no-op with justification)
□ Indexes on all new FK columns
□ Expand-deploy-contract observed for schema changes
□ alembic upgrade head without errors

TESTS (minimum)
□ Happy path passes
□ Not found: 404 on non-existent ID
□ IDOR: ID from another tenant → 404
□ Validation: invalid data → 422
□ (Finance) Concurrent: no double charge
□ (Finance) Idempotency: repeated call → same result

STUBS
□ All stubs explicitly marked [STUB] in code
□ Open stubs listed in the report
□ Stubs not included in the phase completion criterion
```

---

## §6. PERFORMANCE DIAGNOSTICS (locally)

How to check performance before sending to @QA_ARCH.

### N+1 check via SQL logs

```python
# In settings for development — enable SQL logging
SQLALCHEMY_ECHO = True  # or logging for sqlalchemy.engine

# Sign of N+1: after one query follow N identical ones
# [SELECT * FROM bookings WHERE tenant_id = ...]
# [SELECT * FROM doctors WHERE id = 1]
# [SELECT * FROM doctors WHERE id = 2]
# [SELECT * FROM doctors WHERE id = 3]
# → this is N+1, selectinload is needed
```

### Quick EXPLAIN ANALYZE

```sql
-- Run in psql or pgAdmin on a slow query
EXPLAIN ANALYZE
SELECT b.id, b.start_time, d.full_name
FROM bookings b
JOIN doctors d ON b.doctor_id = d.id
WHERE b.tenant_id = 'xxx' AND b.deleted_at IS NULL
ORDER BY b.start_time DESC
LIMIT 20;

-- Look for: Seq Scan (bad on large tables) vs Index Scan (good)
-- Cost > 1000 on a simple query → an index is needed
```

### Local latency check

```python
# Quick check of endpoint execution time
import time

@router.get("/")
async def list_bookings(...):
    start = time.perf_counter()
    result = await service.list(tenant_id)
    elapsed = (time.perf_counter() - start) * 1000
    if elapsed > 200:  # > 200ms — warning
        logger.warning(f"Slow endpoint: {elapsed:.0f}ms", extra={"path": "/bookings"})
    return result
```

### React Query — checking unnecessary re-renders

```typescript
// In development mode: React DevTools Profiler
// Sign of a problem: component renders > 3 times on one action
// Solution: useMemo/useCallback for stable data references

// Check stale queries: open React Query DevTools
// Status "stale" immediately after loading → staleTime is too small
useQuery({
  queryKey: ['bookings'],
  queryFn: fetchBookings,
  staleTime: 30_000,  // 30 sec — do not refresh on every focus
})
```

---

## §7. MOBILE PATTERNS (Swift / Kotlin / React Native)

### 7.1 Offline-first with sync on network restoration

```typescript
// React Native — offline queue pattern
import NetInfo from '@react-native-community/netinfo'
import AsyncStorage from '@react-native-async-storage/async-storage'

const OFFLINE_QUEUE_KEY = 'offline_mutations_queue'

export async function enqueueOfflineMutation(mutation: PendingMutation) {
  const queue = await getOfflineQueue()
  queue.push({ ...mutation, enqueuedAt: Date.now() })
  await AsyncStorage.setItem(OFFLINE_QUEUE_KEY, JSON.stringify(queue))
}

export async function flushOfflineQueue() {
  const queue = await getOfflineQueue()
  for (const mutation of queue) {
    try {
      await executeMutation(mutation)
    } catch (e) {
      // Do not remove from queue on server error — only on client error
      if (e.response?.status < 500) {
        removeFromQueue(mutation.id)
      }
    }
  }
}

// Subscribe to network change
NetInfo.addEventListener(state => {
  if (state.isConnected) flushOfflineQueue()
})
```

### 7.2 Secure token storage (React Native)

```typescript
import * as SecureStore from 'expo-secure-store'

// NEVER store tokens in AsyncStorage (not encrypted)
const TOKEN_KEY = 'auth_access_token'
const REFRESH_KEY = 'auth_refresh_token'

export const tokenStorage = {
  async setAccessToken(token: string) {
    await SecureStore.setItemAsync(TOKEN_KEY, token)
  },
  async getAccessToken(): Promise<string | null> {
    return SecureStore.getItemAsync(TOKEN_KEY)
  },
  async clear() {
    await SecureStore.deleteItemAsync(TOKEN_KEY)
    await SecureStore.deleteItemAsync(REFRESH_KEY)
  },
}
```

### 7.3 API versioning for mobile clients

```typescript
// Every request includes app version
// Server can reject too-old versions (426 Upgrade Required)
const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'X-App-Version': APP_VERSION,        // "2.4.1"
    'X-Platform': Platform.OS,           // "ios" / "android"
    'X-App-Build': APP_BUILD_NUMBER,     // "241"
  },
})

// Force update handling
apiClient.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 426) {
      // Force update
      showForceUpdateModal()
    }
    return Promise.reject(error)
  }
)
```

---

## §8. AI/RAG PATTERNS

### 8.1 RAG pipeline with tenant isolation and fallback

```python
from langchain_core.prompts import ChatPromptTemplate

async def generate_response(
    query: str,
    tenant_id: str,
    session_id: str,
) -> AsyncGenerator[str, None]:
    """RAG with streaming, tenant isolation, token budget, fallback."""

    # 1. Retrieval with tenant isolation (Pillar X1 ROLE_AI_ENGINEER)
    chunks = await vector_store.search(
        query=query,
        filter={"tenant_id": tenant_id},  # MANDATORY
        k=5,
    )

    # 2. Empty retrieval — explicit branch (do not hallucinate)
    if not chunks:
        yield "Information for your query was not found in the knowledge base."
        return

    # 3. Token budget check
    context = "\n".join([c.page_content for c in chunks])
    if count_tokens(context + query) > MAX_CONTEXT_TOKENS:
        context = truncate_to_budget(context, MAX_CONTEXT_TOKENS - count_tokens(query))

    # 4. Streaming generation with fallback
    try:
        async for chunk in llm.astream(
            prompt.format(context=context, query=query),
            timeout=60.0,  # explicit timeout
        ):
            yield chunk.content

    except LLMProviderUnavailable:
        logger.error("LLM unavailable",
                     extra={"tenant_id": tenant_id, "session_id": session_id})
        yield "Service temporarily unavailable. Please try again later."

    except LLMTimeout:
        logger.warning("LLM timeout",
                       extra={"tenant_id": tenant_id, "latency_ms": 60000})
        yield "Response timeout exceeded. Please try a shorter query."
```

### 8.2 Structured output with validation

```python
from pydantic import BaseModel, validator
from langchain_core.output_parsers import PydanticOutputParser

class ExtractedData(BaseModel):
    name: str
    amount: Decimal
    category: str

    @validator('amount')
    def amount_must_be_positive(cls, v):
        if v <= 0:
            raise ValueError('Amount must be positive')
        return v


parser = PydanticOutputParser(pydantic_object=ExtractedData)

async def extract_structured(text: str, tenant_id: str) -> ExtractedData:
    try:
        response = await llm.ainvoke(
            prompt.format(text=text, format_instructions=parser.get_format_instructions())
        )
        return parser.parse(response.content)

    except Exception:
        logger.error("Failed to parse LLM output",
                     extra={"tenant_id": tenant_id})
        # DO NOT pass unparsed response further — only after validation
        raise HTTPException(422, {
            "detail": "Failed to extract data from text",
            "code": "LLM_PARSE_ERROR"
        })
```

---

## §9. REFLEX-LINK (living passport)

The passport is updated on every new error found by @QA_ARCH.

```
⚡ REFLEX @DEV — passport update:

Error found:      [what @QA_ARCH found — specifically]
Class:            [A-omission / B-logic / C-quality / D-context]
Why it happened:  [no checkpoint / unclear contract / layer switch]
How to reproduce: [minimal code demonstrating the error]
Add to passport:  [to §2 Anti-pattern with sign and solution]
                  [or to §4 Catalogue with a correct example]
```

After @LEAD approval — @DEV updates the relevant section of this file.
Goal: no error should occur twice.

---

Reference: roles/ROLE_DEV.md · roles/ROLE_QA_ARCH.md · roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md · roles/DOMAIN_STANDARDS.md · roles/MIGRATIONS_PLAYBOOK.md · roles/METRICS_PROTOCOL.md · roles/CACHE_STRATEGY.md · roles/STACK_SELECTION.md · roles/ROLE_AI_ENGINEER.md · roles/SYSTEM_DESIGN_PROTOCOL.md
Version: 2.0 | 2026-05-22
