# Data Flow & Integration Patterns

[← Back to Wiki Home](README.md) | [← L7 Security & Governance](11-L7-security-governance.md)

---

## Purpose

This document maps how data flows through the entire accelerator stack, how layers depend on each other, and what the integration patterns look like. It serves as the single reference for understanding **what connects to what** and **what data passes between components**.

---

## End-to-End Data Flow (NL2SQL Analytics Agent)

This is the complete flow for the primary use case — an analytics agent that answers natural language questions by generating and executing SQL queries.

```
                    ┌─────────────────────────────┐
                    │     CLIENT DATABASE(S)        │
                    │  (PostgreSQL, Snowflake, etc.) │
                    └──────────────┬──────────────┘
                                   │
           ┌───────────────────────┼───────────────────────┐
           │                       │                       │
           ▼                       ▼                       ▼
    ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
    │ L1.1 Data    │     │ L0.1 Data    │     │ L1.5 Synthetic│
    │ Connector    │     │ Readiness    │     │ Data Gen     │
    │ Library      │     │ Assessment   │     │ (if no real  │
    │              │     │ Playbook     │     │  data avail) │
    └──────┬───────┘     └──────┬───────┘     └──────┬───────┘
           │                    │                     │
           │              Readiness Grade             │
           │              + Gap Report                │
           │                    │                     │
           ▼                    ▼                     │
    ┌──────────────────────────────────────┐          │
    │ L1.2 Schema Discovery & Profiling    │◄─────────┘
    │                                       │
    │  Outputs:                             │
    │  • Schema map (tables, columns, FKs)  │
    │  • Profiling report (stats per col)   │
    │  • Quality scorecard (4 dimensions)   │
    │  • JSON (machine) + MD (human)        │
    └──────────────┬───────────────────────┘
                   │
           ┌───────┴───────┐
           │               │
           ▼               ▼
    ┌──────────────┐  ┌──────────────────────┐
    │ L1.3 Knowledge│  │ L1.4 Vector Store    │
    │ Graph Gen    │  │ Management           │
    │ Assembler    │  │ (if RAG needed)      │
    │              │  └──────────┬───────────┘
    │ + Business   │             │
    │   Context    │             ▼
    │ + Sample     │  ┌──────────────────────┐
    │   Data       │  │ L2.1 RAG Pipelines   │
    │              │  │ (for unstructured     │
    │ Outputs:     │  │  document retrieval)  │
    │ • Table desc │  └──────────┬───────────┘
    │ • Column desc│             │
    │ • Patterns   │             │
    │ • NL→SQL     │             │
    │   examples   │             │
    └──────┬───────┘             │
           │                     │
           └─────────┬───────────┘
                     │
                     ▼
    ┌──────────────────────────────────────┐
    │ L3 AGENT FRAMEWORK                    │
    │                                       │
    │  ┌───────────┐   ┌────────────────┐  │
    │  │ L3.1 Agent│   │ L3.3 Prompt    │  │
    │  │ Template  │◄──│ Management     │  │
    │  │ (NL2SQL)  │   │ (versioned     │  │
    │  └─────┬─────┘   │  templates)    │  │
    │        │          └────────────────┘  │
    │        │                              │
    │  ┌─────┴─────┐   ┌────────────────┐  │
    │  │ L3.4 Tool │   │ L3.5 LLM      │  │
    │  │ Registry  │   │ Gateway        │  │
    │  │ • SQL Exec│   │ • Model routing│  │
    │  │ • Chart   │   │ • Fallback     │  │
    │  │ • API     │   │ • Cost track   │  │
    │  └───────────┘   └────────────────┘  │
    │                                       │
    │  ┌────────────────┐                   │
    │  │ L3.6 Memory    │                   │
    │  │ • Buffer/Summary│                  │
    │  │ • Redis/PG     │                   │
    │  └────────────────┘                   │
    └──────────────┬───────────────────────┘
                   │
                   │  Runnable Agent Instance
                   │
           ┌───────┴───────┐
           │               │
           ▼               ▼
    ┌──────────────┐  ┌──────────────────────┐
    │ L4 CONV & UX │  │ L5 TESTING & EVAL    │
    │              │  │                       │
    │ • Behavior   │  │ • Eval Framework      │
    │   Config     │  │ • Test Generation     │
    │ • Response   │  │ • Regression Suite    │
    │   Rendering  │  │ • User Simulation     │
    │ • Follow-ups │  │                       │
    │ • UI Comps   │  │                       │
    └──────┬───────┘  └───────────────────────┘
           │
           ▼
    ┌──────────────────────────────────────┐
    │ END USER                              │
    │ "What were total sales last quarter?" │
    └──────────────────────────────────────┘
```

