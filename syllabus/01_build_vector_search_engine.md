

# 🧠 Project: Build a Simple Vector Search Engine using Qdrant + Pandas

---

## 📌 Objective

Use **Qdrant** and **Pandas** to build a lightweight vector search engine. We'll index fruit names using **randomly generated 4D embeddings** and perform **cosine similarity-based search**.

---

## 🛠️ Tools Used

* Python
* `pandas`
* `numpy`
* `qdrant-client`

---

## 📦 Installation

```bash
pip install pandas numpy qdrant-client
```

Make sure Qdrant is running locally (Docker or system service):

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## 📁 Step 1: Initialize Qdrant Client

```python
from qdrant_client import QdrantClient

client = QdrantClient(host="localhost", port=6333)
```

---

## 📁 Step 2: Create Sample Dataset with Pandas

```python
import pandas as pd
import numpy as np

fruits = ["apple", "banana", "cherry", "grape", "orange", "pineapple", "kiwi", "mango"]
df = pd.DataFrame({"name": fruits})

# Generate 4D random embeddings
df["embedding"] = df["name"].apply(lambda _: np.random.rand(4).tolist())
df
```

---

## 📁 Step 3: Create a Qdrant Collection

```python
client.recreate_collection(
    collection_name="fruit_vectors",
    vectors_config={"size": 4, "distance": "Cosine"}
)
```

---

## 📁 Step 4: Upload Data to Qdrant

```python
client.upload_collection(
    collection_name="fruit_vectors",
    vectors=df["embedding"].tolist(),
    payload=df.to_dict(orient="records"),
    ids=list(range(len(df)))
)
```

---

## 📁 Step 5: Perform Similarity Search

```python
query_vector = np.random.rand(4).tolist()  # simulate a user query
results = client.search(
    collection_name="fruit_vectors",
    query_vector=query_vector,
    top=3
)

for r in results:
    print(f"Name: {r.payload['name']} | Score: {r.score:.4f}")
```

---

## 📁 Step 6: Clean Up (Optional)

```python
client.delete_collection("fruit_vectors")
```

---

## ✅ Output Example

```
Name: mango | Score: 0.9881
Name: orange | Score: 0.9653
Name: apple | Score: 0.9512
```

---

## 📚 What You Learned

* How to use Qdrant with Pandas
* How to create random embeddings for text
* How to index, search, and retrieve data using vector similarity (cosine)
* Basic CRUD operations in Qdrant

---
