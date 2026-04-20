# Solution: Qdrant

## Summary

Qdrant is a purpose-built vector database written in Rust. It supports dense and sparse vectors, payload filtering, and horizontal sharding. Available as open-source (self-hosted) or managed cloud.

## Sources

- https://github.com/qdrant/qdrant
- https://qdrant.tech/documentation/
- https://qdrant.tech/benchmarks/

## Pros

- **Purpose-built for vectors** — HNSW with payload-aware filtering (no recall drop on filtered search)
- **Horizontal scaling** — native sharding and replication
- **Sparse + dense vectors** — hybrid search (BM25 + semantic) out of the box
- **Fast Rust core** — low latency at high concurrency
- **Rich payload filtering** — filter during ANN traversal, not as a post-filter

## Cons

- **New service to operate** — adds deployment and ops overhead
- **Smaller ecosystem** — fewer integrations than Postgres-based solutions
- **Managed cloud cost** — Qdrant Cloud is not free at scale
- **No relational joins** — pure vector store; application must handle relational logic
