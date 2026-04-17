# Technical Decisions & Rationale

[← Back to Wiki Home](README.md) | [← P0 Demo-Critical Path](13-demo-critical-path.md)

---

## Purpose

This document captures every significant technical decision, the alternatives considered, the rationale for the chosen approach, and the trade-offs accepted. It serves as an Architecture Decision Record (ADR) for the project.

---

## Decision 1: Configuration Format — YAML/JSON

| | |
|---|---|
| **Decision** | Use YAML and JSON as the universal configuration format for all accelerators |
| **Status** | Accepted |

**Context**: Agents, data connections, conversation behavior, guardrails, quality rules — everything needs to be configurable without code changes.

**Alternatives considered**:

| Option | Pros | Cons |
|---|---|---|
| **YAML/JSON** | Human-readable, git-trackable, widely supported, no code changes needed | No runtime validation by default, can get complex for deeply nested configs |
| **Python config objects** | Type safety, IDE support | Requires code changes to reconfigure, harder for non-developers |
| **Database-backed config** | Dynamic updates without redeployment | External dependency, harder to version control, overkill for most cases |
| **Environment variables** | Simple, 12-factor app compatible | Limited structure, poor for complex configs |

**Rationale**: YAML/JSON provides the best balance of human readability, version control compatibility (git-tracked), and zero external dependencies. This supports the core principle of "configuration over code."

**Trade-off accepted**: Complex nested configs can be harder to validate. Mitigated by JSON Schema validation on config files.

---

## Decision 2: Orchestration Engine — LangGraph

| | |
|---|---|
| **Decision** | Use LangGraph as the primary agent orchestration engine |
| **Status** | Accepted |

**Context**: Agents need a runtime for managing state, tool execution, multi-step reasoning, and multi-agent coordination.

**Alternatives considered**:

| Option | Pros | Cons |
|---|---|---|
| **LangGraph** | Proven state management, flexible graph-based orchestration, good ecosystem | LangChain dependency, learning curve for graph-based thinking |
| **CrewAI** | Simpler multi-agent setup, role-based | Less flexible for custom workflows, less mature state management |
| **AutoGen** | Strong multi-agent conversation support | More complex setup, heavier framework |
| **Custom from scratch** | Full control, no dependencies | Massive development effort, reinventing tested patterns |

**Rationale**: LangGraph provides the best combination of flexibility (graph-based orchestration handles any workflow pattern) and proven reliability (widely used, well-documented, actively maintained). The state management model is particularly well-suited for multi-step agent interactions.

**Trade-off accepted**: LangGraph is tied to the LangChain ecosystem. Mitigated by the **config abstraction layer** — the framework abstracts LangGraph behind configuration, so swapping the engine for a specific engagement means changing the adapter, not rewriting configs or tools.

**Escape hatch**: If someone needs raw LangGraph or direct API calls, they can drop down. The framework helps; it never blocks.

---

## Decision 3: Prompt Storage — File-Based (Git-Tracked)

| | |
|---|---|
| **Decision** | Store prompts as files in a structured directory hierarchy, tracked in git |
| **Status** | Accepted |

**Context**: Prompts are the "code" of AI applications. They need versioning, templating, and cross-project sharing.

**Alternatives considered**:

| Option | Pros | Cons |
|---|---|---|
| **File-based (git-tracked)** | Zero external dependencies, version control built-in, works with existing workflow | No dynamic updates without redeployment |
| **Database-backed prompt store** | Dynamic updates, admin UI possible | External dependency, separate service to maintain, version control harder |
| **Embedded in source code** | Simple, close to usage | Un-versioned independently, duplicated across projects, hard to A/B test |
| **Third-party prompt management (e.g., PromptLayer)** | Feature-rich, analytics built-in | Vendor lock-in, cost, external dependency |

**Rationale**: File-based storage has near-zero overhead, works with the existing git workflow, and requires no separate service or database. Prompts can be versioned, branched, and merged like any other code artifact. The Jinja2 templating provides sufficient power for variable interpolation and conditional logic.

**Directory structure**:
```
prompts/
├── system/          # System prompts per agent type
├── user/            # User-facing prompt templates
├── few-shot/        # Few-shot example banks (per domain)
└── tool-calling/    # Tool invocation prompt templates
```

**Trade-off accepted**: No dynamic updates without redeployment. For most cases this is acceptable — prompt changes should go through review (they can break agent behavior as easily as code changes).

---

## Decision 4: Database Abstraction — SQLAlchemy + Cloud Adapters

| | |
|---|---|
| **Decision** | Use SQLAlchemy for relational database access with dedicated adapters for cloud warehouses |
| **Status** | Accepted |

**Context**: The Data Foundation Layer needs to connect to diverse databases for schema discovery and data profiling.

**Alternatives considered**:

| Option | Pros | Cons |
|---|---|---|
| **SQLAlchemy + dedicated adapters** | Broad coverage, mature, well-documented, reflection/introspection support | Some cloud warehouses have gaps in SQLAlchemy support |
| **SQLAlchemy only** | Single library for everything | Snowflake and BigQuery support has limitations |
| **Database-specific drivers only** | Best per-database support | No abstraction, massive code duplication |
| **Apache Arrow / DuckDB** | Fast analytics, cross-format support | Not designed for connection management or schema introspection |

