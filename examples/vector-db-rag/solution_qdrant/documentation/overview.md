# Qdrant — Technical Overview

## Quickstart (Docker)

```bash
docker run -p 6333:6333 qdrant/qdrant
```

## Create collection

```python
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams

client = QdrantClient("localhost", port=6333)
client.create_collection(
    collection_name="documents",
    vectors_config=VectorParams(size=1536, distance=Distance.COSINE),
)
```

## Upsert vectors

```python
from qdrant_client.models import PointStruct

client.upsert(
    collection_name="documents",
    points=[
        PointStruct(id=1, vector=[0.1, 0.2, ...], payload={"content": "...", "lang": "en"}),
    ]
)
```

## Query with payload filter

```python
from qdrant_client.models import Filter, FieldCondition, MatchValue

results = client.search(
    collection_name="documents",
    query_vector=[0.1, 0.2, ...],
    query_filter=Filter(must=[FieldCondition(key="lang", match=MatchValue(value="en"))]),
    limit=5,
)
```

## Performance benchmarks (1M vectors, 1536 dims)

| Mode | QPS | Recall@10 |
|------|-----|-----------|
| No filter | ~4,500 | 0.99 |
| Payload filter | ~3,800 | 0.98 |
| Sparse+dense hybrid | ~2,100 | 0.97 |
