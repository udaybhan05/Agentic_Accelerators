# L2 — Knowledge & Retrieval Layer

[← Back to Wiki Home](README.md) | [← L1 Data Foundation](05-L1-data-foundation.md)

---

## Purpose

This layer provides the retrieval strategies and pipelines that sit between raw data and agents. When agents need to answer questions from unstructured documents or combine structured and unstructured knowledge, this layer provides the infrastructure.

---

## Components

### L2.1 — RAG Pipeline Templates

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Templates |
| **Reuse potential** | 85% — pipeline structure reusable; tuning is per-engagement |

**Problem solved**: Every RAG (Retrieval-Augmented Generation) implementation re-architects the same retrieve-augment-generate flow from scratch.

**Pipeline variants**:

| Variant | When to Use |
|---|---|
| **Naive RAG** | Simple use cases; baseline for comparison |
| **Sentence-window** | When surrounding context improves answer quality |
| **Auto-merging** | When related chunks should be combined before generation |
| **Parent-child retrieval** | When documents have hierarchical structure (sections, subsections) |

**Retrieval strategies**:
- **Hybrid retrieval**: Dense (vector) + Sparse (BM25) fusion
- **Graph-enhanced RAG**: Uses L1.3 knowledge graph for structured retrieval
- **Query transformation**: Decomposition, HyDE (Hypothetical Document Embeddings), step-back prompting

**Configuration**: All pipelines are configurable via YAML — swap retriever, generator, or re-ranker without code changes.

**Inputs → Outputs**:
```
User query + retrieval config  →  Retrieved context + generated answer with citations
```

---

### L2.2 — Re-ranking & Filtering Strategy Library

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Library |
| **Reuse potential** | 95% |

**Problem solved**: Initial retrieval returns noisy results; re-ranking logic is rebuilt every time.

**Available strategies**:

| Strategy | Description |
|---|---|
| **Cross-encoder re-ranking** | Using Cohere or local models for semantic re-ranking |
| **LLM-as-judge re-ranking** | LLM evaluates and reorders results by relevance |
| **Metadata-based filtering** | Filter by date, source, access level before re-ranking |
| **Diversity-aware re-ranking** | Reduce redundancy in returned results |

**Configuration options**:
- Configurable top-k (number of results to return)
- Score thresholds (minimum relevance score)
- Pluggable re-ranker modules

**Inputs → Outputs**:
```
Candidate results + re-rank config  →  Ordered, filtered results
```

---

### L2.3 — Document Processing Pipeline

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Pipeline |
| **Reuse potential** | 95% |

**Problem solved**: Every engagement writes custom parsers for PDFs, Word docs, slides, spreadsheets.

**Supported formats**:

| Format | Capabilities |
|---|---|
| **PDF** | Full text extraction with table extraction |
| **DOCX** | Structure-aware parsing |
| **PPTX** | Slide content extraction |
| **XLSX** | Tabular data extraction |
| **HTML** | Web content parsing |
| **Markdown** | Direct parsing |

**Key capabilities**:
- **Layout-aware parsing**: Headers, sections, tables, lists preserved as metadata
- **Image/chart extraction**: Description via multimodal LLM
- **Configurable output**: Plain text, structured JSON, or Markdown
- **Batch processing** with progress tracking

**Inputs → Outputs**:
```
Document files  →  Structured text with layout metadata
```

---

## How L2 Connects to Other Layers

```
L1.3 Knowledge Graph  ──→  L2.1 Graph-enhanced RAG
L1.4 Vector Store      ──→  L2.1 Vector retrieval backend
L2.3 Document Pipeline ──→  L1.4 Vector Store (processed docs → embeddings)
L2.1 RAG Pipelines     ──→  L3 Agent Framework (retrieval tool for agents)
L2.2 Re-ranking        ──→  L2.1 RAG Pipelines (post-retrieval quality)
```

---

## Scope Boundaries

| In Scope | Out of Scope |
|---|---|
| Pre-built RAG pipeline variants | Custom retriever model training |
| Hybrid retrieval (vector + sparse) | Real-time streaming retrieval |
| Document parsing for common formats | OCR for handwritten text |
| Configurable re-ranking modules | Training custom cross-encoders |
| Query transformation strategies | Custom embedding model training |

---

[Next: L3 — Agent Framework →](07-L3-agent-framework.md)
