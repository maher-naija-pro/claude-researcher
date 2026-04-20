# Test Results — Qdrant

## Environment

- Qdrant 1.8.4 (Docker, single node)
- Dataset: 100k documents, OpenAI `text-embedding-ada-002` (1536 dims)
- Hardware: 4 vCPU, 16 GB RAM

## Setup time

| Step | Duration |
|------|----------|
| Docker pull + start | 30s |
| Collection creation | < 1s |
| Bulk upsert (100k vectors) | 1m 50s |
| Index build (automatic) | included |

## Query performance

| Queries | Avg latency | p99 latency | Recall@10 |
|---------|-------------|-------------|-----------|
| 100 | 4ms | 7ms | 0.99 |
| 1,000 concurrent | 11ms | 28ms | 0.99 |

## Hybrid search test

Dense + sparse (BM25) combined query:
- Latency: 9ms avg
- Recall: 0.97

## Filtered search test

Payload filter (`lang = en`, ~60% of collection):
- Latency: 6ms avg
- Recall: 0.98 (no drop vs unfiltered — filter-aware HNSW traversal)

## Result

**PASS** — Best raw performance in the benchmark. Filtered search recall is excellent. Adds operational overhead (new Docker service), but justified at scale.
