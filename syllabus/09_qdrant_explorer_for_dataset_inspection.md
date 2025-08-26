
---

### 📁 `09_qdrant_explorer_for_dataset_inspection.md`

---

# 🧪 Project: Qdrant Data Inspector using Pandas & Matplotlib

---

## 📌 Objective

Build a tool that connects to Qdrant, retrieves stored vectors and payloads, and gives a quick **visual + tabular summary**, including:

* Label/category **distribution**
* **Vector dimension stats**
* **Missing value** detection in payloads

---

## 🛠️ Tools Required

```bash
pip install pandas matplotlib qdrant-client
```

Ensure Qdrant is running:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## ✅ Step 1: Connect and Retrieve All Vectors

```python
from qdrant_client import QdrantClient
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

COLLECTION = "product_search"

client = QdrantClient(host="localhost", port=6333)

# Retrieve all vectors + payloads
points, _ = client.scroll(collection_name=COLLECTION, limit=1000)

# Convert payloads and vectors to DataFrame
payloads = [point.payload for point in points]
vectors = [point.vector for point in points]

df_payload = pd.DataFrame(payloads)
df_vectors = pd.DataFrame(vectors, columns=[f"v{i}" for i in range(len(vectors[0]))])
df = pd.concat([df_payload, df_vectors], axis=1)
```

---

## ✅ Step 2: Visualize Label Distribution

```python
if "name" in df.columns:
    df["name"].value_counts().plot(kind="bar", figsize=(8, 4), title="Product Name Frequency")
    plt.xlabel("Product Name")
    plt.ylabel("Count")
    plt.tight_layout()
    plt.show()
```

---

## ✅ Step 3: Check for Missing Values

```python
print("🔍 Missing Values per Column:")
print(df.isnull().sum())

# Optional: visualize
df.isnull().sum().plot(kind="bar", title="Missing Values", figsize=(8, 4))
plt.tight_layout()
plt.show()
```

---

## ✅ Step 4: Check Vector Distribution (per-dimension)

```python
df_vectors.describe().T[["mean", "std", "min", "max"]].plot(kind="bar", figsize=(10, 5), title="Vector Dimension Stats")
plt.ylabel("Value")
plt.tight_layout()
plt.show()
```

---

## ✅ Step 5: Export to CSV for QA (Optional)

```python
df.to_csv("qdrant_export.csv", index=False)
print("✅ Data exported to qdrant_export.csv")
```

---

## 📚 What You Learned

* How to scroll through a full Qdrant collection
* How to merge vector + metadata for inspection
* How to find missing data and understand vector stats
* How to visualize label/category distribution using `matplotlib`

---

Let me know if you want to:

* Build a **Streamlit dashboard** from this
* Apply **filters** (e.g. only show category = “fruit”)
* Add **vector clustering** or **UMAP visualization**

