# Agentic AI Application Accelerators

> **Objective**: When a new client engagement starts, the team should not be building from zero. Pre-built, configurable accelerators should enable a plug-configure-go-live approach — reducing delivery effort by 30–40%.
>
> **Document scope**: High-level accelerator landscape + detailed scope for the first two accelerators (Data Layer, Agent Framework). Detailed specs for remaining accelerators will follow as implementation begins.
>
> **Status**: v0.1 — For internal alignment

---

## Design Principles

These principles are derived by inverting the most common failure modes in accelerator initiatives:

| Failure mode (what kills accelerators) | Principle (how we avoid it) |
|---|---|
| Accelerators become a "platform" nobody asked for | **Composable building blocks, not a platform** — each accelerator is independently adoptable and ejectable |
| Over-abstraction makes nothing actually useful | **Useful on day one** — every component must solve a real, immediate problem on the current engagement |
| Hard-coded assumptions don't transfer across clients | **Configuration over code** — behavior driven by YAML/JSON configs, not embedded logic |
| Teams skip testing because "it's just a chatbot" | **If you can't measure it, don't ship it** — evaluation is a first-class concern, not an afterthought |
| Accelerators rot because nobody maintains them | **Built through real projects** — accelerators are extracted from working implementations, not theorized in isolation |
| Scope creep turns a 2-week task into a 2-month project | **Start narrow, widen with evidence** — begin with the first use case, generalize only when a second use case demands it |

---

## Accelerator Landscape

The full stack of accelerator areas. **Two are detailed in this document; the rest are scoped at headline level.**

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
├─────────────────────────────────────────────────────────────────┤
│              SECURITY & GOVERNANCE                              │
│         (Guardrails, auth/RBAC, audit, compliance)              │
└─────────────────────────────────────────────────────────────────┘
```

Each layer is independent and can be developed in parallel.

### High-Level Summary

| # | Accelerator | Type | Status | One-liner |
|---|---|---|---|---|
| 1 | **Client Onboarding & Playbooks** | Playbook + Tool | Planned | Structured assessment of client data readiness; architecture decision playbooks; project scaffolding for new engagements |
| 2 | **Data Layer Accelerator** | Technical | **Detailed below** | Fast ingestion, profiling, and quality assessment of client data — so agents have a solid data foundation from day one |
| 3 | **Knowledge Graph Generation Assembler** | Technical | Planned | Takes denormalized schema + business context + sample data → generates enriched knowledge graph with table/column descriptions, patterns, anti-patterns, and one-shot examples. Includes interactive preview-edit-commit flow. Directly feeds NL2SQL and analytics agents |
| 4 | **Agent Framework Accelerator** | Technical | **Detailed below** | Reusable building blocks for assembling single and multi-agent systems — templates, orchestration, prompt management, tool registry, LLM abstraction |
| 5 | **Conversation & UX Accelerator** | Technical + Playbook | Planned | Config-driven conversation behavior (tone, guardrails, response formats); follow-up question engine; conversation design playbook for architects and clients; reusable UI component library |
| 6 | **Testing & Evaluation Framework** | Technical | Planned | Standardized evaluation metrics, automated test case generation, regression suites, user simulation engine — the layer most teams skip and most regret skipping |
| 7 | **Observability & DevOps** | Technical | Future | Structured logging with trace propagation, LLM cost tracking and attribution, CI/CD pipeline templates, deployment configs |
| 8 | **Security & Governance** | Technical | Future | Input/output guardrails (prompt injection, PII, SQL safety), RBAC patterns, audit trails for compliance |

---

## DETAILED: Data Layer Accelerator

### Purpose

The single biggest time sink when onboarding a new client is understanding their data — what tables exist, what they mean, how they relate, and whether the data quality is good enough for an agent to use reliably. This accelerator compresses that from weeks to days.

### Core Components

| Component | What it does | Why it matters |
|---|---|---|
| **Data Connector Library** | Pre-built, config-driven connectors for common databases (PostgreSQL, MySQL, SQL Server, Snowflake, BigQuery, MongoDB) and file formats (CSV, Excel, Parquet, JSON). Standardized output format regardless of source. | Eliminates the boilerplate of writing custom ingestion code for every client. Connect via a YAML config, not custom code. |
| **Schema Discovery Engine** | Auto-introspects database to discover tables, columns, types, primary keys, foreign keys, indexes, and inferred relationships (even when FKs aren't explicitly defined). | Gives the structural understanding of a client's data model in minutes, not days of manual exploration. |
| **Data Profiling Engine** | Statistical profiling of actual data: cardinality, null rates, value distributions, outlier detection, pattern recognition (e.g., "this column looks like an email/phone/date"). | Raw schema tells you the structure; profiling tells you the reality. An agent that doesn't know 40% of a column is NULL will generate misleading queries. |
| **Data Quality Assessor** | Scores data across four dimensions — **completeness**, **consistency**, **accuracy**, **timeliness** — and produces a scorecard with specific findings and remediation guidance. | Sets realistic expectations early. If data quality is poor, we know before building the agent, not after deploying it. |

### Inputs and Outputs

```
INPUTS                              OUTPUTS
─────────                           ───────
• Database connection config        • Schema map (tables, columns,
  (YAML/JSON)                         relationships, types)

