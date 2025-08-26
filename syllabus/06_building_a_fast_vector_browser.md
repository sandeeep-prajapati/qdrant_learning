
---

# 🔎 Project: Qdrant Vector Search Viewer with `matplotlib` Visualization

---

## 📌 Objective

Build a Python script that:

* Connects to a Qdrant collection
* Performs **top-N similarity search** based on a query vector
* Displays the **`score`**, **`payload`**, and visualizes the scores using `matplotlib`

---

## 🛠️ Tools Required

```bash
pip install numpy pandas qdrant-client matplotlib
```

Make sure Qdrant is running locally:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## 🧪 Script: `qdrant_vector_browser.py`

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from qdrant_client import QdrantClient

# Configuration
COLLECTION_NAME = "demo_collection"
TOP_K = 5
VECTOR_SIZE = 4

# Step 1: Connect to Qdrant
client = QdrantClient(host="localhost", port=6333)

# Step 2: Generate a mock query vector
query_vector = np.random.rand(VECTOR_SIZE).tolist()

# Step 3: Perform similarity search
results = client.search(
    collection_name=COLLECTION_NAME,
    query_vector=query_vector,
    top=TOP_K
)

# Step 4: Collect and display results
names = []
scores = []
payloads = []

print("\n📌 Top-N Similar Results:\n")
for point in results:
    payload = point.payload
    score = point.score
    name = payload.get("name", "unknown")
    
    names.append(name)
    scores.append(score)
    payloads.append(payload)
    
    print(f"Name: {name} | Score: {score:.4f} | Payload: {payload}")

# Step 5: Visualize scores with matplotlib
plt.figure(figsize=(8, 5))
bars = plt.barh(names, scores)
plt.xlabel("Similarity Score")
plt.title(f"Top {TOP_K} Similar Items")
plt.gca().invert_yaxis()  # Highest score on top

for bar in bars:
    plt.text(bar.get_width(), bar.get_y() + 0.4, f"{bar.get_width():.2f}")

plt.tight_layout()
plt.show()
```

---

## 🧪 Sample Output

```
📌 Top-N Similar Results:

Name: banana | Score: 0.9571 | Payload: {'name': 'banana', 'type': 'fruit'}
Name: mango  | Score: 0.9234 | Payload: {'name': 'mango', 'type': 'fruit'}
...
```

### 📊 Visualization:

A horizontal bar chart showing the most similar items sorted by similarity score.

---

## 🧹 Optional Clean-Up

```python
client.delete_collection("demo_collection")
```

---

## 📚 What You Learned

* How to perform top-N similarity search in Qdrant
* How to access and interpret `payload` and `score`
* How to visualize similarity results using `matplotlib`

---

Let me know if you want to:

* Add **real sentence embeddings** using `sentence-transformers`
* Export results to **CSV or Excel**
* Add a **Streamlit UI** to make it interactive
