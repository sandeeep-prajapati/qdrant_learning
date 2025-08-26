
---

## 🧠 Qdrant Cookbook: Project-Style Prompt Titles

---

### **01_build_vector_search_engine.md**

Use Qdrant and Pandas to build a simple vector search engine that indexes fruit names with randomly generated embeddings. Perform similarity search queries using cosine distance.

---

### **02_pandas_to_qdrant_uploader.md**

Create a script that converts any CSV file with a `text` column into 4D mock embeddings using NumPy and uploads the data to Qdrant for similarity search.

---

### **03_search_similar_items_with_filters.md**

Perform hybrid search in Qdrant by combining vector similarity with metadata filters (e.g., retrieve items similar to a query vector *and* tagged with `type = "vehicle"`).

---

### **04_indexing_text_embeddings_with_qdrant.md**

Use Sentence Transformers to convert text data into real embeddings and index them in Qdrant. Perform semantic search on short descriptions from a Pandas DataFrame.

---

### **05_qdrant_vector_update_and_upsert.md**

Build a Python utility to update (upsert) existing vectors and payloads in Qdrant based on changes in a Pandas DataFrame row (e.g., modify text or replace vector).

---

### **06_building_a_fast_vector_browser.md**

Create a script that retrieves top-N similar vectors from Qdrant and displays the results with `score`, `payload`, and optional chart visualization using `matplotlib`.

---

### **07_delete_and_clean_vector_data.md**

Write scripts to delete specific records by ID or filter (e.g., delete all vectors with `category = "test"`). Clean and reset Qdrant collections from Python.

---

### **08_vectorize_csv_and_build_semantic_search.md**

Load a CSV file with product descriptions, generate embeddings, and store in Qdrant. Then, allow users to input a query and retrieve semantically similar products.

---

### **09_qdrant_explorer_for_dataset_inspection.md**

Build a Pandas + Qdrant-based tool that retrieves vectors and visualizes the payload (e.g., label distribution, vector dimensions, missing values) for QA or inspection.

---

### **10_create_custom_vector_collections.md**

Build a CLI tool to create and manage multiple Qdrant collections from different datasets with user-defined vector size, distance metric, and payload schema.

---
