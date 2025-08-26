
---

# 🧹 Project: Deleting and Resetting Vector Data in Qdrant via Python

---

## 📌 Objective

Write reusable Python scripts to:

* Delete specific records by **point ID**
* Delete all records matching a **metadata filter**
* **Reset or delete** an entire collection

---

## 🛠️ Tools Required

```bash
pip install qdrant-client
```

Make sure Qdrant is running:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## ✅ Step 1: Connect to Qdrant

```python
from qdrant_client import QdrantClient

client = QdrantClient(host="localhost", port=6333)
COLLECTION_NAME = "demo_cleanup"
```

---

## ✅ Step 2: Delete a Specific Record by ID

```python
client.delete(
    collection_name=COLLECTION_NAME,
    points_selector=[2]  # example: delete point with ID = 2
)
print("✅ Deleted point with ID 2.")
```

---

## ✅ Step 3: Delete Records by Filter (e.g., `category == "test"`)

```python
from qdrant_client.http.models import Filter, FieldCondition, MatchValue

client.delete(
    collection_name=COLLECTION_NAME,
    points_selector=Filter(
        must=[
            FieldCondition(
                key="category", 
                match=MatchValue(value="test")
            )
        ]
    )
)
print("✅ Deleted all points with category = 'test'.")
```

---

## ✅ Step 4: Delete an Entire Collection (Reset All Data)

```python
client.delete_collection(COLLECTION_NAME)
print(f"✅ Deleted entire collection: {COLLECTION_NAME}")
```

---

## ✅ Step 5: Verify Remaining Records (Optional)

```python
points, _ = client.scroll(collection_name=COLLECTION_NAME, limit=100)
print(f"Remaining records in '{COLLECTION_NAME}': {len(points)}")
for p in points:
    print(p.id, p.payload)
```

---

## 📚 What You Learned

* How to delete Qdrant points using:

  * Direct **IDs**
  * Flexible **metadata filters**
  * Full **collection reset**
* How to verify remaining records with `scroll()`

---

Let me know if you want to build a:

* Web-based **record deletion dashboard**
* Automated **data cleanup script** for expired or temporary entries
* CLI tool for interactive collection management
