# Decision — Vector Database for RAG

**Research question:** Which vector database should we use for a production RAG pipeline handling 100k–1M documents with hybrid search and metadata filtering?

---

## Scoring Table

| Aspect | Weight | pgvector | Qdrant | Chroma |
|--------|--------|----------|--------|--------|
| Query performance (latency / QPS) | 25% | 7 | 10 | 4 |
| Filtered search recall | 20% | 8 | 10 | 6 |
| Ops simplicity (setup + maintenance) | 20% | 10 | 7 | 9 |
| Scalability | 15% | 5 | 10 | 3 |
| Ecosystem / integrations | 10% | 9 | 7 | 9 |
| Hybrid search (dense + sparse) | 10% | 4 | 9 | 3 |

### Weighted scores

| Solution | Score |
|----------|-------|
| **Qdrant** | **8.75 / 10** |
| pgvector | 7.30 / 10 |
| Chroma | 5.35 / 10 |

---

## Recommendation

**Use Qdrant** for production RAG workloads requiring scale, filtered search, and hybrid retrieval.

**Use pgvector** if you already run Postgres and your dataset stays under ~500k vectors with moderate concurrency — zero extra infrastructure and excellent ACID guarantees.

**Use Chroma** only for local prototypes and developer demos — it is not suitable for production at this scale.

---

## Rationale

Qdrant leads in every performance metric. Its filter-aware HNSW traversal maintains recall under payload filters (0.98 vs. 0.94 for pgvector, 0.86 for Chroma), which is the critical bottleneck for multi-tenant RAG. Native sparse+dense hybrid search removes the need for a separate BM25 service.

The ops overhead of running a Qdrant container is real but acceptable; the managed cloud option (Qdrant Cloud) eliminates it entirely.

pgvector is the right pragmatic choice for teams already on Postgres who do not need extreme QPS or horizontal scale today. Migrating later is straightforward — the embedding format is identical.

Chroma is eliminated for production: SQLite write contention, 10x slower bulk ingestion, and recall degradation under filtering make it unsuitable beyond prototyping.

---

## Decision

```
WINNER: Qdrant
RUNNER-UP: pgvector (for Postgres-native teams)
ELIMINATED: Chroma (prototype only)
```
