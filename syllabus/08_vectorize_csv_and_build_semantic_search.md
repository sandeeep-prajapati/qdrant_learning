
---

# 🛒 Project: Semantic Search for Product Descriptions using Qdrant + CSV

---

## 📌 Objective

* Load a CSV file with product descriptions
* Generate **sentence embeddings** using `sentence-transformers`
* Store vectors + metadata in **Qdrant**
* Allow the user to **input a search query** and return the top-k most relevant products using **semantic similarity**

---

## 🛠️ Tools Required

```bash
pip install pandas qdrant-client sentence-transformers
```

Also run Qdrant locally:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## ✅ Step 1: Sample CSV (`products.csv`)

```csv
product_id,name,description
101,Apple iPhone,A powerful smartphone with great camera.
102,Dell Laptop,Lightweight and fast laptop for students.
103,Samsung TV,Smart television with 4K UHD display.
104,Canon DSLR,Professional digital camera for photography.
```

---

## ✅ Step 2: Load CSV and Generate Embeddings

```python
import pandas as pd
from sentence_transformers import SentenceTransformer

# Load CSV
df = pd.read_csv("products.csv")

# Load sentence embedding model
model = SentenceTransformer("all-MiniLM-L6-v2")

# Generate embeddings from product descriptions
df["embedding"] = model.encode(df["description"].tolist(), convert_to_numpy=True).tolist()
```

---

## ✅ Step 3: Upload to Qdrant

```python
from qdrant_client import QdrantClient

COLLECTION_NAME = "product_search"

client = QdrantClient(host="localhost", port=6333)

# Create collection with correct vector size
vector_size = len(df["embedding"][0])

client.recreate_collection(
    collection_name=COLLECTION_NAME,
    vectors_config={"size": vector_size, "distance": "Cosine"}
)

# Upload vectors and payloads
client.upload_collection(
    collection_name=COLLECTION_NAME,
    vectors=df["embedding"].tolist(),
    payload=df.to_dict(orient="records"),
    ids=df["product_id"].tolist()
)
```

---

## ✅ Step 4: Semantic Search from User Query

```python
query = input("Enter your product search query: ")

query_vector = model.encode(query).tolist()

results = client.search(
    collection_name=COLLECTION_NAME,
    query_vector=query_vector,
    top=3
)

print("\n🔍 Top Matches:\n")
for hit in results:
    p = hit.payload
    print(f"Product: {p['name']} | Score: {hit.score:.4f}")
    print(f"Description: {p['description']}\n")
```

---

### 🧪 Example Interaction

```
Enter your product search query: high-quality camera

🔍 Top Matches:

Product: Canon DSLR | Score: 0.9213  
Description: Professional digital camera for photography.

Product: Apple iPhone | Score: 0.8412  
Description: A powerful smartphone with great camera.
```

---

## ✅ Step 5: Clean Up (Optional)

```python
client.delete_collection("product_search")
```

---

## 📚 What You Learned

* How to load real-world product data from CSV
* How to convert descriptions into semantic vectors
* How to index and search with Qdrant using cosine similarity
* How to build a simple semantic search tool for e-commerce or product catalogs

---

Would you like to:

* Add category filters?
* Integrate with Streamlit for web UI?
* Deploy with Flask as a REST API?
