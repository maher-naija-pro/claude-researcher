# pgvector — Technical Overview

## Installation

```sql
CREATE EXTENSION vector;
```

Available on: AWS RDS, Supabase, Neon, Railway, self-hosted Postgres 13+.

## Data types

```sql
CREATE TABLE documents (
  id    SERIAL PRIMARY KEY,
  content TEXT,
  embedding vector(1536)  -- OpenAI ada-002 dimension
);
```

## Indexing

**IVFFlat** (faster build, lower recall):
```sql
CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops) WITH (lists = 100);
```

**HNSW** (slower build, higher recall — recommended):
```sql
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops) WITH (m = 16, ef_construction = 64);
```

## Query

```sql
SELECT content, embedding <=> '[0.1, 0.2, ...]' AS distance
FROM documents
ORDER BY distance
LIMIT 5;
```

## Operators

| Operator | Distance |
|----------|----------|
| `<->` | L2 (Euclidean) |
| `<=>` | Cosine |
| `<#>` | Inner product |

## Performance benchmarks (1M vectors, 1536 dims)

| Index | QPS | Recall@10 |
|-------|-----|-----------|
| IVFFlat | ~800 | 0.93 |
| HNSW | ~2,400 | 0.98 |
