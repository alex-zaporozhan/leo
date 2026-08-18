# RAG ARCHITECTURE STACK 2026
> Full technical stack, pipelines, quality evaluation and UI management
> Version: 1.2 | Date: 27.04.2026 | Status: CANON

---

## TABLE OF CONTENTS

0. [Binding to a multi-tenant RAG product](#0-binding-to-a-multi-tenant-rag-product)
1. [Key concepts and RAG evolution](#1-key-concepts-and-rag-evolution)
2. [Technology stack — winner selection](#2-technology-stack--winner-selection)
3. [Ingestion Pipeline](#3-ingestion-pipeline)
4. [Retrieval Pipeline](#4-retrieval-pipeline)
5. [Generation and post-processing](#5-generation-and-post-processing)
6. [RAG quality evaluation](#6-rag-quality-evaluation)
7. [Filtering, security and control](#7-filtering-security-and-control)
8. [Observability and monitoring](#8-observability-and-monitoring)
9. [UI for RAG system management](#9-ui-for-rag-system-management)
10. [Architectural patterns and anti-patterns](#10-architectural-patterns-and-anti-patterns)
11. [Reference: API contracts and DB schema](#11-reference-api-contracts-and-db-schema)

---

## 0. BINDING TO A MULTI-TENANT RAG PRODUCT

This document describes a universal RAG stack. When the product is multi-tenant, the additional rules of section **§0** apply; the product requirements and organisation model live in the project's own knowledge layer (`docs/knowledge/` or `docs/[project]/`), not in this canon.

### 0.1 Isolation by client organisation

- Every knowledge artifact (file, collection, chunk, ingestion job, RAG query record) **must** contain a client organisation identifier: **`organization_id`** in the product requirements terms, or the equivalent **`tenant_id`** in SQL/vector — the same partitioning key. Queries to the vector store and SQL **always** filter by this key; absence of a filter in code is a security defect at blocker level.
- Cross-organisation retrieval is forbidden. Hybrid search, rerank, and cache keys include the organisation identifier.
- An **Administrator** account (content preparation per product requirements) does not cancel filtering of end-user data by organisation; access to metadata of multiple clients if necessary — only through explicit admin scenarios with logging.

### 0.2 Ingestion volumes and SLA (aligned with client brief)

Benchmark per tenant: **20–100** files, **100–500 MB** text, **10–30** sections; updates from several times a month to weekly; **re-indexing within 24 hours** on a schedule + **manual trigger** for urgent changes.

The pipeline of §3–§4 of this document is considered the **implementation canon**; chunk_size, overlap, and embedding model parameters are fixed in ADR and the collection config, not "by eye" in code.

### 0.3 Data minimisation to external APIs

Agreed with the client: **only the minimum necessary** scenario text is sent to external LLM/embeddings; PII and unnecessary identifiers are masked or stripped **before** the call. For the RF contour if required — local embeddings and/or on-prem enrichment (see §7).

### 0.4 RAG quality and acceptance in the product

- **Faithfulness / grounding:** lesson/test/hint responses are not considered ready without citation verification where the requirements mandate reliance on the knowledge base (see §6 evaluation, minimum gate before prompt promotion).
- **Observability:** latency by stage, share of empty retrieval, document parsing errors — mandatory metrics for operations (§8).

### 0.5 Connection with §11 schema

Section **§11.4** below supplements the SQL example with mandatory **`tenant_id`** fields and explains the mapping to product roles.

---

## 1. KEY CONCEPTS AND RAG EVOLUTION

### 1.1 RAG generations

```
RAG 1.0 (2022–2023) — Naive RAG
  Embed → Store → Retrieve top-K → Generate
  Problem: low precision, no context control, no evaluation

RAG 2.0 (2024) — Advanced RAG
  + Query transformation, hybrid search, reranking
  + Chunk optimization, metadata filtering
  Problem: no feedback loop, retrieval is a black box

RAG 3.0 (2025–2026) — Agentic RAG
  + Multi-hop reasoning, self-correction, tool use
  + Dynamic knowledge graph traversal
  + Real-time streaming evaluation
  + Unified pipeline with LLM-as-judge
  Problem: orchestration complexity, latency
```

### 1.2 System architecture map

```
┌──────────────────────────────────────────────────────────────────┐
│                        RAG SYSTEM 2026                           │
├─────────────────┬────────────────────┬───────────────────────────┤
│  INGESTION      │    RETRIEVAL       │    GENERATION             │
│                 │                    │                           │
│ Documents →     │ Query →            │ Context + Query →         │
│ Parse →         │ Transform →        │ LLM →                     │
│ Chunk →         │ Embed + Sparse →   │ Stream →                  │
│ Enrich →        │ Vector Search →    │ Evaluate →                │
│ Embed →         │ Rerank →           │ Cite →                    │
│ Index           │ Filter →           │ Response                  │
│                 │ Context Assemble   │                           │
├─────────────────┴────────────────────┴───────────────────────────┤
│                     OBSERVABILITY LAYER                          │
│  Tracing · Metrics · Evaluation · Feedback · Drift Detection     │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. TECHNOLOGY STACK — WINNER SELECTION

### 2.1 LLM (language models)

| Layer | Winner 2026 | Alternative | When alternative |
|-------|------------|-------------|-----------------|
| Generation (cloud) | **Claude claude-sonnet-4-20250514** | GPT-4o, Gemini 2.5 Pro | If Google Workspace needed |
| Generation (self-hosted) | **Mistral Large 2** (VLLM) | LLaMA 3.3 70B | Strict data residency requirements |
| Reranking LLM | **Cohere Rerank 3.5** | BGE Reranker v2-m3 | Budget / latency |
| Embedding (cloud) | **text-embedding-3-large** (OpenAI) | voyage-3 (Anthropic) | Best for code |
| Embedding (self-hosted) | **BGE-M3** | nomic-embed-text-v2 | Multilingual content |
| Judge LLM | **Claude claude-opus-4-20250514** | GPT-4o | Response quality evaluation |

**Rule:** for production never rely on a single LLM provider. Minimum two — primary + fallback.

### 2.2 Vector Store

| Product | Use Case | Strengths | Weaknesses |
|---------|---------|--------|-------|
| **Qdrant** ⭐ | Production, self-hosted | Speed, filters, payload | Fewer managed options |
| **Weaviate** | Multi-modal, hybrid | GraphQL, modules | More complex deploy |
| **pgvector + pgai** | Existing Postgres | Zero overhead, SQL | Scale >10M vectors |
| Pinecone | Managed, serverless | Simplicity | Expensive at scale |
| Chroma | Dev / prototypes | Simplicity | Not for production |
| Milvus | Large scale >100M | Sharding | Over-engineering for <50M |

**Winner for most projects: Qdrant** (self-hosted on K8s) or **pgvector** (if PostgreSQL already exists).

### 2.3 Document Parsing

| Content type | Winner | Why |
|-------------|-----------|--------|
| PDF (complex layout) | **Docling** (IBM) | Layout-aware, tables, formulas |
| PDF (simple) | **pdfplumber** | Fast, reliable |
| DOCX / PPTX | **python-docx + Docling** | Structure + metadata |
| HTML / Web | **Trafilatura** | Best boilerplate removal |
| Markdown | **mistune** + custom | Native |
| Images in doc | **GPT-4o Vision / Claude** | LLM as parser |
| Tables | **Camelot** + **Docling** | Structure → JSON |
| Code | **tree-sitter** | AST-aware chunking |

### 2.4 Orchestration and frameworks

```
2026 Stack:

Query orchestrator:    LangGraph (StateGraph) — best for agentic RAG
Alternative:           LlamaIndex Workflows — better for document-heavy
Pipeline as code:      Haystack 2.x — production-grade, enterprise
Async execution:       Celery + Redis (ingestion) / asyncio (retrieval)
Serving:               FastAPI + uvicorn (uvloop)
```

**Why LangGraph and not LangChain:** LangChain — too many abstractions, hard to debug. LangGraph provides a state graph with explicit transitions — exactly what is needed for agentic RAG with self-correction.

### 2.5 Infrastructure

```
Container:    Docker + Kubernetes (production) / Docker Compose (dev)
CI/CD:        Jenkins + GHCR (primary) / GitHub Actions (PR-gates)
Cache:        Redis Stack (vector cache + semantic cache)
Queue:        Redis Streams (lightweight) / Kafka (>10k docs/day)
Storage:      MinIO (self-hosted S3) / AWS S3
DB:           PostgreSQL 17 (metadata, jobs, audit)
Search:       OpenSearch (if BM25 needed separately) or Qdrant hybrid
Tracing:      OpenTelemetry → Jaeger / Grafana Tempo
Metrics:      Prometheus + Grafana
Logs:         Loki + Grafana
```

---

## 3. INGESTION PIPELINE

### 3.1 Full diagram

```
Source Documents
      │
      ▼
┌─────────────┐
│  CONNECTOR  │  File upload / S3 / SharePoint / URL / API
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   PARSER    │  Docling / pdfplumber / trafilatura
│             │  → raw text + structure + images + tables
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  CLEANER    │  Remove boilerplate, fix encoding,
│             │  normalize whitespace, deduplicate
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  CHUNKER    │  Semantic / Hierarchical / Token-based
│             │  → chunks[] with overlap
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  ENRICHER   │  + metadata, + summary, + hypothetical Q,
│             │  + keywords, + entity extraction
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  EMBEDDER   │  text-embedding-3-large / BGE-M3
│             │  → dense vector (3072d / 1024d)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  INDEXER    │  Qdrant upsert (vector + payload)
│             │  + BM25 index (sparse)
└─────────────┘
```

### 3.2 Chunking strategies

**Fixed-size chunking** — outdated. Do not use in production.

**Semantic chunking** — winner for text:
```python
# Using sentence-transformers to detect semantic boundaries
# Split where cosine similarity between adjacent sentences drops
from langchain_experimental.text_splitter import SemanticChunker
from langchain_openai import OpenAIEmbeddings

chunker = SemanticChunker(
    OpenAIEmbeddings(model="text-embedding-3-large"),
    breakpoint_threshold_type="percentile",   # or "standard_deviation"
    breakpoint_threshold_amount=95,
)
```

**Hierarchical (Parent-Child) chunking** — best for long documents:
```
Document
  └── Section (large chunk: 1024-2048 tokens) — retrieval context
        └── Paragraph (small chunk: 128-256 tokens) — retrieval unit
              └── Sentence — for precise citation
```

How it works:
- Index **small chunks** (precise search)
- On hits, fetch the **parent chunk** (full context)
- LLM receives wide context, but search is precise

**Code-aware chunking:**
```python
# tree-sitter for AST-based code splitting
# Split by functions/classes, not by tokens
from tree_sitter import Language, Parser
# Preserve the full function context as a single chunk
```

### 3.3 Enrichment (chunk enrichment)

Each chunk must contain:

```json
{
  "id": "uuid",
  "text": "original chunk text",
  "vector": [/* dense embedding */],
  "sparse_vector": {/* BM25 sparse */},
  "metadata": {
    "source": "file.pdf",
    "page": 3,
    "section": "Introduction",
    "doc_type": "technical",
    "created_at": "2026-01-15",
    "chunk_index": 7,
    "parent_id": "section-uuid",
    "language": "en"
  },
  "enrichments": {
    "summary": "Brief summary of this chunk (LLM-generated)",
    "hypothetical_questions": [
      "What question could this text answer?"
    ],
    "keywords": ["term1", "term2"],
    "entities": [{"name": "OpenAI", "type": "ORG"}],
    "quality_score": 0.87
  }
}
```

**Hypothetical Document Embeddings (HyDE):** generate hypothetical questions for each chunk and embed them. On query: query → hypothetical document → search. Increases recall by 15-25%.

### 3.4 Deduplication

```python
# MinHash LSH for near-duplicate detection
from datasketch import MinHash, MinHashLSH

lsh = MinHashLSH(threshold=0.85, num_perm=128)

def is_duplicate(text: str, doc_id: str) -> bool:
    m = MinHash(num_perm=128)
    for word in text.lower().split():
        m.update(word.encode('utf8'))
    
    result = lsh.query(m)
    if result:
        return True
    lsh.insert(doc_id, m)
    return False
```

---

## 4. RETRIEVAL PIPELINE

### 4.1 Full diagram

```
User Query
    │
    ▼
┌──────────────────┐
│ QUERY ANALYSIS   │  Intent detection, language detection,
│                  │  complexity scoring (simple/multi-hop)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ QUERY TRANSFORM  │  Rewrite + HyDE + Multi-query expansion
└────────┬─────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌───────┐ ┌───────┐
│DENSE  │ │SPARSE │   Hybrid Search
│SEARCH │ │ BM25  │
└───┬───┘ └───┬───┘
    │         │
    └────┬────┘
         │ Reciprocal Rank Fusion (RRF)
         ▼
┌──────────────────┐
│   RERANKER       │  Cohere Rerank 3.5 / BGE Reranker
│                  │  Cross-encoder scoring
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ CONTEXT FILTER   │  Relevance threshold, diversity filter,
│                  │  MMR (Max Marginal Relevance)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ CONTEXT ASSEMBLY │  Parent chunk expansion, dedup,
│                  │  token budget management
└────────┬─────────┘
         │
         ▼
      Prompt + Context → LLM
```

### 4.2 Query Transformation

**Multi-query expansion:**
```python
async def expand_query(query: str, llm) -> list[str]:
    """Generate 3-5 rephrasings of the query"""
    prompt = f"""Generate 4 different phrasings of this query for document search.
Return ONLY a JSON array of strings.
Original: {query}"""
    
    result = await llm.ainvoke(prompt)
    queries = json.loads(result.content)
    return [query] + queries  # original + expansions
```

**Step-back prompting (for complex queries):**
```
Query: "Why did the API return 403 when using an OAuth token?"
Step-back: "How does OAuth authorisation work?" → search for the general doctrine
Then: search for the specific answer
Combine both results
```

**HyDE (Hypothetical Document Embeddings):**
```python
async def hyde_search(query: str, llm, vectorstore):
    # Generate a hypothetical answer
    hyp_doc = await llm.ainvoke(
        f"Write a passage that would answer: {query}"
    )
    # Embed the hypothetical document (not the query!)
    hyp_vector = await embedder.embed(hyp_doc.content)
    # Search for similar real documents
    return await vectorstore.search(hyp_vector, top_k=20)
```

### 4.3 Hybrid Search + RRF

```python
from qdrant_client import QdrantClient, models

async def hybrid_search(
    query: str,
    dense_vector: list[float],
    sparse_vector: dict,  # BM25
    top_k: int = 20,
    filters: dict | None = None
) -> list[ScoredPoint]:
    
    results = client.query_points(
        collection_name="documents",
        prefetch=[
            # Dense vector search
            models.Prefetch(
                query=dense_vector,
                using="dense",
                limit=top_k,
            ),
            # Sparse BM25 search
            models.Prefetch(
                query=models.SparseVector(
                    indices=sparse_vector["indices"],
                    values=sparse_vector["values"]
                ),
                using="sparse",
                limit=top_k,
            ),
        ],
        # RRF fusion
        query=models.FusionQuery(fusion=models.Fusion.RRF),
        limit=top_k,
        query_filter=models.Filter(**filters) if filters else None,
        with_payload=True,
    )
    return results.points
```

### 4.4 Reranking

```python
import cohere

co = cohere.AsyncClient(api_key=COHERE_API_KEY)

async def rerank(query: str, documents: list[str], top_n: int = 5):
    response = await co.rerank(
        model="rerank-v3.5",
        query=query,
        documents=documents,
        top_n=top_n,
        return_documents=True,
    )
    return response.results

# Threshold: discard documents with relevance_score < 0.3
RERANK_THRESHOLD = 0.3
```

### 4.5 Semantic Cache

```python
# Cache search results by semantic similarity of queries
import redis
from redisvl.extensions.llmcache import SemanticCache

cache = SemanticCache(
    name="rag_cache",
    redis_url=REDIS_URL,
    distance_threshold=0.1,   # queries with similarity >90% = cache hit
    ttl=3600,                 # 1 hour
)

async def cached_search(query: str):
    cached = cache.check(prompt=query)
    if cached:
        return cached[0]["response"]
    
    result = await run_retrieval(query)
    cache.store(prompt=query, response=result)
    return result
```

### 4.6 Multi-hop (Agentic) Retrieval

For queries requiring multiple search steps:

```python
from langgraph.graph import StateGraph, END

class RAGState(TypedDict):
    query: str
    search_queries: list[str]
    retrieved_docs: list[Document]
    answer: str
    iterations: int
    sufficient: bool

def should_continue(state: RAGState) -> str:
    if state["sufficient"] or state["iterations"] >= 3:
        return "generate"
    return "search"

graph = StateGraph(RAGState)
graph.add_node("analyze", analyze_query)
graph.add_node("search", search_documents)
graph.add_node("evaluate_sufficiency", check_if_enough_context)
graph.add_node("generate", generate_answer)

graph.add_conditional_edges("evaluate_sufficiency", should_continue)
# ... compile and run
```

---

## 5. GENERATION AND POST-PROCESSING

### 5.1 Prompt Engineering for RAG

**System prompt (template):**
```
You are a precise assistant. Answer ONLY based on the provided context.
Rules:
1. If the context doesn't contain the answer, say "I don't have this information in the provided documents"
2. Always cite the source document for each claim using [Source: {doc_name}, page {N}]
3. Never add information from your training data
4. If multiple sources contradict each other, acknowledge the conflict

Context:
{context}

Current date: {date}
```

**Context assembly — order matters:**
```
[MOST RELEVANT] Chunk with highest rerank score → first
[SUPPORTING]    Related chunks → in the middle
[BACKGROUND]    General context → last

Reason: LLM uses information at the start and end of context better
("Lost in the Middle" problem — the middle is ignored)
```

### 5.2 Streaming Response

```python
async def stream_rag_response(query: str, context: list[str]):
    prompt = build_prompt(query, context)
    
    async with anthropic_client.messages.stream(
        model="claude-sonnet-4-20250514",
        max_tokens=2048,
        messages=[{"role": "user", "content": prompt}]
    ) as stream:
        async for text in stream.text_stream:
            yield text
        
        # After streaming — final evaluation
        final_message = await stream.get_final_message()
        yield evaluate_response(final_message)
```

### 5.3 Citation Extraction

```python
import re

def extract_citations(response: str, chunks: list[Document]) -> dict:
    """Parse citations and verify they exist in the sources"""
    citations = re.findall(r'\[Source: ([^,\]]+)(?:, page (\d+))?\]', response)
    
    verified = []
    for source_name, page in citations:
        matching_chunk = next(
            (c for c in chunks if source_name in c.metadata.get("source", "")),
            None
        )
        verified.append({
            "source": source_name,
            "page": int(page) if page else None,
            "verified": matching_chunk is not None,
            "chunk_id": matching_chunk.id if matching_chunk else None
        })
    
    return {"citations": verified, "hallucination_risk": not all(c["verified"] for c in verified)}
```

---

## 6. RAG QUALITY EVALUATION

### 6.1 Metrics framework

Three evaluation axes:

```
RETRIEVAL QUALITY          GENERATION QUALITY         SYSTEM QUALITY
──────────────────         ──────────────────         ──────────────
Context Recall             Answer Faithfulness        Latency P50/P95/P99
Context Precision          Answer Relevance           Throughput (QPS)
NDCG@K                     Completeness               Cost per query
MRR (Mean Reciprocal Rank) Hallucination Rate         Cache hit rate
Hit Rate                   Citation Accuracy          Error rate
```

### 6.2 RAGAS Framework (de facto standard)

```python
from ragas import evaluate
from ragas.metrics import (
    faithfulness,           # How faithful the answer is to context (0-1)
    answer_relevancy,       # How relevant the answer is to the question (0-1)
    context_recall,         # How much the context covers the answer (0-1)
    context_precision,      # Share of relevant chunks in context (0-1)
    answer_correctness,     # Factual correctness (needs ground truth)
)
from datasets import Dataset

# Evaluation dataset structure
eval_data = {
    "question": ["What is RAG?", ...],
    "answer": ["RAG is...", ...],              # system response
    "contexts": [["context1", ...], ...],     # retrieved chunks
    "ground_truth": ["correct answer", ...],  # optional
}

dataset = Dataset.from_dict(eval_data)
result = evaluate(
    dataset=dataset,
    metrics=[faithfulness, answer_relevancy, context_recall, context_precision],
    llm=judge_llm,
    embeddings=embedder,
)
print(result)
# faithfulness: 0.87, answer_relevancy: 0.91, context_recall: 0.79
```

### 6.3 LLM-as-Judge (detailed evaluation)

```python
JUDGE_PROMPT = """
You are evaluating a RAG system response. Score each dimension 1-5.

Question: {question}
Retrieved Context: {context}
System Answer: {answer}
Reference Answer: {ground_truth}

Evaluate:
1. FAITHFULNESS (1-5): Is every claim in the answer supported by the context?
   - 5: All claims directly supported
   - 3: Most claims supported, minor extrapolations
   - 1: Significant hallucinations

2. RELEVANCE (1-5): Does the answer address the question?
   - 5: Directly and completely answers
   - 3: Partially answers
   - 1: Off-topic

3. COMPLETENESS (1-5): Is important information missing?
   - 5: Nothing important omitted
   - 3: Some gaps
   - 1: Major information missing

Return JSON: {{"faithfulness": N, "relevance": N, "completeness": N, "reasoning": "..."}}
"""

async def judge_response(question, context, answer, ground_truth=None):
    response = await claude_opus.ainvoke(
        JUDGE_PROMPT.format(
            question=question,
            context=context[:3000],
            answer=answer,
            ground_truth=ground_truth or "Not provided"
        )
    )
    return json.loads(response.content)
```

### 6.4 Offline Evaluation Pipeline

```
Test dataset (golden set):
  ├── 100-500 questions with correct answers
  ├── Covers all query types (simple / multi-hop / ambiguous)
  └── Updated when new documents are added

Automatic run (CI/CD):
  ├── On every embedding model change
  ├── On chunk size or strategy change
  ├── On top-K or reranker change
  └── Result → regression gate (quality has not degraded)

Regression metrics:
  ├── faithfulness >= 0.85 (blocker)
  ├── context_recall >= 0.75 (blocker)
  └── answer_relevancy >= 0.80 (warning)
```

### 6.5 Online Evaluation (production)

```python
# Sample 5% of production queries for evaluation
# Evaluate asynchronously, do not block user response

async def async_evaluate_sample(query_id: str, query: str, response: str, contexts: list):
    if random.random() > 0.05:  # 5% sampling
        return
    
    scores = await judge_response(query, contexts, response)
    
    await metrics_client.record({
        "query_id": query_id,
        "faithfulness": scores["faithfulness"],
        "relevance": scores["relevance"],
        "timestamp": datetime.utcnow().isoformat(),
    })
    
    # If quality degraded — alert
    if scores["faithfulness"] < 3:
        await alert_manager.trigger("low_faithfulness", query_id=query_id)
```

### 6.6 Human feedback (RLHF-light)

```
UI widget (embeds in any chat):
  👍 / 👎 → basic signal
  "What's wrong?" → error category:
    [ ] Inaccurate answer
    [ ] Didn't find the needed information
    [ ] Answer is off-topic
    [ ] Too long / short

Storage in PostgreSQL:
  feedback (query_id, rating, category, comment, created_at)

Usage:
  → Fine-tuning reranker on positive examples
  → Identifying failing query categories
  → A/B testing pipeline changes
```

---

## 7. FILTERING, SECURITY AND CONTROL

### 7.1 Input Validation

```python
class QueryValidator:
    MAX_QUERY_LENGTH = 2000
    MAX_TOKENS_PER_MINUTE = 100_000  # rate limit
    
    INJECTION_PATTERNS = [
        r"ignore previous instructions",
        r"system prompt",
        r"<\|.*?\|>",  # special tokens
        r"\[INST\]",
    ]
    
    async def validate(self, query: str, user_id: str) -> ValidationResult:
        # Length
        if len(query) > self.MAX_QUERY_LENGTH:
            return ValidationResult(valid=False, reason="QUERY_TOO_LONG")
        
        # Prompt injection
        for pattern in self.INJECTION_PATTERNS:
            if re.search(pattern, query, re.IGNORECASE):
                return ValidationResult(valid=False, reason="INJECTION_ATTEMPT")
        
        # Rate limiting
        if not await self.check_rate_limit(user_id):
            return ValidationResult(valid=False, reason="RATE_LIMITED")
        
        return ValidationResult(valid=True)
```

### 7.2 Output Filtering

```python
class ResponseFilter:
    async def filter(self, response: str, query: str, user_context: dict) -> FilterResult:
        checks = await asyncio.gather(
            self.check_pii(response),
            self.check_sensitive_data(response, user_context),
            self.check_hallucination_markers(response),
            self.check_forbidden_content(response),
        )
        
        if any(c.triggered for c in checks):
            triggered = [c for c in checks if c.triggered]
            return FilterResult(
                safe=False,
                reasons=[c.reason for c in triggered],
                sanitized_response=self.sanitize(response, triggered)
            )
        
        return FilterResult(safe=True, sanitized_response=response)
    
    async def check_pii(self, text: str) -> CheckResult:
        # Presidio for PII detection
        from presidio_analyzer import AnalyzerEngine
        analyzer = AnalyzerEngine()
        results = analyzer.analyze(text=text, language="en")
        return CheckResult(triggered=bool(results), reason="PII_DETECTED")
```

### 7.3 Access Control (RBAC at document level)

```python
# Every document/chunk has an access label
# Retrieval filters by current user's permissions

class AccessControlFilter:
    async def apply(self, user: User, query_filter: dict) -> dict:
        user_permissions = await self.get_permissions(user.id)
        
        # Add filter to Qdrant query
        access_filter = {
            "should": [
                {"key": "access_level", "match": {"value": level}}
                for level in user_permissions.allowed_levels
            ]
        }
        
        if query_filter:
            return {"must": [query_filter, access_filter]}
        return access_filter
```

### 7.4 Audit Log

```sql
-- Full audit of every query
CREATE TABLE rag_audit_log (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    query_id        UUID NOT NULL,
    user_id         UUID NOT NULL,
    query_text      TEXT NOT NULL,
    query_hash      VARCHAR(64) NOT NULL,   -- for anonymisation
    retrieved_doc_ids UUID[] NOT NULL,
    response_length INTEGER,
    latency_ms      INTEGER,
    faithfulness_score DECIMAL(3,2),
    filter_triggered BOOLEAN DEFAULT FALSE,
    filter_reasons  TEXT[],
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_audit_user_date ON rag_audit_log(user_id, created_at DESC);
CREATE INDEX idx_audit_filter ON rag_audit_log(filter_triggered) WHERE filter_triggered = TRUE;
```

---

## 8. OBSERVABILITY AND MONITORING

### 8.1 Distributed Tracing (OpenTelemetry)

```python
from opentelemetry import trace
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor

tracer = trace.get_tracer("rag-system")

async def retrieval_pipeline(query: str):
    with tracer.start_as_current_span("rag.full_pipeline") as span:
        span.set_attribute("query.length", len(query))
        
        with tracer.start_as_current_span("rag.query_transform"):
            transformed = await transform_query(query)
        
        with tracer.start_as_current_span("rag.hybrid_search") as s:
            results = await hybrid_search(transformed)
            s.set_attribute("results.count", len(results))
        
        with tracer.start_as_current_span("rag.rerank") as s:
            reranked = await rerank(query, results)
            s.set_attribute("reranked.count", len(reranked))
        
        with tracer.start_as_current_span("rag.generate"):
            response = await generate(query, reranked)
        
        span.set_attribute("response.tokens", count_tokens(response))
        return response
```

### 8.2 Key Prometheus metrics

```python
from prometheus_client import Histogram, Counter, Gauge

# Latency per stage
rag_stage_latency = Histogram(
    'rag_stage_latency_seconds',
    'Latency of each RAG stage',
    ['stage'],  # embed, search, rerank, generate
    buckets=[0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0, 10.0]
)

# Retrieval quality
rag_retrieved_chunks = Histogram(
    'rag_retrieved_chunks_count',
    'Number of chunks retrieved',
    buckets=[1, 3, 5, 10, 20]
)

# Errors
rag_errors_total = Counter(
    'rag_errors_total',
    'Total errors by type',
    ['error_type']  # retrieval_failed, generation_failed, filter_triggered
)

# Online quality (from LLM-judge)
rag_faithfulness_score = Histogram(
    'rag_faithfulness_score',
    'Faithfulness score from LLM judge',
    buckets=[0.1, 0.2, 0.3, 0.5, 0.7, 0.8, 0.9, 1.0]
)

# Ingestion queue size
ingestion_queue_size = Gauge('rag_ingestion_queue_size', 'Documents waiting for ingestion')
```

### 8.3 Grafana Dashboard — key panels

```
Panel 1: Health Overview
  ├── RAG System Status (Green/Yellow/Red)
  ├── QPS (last 5 minutes)
  ├── Error Rate %
  └── P95 Latency

Panel 2: Retrieval Quality
  ├── Avg Faithfulness Score (rolling 1h)
  ├── Context Recall (rolling 1h)
  ├── Cache Hit Rate %
  └── Reranker Score Distribution

Panel 3: Ingestion Pipeline
  ├── Documents Processed (24h)
  ├── Ingestion Queue Depth
  ├── Parse Errors
  └── Chunk Count by Source

Panel 4: Cost Tracking
  ├── LLM Tokens per day
  ├── Embedding API calls
  ├── Cost per 1000 queries
  └── Cache Savings (tokens saved)

Panel 5: User Feedback
  ├── Thumbs Up/Down ratio (rolling 7d)
  ├── Feedback Categories Breakdown
  └── Low Quality Query Samples
```

### 8.4 Alerts

```yaml
# alerting_rules.yml
groups:
  - name: rag_quality
    rules:
      - alert: LowFaithfulnessScore
        expr: avg_over_time(rag_faithfulness_score[30m]) < 0.7
        for: 10m
        severity: warning
        
      - alert: HighHallucinationRate
        expr: rate(rag_errors_total{error_type="hallucination"}[5m]) > 0.1
        for: 5m
        severity: critical
        
      - alert: RetrievalLatencyHigh
        expr: histogram_quantile(0.95, rag_stage_latency_seconds{stage="search"}) > 2.0
        for: 5m
        severity: warning
        
      - alert: IngestionQueueBacklog
        expr: rag_ingestion_queue_size > 1000
        for: 15m
        severity: warning
```

---

## 9. UI FOR RAG SYSTEM MANAGEMENT

### 9.1 Screen map (Admin UI)

```
RAG Management Console
├── Dashboard (system overview)
│   ├── Health status of all components
│   ├── Quality metrics (faithfulness, recall)
│   ├── Ingestion throughput
│   └── Recent errors
│
├── Knowledge Base
│   ├── Documents List (source management)
│   │   ├── Upload (drag & drop)
│   │   ├── Source connectors (S3, SharePoint, URL)
│   │   ├── Status: Processing / Indexed / Failed / Outdated
│   │   └── Actions: Re-index, Delete, View chunks
│   │
│   ├── Document Detail
│   │   ├── Parsing result + preview
│   │   ├── Chunks list with scores
│   │   ├── Metadata editor
│   │   └── Reindex button
│   │
│   └── Collections (document groups)
│       ├── Access control settings
│       └── Bulk operations
│
├── Query Lab (testing)
│   ├── Playground — send a query, see the full pipeline
│   ├── Retrieved chunks with scores
│   ├── Reranking result
│   ├── LLM response + citations
│   └── Quality scores (faithfulness, relevance)
│
├── Evaluation
│   ├── Test Suite management
│   ├── Run evaluation (dataset + metrics selection)
│   ├── Results history + comparison
│   └── Regression alerts
│
├── Analytics
│   ├── Query patterns (top queries, fail queries)
│   ├── User feedback breakdown
│   ├── Quality trends (7d / 30d)
│   └── Cost analysis
│
├── Configuration
│   ├── Chunking strategy editor
│   ├── Embedding model selector
│   ├── Retrieval parameters (top-K, threshold)
│   ├── Reranker settings
│   └── LLM prompt editor
│
└── Access Control
    ├── Users & Roles
    ├── Document-level permissions
    └── API Keys management
```

### 9.2 Query Lab — key screen

Query Lab is a debug interface for developer/operator. Shows the full pipeline step by step.

```
┌─────────────────────────────────────────────────────────────────┐
│  Query Lab                                              [Export] │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Query: [_____________________________________________] [Run]    │
│                                                                  │
│  Filters: Collection [All ▼]  Date range [Any ▼]               │
│                                                                  │
├──────────────────────────┬──────────────────────────────────────┤
│  PIPELINE TRACE          │  RESPONSE                            │
│                          │                                      │
│  ① Query Transform  12ms │  Based on the documents, RAG...     │
│    Original: "what..."   │                                      │
│    Expanded: [3 queries] │  [Source: doc1.pdf, p.3] ▶          │
│                          │  [Source: doc2.pdf, p.17] ▶         │
│  ② Dense Search    45ms  │                                      │
│    Results: 20 chunks    │  ─────────────────────────          │
│    Top score: 0.94       │  Quality Scores                      │
│                          │  Faithfulness:  ████████░  0.87     │
│  ③ Sparse Search   23ms  │  Relevance:     █████████░ 0.91     │
│    Results: 15 chunks    │  Completeness:  ███████░░░ 0.74     │
│                          │                                      │
│  ④ RRF Fusion       3ms  │  ⚠ Warning: 1 citation unverified  │
│    Merged: 28 chunks     │                                      │
│                          │                                      │
│  ⑤ Reranker        89ms  │  [👍 Good] [👎 Bad] [📋 Add to     │
│    After: 5 chunks       │             test suite]              │
│    Threshold: 0.3        │                                      │
│                          │                                      │
│  ⑥ Generate       1.2s   │                                      │
│    Tokens: 847           │                                      │
│    Cost: $0.0023         │                                      │
│                          │                                      │
│  Total: 1.37s            │                                      │
├──────────────────────────┴──────────────────────────────────────┤
│  RETRIEVED CHUNKS (5)                                    [Sort ▼]│
│                                                                  │
│  #1 Score: 0.94  doc1.pdf / p.3  [View] [Copy]                 │
│  Lorem ipsum...                                                  │
│                                                                  │
│  #2 Score: 0.87  doc2.pdf / p.17 [View] [Copy]                 │
│  ...                                                             │
└─────────────────────────────────────────────────────────────────┘
```

### 9.3 Documents List — design patterns

**Document statuses (visual):**
```
● Indexed      — green, ready for search
◐ Processing   — yellow + spinner, queued/processing
✗ Failed       — red, parsing/indexing error
⚠ Outdated     — grey-yellow, document changed, re-index needed
```

**Mandatory table columns:**
| Document | Source | Chunks | Status | Last updated | Actions |
|---------|---------|--------|--------|-------------|---------|

**Bulk actions (mandatory):** Re-index selected / Delete selected / Export metadata

**EmptyState (no documents):**
```
📄 Knowledge base is empty

Upload documents or connect a data source
to start using RAG

[Upload Documents]  [Connect Source]
```

### 9.4 Evaluation Dashboard

```
Evaluation Suite
│
├─ Test Sets
│   ├── production-sample-2026-04.json  (500 questions)  [Run] [Edit]
│   ├── edge-cases-v2.json              (87 questions)   [Run] [Edit]
│   └── [+ New Test Set]
│
├─ Recent Runs
│   │  Date         Dataset              F     R     P    Status
│   ├─ 29.04 14:23  production-sample  0.87  0.79  0.83   ✅ Pass
│   ├─ 28.04 09:11  production-sample  0.84  0.76  0.81   ✅ Pass
│   └─ 27.04 17:45  production-sample  0.71  0.68  0.73   ❌ Fail  ← regression
│
└─ Thresholds (for CI/CD regression gate)
    ├── faithfulness:      min 0.85  [Edit]
    ├── context_recall:    min 0.75  [Edit]
    └── answer_relevancy:  min 0.80  [Edit]
```

### 9.5 Configuration UI — patterns

**Chunking strategy editor — visual preview:**
```
Strategy: [Semantic ▼]
Size: [512]  Overlap: [64]
Split model: [text-embedding-3-large ▼]

Preview on test text:
┌─ Chunk 1 (243 tokens) ─────────────────┐
│ Lorem ipsum dolor sit amet...          │
└─────────────────────────────────────── ┘
         ↕ overlap: 64 tokens
┌─ Chunk 2 (287 tokens) ─────────────────┐
│ ...amet consectetur adipiscing...      │
└─────────────────────────────────────── ┘

[Save & Re-index Collection]
⚠ Changing parameters will require full re-indexing (1,243 documents, ~45 min)
```

---

## 10. ARCHITECTURAL PATTERNS AND ANTI-PATTERNS

### 10.1 Proven patterns

**RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval)**
Build a hierarchy of summaries: documents → sections → paragraphs. Search at the right level of abstraction.

**Corrective RAG (CRAG)**
After first retrieval — LLM evaluates quality. If below threshold → web search or decline to answer. Reduces hallucinations by 30-40%.

**Self-RAG**
LLM itself decides when retrieval is needed (not always). Special reflection tokens: `[Retrieve]`, `[IsRel]`, `[IsSup]`, `[IsUse]`. Requires fine-tuning.

**Fusion RAG**
Query → 5 rephrasings → 5 parallel searches → RRF fusion → rerank. Best recall on complex queries.

### 10.2 Anti-patterns (do not do)

| Anti-pattern | Problem | Solution |
|------------|---------|--------|
| Fixed-size chunking 512 tokens | Breaks meaning, loses context | Semantic chunking |
| Dense search only | Misses key terms | Hybrid search (dense + BM25) |
| Top-K without reranking | Irrelevant chunks in context | Always rerank |
| Entire document in context | Token limit exceeded, "lost in middle" | Chunking + parent retrieval |
| No semantic cache | Duplicate LLM calls | Redis SemanticCache |
| No quality evaluation | Degradation unnoticed | RAGAS + online evaluation |
| Single LLM provider | Single point of failure | Fallback to second provider |
| No access control | Data leak between tenants | RBAC at vector search level |
| Synchronous ingestion | Blocks API on upload | Async queue (Redis Streams) |
| Ignore feedback | No improvement | Feedback loop → reranker fine-tuning |

---

## 11. REFERENCE: API CONTRACTS AND DB SCHEMA

### 11.1 Core API Endpoints

```
POST   /api/v1/query                    Send a query to RAG
POST   /api/v1/query/stream             Streaming version
GET    /api/v1/query/{id}/feedback      Get feedback on a query
POST   /api/v1/query/{id}/feedback      Add feedback

POST   /api/v1/documents                Upload a document
GET    /api/v1/documents                Document list (paginated)
GET    /api/v1/documents/{id}           Document details
DELETE /api/v1/documents/{id}           Delete document
POST   /api/v1/documents/{id}/reindex   Re-index

GET    /api/v1/documents/{id}/chunks    Document chunks
GET    /api/v1/chunks/{id}              Chunk details

GET    /api/v1/collections              Collection list
POST   /api/v1/collections              Create collection
PUT    /api/v1/collections/{id}         Update collection

POST   /api/v1/evaluation/runs          Start evaluation
GET    /api/v1/evaluation/runs          Run history
GET    /api/v1/evaluation/runs/{id}     Run results

GET    /api/v1/analytics/queries        Query analytics
GET    /api/v1/analytics/quality        Quality metrics

GET    /api/v1/health                   Health check
GET    /metrics                         Prometheus metrics
```

### 11.2 DB Schema (PostgreSQL)

```sql
-- Documents
CREATE TABLE documents (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    collection_id   UUID REFERENCES collections(id),
    filename        VARCHAR(500) NOT NULL,
    source_url      TEXT,
    source_type     VARCHAR(50) NOT NULL, -- 'upload'|'s3'|'url'|'sharepoint'
    status          VARCHAR(30) NOT NULL DEFAULT 'pending',
    -- 'pending'|'parsing'|'chunking'|'embedding'|'indexed'|'failed'|'outdated'
    chunk_count     INTEGER,
    token_count     INTEGER,
    file_size_bytes BIGINT,
    content_hash    VARCHAR(64),          -- for deduplication
    parse_error     TEXT,
    metadata        JSONB DEFAULT '{}',
    created_at      TIMESTAMPTZ DEFAULT NOW(),
    indexed_at      TIMESTAMPTZ,
    updated_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Collections (document groups with settings)
CREATE TABLE collections (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            VARCHAR(255) NOT NULL,
    description     TEXT,
    config          JSONB DEFAULT '{}',   -- chunk_size, embedding_model, etc.
    access_level    VARCHAR(50) DEFAULT 'private',
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Query history
CREATE TABLE query_log (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id      UUID,
    user_id         UUID,
    query_text      TEXT NOT NULL,
    query_hash      VARCHAR(64),
    collection_id   UUID REFERENCES collections(id),
    retrieved_doc_ids UUID[],
    retrieved_chunk_ids UUID[],
    response_text   TEXT,
    response_tokens INTEGER,
    latency_ms      INTEGER,
    stage_latencies JSONB,               -- {embed: 45, search: 89, rerank: 120, generate: 1200}
    faithfulness    DECIMAL(3,2),
    relevance       DECIMAL(3,2),
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- User feedback
CREATE TABLE query_feedback (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    query_id        UUID REFERENCES query_log(id),
    rating          SMALLINT CHECK (rating IN (1, -1)),  -- 1=👍, -1=👎
    categories      TEXT[],
    comment         TEXT,
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Test sets for evaluation
CREATE TABLE eval_test_sets (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            VARCHAR(255) NOT NULL,
    description     TEXT,
    questions       JSONB NOT NULL,      -- [{question, ground_truth, expected_sources}]
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Evaluation run history
CREATE TABLE eval_runs (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    test_set_id     UUID REFERENCES eval_test_sets(id),
    config_snapshot JSONB NOT NULL,      -- pipeline parameters at run time
    status          VARCHAR(30) DEFAULT 'running',
    faithfulness    DECIMAL(4,3),
    context_recall  DECIMAL(4,3),
    context_precision DECIMAL(4,3),
    answer_relevancy DECIMAL(4,3),
    passed_gate     BOOLEAN,
    results_detail  JSONB,               -- per-item results
    started_at      TIMESTAMPTZ DEFAULT NOW(),
    finished_at     TIMESTAMPTZ
);
```

### 11.3 RAG API response format

```json
{
  "query_id": "uuid",
  "answer": "Based on the documents...",
  "citations": [
    {
      "source": "filename.pdf",
      "page": 3,
      "chunk_id": "uuid",
      "verified": true,
      "excerpt": "...relevant fragment..."
    }
  ],
  "retrieved_chunks": [
    {
      "chunk_id": "uuid",
      "doc_id": "uuid",
      "text": "chunk text",
      "score": 0.94,
      "rerank_score": 0.87,
      "metadata": {}
    }
  ],
  "metadata": {
    "latency_ms": 1370,
    "stage_latencies": {
      "query_transform": 12,
      "search": 68,
      "rerank": 89,
      "generate": 1200
    },
    "tokens_used": 847,
    "cache_hit": false
  }
}
```

### 11.4 Extension for multi-tenancy

The example from §11.2 is instructional; in the product DB at minimum:

```sql
-- Add to all listed entities:
ALTER TABLE collections ADD COLUMN tenant_id UUID NOT NULL REFERENCES tenants(id);
ALTER TABLE documents ADD COLUMN tenant_id UUID NOT NULL REFERENCES tenants(id);
ALTER TABLE query_log ADD COLUMN tenant_id UUID NOT NULL REFERENCES tenants(id);
-- payload in the vector store (Qdrant/pgvector) must contain tenant_id for filter on query
```

Indexes: `(tenant_id, created_at)`, `(tenant_id, status)` for hot lists. Row Level Security policy in PostgreSQL — recommended as an additional barrier when included in the project spine.

---

## APPENDIX: STARTER STACK (minimum production-ready)

```yaml
# docker-compose.yml (production baseline)
services:
  api:
    image: ghcr.io/your-org/rag-api:latest
    environment:
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - OPENAI_API_KEY=${OPENAI_API_KEY}     # for embeddings
      - COHERE_API_KEY=${COHERE_API_KEY}      # for reranking
      - QDRANT_URL=http://qdrant:6333
      - REDIS_URL=redis://redis:6379
      - DATABASE_URL=postgresql://postgres:${DB_PASS}@postgres:5432/rag
  
  qdrant:
    image: qdrant/qdrant:v1.10.0
    volumes:
      - qdrant_data:/qdrant/storage
  
  postgres:
    image: postgres:17
    environment:
      POSTGRES_PASSWORD: ${DB_PASS}
      POSTGRES_DB: rag
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis/redis-stack:7.4.0-v1
    # Redis Stack includes RedisSearch + RediSearch for SemanticCache
  
  worker:
    image: ghcr.io/your-org/rag-worker:latest
    command: celery -A app.worker worker --concurrency=4
    # Async ingestion worker
  
  grafana:
    image: grafana/grafana:11.x
  
  prometheus:
    image: prom/prometheus:v3.x
```

---

> Document version: 1.2 | 27.04.2026
> Next review: on embedding model change, tenancy contract change, or new LLM generation release
> Canon owner: project architecture role (changes recorded in spine / ADR).
