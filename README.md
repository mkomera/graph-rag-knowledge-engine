# Graph RAG Knowledge Engine

A knowledge graph system with Graph RAG (Retrieval Augmented Generation) enabling natural language querying over millions of nodes and complex relationships. Combines Neo4j graph database with LLM-powered natural language interface for intelligent relationship discovery.

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                  Graph RAG Knowledge Engine                     │
├──────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌────────────┐    ┌─────────────────┐    ┌───────────────┐  │
│  │  Data      │───>│  4-Phase ETL    │───>│   Neo4j       │  │
│  │  Sources   │    │  Pipeline       │    │   Graph DB    │  │
│  └────────────┘    └─────────────────┘    └───────┬───────┘  │
│                                                    │          │
│  ┌────────────┐    ┌─────────────────┐    ┌───────v───────┐  │
│  │  Natural   │───>│  Graph RAG      │───>│  Cypher       │  │
│  │  Language  │    │  Engine         │    │  Generation   │  │
│  │  Query     │    │  (LLM + Graph)  │    │  + Traversal  │  │
│  └────────────┘    └─────────────────┘    └───────────────┘  │
│                                                                │
│  Scale: 7.8M nodes | 28M relationships | 14 relationship types│
└──────────────────────────────────────────────────────────────┘
```

---

## Features

- Enterprise scale — 7.8M+ nodes, 28M+ relationships, processes in 3-5 minutes
- Graph RAG — natural language queries translated to Cypher via LLM
- 4-phase ETL pipeline with bulk loading, integrity validation, and progress tracking
- Multi-source support — Oracle, SQL Server, PostgreSQL with thick client optimization
- 14+ relationship types with referential integrity
- Natural language interface — ask questions in plain English, get graph-powered answers
- 25K record batch processing with transaction management
- Business intelligence — compliance checking, impact analysis, risk assessment

---

## Quick Start

```bash
git clone https://github.com/mkomera/graph-rag-knowledge-engine.git
cd graph-rag-knowledge-engine

docker-compose up -d neo4j

pip install -r requirements.txt
cp .env.example .env

python -m etl.load_sample_data

uvicorn app.main:app --host 0.0.0.0 --port 8000
```

---

## Project Structure

```
graph-rag-knowledge-engine/
├── app/
│   ├── main.py                 # FastAPI application
│   ├── config.py
│   └── graph_rag/
│       ├── engine.py           # Graph RAG query engine
│       ├── cypher_generator.py # NL to Cypher translation via LLM
│       ├── graph_traversal.py  # Intelligent graph walking
│       ├── response_builder.py # Answer synthesis from graph results
│       └── schema_context.py   # Graph schema for LLM context
├── etl/
│   ├── pipeline.py             # 4-phase ETL orchestrator
│   ├── phase1_reference.py     # Reference/lookup data
│   ├── phase2_core.py          # Core connected entities
│   ├── phase3_relationships.py # Critical relationships
│   ├── phase4_additional.py    # Additional data
│   ├── bulk_loader.py          # Neo4j bulk operations (25K batches)
│   └── source_connectors/
│       ├── oracle_connector.py
│       ├── sqlserver_connector.py
│       └── postgres_connector.py
├── tests/
├── docker-compose.yml
├── requirements.txt
└── README.md
```

---

## Usage

### Natural Language Querying

```python
from app.graph_rag.engine import GraphRAGEngine

engine = GraphRAGEngine(neo4j_uri="bolt://localhost:7687")

# Relationship query
result = engine.query("What components are supplied by Vendor X?")

# Impact analysis
result = engine.query("If Supplier ABC has a disruption, which contracts are affected?")

# Multi-hop traversal
result = engine.query("Which parts have export control restrictions for country Y?")

print(result.answer)       # Natural language answer
print(result.cypher)       # Generated Cypher query
print(result.graph_data)   # Raw graph results
```

### ETL Pipeline

```python
from etl.pipeline import GraphETLPipeline

pipeline = GraphETLPipeline(
    source="oracle",
    target_neo4j="bolt://localhost:7687",
    batch_size=25000
)

pipeline.run(
    phases=[1, 2, 3, 4],
    on_progress=lambda phase, pct: print(f"Phase {phase}: {pct}%")
)

report = pipeline.validate()
print(f"Nodes: {report.node_count}")        # 7.8M+
print(f"Relationships: {report.rel_count}")  # 28M+
```

---

## Performance

| Metric | Value |
|--------|-------|
| Total Nodes | 7.8M+ |
| Total Relationships | 28M+ |
| Full ETL Load Time | 3-5 minutes |
| Batch Size | 25,000 records |
| Query Latency (simple) | < 500ms |
| Query Latency (multi-hop) | 1-3s |

---

## License

MIT
