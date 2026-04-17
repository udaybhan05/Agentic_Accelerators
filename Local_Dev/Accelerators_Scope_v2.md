# Agentic AI Application Accelerators

> **Objective**: New client engagements shouldn't start from zero. These accelerators give teams a head start with pre-built, configurable components that cut delivery effort by 30–40%.
>
> **What this covers**: The full landscape of accelerator areas at headline level, plus a detailed scope for the two we're building first — Data Layer and Agent Framework.
>
> **Status**: v0.1 — Internal alignment

---

## Design Principles

We've seen accelerator initiatives fail in predictable ways. These principles exist because of those failures:

| What typically goes wrong | The principle we follow |
|---|---|
| The accelerator becomes a "platform" nobody asked for | **Composable blocks over monolith.** Each piece is independently adoptable. If a team doesn't need it, they skip it. |
| Abstractions get so thick nothing is usable on real projects | **Solve a real problem on the current engagement.** If a component isn't useful this sprint, it's not ready. |
| Hard-coded assumptions break on the next client | **Config-driven.** Behavior lives in YAML/JSON, not in code. |
| Nobody writes tests because "it's a chatbot" | **Measure it or don't ship it.** Evaluation is built in, not bolted on later. |
| Accelerators decay because they were built in a vacuum | **Extract from real work.** We pull patterns from working implementations. Theory-first accelerators rot. |
| A 2-week piece of work balloons into 2 months | **Narrow first.** Build for the current use case. Generalize when a second use case actually shows up. |

---

## Accelerator Landscape

Eight areas, spanning the full lifecycle. **Two are detailed below; the rest are headline-level for now.**

```
┌─────────────────────────────────────────────────────────────────┐
│              CLIENT ONBOARDING & PLAYBOOKS                      │
│         (Assessment, decision playbooks, kickoff guides)        │
├─────────────────────────────────────────────────────────────────┤
│          ★ DATA LAYER ACCELERATOR  [DETAILED BELOW]             │
│      (Connectors, profiling, quality, data preparation)         │
├─────────────────────────────────────────────────────────────────┤
│              KNOWLEDGE GRAPH GENERATION ASSEMBLER               │
│       (Schema → enriched graph with descriptions & examples)    │
├─────────────────────────────────────────────────────────────────┤
│          ★ AGENT FRAMEWORK ACCELERATOR  [DETAILED BELOW]        │
│     (Templates, orchestration, prompts, tools, LLM routing)     │
├─────────────────────────────────────────────────────────────────┤
│              CONVERSATION & UX ACCELERATOR                      │
│    (Behavior config, response formats, follow-ups, UI kit)      │
├─────────────────────────────────────────────────────────────────┤
│              TESTING & EVALUATION FRAMEWORK                     │
│       (Eval metrics, test generation, regression, simulation)   │
├─────────────────────────────────────────────────────────────────┤
│              OBSERVABILITY & DEVOPS                              │
│         (Logging, tracing, CI/CD, cost tracking)                │
├─────────────────────────────────────────────────────────────────┘
│              SECURITY & GOVERNANCE                              │
│         (Guardrails, auth/RBAC, audit, compliance)              │
└─────────────────────────────────────────────────────────────────┘
```

### Summary

| # | Accelerator | Type | Status | What it gives you |
|---|---|---|---|---|
| 1 | **Client Onboarding & Playbooks** | Playbook + Tool | Planned | Data readiness assessment, architecture decision guides, project scaffolding for new engagements |
| 2 | **Data Layer Accelerator** | Technical | **Detailed below** | Fast ingestion, profiling, and quality assessment so agents start with solid data |
| 3 | **Knowledge Graph Generation Assembler** | Technical | Planned | Takes schema + context + sample data and produces enriched knowledge graphs with descriptions, patterns, and one-shot examples. Feeds directly into NL2SQL agents |
| 4 | **Agent Framework Accelerator** | Technical | **Detailed below** | Building blocks for single and multi-agent systems: templates, orchestration, prompt management, tool registry, LLM abstraction |
| 5 | **Conversation & UX Accelerator** | Technical + Playbook | Planned | Config-driven conversation behavior, follow-up engine, conversation design playbook, UI component library |
| 6 | **Testing & Evaluation Framework** | Technical | Planned | Eval metrics, automated test generation, regression suites, user simulation |
| 7 | **Observability & DevOps** | Technical | Future | Structured logging, trace propagation, LLM cost tracking, CI/CD templates |
| 8 | **Security & Governance** | Technical | Future | Input/output guardrails, RBAC patterns, audit trails |

---

## DETAILED: Data Layer Accelerator

### The Problem