• OR file paths (CSV, Excel,        • Data profiling report
  Parquet, JSON)                      (statistics per column)

                                    • Data quality scorecard
                                      (4-dimension scoring + findings)

                                    • Machine-readable output (JSON)
                                      + human-readable report (markdown)
```

### Scope Boundaries

| In scope | Explicitly out of scope |
|---|---|
| Connecting to client data sources | Data migration or ETL pipelines |
| Schema extraction and relationship inference | Data transformation or cleansing |
| Statistical profiling and quality scoring | Real-time streaming data ingestion |
| Quality reports with remediation recommendations | Building or managing a data warehouse |
| Support for structured data (databases, tabular files) | Unstructured document parsing (belongs in a separate retrieval/RAG layer) |

### Technical Approach (High Level)

- **Config-driven**: All connections defined in YAML/JSON — no code changes to onboard a new data source
- **Provider-agnostic**: SQLAlchemy-based for relational databases; dedicated adapters for cloud warehouses (Snowflake, BigQuery) where SQLAlchemy falls short
- **Profiling engine**: Combination of SQL-based sampling (for large datasets — profile on a representative sample, not full table scans) and in-memory analysis (for smaller datasets/files)
- **Quality framework**: Pluggable quality rules — ships with sensible defaults (null checks, uniqueness, referential integrity) but allows engagement-specific rules to be added
- **Output format**: Dual output — JSON for downstream consumption by other accelerators (especially Knowledge Graph Assembler and Agent Framework) and markdown for human review

### What Can Go Wrong (Inversion Analysis)

| Risk | How we mitigate |
|---|---|
| **Connector sprawl** — trying to support every database becomes a maintenance burden | Start with the 4–5 most common in the client base. Add new connectors only when an actual engagement demands it. |
| **False confidence from profiling** — automated profiles may miss semantic issues (e.g., a "status" column with inconsistent string values) | Quality reports always include "human review needed" flags for ambiguous findings. Profiling informs; it does not replace domain understanding. |
| **Performance on large databases** — profiling 500+ tables with billions of rows can be slow and expensive | Sampling-based profiling by default. Configurable sample sizes. Option to profile a subset of tables (schema-filtered). |
| **Quality scores without actionable guidance are useless** | Every quality finding includes: what is wrong, why it matters for agents, and what to do about it. Not just "completeness: 72%". |
| **Overfitting to SQL databases** — not every client has structured data | This accelerator deliberately focuses on structured data. Unstructured data handling is a separate concern (retrieval/RAG layer). Clear boundary, no scope creep. |

### Success Criteria

This accelerator is "done enough" when:

1. A new client database can be connected and fully profiled in under 2 hours (not days)
2. The schema map is accurate enough to feed directly into the Knowledge Graph Assembler
3. The quality scorecard surfaces at least 80% of the data issues that would affect agent performance
4. The output formats (JSON + markdown) are stable and consumed by at least one downstream accelerator

---

## DETAILED: Agent Framework Accelerator

### Purpose

Every agent built from scratch re-solves the same problems: how to wire tools, manage prompts, handle errors, route between models, manage memory, and orchestrate multiple agents. This accelerator provides the reusable building blocks so the team focuses on business logic, not plumbing.

### Core Components

| Component | What it does | Why it matters |
|---|---|---|
| **Base Agent Templates** | Pre-wired agent templates for common patterns: NL2SQL, Conversational QA, Plan-and-Execute, ReAct, Workflow. Each comes with tool binding, structured output parsing, error handling, and logging hooks. | Starting from a working template and customizing is 10x faster than building from scratch. The NL2SQL template is the immediate priority for the first demo. |
| **Agent Orchestrator** | Patterns for coordinating multiple agents: Sequential, Parallel, Hierarchical (supervisor), Router-based (intent classification → agent selection). Includes state management and handoff protocols. | Single-agent systems are simple; real-world use cases almost always need multi-agent coordination. Having proven patterns prevents ad-hoc, fragile wiring. |
| **Prompt Management System** | Centralized prompt registry with versioning, Jinja2-style templating, few-shot example banks, and system/user/tool prompt separation. File-based storage (no external dependencies). | Prompts are the "code" of AI applications. Having them scattered in source files, un-versioned, and duplicated across projects is the #1 maintenance headache. |
| **Tool & Function Registry** | Cataloged, tested, reusable tool functions: SQL executor (with safety checks), chart/visualization generator, REST API caller, Python code executor (sandboxed), file reader/writer. Standardized interface (name, description, input/output schema). | Agents are only as capable as their tools. Pre-built tools with safety checks (e.g., SQL executor that blocks destructive queries) eliminate repeated work and reduce risk. |
| **LLM Gateway** | Abstraction layer over LLM providers (OpenAI, Anthropic, Google, Azure, Ollama/local). Unified API with fallback chains, rate limiting, and token usage tracking. | Hard-coded model dependencies are a liability. Clients change providers, models get deprecated, costs need optimization — the gateway makes all of this a config change, not a code rewrite. |
| **Memory & Context Manager** | Pluggable memory backends: buffer (recent N turns), summary (LLM-compressed), sliding window, entity extraction. Storage options: in-memory, Redis, PostgreSQL. Includes automatic context window management. | Without managed memory, every agent either runs out of context window or sends too much irrelevant history. This is the difference between an agent that feels coherent and one that does not. |

### Inputs and Outputs

```
INPUTS                              OUTPUTS
─────────                           ───────
• Agent config (YAML/JSON):         • Runnable agent instance with:
  - Agent type (NL2SQL, QA, etc.)     - Bound tools
  - Model selection                   - Managed prompts
  - Tool bindings                     - Memory handling
  - Memory strategy                   - Error recovery
  - Prompt references                 - Structured output parsing
                                      - Logging & observability hooks
