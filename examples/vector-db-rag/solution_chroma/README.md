# Solution: Chroma

## Summary

Chroma is an open-source embedding database designed for simplicity. It runs in-process (no server needed for prototyping) or as a standalone server. Popular in LangChain and LlamaIndex integrations.

## Sources

- https://github.com/chroma-core/chroma
- https://docs.trychroma.com/
- https://python.langchain.com/docs/integrations/vectorstores/chroma

## Pros

- **Zero-setup for prototyping** — in-process mode, no Docker, no server
- **LangChain / LlamaIndex native** — first-class integration, minimal boilerplate
- **Simple Python API** — fastest to get a prototype running
- **Metadata filtering** — supported via where clause
- **Persistent storage** — SQLite + parquet on disk

## Cons

- **Not production-ready at scale** — single-node only, no sharding or replication
- **Performance** — significantly slower than Qdrant at high vector counts
- **SQLite bottleneck** — concurrent writes degrade under load
- **Immature stability** — breaking API changes between versions
- **No sparse vector support** — dense only