Understanding a new client's data takes weeks. What tables exist, what they actually mean, how they relate, whether the quality is good enough for agents to use reliably. This accelerator compresses that.

### What It Includes

| Component | What it does |
|---|---|
| **Data Connector Library** | Config-driven connectors for common databases (Postgres, MySQL, SQL Server, Snowflake, BigQuery, MongoDB) and file formats (CSV, Excel, Parquet, JSON). One YAML file to connect, standardized output regardless of source. |
| **Schema Discovery Engine** | Auto-introspects the database: tables, columns, types, PKs, FKs, indexes. Also infers relationships even when foreign keys aren't explicitly defined. |
| **Data Profiling Engine** | Statistical analysis of actual data: cardinality, null rates, distributions, outlier detection, pattern recognition (identifies columns that look like emails, dates, phone numbers, etc.) |
| **Data Quality Assessor** | Scores data across four dimensions — completeness, consistency, accuracy, timeliness. Produces a scorecard with specific findings and remediation steps. |

### What Goes In / What Comes Out

**Inputs**: Database connection config (YAML/JSON) or file paths (CSV, Excel, Parquet, JSON)

**Outputs**:
- Schema map — tables, columns, relationships, types
- Profiling report — statistics per column
- Quality scorecard — 4-dimension scores with specific findings
- All outputs in both JSON (for downstream consumption) and markdown (for human review)

### Scope

**In scope:**
- Connecting to client data sources via config
- Schema extraction and relationship inference
- Statistical profiling and quality scoring
- Reports with remediation recommendations
- Structured data: databases and tabular files

**Out of scope:**
- Data migration, ETL, or transformation pipelines
- Real-time streaming ingestion
- Data warehouse management
- Unstructured document parsing (separate RAG/retrieval concern)

### Technical Direction

- All connections defined in YAML/JSON. No code changes to add a new source.
- SQLAlchemy for relational databases; dedicated adapters for Snowflake and BigQuery where SQLAlchemy has gaps.
- Profiling uses SQL-based sampling for large datasets. Full scans only for smaller datasets/files.
- Quality rules are pluggable. Ships with defaults (null checks, uniqueness, referential integrity). Teams can add engagement-specific rules.
- Dual output: JSON for consumption by downstream accelerators, markdown for human review.

### Risks

| What could break | What we do about it |
|---|---|
| **Connector sprawl** — supporting every database becomes a burden | Start with 4-5 most common ones. Add new connectors when a real engagement needs them. |
| **Misleading profiles** — automated profiling can miss semantic issues (e.g., inconsistent string values in a "status" column) | Reports include "needs human review" flags for ambiguous findings. Profiling supports decision-making; it doesn't replace domain understanding. |
| **Slow on large databases** — profiling 500+ tables with billions of rows | Sampling-based by default. Configurable sample sizes. Option to scope to specific schemas/tables. |
| **Scores without guidance** — "completeness: 72%" tells you nothing actionable | Every finding includes what's wrong, why it affects agents, and suggested remediation. |

### When Is It Done

- New client database connected and profiled in under 2 hours
- Schema map accurate enough to feed into the Knowledge Graph Assembler
- Quality scorecard catches the majority of data issues that would hurt agent performance
- JSON + markdown outputs are stable and at least one downstream accelerator consumes them

---

## DETAILED: Agent Framework Accelerator

### The Problem

Building agents from scratch means re-solving the same problems every time: tool wiring, prompt management, error handling, model routing, memory, multi-agent coordination. The framework provides reusable pieces so teams focus on business logic.

### What It Includes

| Component | What it does |
|---|---|
| **Base Agent Templates** | Pre-wired templates for common patterns: NL2SQL, Conversational QA, Plan-and-Execute, ReAct, Workflow. Each includes tool binding, output parsing, error handling, and logging hooks. NL2SQL is the immediate priority. |
| **Agent Orchestrator** | Patterns for multi-agent coordination: sequential, parallel, hierarchical (supervisor), and router-based (intent classification → agent routing). Includes state management and handoff protocols. Optional — single-agent setups don't need it. |
| **Prompt Management** | File-based prompt registry with versioning, Jinja2 templating, few-shot example banks. Prompts live in a structured directory, tracked in git. No database dependency. Can be used independently of the rest of the framework. |
| **Tool & Function Registry** | Cataloged, tested tool functions: SQL executor (with query safety checks), chart generator, REST API caller, sandboxed code executor, file reader/writer. Standardized interface: name, description, input schema, output schema, execute. |
| **LLM Gateway** | Abstraction over LLM providers (OpenAI, Anthropic, Google, Azure, Ollama). Unified API with fallback chains, rate limiting, token tracking. Switching providers is a config change. |
| **Memory & Context Manager** | Pluggable memory: buffer (recent N turns), summary (LLM-compressed), sliding window, entity extraction. Storage: in-memory, Redis, or PostgreSQL. Handles context window management automatically. |

