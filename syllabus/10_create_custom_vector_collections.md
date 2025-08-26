
---

# 🧰 Project: CLI Tool to Create & Manage Multiple Qdrant Collections

---

## 📌 Objective

Build a **Python-based CLI** that allows you to:

* Create Qdrant collections from **different datasets**
* Specify **vector size**, **distance metric**, and **payload fields**
* List all collections
* Delete specific collections
* Extend easily for future automation and dataset ingestion

---

## 🛠️ Tools Required

```bash
pip install qdrant-client click
```

Run Qdrant locally:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

---

## 🧪 CLI Script: `qdrant_manager.py`

```python
import click
from qdrant_client import QdrantClient

# Connect to Qdrant
client = QdrantClient(host="localhost", port=6333)


@click.group()
def cli():
    """📦 Qdrant Collection Manager CLI"""
    pass


@cli.command()
@click.argument("name")
@click.option("--size", default=4, help="Vector size (default: 4)")
@click.option("--distance", default="Cosine", type=click.Choice(["Cosine", "Dot", "Euclid"]),
              help="Distance metric")
def create(name, size, distance):
    """Create a new Qdrant collection"""
    client.recreate_collection(
        collection_name=name,
        vectors_config={"size": size, "distance": distance}
    )
    click.echo(f"✅ Collection '{name}' created with size={size}, distance={distance}")


@cli.command()
def list_collections():
    """List all Qdrant collections"""
    collections = client.get_collections().collections
    if not collections:
        click.echo("❌ No collections found.")
        return

    click.echo("📂 Available Collections:")
    for col in collections:
        click.echo(f" - {col.name}")


@cli.command()
@click.argument("name")
def delete(name):
    """Delete a Qdrant collection"""
    client.delete_collection(name)
    click.echo(f"🗑️  Collection '{name}' deleted.")


@cli.command()
@click.argument("name")
@click.option("--limit", default=10)
def preview(name, limit):
    """Preview contents of a collection"""
    points, _ = client.scroll(collection_name=name, limit=limit)
    if not points:
        click.echo("🔍 No data found.")
        return
    for point in points:
        click.echo(f"ID: {point.id} | Payload: {point.payload}")


if __name__ == "__main__":
    cli()
```

---

## ✅ Example Commands

### 🔧 Create a Collection

```bash
python qdrant_manager.py create movies --size 384 --distance Cosine
```

### 📂 List All Collections

```bash
python qdrant_manager.py list-collections
```

### 🗑️ Delete a Collection

```bash
python qdrant_manager.py delete movies
```

### 🔍 Preview Points in a Collection

```bash
python qdrant_manager.py preview movies --limit 5
```

---

## 📚 What You Built

* A **modular CLI** to manage vector collections in Qdrant
* Support for:

  * Custom **vector sizes**
  * Configurable **distance metrics**
  * Multiple datasets or projects
* Reusable for future automation or dataset onboarding

---

Let me know if you want to:

* Add `upload-csv` functionality to this CLI
* Auto-detect vector size from embedding files
* Build a `streamlit` version of this tool with UI

