# Glossary & Project Roadmap

[← Back to Wiki Home](README.md) | [← Risk Analysis](15-risk-analysis.md)

---

## Glossary

### Core Concepts

| Term | Definition |
|---|---|
| **Accelerator** | A reusable, configurable component that reduces delivery effort for new AI agent engagements. Can be code (libraries, frameworks, tools) or knowledge assets (playbooks, decision guides). |
| **Engagement** | A client project where an AI agent solution is being built and delivered. |
| **Layer** | One of the 8 architectural tiers (L0–L7) that organize accelerators by concern area. |
| **P0 / P1 / P2** | Priority levels. P0 = must-have for demo. P1 = high value, build as capacity allows. P2 = nice-to-have, defer until needed. |
| **Knowledge Graph** | An enriched representation of a database schema including table/column descriptions, relationships, patterns, anti-patterns, and example queries. Used as context for NL2SQL agents. |
| **Config-driven** | An approach where behavior is controlled by YAML/JSON configuration files rather than code changes. |

### Agent Concepts

| Term | Definition |
|---|---|
| **NL2SQL** | Natural Language to SQL — converting human language questions into executable SQL queries. |
| **ReAct Agent** | An agent pattern that alternates between Reasoning and Acting (thinking about what to do, then using a tool to do it). |
| **Plan-and-Execute** | An agent pattern that first creates a multi-step plan, then executes each step sequentially. |
| **Multi-agent Orchestration** | Coordinating multiple specialized agents to work together on complex tasks. |
| **Tool Binding** | Connecting an agent to specific tools (SQL executor, chart generator, etc.) that it can invoke during execution. |
| **Few-shot Examples** | Demonstration input-output pairs included in prompts to guide the LLM's behavior. |
| **Context Window** | The maximum amount of text (measured in tokens) an LLM can process in a single request. |

### Data Concepts

| Term | Definition |
|---|---|
| **Schema Discovery** | Automatically detecting the structure of a database — tables, columns, types, relationships, indexes. |
| **Data Profiling** | Statistical analysis of actual data values — distributions, null rates, cardinality, outliers, patterns. |
| **Data Quality Dimensions** | Four measures of data fitness: **Completeness** (non-null values), **Consistency** (format conformance), **Accuracy** (value correctness), **Timeliness** (data freshness). |
| **Readiness Grade** | A Red / Amber / Green assessment of whether client data is ready for agent use. |
| **Denormalized Schema** | A flattened representation of the database schema used as input for knowledge graph generation. |
| **Vector Store** | A database optimized for storing and querying embedding vectors for semantic search. |

### Retrieval Concepts

| Term | Definition |
|---|---|
| **RAG** | Retrieval-Augmented Generation — a pattern where relevant documents are retrieved and included in the LLM prompt to ground the response in factual content. |
| **Chunking** | Breaking documents into smaller segments for embedding and retrieval. |
| **Re-ranking** | Reordering initial retrieval results to improve relevance before passing to the LLM. |
| **HyDE** | Hypothetical Document Embeddings — generating a hypothetical answer, embedding it, and using it to retrieve similar real documents. |
| **BM25** | A sparse retrieval algorithm based on term frequency; used in hybrid retrieval alongside vector search. |

### Infrastructure Concepts

| Term | Definition |
|---|---|
| **LLM Gateway** | An abstraction layer over multiple LLM providers that provides a unified API, routing, fallback, and cost tracking. |
| **Trace ID** | A unique identifier propagated through the entire agent execution chain (query → retrieval → LLM → tool → response) for debugging. |
| **Guardrails** | Input and output validation checks that prevent unsafe agent behavior (prompt injection, data leakage, harmful actions). |
| **RBAC** | Role-Based Access Control — restricting what data and actions are available based on the user's role. |
| **Golden Test Set** | A curated set of queries with verified correct outputs, used for regression testing. |

---

## Abbreviations

| Abbreviation | Full Form |
|---|---|
| ADR | Architecture Decision Record |
| API | Application Programming Interface |
| CI/CD | Continuous Integration / Continuous Deployment |
| CSV | Comma-Separated Values |
| DB | Database |
| FK | Foreign Key |
| KG | Knowledge Graph |
| LLM | Large Language Model |
| NL2SQL | Natural Language to SQL |
| PII | Personally Identifiable Information |
| PK | Primary Key |
| PRD | Product Requirements Document |
| RAG | Retrieval-Augmented Generation |
| RBAC | Role-Based Access Control |
| SQL | Structured Query Language |
| UX | User Experience |
| YAML | Yet Another Markup Language |

---

## Project Roadmap

### Phase 0: Foundation & Demo (Current)