### What Goes In / What Comes Out

**Inputs**:
- Agent config (YAML/JSON): agent type, model, tool bindings, memory strategy, prompt references
- Orchestration graph config (for multi-agent setups)
- Prompt templates + few-shot examples

**Outputs**:
- Runnable agent instance with bound tools, managed prompts, memory, error recovery, logging hooks
- For multi-agent setups: coordinated system with managed execution flow and state
- Versioned prompt registry

### Scope

**In scope:**
- Agent templates for common patterns
- Multi-agent orchestration patterns
- Prompt management with versioning
- Pre-built tool functions with standard interfaces
- LLM provider abstraction and routing
- Pluggable memory backends
- Config-driven agent assembly

**Out of scope:**
- Custom agent architectures (use templates as starting point, extend)
- Distributed agents across network clusters
- Automated prompt optimization / tuning
- Domain-specific business logic tools (built per engagement using the standard interface)
- Model fine-tuning
- GUI-based agent builder — maybe later, not now

### Technical Direction

- **Assembly via config**: Agents defined in YAML/JSON — type, model, tools, memory, prompts. The framework returns a runnable instance.
- **LangGraph under the hood**: We use LangGraph for orchestration (good state management, flexible). The config layer abstracts this, so swapping the engine for a specific engagement means changing the adapter, not rewriting configs or tools.
- **Prompts as files**: Structured directory hierarchy, git-tracked, Jinja2-templated. Few-shot examples stored alongside prompts, loaded based on domain context.
- **Tool contract**: Standard interface — `name`, `description`, `input_schema`, `output_schema`, `execute`. Makes tools discoverable and composable.
- **Incremental adoption**: Prompt management works without agent templates. The LLM gateway works without the orchestrator. Use what you need.

### Risks

| What could break | What we do about it |
|---|---|
| **Abstraction becomes the bottleneck** — adds latency, limits capabilities | Keep abstraction thin. If someone needs raw LangGraph or direct API calls, they can drop down. The framework helps; it should never block. |
| **Templates too rigid** — every engagement ends up forking | Inheritance model: base template provides skeleton, config overrides behavior. If >30% needs changing, that's a signal for a new template type. |
| **Prompt management adds overhead without value** | File-based, git-native, no separate service. Near-zero overhead. The value is versioning and cross-project reuse. |
| **Tool safety** — SQL executor runs something destructive, code executor escapes sandbox | SQL executor defaults to SELECT-only allowlist. Destructive ops require explicit opt-in with confirmation. Code executor uses isolated subprocess with resource limits. |
| **Gateway hides provider features** | Common operations use a unified API. Provider-specific features (structured outputs, vision, function calling variants) are accessible via pass-through. We abstract plumbing, not capability. |

### When Is It Done

- New NL2SQL agent assembled from config + prompts in under a day
- LLM provider switch is a config change
- Adding a tool means adding a definition file, not modifying agent code
- Prompts are versioned, templated, shared across agents without copy-paste
- At least two different agent types built from the same framework (proves it's actually reusable)

---

## How They Connect

These accelerators are independent — you can adopt any piece on its own. But the natural flow for an analytics/NL2SQL engagement looks like:

```
  Client Database
       │
       ▼
┌──────────────────┐
│  DATA LAYER      │──→  Schema map + Profiling report + Quality scorecard
│  ACCELERATOR     │
└──────────────────┘
       │
       ▼
┌──────────────────┐
│  KNOWLEDGE GRAPH │──→  Enriched descriptions, patterns, examples
│  ASSEMBLER       │
└──────────────────┘
       │
       ▼
┌──────────────────┐
│  AGENT FRAMEWORK │──→  Configured agent with tools, prompts, memory
│  ACCELERATOR     │
└──────────────────┘
       │
       ▼
  Agent serving user queries
```

Data Layer and Agent Framework are the two ends of this pipeline. That's why they're detailed first.

---

## Next Steps

1. Review this scope — push back on anything that's wrong, missing, or over-scoped
2. Share expected capabilities and sample conversation flows for the first demo agent (analytics / NL2SQL)
3. Share the existing insights agent repo for review
4. Start implementation planning for Data Layer and Agent Framework
5. Deep-dive on Knowledge Graph Assembler once the Data Layer shape is confirmed — it sits between the two and depends on both
6. Remaining accelerators (Conversation/UX, Testing, DevOps, Security) get detailed as work reaches them

---

*Open areas and gaps will be tracked as this document evolves through implementation.*
