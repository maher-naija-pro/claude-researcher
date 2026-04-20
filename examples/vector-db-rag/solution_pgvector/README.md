# Solution: pgvector

## Summary

pgvector is a PostgreSQL extension that adds vector similarity search directly into your existing Postgres database. It supports exact and approximate nearest-neighbor search using IVFFlat and HNSW indexes.

## Sources

- https://github.com/pgvector/pgvector
- https://supabase.com/docs/guides/ai/vector-columns
- https://www.postgresql.org/docs/current/indexes.html

## Pros

- **No new infrastructure** — runs inside your existing Postgres instance
- **ACID transactions** — vector search + relational queries in a single transaction
- **Mature ecosystem** — works with every Postgres client, ORM, and cloud provider
- **HNSW index** (added in 0.5.0) — competitive ANN performance
- **SQL interface** — familiar, no new query language

## Cons

- **Vertical scaling only** — Postgres does not shard natively; horizontal scale requires Citus or similar
- **Memory pressure** — large HNSW indexes live in shared_buffers; needs tuning
- **Limited filtering performance** — pre-filter via SQL WHERE before ANN can hurt recall
- **Not purpose-built** — dedicated vector DBs still outperform at very high dimension counts
