# Chroma — Technical Overview

## Quickstart (in-process)

```python
import chromadb

client = chromadb.Client()  # in-memory
# or
client = chromadb.PersistentClient(path="./chroma_db")
```

## Create collection and insert

```python
collection = client.create_collection("documents")

collection.add(
    ids=["1", "2"],
    embeddings=[[0.1, 0.2, ...], [0.3, 0.4, ...]],
    documents=["text of doc 1", "text of doc 2"],
    metadatas=[{"lang": "en"}, {"lang": "fr"}],
)
```

## Query

```python
results = collection.query(
    query_embeddings=[[0.1, 0.2, ...]],
    n_results=5,
    where={"lang": "en"},
)
```

## LangChain integration

```python
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings

vectorstore = Chroma.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    persist_directory="./chroma_db",
)
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
```

## Performance benchmarks (100k vectors, 1536 dims)

| Mode | QPS | Recall@10 |
|------|-----|-----------|
| In-process | ~180 | 0.91 |
| Server mode | ~320 | 0.91 |
