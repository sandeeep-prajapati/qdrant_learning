
---

# 🔍 Project: Perform Hybrid Search with Qdrant (Vector + Metadata)

---

## 📌 Objective

Use Qdrant to perform a **hybrid search**:

* Retrieve points **similar to a query vector**
* AND filter them based on metadata (payload)
* Example: return only items similar to a query AND where `type == "vehicle"`

---

## 🛠️ Tools Required

```bash
pip install qdrant-client numpy pandas
```

Run Qdrant locally:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## 🧪 Step-by-Step Hybrid Search Demo

---

### ✅ Step 1: Create Sample Data with Tags

```python
import pandas as pd
import numpy as np

data = {
    "name": ["apple", "banana", "car", "bus", "bike", "grapes", "truck"],
    "type": ["fruit", "fruit", "vehicle", "vehicle", "vehicle", "fruit", "vehicle"]
}
df = pd.DataFrame(data)
df["embedding"] = df["name"].apply(lambda _: np.random.rand(4).tolist())
```

---

### ✅ Step 2: Upload Data to Qdrant

```python
from qdrant_client import QdrantClient

client = QdrantClient(host="localhost", port=6333)

client.recreate_collection(
    collection_name="hybrid_search_demo",
    vectors_config={"size": 4, "distance": "Cosine"}
)

client.upload_collection(
    collection_name="hybrid_search_demo",
    vectors=df["embedding"].tolist(),
    payload=df.to_dict(orient="records"),
    ids=list(range(len(df)))
)
```

---

### ✅ Step 3: Perform Hybrid Search (Similar + Filtered)

```python
from qdrant_client.http.models import Filter, FieldCondition, MatchValue

query_vector = np.random.rand(4).tolist()

results = client.search(
    collection_name="hybrid_search_demo",
    query_vector=query_vector,
    top=5,
    query_filter=Filter(
        must=[
            FieldCondition(
                key="type",
                match=MatchValue(value="vehicle")
            )
        ]
    )
)

for hit in results:
    print(f"{hit.payload['name']} | Type: {hit.payload['type']} | Score: {hit.score:.4f}")
```

---

### ✅ Output (Example)

```
truck | Type: vehicle | Score: 0.9342  
car   | Type: vehicle | Score: 0.8991  
bike  | Type: vehicle | Score: 0.8423  
```

---

## 🎯 Key Concept

You're combining:

* **Vector similarity** (based on 4D vectors)
* **Structured filtering** (e.g., metadata `type == "vehicle"`)

This is useful for:

* E-commerce filters
* Semantic + categorical search
* Medical or document search with tags

---

## 🧹 Optional: Delete Collection

```python
client.delete_collection("hybrid_search_demo")
```

---

## 📚 What You Learned

* How to store metadata alongside vectors
* How to use Qdrant’s `Filter` and `FieldCondition`
* How to perform filtered vector search efficiently

---
