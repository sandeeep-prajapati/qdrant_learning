
---

# 🚀 Project: CSV to Qdrant Vector Uploader with Mock Embeddings

---

## 📌 Objective

Create a script that:

* Accepts any CSV file with a `text` column.
* Generates **4-dimensional mock embeddings** using NumPy.
* Uploads both vectors and payloads to a Qdrant collection.
* Enables quick similarity search via cosine distance.

---

## 🛠️ Tools Required

Install necessary packages:

```bash
pip install pandas numpy qdrant-client
```

Ensure Qdrant is running (locally or via Docker):

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## 📁 Script: `csv_to_qdrant_uploader.py`

```python
import pandas as pd
import numpy as np
from qdrant_client import QdrantClient
import sys
import os

# -------------------- CONFIG --------------------
CSV_PATH = sys.argv[1] if len(sys.argv) > 1 else "data.csv"
COLLECTION_NAME = "csv_vectors"
VECTOR_SIZE = 4
QDRANT_HOST = "localhost"
QDRANT_PORT = 6333
# ------------------------------------------------

# Step 1: Load CSV
if not os.path.exists(CSV_PATH):
    raise FileNotFoundError(f"CSV file not found: {CSV_PATH}")

df = pd.read_csv(CSV_PATH)

if "text" not in df.columns:
    raise ValueError("CSV must contain a 'text' column.")

# Step 2: Generate 4D mock embeddings
df["embedding"] = df["text"].apply(lambda x: np.random.rand(VECTOR_SIZE).tolist())

# Step 3: Connect to Qdrant
client = QdrantClient(host=QDRANT_HOST, port=QDRANT_PORT)

# Step 4: Create/Recreate collection
client.recreate_collection(
    collection_name=COLLECTION_NAME,
    vectors_config={"size": VECTOR_SIZE, "distance": "Cosine"}
)

# Step 5: Upload data to Qdrant
client.upload_collection(
    collection_name=COLLECTION_NAME,
    vectors=df["embedding"].tolist(),
    payload=df.to_dict(orient="records"),
    ids=list(range(len(df)))
)

print(f"✅ Successfully uploaded {len(df)} records to collection '{COLLECTION_NAME}'")
```

---

## 📁 Example Usage

Create a file named `data.csv`:

```csv
text
apple is red
banana is yellow
grapes are small
mango is juicy
```

Run the script:

```bash
python csv_to_qdrant_uploader.py data.csv
```

---

## ✅ Output

```
✅ Successfully uploaded 4 records to collection 'csv_vectors'
```

---

## 📚 What You Learned

* How to read CSV with Pandas.
* How to simulate embeddings for basic vector search.
* How to upload both vectors and payloads to Qdrant.
* How to make this process reusable for any CSV.

---

Let me know if you want to:

* Add real embeddings using `sentence-transformers`.
* Add a CLI query interface to search the collection.
* Extend this into a Streamlit web app.