**Rationale**: SQLAlchemy provides the best foundation for schema introspection (reflection) and covers the majority of relational databases. For Snowflake and BigQuery, where SQLAlchemy falls short, dedicated adapters provide the escape hatch without compromising the core architecture.

**Trade-off accepted**: Maintaining dedicated adapters adds complexity. Mitigated by only adding adapters when a real engagement needs them (per the "start narrow" principle).

---

## Decision 5: Memory Backends — Pluggable Architecture

| | |
|---|---|
| **Decision** | Support multiple memory backends (in-memory, Redis, PostgreSQL) via a pluggable interface |
| **Status** | Accepted |

**Context**: Different deployment scenarios need different memory characteristics — development needs simplicity, production needs persistence and performance.

| Backend | Best for |
|---|---|
| **In-memory** | Development, testing, ephemeral sessions |
| **Redis** | Production with low-latency requirements |
| **PostgreSQL** | Production with persistence and queryability |
| **File-based** | Simple deployments, debugging |

**Rationale**: A pluggable architecture allows right-sizing per engagement without code changes. Development uses in-memory (zero setup), production uses Redis or PostgreSQL (persistent, scalable).

---

## Decision 6: Component Coupling — Independently Adoptable

| | |
|---|---|
| **Decision** | Every accelerator is independently adoptable; no "all-or-nothing" adoption |
| **Status** | Accepted |

**Context**: Forcing teams to adopt the full stack when they only need one piece kills adoption.

**Architectural consequences**:
- No shared runtime or central registry that all components depend on
- Inter-layer communication via file-based data exchange (JSON/YAML), not in-process calls
- Each component has its own dependencies, installation, and documentation
- Integration is optional and additive

**Trade-off accepted**: Some integration efficiency is lost (components could share more if tightly coupled). The adoption benefit far outweighs this cost.

---

## Decision 7: Agent Template Extensibility — Config Override Model

| | |
|---|---|
| **Decision** | Agent templates use an inheritance model with config overrides, not forking |
| **Status** | Accepted |

**Context**: Templates need to be customizable per engagement without being so rigid that every team forks them.

**The model**:
```
Base Template (skeleton with defaults)
    ↓ overridden by
Engagement Config (YAML — specific model, tools, prompts, memory)
    ↓ extended by
Custom Code (only for truly custom behavior)
```

**Rule of thumb**: If more than 30% of a template needs changing, create a new template type rather than stretching the existing one.

---

## Decision 8: SQL Safety — Allowlist by Default

| | |
|---|---|
| **Decision** | SQL executor defaults to SELECT-only; destructive operations require explicit opt-in |
| **Status** | Accepted |

**Context**: An NL2SQL agent that accidentally runs DROP TABLE or DELETE is a catastrophic failure mode.

**Safety model**:
1. **Default**: Only SELECT queries allowed
2. **Explicit opt-in**: Destructive operations (INSERT, UPDATE, DELETE, DROP) require configuration-level approval
3. **Confirmation step**: Even approved destructive operations require a confirmation before execution
4. **Audit logging**: All SQL executions are logged with trace IDs

**Trade-off accepted**: Legitimate use cases requiring data modification need additional configuration. This friction is intentional — the cost of accidental data destruction far outweighs the inconvenience of opt-in configuration.

---

## Decision 9: Frontend Framework — React (Primary)

| | |
|---|---|
| **Decision** | UI Component Library is built in React with adapter patterns for other frameworks |
| **Status** | Accepted |

**Context**: The UI layer needs to provide reusable chat, chart, and admin components.

**Rationale**: React has the largest ecosystem, most team familiarity, and best component library ecosystem. Adapter patterns allow integration with other frameworks when client requirements dictate.

---

## Decision 10: Evaluation Priority — First-Class Concern

| | |
|---|---|
| **Decision** | Testing & Evaluation (L5) is a P0 priority, built alongside the core agent framework |
| **Status** | Accepted |

**Context**: Most AI agent projects treat evaluation as an afterthought — something to add later. This consistently leads to shipping agents whose quality is unknown.

**Rationale**: Aligns with the core principle "if you can't measure it, don't ship it." The evaluation framework is as critical to the demo as the agent itself — proving quality is what separates a demo from a toy.

---

## Decisions Yet to Be Made

| Decision | When | Depends On |
|---|---|---|
| Vector store selection for production | When L1.4 is detailed | Client requirements, data volume |
| Multi-agent orchestration patterns | When L3.2 is implemented | Real multi-agent use case |
| Channel-specific rendering strategies | When L4.6 is detailed | Client channel requirements |
| Log storage infrastructure | When L6.1 is deployed | Client infrastructure (CloudWatch, Datadog, etc.) |
| Identity provider integration | When L7.2 is detailed | Client auth infrastructure |

---

[Next: Risk Analysis & Mitigation →](15-risk-analysis.md)