**Objective**: Build the P0 demo-critical path — 6 accelerators that enable an NL2SQL analytics demo agent.

| Milestone | Accelerators | Deliverable |
|---|---|---|
| **M0.1** — Data Understanding | L1.2 Schema Discovery, L1.3 Knowledge Graph | Connect to DB, auto-profile, generate knowledge graph |
| **M0.2** — Agent Assembly | L3.1 NL2SQL Template, L3.4 Tool Registry | Working NL2SQL agent from config |
| **M0.3** — Output & Quality | L4.3 Response Rendering, L5.1 Evaluation | Formatted results + quality scores |
| **M0.4** — Demo Ready | All P0 integrated | End-to-end demo: connect → profile → generate KG → agent answers questions → quality measured |

**Parallel work during Phase 0**:
- Review existing insights agent repository for improvement opportunities
- Begin high-level scoping for Phase 1 accelerators

---

### Phase 1: Core Infrastructure

**Objective**: Build the P1 accelerators that make the platform production-ready.

| Milestone | Accelerators | Deliverable |
|---|---|---|
| **M1.1** — Data Layer Complete | L1.1 Connectors, L1.4 Vector Store | Full data onboarding pipeline |
| **M1.2** — Agent Framework Complete | L3.2 Orchestrator, L3.3 Prompts, L3.5 LLM Gateway, L3.6 Memory | Full agent assembly toolkit |
| **M1.3** — Retrieval Layer | L2.1 RAG Pipelines, L2.3 Document Processing | Unstructured document support |
| **M1.4** — UX Layer | L4.1 Behavior Config, L4.2 Conversation Playbook, L4.4 Follow-ups, L4.5 UI Components | Full conversation experience |
| **M1.5** — Testing & Ops | L5.2 Test Gen, L5.4 Regression, L6.1 Logging, L6.3 CI/CD | Production readiness infrastructure |
| **M1.6** — Security | L7.1 Guardrails | Enterprise safety baseline |

---

### Phase 2: Advanced Capabilities

**Objective**: Build P2 accelerators and advanced patterns as real engagement demand emerges.

| Milestone | Accelerators | Trigger |
|---|---|---|
| **M2.1** — Advanced Data | L1.5 Synthetic Data | When client data is unavailable/restricted |
| **M2.2** — Advanced Retrieval | L2.2 Re-ranking | When retrieval quality needs improvement |
| **M2.3** — Advanced Testing | L5.3 User Simulation, L5.5 A/B Testing | When testing coverage needs expansion |
| **M2.4** — Multi-Channel | L4.6 Channel Adapters | When Slack/Teams integration is demanded |
| **M2.5** — Advanced Ops | L6.2 Cost Tracking | When cost optimization is needed |
| **M2.6** — Compliance | L7.2 Auth/RBAC, L7.3 Audit | When enterprise compliance is required |
| **M2.7** — Onboarding Tooling | L0.2 Kickoff Wizard | When engagement volume justifies automation |

---

### Phase 3: Scale & Generalize

**Objective**: Extract patterns from multiple engagements and generalize the platform.

| Focus Area | Activities |
|---|---|
| **Cross-engagement patterns** | Identify patterns used across 3+ engagements; promote to core accelerators |
| **Template expansion** | New agent templates driven by real use cases (Document QA, Workflow, etc.) |
| **Community contribution** | Enable teams to contribute accelerators back to the shared library |
| **Performance optimization** | Profile and optimize high-usage accelerators |
| **Documentation maturity** | Expand playbooks, add tutorials, record walkthroughs |

---

## Evolution Principles

1. **Each accelerator gets its own detailed specification as implementation begins** — this wiki grows alongside the code
2. **Remaining accelerators are detailed as work reaches them** — roughly one accelerator specification per sprint cycle
3. **Open areas and gaps are tracked** — this is a living document, not a fixed plan
4. **Decisions are revisited** when new evidence emerges from real engagements
5. **Deep-dive brainstorming on interdependent components** (e.g., Knowledge Graph Assembler) happens once the upstream dependency is confirmed

---

## Source Materials

This wiki was synthesized from the following internal materials:

| Source | Content |
|---|---|
| **Accelerators Scope Document (v2)** | Design principles, landscape overview, detailed Data Layer and Agent Framework specifications |
| **Accelerators Capability Map** | Comprehensive L0–L7 layer map with 34 accelerator definitions, priority matrix, and P0 demo path |
| **Discussion Transcript** | Strategic alignment discussion covering accelerator areas, parallel development strategy, team structure, and immediate next steps |

All content has been anonymized and generalized for use as a shared knowledge base.

---

*This wiki is a living knowledge base. It will be expanded as implementation progresses and new patterns emerge from real engagements.*
