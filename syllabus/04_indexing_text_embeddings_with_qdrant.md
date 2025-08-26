
---

# 🧠 Project: Index Text Embeddings with Sentence Transformers + Qdrant

---

## 📌 Objective

Use **Sentence Transformers** to generate real semantic embeddings from text and store them in **Qdrant** for fast, meaningful **semantic search** using cosine similarity.

---

## 🛠️ Tools Required

```bash
pip install pandas sentence-transformers qdrant-client
```

And make sure Qdrant is running locally:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## ✅ Step 1: Load Text Data

```python
import pandas as pd

df = pd.DataFrame({
    "id": [1, 2, 3, 4],
    "description": [
        "A red apple that is juicy and sweet.",
        "A yellow banana full of potassium.",
        "A sports car with a powerful engine.",
        "A mountain bike built for rough terrain."
    ]
})
```

---

## ✅ Step 2: Generate Real Embeddings using Sentence Transformers

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("all-MiniLM-L6-v2")  # small, fast model
embeddings = model.encode(df["description"].tolist(), convert_to_numpy=True)
```

---

## ✅ Step 3: Store Embeddings in Qdrant

```python
from qdrant_client import QdrantClient

client = QdrantClient(host="localhost", port=6333)

client.recreate_collection(
    collection_name="semantic_text_search",
    vectors_config={"size": embeddings.shape[1], "distance": "Cosine"}
)

client.upload_collection(
    collection_name="semantic_text_search",
    vectors=embeddings.tolist(),
    payload=df.to_dict(orient="records"),
    ids=df["id"].tolist()
)
```

---

## ✅ Step 4: Perform Semantic Search

```python
query = "a vehicle designed for speed and performance"
query_vector = model.encode(query).tolist()

results = client.search(
    collection_name="semantic_text_search",
    query_vector=query_vector,
    top=3
)

for hit in results:
    print(f"ID: {hit.id} | Desc: {hit.payload['description']} | Score: {hit.score:.4f}")
```

---

### ✅ Output (Example)

```
ID: 3 | Desc: A sports car with a powerful engine. | Score: 0.9431  
ID: 4 | Desc: A mountain bike built for rough terrain. | Score: 0.8112  
ID: 1 | Desc: A red apple that is juicy and sweet. | Score: 0.5010  
```

---

## 🎯 What You Accomplished

* Generated real sentence embeddings using `SentenceTransformer`
* Indexed them in Qdrant with metadata
* Performed **true semantic search** using a natural language query

---

## 🧹 Optional: Clean Up

```python
client.delete_collection("semantic_text_search")
```

---

Want to add:

* **Metadata filters** (e.g., search in fruits only)?
* **Web interface using Streamlit**?
* **Multilingual queries**?

