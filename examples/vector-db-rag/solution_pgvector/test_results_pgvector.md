# Test Results — pgvector

## Environment

- PostgreSQL 16.2 + pgvector 0.6.2
- Dataset: 100k documents, OpenAI `text-embedding-ada-002` (1536 dims)
- Hardware: 4 vCPU, 16 GB RAM

## Setup time

| Step | Duration |
|------|----------|
| Extension install | 2s |
| Table + index creation | 4 min (HNSW, 100k vectors) |
| First query | < 1s |

## Query performance

| Queries | Avg latency | p99 latency | Recall@10 |
|---------|-------------|-------------|-----------|
| 100 | 12ms | 18ms | 0.97 |
| 1,000 concurrent | 45ms | 110ms | 0.97 |

## Hybrid search test

Combined vector + keyword filter (SQL WHERE clause):
```sql
SELECT content, embedding <=> $1 AS dist
FROM documents
WHERE metadata->>'lang' = 'en'
ORDER BY dist LIMIT 5;
```
- Latency: 28ms avg (pre-filter reduces search space)
- Recall: 0.94 (slight drop vs. pure ANN)

## Result

**PASS** — Meets latency targets. Hybrid search works well with correct index configuration. No additional infrastructure required beyond existing Postgres.
