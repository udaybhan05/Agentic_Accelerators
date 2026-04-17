# L1 — Data Foundation Layer

[← Back to Wiki Home](README.md) | [← L0 Client Onboarding](04-L0-client-onboarding.md)

> **Status**: Detailed specification — this is one of the two priority layers with full scope definition.

---

## Purpose

The single biggest time sink when onboarding a new client is understanding their data — what tables exist, what they mean, how they relate, and whether the data quality is good enough for an agent to use reliably. This accelerator compresses that process from **weeks to days** (target: under 2 hours for connect + profile).

---

## Components

### L1.1 — Data Connector Library

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Library |
| **Reuse potential** | 95% — connectors are fully generic |

**Problem solved**: Every engagement writes custom ingestion code for databases, APIs, and file formats.

**Supported sources**:

| Category | Supported |
|---|---|
| **Relational databases** | PostgreSQL, MySQL, SQL Server, Snowflake, BigQuery, MongoDB |
| **File formats** | CSV, Excel, Parquet, JSON, XML |
| **API connectors** | REST (generic), GraphQL (generic) |

**Key capabilities**:
- Plug-and-play via YAML/JSON configuration — no code changes to connect a new source
- Standardized output format (unified schema representation) regardless of source
- Connection pooling, retry logic, credential management built-in

**Technical approach**:
- **SQLAlchemy** for relational databases (broad coverage)
- **Dedicated adapters** for Snowflake and BigQuery where SQLAlchemy has limitations
- All connections defined in YAML/JSON — adding a new data source is a config change, not a code change

**Inputs → Outputs**:
```
Connection config (YAML/JSON)  →  Normalized data objects or schema metadata
```

**Risk — Connector sprawl**: Start with the 4–5 most common databases in the client base. Add new connectors only when an actual engagement demands it.

---

### L1.2 — Schema Discovery & Data Profiling Engine

| | |
|---|---|
| **Priority** | **P0 (demo-critical)** |
| **Type** | Engine |
| **Reuse potential** | 90% — profiling logic is universal; report templates may need domain labeling |

**Problem solved**: Understanding a new client's data model (tables, columns, types, relationships, quality) takes days of manual exploration.

**Schema Discovery capabilities**:
- Auto-discover tables, columns, types, primary keys, foreign keys, indexes
- Relationship inference — detects relationships even when FKs are not explicitly defined
- Join-path discovery

**Data Profiling capabilities**:
- Statistical profiling per column: cardinality, null rates, value distributions, outlier detection
- Pattern recognition: identifies columns that look like emails, dates, phone numbers, etc.
- Data quality scoring across four dimensions:

| Dimension | What it measures |
|---|---|
| **Completeness** | Percentage of non-null, non-empty values |
| **Consistency** | Conformance to expected formats and patterns |
| **Accuracy** | Correctness of values relative to domain expectations |
| **Timeliness** | Freshness and recency of data |

**Quality report output**:
- Every quality finding includes:
  1. **What is wrong** — the specific issue
  2. **Why it matters for agents** — impact on agent performance
  3. **What to do about it** — actionable remediation steps
- Reports include "human review needed" flags for ambiguous findings

**Technical approach**:
- **SQL-based sampling** for large datasets (profile on a representative sample, not full table scans)
- **In-memory analysis** for smaller datasets and files
- Configurable sample sizes
- Option to scope profiling to specific schemas or tables
- **Pluggable quality rules** — ships with sensible defaults (null checks, uniqueness, referential integrity); teams add engagement-specific rules

**Inputs → Outputs**:
```
DB connection / data files
    → Schema map (tables, columns, relationships, types)
    → Profiling report (statistics per column)
    → Quality scorecard (4-dimension scores + specific findings)
    → All outputs in JSON (machine-readable) + Markdown (human-readable)
```

**Success criteria**:
1. New client database connected and profiled in under 2 hours
2. Schema map accurate enough to feed into the Knowledge Graph Assembler
3. Quality scorecard catches ≥80% of data issues that would affect agent performance

**Risks and mitigations**:

| Risk | Mitigation |
|---|---|
| False confidence from profiling | Reports always flag ambiguous findings for human review |
| Slow on large databases (500+ tables) | Sampling-based by default; configurable sample sizes; schema-filtered profiling |
| Quality scores without guidance | Every finding includes what, why, and remediation |

---

### L1.3 — Knowledge Graph Generation Assembler

| | |
|---|---|
| **Priority** | **P0 (demo-critical)** |
| **Type** | Tool |
| **Reuse potential** | 80% — generation logic reusable; output is engagement-specific |

**Problem solved**: Manually writing table/column descriptions, identifying patterns, and creating knowledge context is the biggest bottleneck in NL2SQL-type agents.

**This is a critical accelerator** because the quality of the knowledge graph directly determines the quality of the NL2SQL agent's query generation.

**How it works**:

