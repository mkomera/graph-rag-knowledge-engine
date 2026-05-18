# 🕸️ Graph RAG Knowledge Engine

A knowledge graph system with Graph RAG (Retrieval Augmented Generation) enabling natural language querying over millions of nodes and complex relationships. Combines Neo4j graph database with LLM-powered natural language interface for intelligent relationship discovery.

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![Neo4j](https://img.shields.io/badge/Neo4j-Graph_DB-green)
![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-orange)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                  Graph RAG Knowledge Engine                     │
├──────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌────────────┐    ┌─────────────────┐    ┌───────────────┐  │
│  │  Data      │───▶│  4-Phase ETL    │───▶│   Neo4j       │  │
│  │  Sources   │    │  Pipeline       │    │   Graph DB    │  │
│  │(SQL/Oracle)│    │  (Bulk Load)    │    │  (7.8M nodes) │  │
│  └────────────┘    └─────────────────┘    └───────┬───────┘  │
│                                                    │          │
│  ┌────────────┐    ┌─────────────────┐    ┌───────▼───────┐  │
│  │  Natural   │───▶│  Graph RAG      │───▶│  Cypher       │  │
│  │  Language  │    │  Engine         │    │  Generation   │  │
│  │  Query     │    │  (LLM + Graph)  │    │  + Traversal  │  │
│  └────────────┘    └─────────────────┘    └───────────────┘  │
│                                                                │
│  Scale: 7.8M nodes │ 28M relationships │ 14 relationship types│
└──────────────────────────────────────────────────────────────┘
```

---

## ✨ Features

- **Enterprise Scale** — 7.8M+ nodes, 28M+ relationships, processes in 3-5 minutes
- **Graph RAG** — Natural language queries translated to Cypher via LLM
- **4-Phase ETL** — Bulk loading with integrity validation and progress tracking
- **Multi-Source** — Oracle, SQL Server, PostgreSQL with thick client optimization
- **Relationship Discovery** — 14+ relationship types with referential integrity
- **Natural Language Interface** — Ask questions in plain English, get graph-powered answers
- **Batch Processing** — 25K record batches with transaction management
- **Business Intelligence** — Compliance checking, impact analysis, risk assessment

---

## 🚀 Quick Start

```bash
# Clone
git clone https://github.com/mkomera/graph-rag-knowledge-engine.git
cd graph-rag-knowledge-engine

# Start Neo4j
docker-compose up -d neo4j

# Install
pip install -r requirements.txt

# Configure
cp .env.example .env

# Load sample data
python -m etl.load_sample_data

# Run Graph RAG API
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

---

## 📁 Project Structure

```
graph-rag-knowledge-engine/
├── app/
│   ├── main.py                 # FastAPI application
│   ├── config.py               # Configuration
│   └── graph_rag/
│       ├── engine.py           # Graph RAG query engine
│       ├── cypher_generator.py # NL → Cypher translation via LLM
│       ├── graph_traversal.py  # Intelligent graph walking
│       ├── response_builder.py # Answer synthesis from graph results
│       └── schema_context.py   # Graph schema for LLM context
├── etl/
│   ├── pipeline.py             # 4-phase ETL orchestrator
│   ├── phase1_reference.py     # Reference/lookup data
│   ├── phase2_core.py          # Core connected entities
│   ├── phase3_relationships.py # Critical relationships
│   ├── phase4_additional.py    # Additional data & metadata
│   ├── bulk_loader.py          # Neo4j bulk operations (25K batches)
│   ├── source_connectors/
│   │   ├── oracle_connector.py # Oracle thick client (50K arraysize)
│   │   ├── sqlserver_connector.py
│   │   └── postgres_connector.py
│   └── validators/
│       ├── integrity_check.py  # Referential integrity validation
│       └── completeness.py     # Data completeness verification
├── graph_rag/
│   ├── handlers.py             # Query type handlers
│   ├── prompts/
│   │   ├── cypher_generation.py
│   │   └── answer_synthesis.py
│   └── examples/
│       └── sample_queries.py   # Example NL → Cypher mappings
├── tests/
├── docker-compose.yml
├── requirements.txt
└── README.md
```

---

## 💡 Usage Examples

### Natural Language Querying

```python
from app.graph_rag.engine import GraphRAGEngine

engine = GraphRAGEngine(neo4j_uri="bolt://localhost:7687")

# Simple relationship query
result = engine.query("What components are supplied by Vendor X?")
# → Generates Cypher: MATCH (v:Vendor {name:'X'})-[:SUPPLIES]->(c:Component) RETURN c

# Impact analysis
result = engine.query("If Supplier ABC has a disruption, which contracts are affected?")
# → Traverses: Supplier → Components → Assemblies → Contracts

# Compliance checking
result = engine.query("Which parts have export control restrictions for country Y?")
# → Traverses: Part → ExportControl → Country restrictions

print(result.answer)       # Natural language answer
print(result.cypher)       # Generated Cypher query
print(result.graph_data)   # Raw graph results
print(result.sources)      # Node/relationship references
```

### ETL Pipeline

```python
from etl.pipeline import GraphETLPipeline

pipeline = GraphETLPipeline(
    source="oracle",
    target_neo4j="bolt://localhost:7687",
    batch_size=25000
)

# Run full 4-phase load
pipeline.run(
    phases=[1, 2, 3, 4],
    on_progress=lambda phase, pct: print(f"Phase {phase}: {pct}%")
)

# Validate integrity
report = pipeline.validate()
print(f"Nodes: {report.node_count}")        # 7.8M+
print(f"Relationships: {report.rel_count}")  # 28M+
print(f"Integrity: {report.status}")         # PASS/FAIL
```

---

## 📊 Performance

| Metric | Value |
|--------|-------|
| Total Nodes | 7.8M+ |
| Total Relationships | 28M+ |
| Full ETL Load Time | 3-5 minutes |
| Batch Size | 25,000 records |
| Query Latency (simple) | < 500ms |
| Query Latency (multi-hop) | 1-3s |
| Concurrent Queries | 50+ |
| Source Array Size | 50K (Oracle thick client) |

---

## 🔧 Configuration

```env
# Neo4j
NEO4J_URI=bolt://localhost:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=<credential>

# Source Database
SOURCE_TYPE=oracle  # oracle | sqlserver | postgres
SOURCE_HOST=localhost
SOURCE_PORT=1521
SOURCE_DB=MYDB

# LLM for Graph RAG
LLM_PROVIDER=bedrock
LLM_MODEL=claude-3-sonnet

# ETL
BATCH_SIZE=25000
ARRAY_SIZE=50000  # Oracle fetch size
PHASES=1,2,3,4
```

---

## 🤝 Contributing

Contributions welcome! Areas of interest:
- Additional source database connectors
- New Graph RAG query patterns
- Performance optimizations for large graphs
- Visualization integrations

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.
