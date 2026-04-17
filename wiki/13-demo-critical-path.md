# P0 Demo-Critical Path

[← Back to Wiki Home](README.md) | [← Data Flow & Integration](12-data-flow-and-integration.md)

---

## Purpose

This document defines the **6 accelerators** that must be built first for the analytics/NL2SQL demo agent. These are the **P0 (must-have)** items that form the minimum viable accelerator stack.

---

## The Demo Agent

The target is an **NL2SQL analytics agent** that:

1. Connects to a client's database
2. Understands the schema and data semantics automatically
3. Accepts natural language questions from business users
4. Translates questions into SQL queries and executes them safely
5. Returns formatted results — tables, charts, summary cards
6. Suggests follow-up questions for deeper exploration
7. Measures and reports its own quality

---

## P0 Accelerators (6 items)

### Execution Order

```
┌──────────────────────────────────────────────┐
│  Phase 1: DATA UNDERSTANDING                  │
│                                               │
│  1. L1.2 Schema Discovery & Data Profiling    │
│     "Understand client's data fast"           │
│                                               │
│  2. L1.3 Knowledge Graph Generation Assembler │
│     "Auto-generate context for NL2SQL"        │
│                                               │
└──────────────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────┐
│  Phase 2: AGENT ASSEMBLY                      │
│                                               │
│  3. L3.1 Base Agent Template Library          │
│     "NL2SQL agent template"                   │
│                                               │
│  4. L3.4 Tool & Function Registry             │
│     "SQL executor, chart generator"           │
│                                               │
└──────────────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────┐
│  Phase 3: OUTPUT & QUALITY                    │
│                                               │
│  5. L4.3 Response Format & Rendering Engine   │
│     "Tables, charts, cards"                   │
│                                               │
│  6. L5.1 Evaluation Framework                 │
│     "Prove agent quality"                     │
│                                               │
└──────────────────────────────────────────────┘
```

---

## Detailed P0 Breakdown

### 1. L1.2 — Schema Discovery & Data Profiling Engine

| | |
|---|---|
| **Why P0** | Without schema understanding, the agent cannot generate SQL. This is the foundation of everything. |
| **Demo deliverable** | Connect to a client database, auto-discover schema, produce profiling report in under 2 hours |
| **Key output** | Schema map (JSON) + Profiling report + Quality scorecard |
| **Feeds into** | L1.3 Knowledge Graph Assembler |

### 2. L1.3 — Knowledge Graph Generation Assembler

| | |
|---|---|
| **Why P0** | The knowledge graph is what makes the agent understand data semantics — table/column descriptions, patterns, and example queries. Without it, NL2SQL quality is poor. |
| **Demo deliverable** | Auto-generate enriched knowledge graph from schema + business context, with interactive preview-edit-commit |
| **Key output** | Enriched knowledge graph (JSON/YAML) with descriptions, patterns, NL→SQL examples |
| **Feeds into** | L3.1 Agent Template (prompt context), L3.3 Prompt Management (few-shot examples) |

### 3. L3.1 — Base Agent Template Library (NL2SQL template)

| | |
|---|---|
| **Why P0** | The NL2SQL agent template is the core product being demonstrated. It must include tool binding, memory, error handling, and output parsing. |
| **Demo deliverable** | NL2SQL agent assembled from YAML config in under a day |
| **Key output** | Runnable agent instance that converts natural language → SQL → results |
| **Feeds into** | L4.3 Response Rendering (formats output), L5.1 Evaluation (measures quality) |

### 4. L3.4 — Tool & Function Registry

| | |
|---|---|
| **Why P0** | The SQL executor and chart generator are the two tools the NL2SQL agent absolutely needs. Without them, the agent cannot execute queries or visualize results. |
| **Demo deliverable** | SQL executor with safety checks (SELECT-only default) + chart generator (Plotly/Vega) |
| **Key output** | Registered tools available to the agent |
| **Critical safety note** | SQL executor must default to SELECT-only allowlist. Destructive operations require explicit opt-in. |

### 5. L4.3 — Response Format & Rendering Engine

