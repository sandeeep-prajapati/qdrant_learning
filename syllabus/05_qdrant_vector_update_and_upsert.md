
---

# 🔁 Project: Update or Upsert Vectors and Payloads in Qdrant using Pandas

---

## 📌 Objective

Build a Python utility to **update** (aka *upsert*) existing vectors and payloads in a Qdrant collection whenever a row in the DataFrame changes — such as modified text or a new vector.

---

## 🛠️ Tools Required

```bash
pip install pandas numpy qdrant-client
```

Make sure Qdrant is running:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## ✅ Step 1: Initial Dataset & Upload to Qdrant

```python
import pandas as pd
import numpy as np
from qdrant_client import QdrantClient

# Sample data
df = pd.DataFrame({
    "id": [1, 2],
    "name": ["apple", "banana"],
    "embedding": [np.random.rand(4).tolist(), np.random.rand(4).tolist()]
})

# Connect to Qdrant
client = QdrantClient(host="localhost", port=6333)

# Create or recreate collection
client.recreate_collection(
    collection_name="upsert_demo",
    vectors_config={"size": 4, "distance": "Cosine"}
)

# Upload initial data
client.upload_collection(
    collection_name="upsert_demo",
    vectors=df["embedding"].tolist(),
    payload=df.to_dict(orient="records"),
    ids=df["id"].tolist()
)
```

---

## ✅ Step 2: Simulate Update in DataFrame

```python
# Let's update 'banana' to 'mango' and change its vector
df.loc[df["id"] == 2, "name"] = "mango"
df.loc[df["id"] == 2, "embedding"] = [np.random.rand(4).tolist()]
```

---

## ✅ Step 3: Perform Upsert in Qdrant

```python
client.upsert(
    collection_name="upsert_demo",
    points=[
        {
            "id": int(row["id"]),
            "vector": row["embedding"],
            "payload": {"id": int(row["id"]), "name": row["name"]}
        }
        for _, row in df.iterrows()
    ]
)
```

---

## ✅ Step 4: Verify Update by Retrieving All Points

```python
result = client.scroll(collection_name="upsert_demo", limit=10)
for point in result[0]:
    print(f"ID: {point.id} | Name: {point.payload['name']}")
```

---

### ✅ Output (Example)

```
ID: 1 | Name: apple  
ID: 2 | Name: mango  
```

---

## 🧹 Optional: Clean Up Collection

```python
client.delete_collection("upsert_demo")
```

---

## 📚 What You Learned

* How to **update** existing vectors and payloads in Qdrant
* How to **sync Qdrant with your Pandas DataFrame**
* How to use the `upsert()` method in a loop-friendly way

---

Want to try:

* Auto-detecting differences between old & new data?
* Upsert based on CSV file or external input?