```
┌────────────────┐   ┌──────────────────┐   ┌──────────────────┐
│  Denormalized  │   │  Business Context │   │   Sample Data    │
│  Schema        │ + │  Document         │ + │   (from L1.2)    │
└───────┬────────┘   └────────┬─────────┘   └────────┬─────────┘
        │                     │                       │
        └─────────────────────┼───────────────────────┘
                              │
                              ▼
                ┌──────────────────────────┐
                │  LLM-Assisted Generation │
                │  (table descriptions,    │
                │   column descriptions,   │
                │   relationship mapping)  │
                └────────────┬─────────────┘
                             │
                             ▼
                ┌──────────────────────────┐
                │  Pattern Discovery       │
                │  (query patterns,        │
                │   anti-patterns,         │
                │   one-shot NL→SQL pairs) │
                └────────────┬─────────────┘
                             │
                             ▼
                ┌──────────────────────────┐
                │  Interactive Preview     │
                │  View → Edit → Validate  │
                │  → Commit                │
                └────────────┬─────────────┘
                             │
                             ▼
                ┌──────────────────────────┐
                │  Export                   │
                │  JSON, YAML, Neo4j,      │
                │  Markdown                │
                └──────────────────────────┘
```

**Key capabilities**:
- LLM-assisted generation of table and column descriptions from schema + sample data
- Relationship mapping with business-meaningful labels
- Pattern and anti-pattern discovery (common query patterns, data access patterns)
- One-shot example generation (natural language → SQL pairs)
- **Interactive preview mode**: view, edit, validate, then commit
- Export formats: JSON, YAML, Neo4j-compatible, Markdown docs

**Inputs → Outputs**:
```
Denormalized schema + business context document + sample data
    → Enriched knowledge graph with descriptions, patterns, examples
```

**The interactive preview-edit-commit flow** is a critical design choice. Automated generation provides a strong starting point, but domain experts must be able to review and refine before the knowledge graph is committed for agent use.

---

### L1.4 — Vector Store Management Toolkit

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Toolkit |
| **Reuse potential** | 90% — pipeline is generic; chunking strategy tuning is per-engagement |

**Problem solved**: Setting up embedding pipelines, chunking strategies, and vector stores is repeated boilerplate across engagements.

**Key capabilities**:
- Configurable chunking strategies:
  - Fixed-size
  - Semantic
  - Recursive
  - Document-structure-aware
- Embedding model abstraction: OpenAI, Cohere, local models via Ollama
- Vector store adapters: Pinecone, Weaviate, Qdrant, ChromaDB, pgvector
- Index management: create, update, delete, versioning
- Metadata enrichment pipeline (auto-tag chunks with source, section, entity info)

**Inputs → Outputs**:
```
Documents/data + embedding config (YAML)  →  Populated, queryable vector store
```

---

### L1.5 — Synthetic & Sample Data Generator

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Tool |
| **Reuse potential** | 85% — generation logic is universal |

**Problem solved**: Development and testing stall when real client data is unavailable, restricted, or contains PII.

**Key capabilities**:
- Schema-aware synthetic data generation (respects types, constraints, distributions)
- Statistical profile matching (uses L1.2 output to mimic real data distributions)
- PII-free by design
- Configurable volume: 10 rows for dev, 10M rows for load testing
- Referential integrity preservation across related tables

**Inputs → Outputs**:
```
Schema + profiling report  →  Synthetic dataset (CSV, SQL inserts, or direct DB load)
```

---

## Scope Boundaries

| In Scope | Explicitly Out of Scope |
|---|---|
| Connecting to client data sources via config | Data migration, ETL, or transformation pipelines |
| Schema extraction and relationship inference | Data transformation or cleansing |
| Statistical profiling and quality scoring | Real-time streaming data ingestion |
| Reports with remediation recommendations | Building or managing a data warehouse |
| Structured data: databases and tabular files | Unstructured document parsing (separate RAG/retrieval concern in L2) |

---

## Internal Data Flow

```
L1.1 Data Connector Library
    │
    │  Raw connection to data sources
    ▼
L1.2 Schema Discovery & Data Profiling
    │
    ├──→ Schema map + Profiling report + Quality scorecard
    │        │
    │        ├──→ L1.3 Knowledge Graph Assembler (schema + profiles as input)
    │        ├──→ L1.5 Synthetic Data Generator (schema + profiles as input)
    │        └──→ Downstream consumers (JSON for machines, Markdown for humans)
    │
L1.3 Knowledge Graph Generation Assembler
    │
    └──→ Enriched knowledge graph → L3 Agent Framework (prompt context + few-shot examples)

L1.4 Vector Store Management Toolkit
    │
    └──→ Queryable vector store → L2 RAG Pipelines (retrieval backend)
```

---

## Success Criteria

This layer is "done enough" when:

1. A new client database can be connected and fully profiled in **under 2 hours** (not days)
2. The schema map is accurate enough to feed directly into the Knowledge Graph Assembler
3. The quality scorecard surfaces at least **80% of data issues** that would affect agent performance
4. The output formats (JSON + Markdown) are stable and consumed by at least one downstream accelerator
5. The knowledge graph is rich enough to enable an NL2SQL agent to generate correct queries

---

[Next: L2 — Knowledge & Retrieval →](06-L2-knowledge-retrieval.md)