• Orchestration graph config
  (for multi-agent setups)          • Multi-agent system with managed
                                      execution flow and state
• Prompt templates + few-shot
  examples                         • Versioned prompt registry
```

### Scope Boundaries

| In scope | Explicitly out of scope |
|---|---|
| Agent templates for common patterns | Fully custom agent architectures (use templates as starting point, extend as needed) |
| Multi-agent orchestration patterns | Distributed agent systems across networks/clusters |
| Centralized prompt management with versioning | Automated prompt optimization / prompt tuning |
| Pre-built tool functions with standard interfaces | Domain-specific business logic tools (built per engagement using the standard interface) |
| LLM provider abstraction and routing | Model fine-tuning or training |
| Pluggable memory backends | Custom memory compression models |
| Configuration-driven agent assembly | GUI-based agent builder (future consideration) |

### Technical Approach (High Level)

- **Config-driven assembly**: Agents defined in YAML/JSON — specify type, model, tools, memory, and prompts. The framework assembles and returns a runnable instance. No framework lock-in in the config.
- **Framework-agnostic core**: Internal implementation uses LangGraph as the primary orchestration engine (proven, flexible, good state management), but the config layer abstracts this — if swapping to a different engine for a specific engagement, only the adapter changes, not the configs or tools.
- **Prompt-as-data**: Prompts live in a structured file hierarchy, not in source code. Versioned like code (git-tracked). Templated with Jinja2 for variable interpolation. Few-shot examples stored alongside prompts and loaded dynamically based on the domain.
- **Tool contract**: Every tool implements a standard interface (`name`, `description`, `input_schema`, `output_schema`, `execute`). This makes tools discoverable by agents and composable (output of one tool can be input to another).
- **Gradual adoption**: The prompt management system can be used without the agent templates. The LLM gateway works without the orchestrator. Components are designed to be useful independently.

### What Can Go Wrong (Inversion Analysis)

| Risk | How we mitigate |
|---|---|
| **Framework becomes the bottleneck** — the abstraction layer adds latency or limits what agents can do | Keep the abstraction thin. If someone needs to drop down to raw LangGraph or direct API calls, they can. The framework helps; it never blocks. |
| **Template rigidity** — templates are so opinionated that every engagement needs to fork them | Templates follow an inheritance model: base template provides the skeleton, engagement-specific config overrides behavior. If more than 30% needs changing, it signals the need for a new template type, not stretching the existing one. |
| **Prompt management overhead** — centralizing prompts adds process without adding value | The system is file-based (no database dependency, no separate service). It works with the existing git workflow. The overhead is near-zero; the benefit is versioning and reuse. |
| **Tool safety gaps** — SQL executor runs a destructive query, code executor escapes sandbox | SQL executor uses allowlists by default (SELECT only). Destructive operations require explicit config-level opt-in with confirmation. Code executor runs in isolated subprocess with resource limits. |
| **LLM gateway hides important provider-specific features** | Gateway exposes a common API for common operations. Provider-specific features (structured outputs, function calling variants, vision) are accessible through a pass-through mechanism. Capability is not abstracted away; only plumbing is. |
| **Over-engineering the orchestrator for single-agent use cases** | The orchestrator is optional. For a single agent, use a template directly. The orchestrator exists for genuine multi-agent coordination — not to make simple things complex. |

### Success Criteria

This accelerator is "done enough" when:

1. A new NL2SQL agent can be assembled from config + prompts in under a day (not a week)
2. Switching the underlying LLM provider is a config change, not a code change
3. Adding a new tool to an agent is adding a tool definition file, not modifying agent internals
4. Prompts are version-controlled, templated, and shared across agents without duplication
5. At least two different agent types (e.g., NL2SQL + Conversational QA) are built from the same framework, proving reusability

---

## Dependencies Between Accelerators

The accelerators are independently adoptable, but they are most powerful when combined. Here is how the first four connect:

```
  Client Database
       │
       ▼