| | |
|---|---|
| **Why P0** | Raw agent output is unusable for business users. The demo impact depends on beautiful, clear formatting — tables, charts, summary cards. |
| **Demo deliverable** | Auto-formatted responses: single values → cards, tabular → tables, time series → charts |
| **Key output** | Rendered responses with follow-up drill-down links |
| **Demo impact** | This is the most visible accelerator. Poor rendering = poor demo impression regardless of agent quality. |

### 6. L5.1 — Evaluation Framework

| | |
|---|---|
| **Why P0** | The ability to prove agent quality is what separates a demo from a product. Evaluation was explicitly called out as the highest-impact layer that teams most commonly skip. |
| **Demo deliverable** | Quality scores for NL2SQL agent: execution accuracy, result-set match, valid SQL |
| **Key output** | Evaluation report with scores, failure analysis |
| **Critical principle** | "If you can't measure it, don't ship it." |

---

## Demo Flow (User Perspective)

```
Step 1: Connect to database
        "Here's our PostgreSQL connection string"
        → System auto-discovers 47 tables, 312 columns
        → Profiling report shows 3 data quality issues

Step 2: Generate knowledge graph
        "Here's our business context document"
        → System generates descriptions, patterns, examples
        → Team reviews and edits in preview mode
        → Commits the knowledge graph

Step 3: Agent is ready
        → NL2SQL agent assembled from config
        → Connected to knowledge graph, SQL executor, chart generator

Step 4: User asks questions
        "What were total sales by region last quarter?"
        → Agent generates SQL
        → Executes safely (SELECT only)
        → Returns formatted table + bar chart
        → Suggests: "Which region grew fastest?" / "Compare to previous year?"

Step 5: Quality is measurable
        → Evaluation report shows 87% execution accuracy
        → Regression suite runs on every prompt change
        → Failure analysis shows which query types need improvement
```

---

## P0 Dependencies

```
L1.2 Schema Discovery  ← No dependencies (connects directly to database)
       │
       ▼
L1.3 Knowledge Graph   ← Depends on L1.2 output (schema + profiles)
       │
       ▼
L3.1 Agent Template    ← Depends on L1.3 (knowledge graph as context)
       │                ← Depends on L3.4 (tools to bind)
       │
L3.4 Tool Registry     ← No upstream dependency (self-contained tools)
       │
       ▼
L4.3 Response Rendering ← Depends on L3.1 (formats agent output)
       │
L5.1 Evaluation         ← Depends on L3.1 (evaluates agent output)
```

**Parallel opportunities**:
- L1.2 and L3.4 can be built in parallel (no mutual dependency)
- L4.3 and L5.1 can be built in parallel (both consume agent output independently)
- L1.3 and L3.1 have a sequential dependency (KG must exist before agent template is fully functional)

---

## Success Criteria for Demo

| Criterion | Target |
|---|---|
| Database connection + profiling time | < 2 hours |
| Knowledge graph generation | Auto-generated, reviewed and committed in < 4 hours |
| Agent assembly from config | < 1 day |
| SQL execution accuracy | ≥ 85% on test suite |
| Response rendering quality | Tables, charts, and cards render correctly |
| Evaluation coverage | All queries in test suite scored and reported |

---

## What is NOT in P0

These are important but deliberately deferred:

| Accelerator | Why deferred |
|---|---|
| L0.2 Kickoff Wizard | Automation is nice but not demo-critical |
| L1.1 Data Connectors (full library) | Only need 1-2 for demo |
| L2.1 RAG Pipelines | NL2SQL doesn't need unstructured document retrieval |
| L3.2 Agent Orchestrator | Demo is single-agent, not multi-agent |
| L3.5 LLM Gateway | Single provider is fine for demo |
| L4.5 UI Component Library (full) | Basic chat component is enough |
| L5.2-L5.5 (advanced testing) | Basic evaluation framework is sufficient for demo |
| L6.* (Observability) | Not visible in demo |
| L7.* (Security) | Not the focus of demo (though SQL safety checks are in L3.4) |

---

[Next: Technical Decisions & Rationale →](14-technical-decisions.md)
