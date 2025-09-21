---

## 🔹 Step 1: Install dependencies

```bash
pip install qdrant-client sentence-transformers
```

If you want to run Qdrant locally via Docker:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

Or you can use **Qdrant Cloud** (free tier available).

---

## 🔹 Step 2: Load the embedding model

```python
from sentence_transformers import SentenceTransformer

# Load all-MiniLM-L6-v2 model
model = SentenceTransformer('sentence-transformers/all-MiniLM-L6-v2')
```

This model gives you **384-dimensional embeddings**.

---

## 🔹 Step 3: Connect to Qdrant

```python
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams

# Connect to local Qdrant
client = QdrantClient(host="localhost", port=6333)

# Or, if using Qdrant Cloud:
# client = QdrantClient(url="https://your-cluster-url", api_key="YOUR_API_KEY")
```

---

## 🔹 Step 4: Create a collection

```python
collection_name = "semantic_docs"

client.recreate_collection(
    collection_name=collection_name,
    vectors_config=VectorParams(size=384, distance=Distance.COSINE)  # COSINE is common for embeddings
)
```

- `size=384` → matches the dimension of `all-MiniLM-L6-v2`
- `distance=cosine` → good choice for semantic similarity

---

## 🔹 Step 5: Insert documents

```python
docs = [
    "Qdrant is a vector search engine.",
    "SentenceTransformers generate embeddings for semantic search.",
    "The all-MiniLM-L6-v2 model is lightweight and fast.",
    "OpenAI develops advanced AI models."
]

# Generate embeddings
vectors = model.encode(docs)

# Upload to Qdrant
client.upsert(
    collection_name=collection_name,
    points=[
        {"id": idx, "vector": vectors[idx], "payload": {"text": docs[idx]}}
        for idx in range(len(docs))
    ]
)
```

Here:

- Each document is stored with its embedding (`vector`)
- Metadata (payload) includes the original text

---

## 🔹 Step 6: Run a semantic search query

```python
query = "What is Qdrant used for?"
query_vector = model.encode(query).tolist()

search_results = client.search(
    collection_name=collection_name,
    query_vector=query_vector,
    limit=3
)

for result in search_results:
    print(result.payload["text"], "→ score:", result.score)
```

✅ This will return the most **semantically similar documents** to your query.

---

## 🔹 Step 7: Add filtering (optional)

You can filter results by metadata:

```python
search_results = client.search(
    collection_name=collection_name,
    query_vector=query_vector,
    limit=3,
    query_filter={"must": [{"key": "category", "match": {"value": "AI"}}]}
)
```

---

👉 That’s the basic pipeline:
**Embed text → Store in Qdrant → Query with embeddings → Get semantic matches.**

---
