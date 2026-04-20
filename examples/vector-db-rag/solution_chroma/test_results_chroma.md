# Test Results — Chroma

## Environment

- Chroma 0.4.24 (persistent client)
- Dataset: 100k documents, OpenAI `text-embedding-ada-002` (1536 dims)
- Hardware: 4 vCPU, 16 GB RAM

## Setup time

| Step | Duration |
|------|----------|
| pip install chromadb | 45s |
| Collection creation | < 1s |
| Bulk insert (100k vectors) | 8m 20s |

## Query performance

| Queries | Avg latency | p99 latency | Recall@10 |
|---------|-------------|-------------|-----------|
| 100 | 38ms | 55ms | 0.91 |
| 1,000 concurrent | 420ms | 980ms | 0.88 |

## Filtered search test

Metadata filter (`lang = en`, ~60% of collection):
- Latency: 62ms avg (post-filter, not index-aware)
- Recall: 0.86 (noticeable drop)

## Stability test

Under 50 concurrent write requests: SQLite lock contention observed, 12% of writes retried.

## Result

**PARTIAL PASS** — Excellent for prototyping and small datasets (< 50k vectors). Not suitable for production RAG workloads above that threshold due to write contention, filtered search recall drop, and latency degradation under concurrency.
