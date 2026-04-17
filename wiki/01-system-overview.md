# System Overview & Vision

[← Back to Wiki Home](README.md)

---

## What This System Is

The **Agentic AI Application Accelerators** platform is a collection of reusable, composable, configuration-driven components designed to dramatically reduce the time and effort required to deliver AI agent-based solutions for new client engagements.

Rather than building every agent system from scratch, this platform provides pre-built building blocks that span the entire AI agent lifecycle — from data onboarding through to production observability and security.

---

## Strategic Objective

> Reduce new-engagement delivery effort by **30–40%** through a plug-configure-go-live approach with reusable assets.

The core insight is that across client engagements, teams repeatedly solve the same problems:
- Understanding and profiling client data
- Wiring agent tools, prompts, and memory
- Building RAG pipelines and retrieval strategies
- Creating chat interfaces and conversation logic
- Setting up testing, evaluation, and deployment infrastructure

Each of these problem areas becomes a reusable accelerator.

---

## Target Use Case: Analytics / NL2SQL Agent

The immediate, concrete use case driving development is a **Natural Language to SQL (NL2SQL) analytics agent**. This agent:

1. Connects to a client's database
2. Understands the schema, relationships, and data semantics
3. Accepts natural language queries from business users
4. Translates questions into SQL, executes them safely
5. Returns formatted results — tables, charts, summary cards
6. Suggests follow-up questions for deeper exploration

This use case exercises the full accelerator stack (data → knowledge → agent → UX → testing) and serves as the validation target for the platform.

---

## How the System Works (End-to-End Flow)

```
  Client Database / Data Sources
       │
       ▼
┌──────────────────────────────┐
│  L1 · DATA FOUNDATION        │
│  Connect, discover, profile,  │
│  assess quality               │
└──────────────────────────────┘
       │
       │  Schema map + Profiling report + Quality scorecard
       ▼
┌──────────────────────────────┐
│  L1.3 · KNOWLEDGE GRAPH       │
│  GENERATION ASSEMBLER         │
│  Enrich schema with           │
│  descriptions, patterns,      │
│  one-shot examples            │
└──────────────────────────────┘
       │
       │  Enriched knowledge graph (JSON, YAML, Markdown)
       ▼
┌──────────────────────────────┐
│  L3 · AGENT FRAMEWORK         │
│  Assemble agent from config:  │
│  template + tools + prompts   │
│  + memory + LLM gateway       │
└──────────────────────────────┘
       │
       │  Runnable agent instance
       ▼
┌──────────────────────────────┐
│  L4 · CONVERSATION & UX       │
│  Behavior, response formats,  │
│  follow-ups, UI components    │
└──────────────────────────────┘
       │
       │  Agent serving end-user queries
       ▼
┌──────────────────────────────┐
│  L5 · TESTING & EVALUATION    │
│  Measure quality, generate    │
│  tests, run regressions       │
└──────────────────────────────┘
```

---

## Key Design Decisions (Summary)

| Decision | Choice | Rationale |
|---|---|---|
| Configuration format | YAML / JSON | Human-readable, git-trackable, no code changes to adapt |
| Orchestration engine | LangGraph | Proven state management, flexible graph-based orchestration |
| Prompt storage | File-based (git-tracked) | Zero external dependencies, version control built-in |
| Database abstraction | SQLAlchemy + dedicated cloud adapters | Broad coverage with escape hatches for Snowflake/BigQuery |
| Memory backends | Pluggable (in-memory, Redis, PostgreSQL) | Right-size per engagement — dev vs. production |
| Component coupling | Independently adoptable | Teams use what they need; no "all-or-nothing" adoption |

See [Technical Decisions & Rationale](14-technical-decisions.md) for full details.

---

## What This System Is NOT

- **Not a managed platform**: There is no hosted service. These are libraries, templates, and tools that teams integrate into their own deployments.
- **Not a no-code builder**: Configuration-driven does not mean no-code. Engineers assemble and extend components.
- **Not a single framework**: Each accelerator is independently adoptable. Teams can use prompt management without the agent framework, or the data layer without the knowledge graph.
- **Not production-ready today**: This is an active development initiative. Layers are being built incrementally, starting with the demo-critical path (P0 items).

---

## Accelerator Reuse Model

The platform is designed around a consistent reuse pattern:

```
┌─────────────────────────────────────────────┐
│  REUSABLE CORE (70–95%)                      │
│  Framework logic, interfaces, pipelines,     │
│  standard tools, evaluation metrics          │
├─────────────────────────────────────────────┤
│  CONFIGURABLE (via YAML/JSON)                │
│  Connection details, model selection,        │
│  behavior profiles, quality rules            │
├─────────────────────────────────────────────┤
│  ENGAGEMENT-SPECIFIC (5–30%)                 │
│  Domain prompts, business logic tools,       │
│  client-specific data adapters               │
└─────────────────────────────────────────────┘
```

Across all 30+ accelerators, the average reuse potential is **~89%**, meaning only ~11% of work is engagement-specific.

---

## Team Structure

The project is organized for parallel development:

- **AI Engineers**: Focus on L1 (Data Foundation), L3 (Agent Framework), and L5 (Testing)
- **Full-Stack Developers**: Focus on L4 (Conversation & UX) and L6 (DevOps)
- **Architects**: Drive L0 (Onboarding Playbooks), L2 (Retrieval Strategy), L7 (Security patterns)

All layers are designed to be developed independently and in parallel.

---

[Next: Design Principles →](02-design-principles.md)