┌──────────────────┐
│  DATA LAYER      │──── Schema map + Profiling report + Quality scorecard
│  ACCELERATOR     │
└──────────────────┘
       │
       ▼
┌──────────────────┐
│  KNOWLEDGE GRAPH │──── Enriched descriptions, patterns, examples
│  ASSEMBLER       │
└──────────────────┘
       │
       ▼
┌──────────────────┐
│  AGENT FRAMEWORK │──── Configured agent with tools, prompts, memory
│  ACCELERATOR     │
└──────────────────┘
       │
       ▼
  Agent ready to serve user queries
```

The Data Layer feeds into the Knowledge Graph Assembler (schema + profiles as input). The Knowledge Graph Assembler feeds into the Agent Framework (descriptions and examples become prompt context and few-shot examples). The Agent Framework produces the running agent.

This chain is why the Data Layer and Agent Framework are the first two to detail — they are the bookends of the core pipeline.

---

## Next Steps

1. Review this scope — confirm, challenge, or reprioritize the accelerator areas and their relative importance
2. Share expected capabilities and sample conversation flows for the first demo agent (analytics/NL2SQL)
3. Share the existing insights agent repository for review and improvement assessment
4. Begin implementation planning for Data Layer and Agent Framework accelerators
5. Deep-dive brainstorming on the Knowledge Graph Assembler once Data Layer shape is confirmed (it sits between the two detailed accelerators and depends on both)
6. Remaining accelerators (Conversation/UX, Testing, DevOps, Security) will be detailed as work progresses — likely one per sprint cycle

---

*This is a living document. Each accelerator will get its own detailed spec as implementation begins.*