---

## Layer Dependency Matrix

This matrix shows which layers produce outputs consumed by other layers.

| Producer → | L0 | L1 | L2 | L3 | L4 | L5 | L6 | L7 |
|---|---|---|---|---|---|---|---|---|
| **L0 Onboarding** | — | Readiness grade constrains data work | — | Architecture decisions guide agent design | — | — | — | — |
| **L1 Data** | — | — | Vectors → RAG pipelines | Schema + KG → prompts, context | — | KG → test case generation | — | — |
| **L2 Retrieval** | — | — | — | Retrieved context → agent input | — | — | — | — |
| **L3 Agent** | — | — | — | — | Agent output → rendering | Agent output → evaluation | Execution → logs/traces | I/O → guardrails |
| **L4 UX** | — | — | — | Follow-ups → agent queries | — | — | — | — |
| **L5 Testing** | — | — | — | Quality signals → agent tuning | — | — | — | — |
| **L6 DevOps** | — | — | — | — | — | CI/CD → regression runs | — | Logs → audit trail |
| **L7 Security** | — | — | — | Access filters → agent scope | — | — | — | — |

---

## Data Format Standards

All inter-layer communication uses standardized formats:

| Data | Machine Format | Human Format |
|---|---|---|
| Schema maps | JSON | Markdown tables |
| Profiling reports | JSON | Markdown with statistics |
| Quality scorecards | JSON | Markdown with Red/Amber/Green indicators |
| Knowledge graphs | JSON / YAML / Neo4j | Markdown documentation |
| Agent configs | YAML / JSON | — |
| Prompt templates | Jinja2 files | — |
| Evaluation results | JSON | Dashboard / Markdown report |
| Audit logs | JSON | CSV export |

---

## Integration Patterns

### Pattern 1: Config-Driven Assembly

The most common pattern. Components are assembled by reading configuration files.

```
YAML Config  →  Framework  →  Runtime Instance
```

Used by: Agent Templates, Data Connectors, Conversation Behavior, Guardrails

### Pattern 2: Pipeline Processing

Sequential transformation of data through multiple stages.

```
Input  →  Stage 1  →  Stage 2  →  ...  →  Output
```

Used by: Document Processing, RAG Pipelines, Data Profiling

### Pattern 3: Registry Lookup

Components discover and bind to other components via a registry.

```
Registry.find("sql_executor")  →  Tool Instance
```

Used by: Tool Registry, Prompt Store, Embedding Model Selection

### Pattern 4: Event-Driven Logging

Components emit structured events that are captured by the observability layer.

```
Component  →  emit(event)  →  Logger  →  Storage
```

Used by: All components → L6.1 Logging, L6.2 Cost Tracking, L7.3 Audit Logger

---

## Critical Integration Points

These are the integration points where issues most commonly arise:

| Integration | Producer | Consumer | Risk | Mitigation |
|---|---|---|---|---|
| Schema → Knowledge Graph | L1.2 | L1.3 | Schema format incompatibility | Standardized JSON schema output |
| Knowledge Graph → Prompts | L1.3 | L3.3 | KG too large for context window | Selective context loading based on query |
| Agent Output → Response Rendering | L3 | L4.3 | Unstructured output hard to format | Structured output parsing in agent template |
| Agent Output → Evaluation | L3 | L5.1 | Non-deterministic outputs hard to evaluate | Fuzzy matching + LLM-as-judge |
| LLM Calls → Cost Tracking | L3.5 | L6.2 | Token counting inconsistencies across providers | Provider-specific token counting adapters |

---

## Parallel Development Strategy

All layers are designed for parallel development. The key interfaces between layers are:

```
Layer boundaries = well-defined JSON/YAML contracts

Team A works on L1 (Data Foundation)
Team B works on L3 (Agent Framework)
Team C works on L4 (Conversation & UX)

Integration point: L1 produces JSON schema maps
                   L3 consumes JSON schema maps
                   Contract is defined upfront → teams work independently
```

This was an explicit design goal: with sufficient resources, all phases can be worked on in parallel because all are independent of each other.

---

[Next: P0 Demo-Critical Path →](13-demo-critical-path.md)
