# Agentic AI Application Accelerators

> **Objective**: New client engagements shouldn't start from zero. These accelerators give teams a head start with pre-built, configurable components that cut delivery effort by 30–40%.
>
> **What this covers**: The full landscape of accelerator areas at headline level, plus a detailed scope for the two we're building first — Data Layer and Agent Framework.
>
> **Status**: Internal alignment

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
| Tools try to replace experts, fail, and erode trust | **Co-pilot, not replacement.** Automate the mechanical work so the expert keeps full ownership of the meaning. |

---

## Accelerator Landscape

Eight areas, spanning the full lifecycle. **Two are detailed below; the rest are headline-level for now.**

```
┌─────────────────────────────────────────────────────────────────┐
│              CLIENT ONBOARDING & PLAYBOOKS                      │
│         (Assessment, decision playbooks, kickoff guides)        │
├─────────────────────────────────────────────────────────────────┤
│   ★ DATA LAYER ACCELERATOR  [DETAILED BELOW]                    │
│        — Knowledge Creation Workbench —                         │
│  (Schema discovery, client doc import, descriptions, glossary,  │
│   patterns, KPI definitions, SME review, strategy recommender)  │
├─────────────────────────────────────────────────────────────────┤
│          KNOWLEDGE REPRESENTATION ASSEMBLER                     │
│      (Multi-strategy: Plain Schema, Vector, Knowledge Graph)    │
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

### Summary

| # | Accelerator | Type | Status | What it gives you |
|---|---|---|---|---|
| 1 | **Client Onboarding & Playbooks** | Playbook + Tool | Planned | Data readiness assessment, architecture decision guides, project scaffolding for new engagements |
| 2 | **Data Layer Accelerator** *(Knowledge Creation Workbench)* | Technical | **Detailed below** | An AI-assisted workbench for the data SME — imports client documentation, drafts descriptions, glossary, patterns, KPI definitions, and a retrieval strategy recommendation, then walks the SME through reviewing all of it |
| 3 | **Knowledge Representation Assembler** | Technical | Planned | Builds whichever representation the recommender selected — plain schema bundle, vector index, or knowledge graph. Pluggable strategies behind a unified retrieval contract |
| 4 | **Agent Framework Accelerator** | Technical | **Detailed below** | Building blocks for single and multi-agent systems: templates, orchestration, prompt management, tool registry, LLM abstraction |
| 5 | **Conversation & UX Accelerator** | Technical + Playbook | Planned | Config-driven conversation behavior, follow-up engine, conversation design playbook, UI component library |
| 6 | **Testing & Evaluation Framework** | Technical | Planned | Eval metrics, automated test generation, regression suites, user simulation |
| 7 | **Observability & DevOps** | Technical | Future | Structured logging, trace propagation, LLM cost tracking, CI/CD templates |
| 8 | **Security & Governance** | Technical | Future | Input/output guardrails, RBAC patterns, audit trails |

---

## DETAILED: Data Layer Accelerator
### *Knowledge Creation Workbench*

### The Problem

Onboarding a new client's data is mostly knowledge work, not technical work. Discovering the schema is the easy part. The hard part is everything else the SME has to produce by hand: table and column descriptions, the business glossary, the patterns the data follows, how each KPI is actually calculated, which relationships are real versus accidental, and ultimately a recommendation for how the agent should reason over the data.

Done manually, this is months of SME time. Most of that time is spent on first-draft typing and reconciling material the client may already have lying around in scattered places — not on the genuinely hard judgment calls.

This accelerator inverts that ratio. It pulls in whatever the client already documented, drafts the rest from the live data, and gives the SME a structured workspace to confirm, correct, and fill the gaps. The SME stays the source of truth. The accelerator handles the typing and the reconciliation.

### Guiding Principle: Co-pilot, Not Replacement

The expert is never removed from the loop. The target is roughly fifty-fifty: automate the mechanical half, leave the expertise half firmly with the human. What the accelerator does well is generate plausible drafts at scale and reconcile multiple inputs. What humans do well is decide which drafts are correct, which are subtly wrong, and which business meaning to attach. Every component is designed around that split.

Practical implication: the workbench should turn knowledge work that previously took roughly a month into knowledge work that takes roughly a week to ten days — by removing the typing, not the thinking.

### What It Includes

| Component | What it does |
|---|---|
| **Data Connector Library** | Config-driven connectors for common sources. Initial set: Postgres, Snowflake, and CSV. One YAML file to connect, standardized output regardless of source. |
| **Client Knowledge Importer** | Ingests client-provided documentation and reconciles it against the discovered schema before any AI drafting happens. Supported formats: CSV/Excel data dictionaries, structured markdown, and dbt manifests (the three formats that cover the majority of real client material). Reconciliation surfaces three categories: matches, conflicts, and gaps. |
| **Schema Discovery Engine** | Auto-introspects the connected source: tables, columns, types, primary keys, foreign keys, indexes. Infers candidate relationships even when foreign keys aren't declared. Every claim it produces — PK, FK, relationship — is tagged with a confidence level (Explicit / Strong / Weak) so downstream stages know how much to trust each input. |
| **Profiling Engine** | Per-column statistics: cardinality, null rates, value distributions, distinct counts, sampled examples. Configurable for read-only sources and sampling-only mode to respect production constraints. |
| **Data Readiness Check** | Surfaces issues that would invalidate later analysis: high null rates in key columns, broken foreign keys, empty tables, columns with all-same values. Flagged by severity (critical / warning / info). Critical flags block downstream confidence; warnings inform it. |
| **AI Description Generator** | Drafts table and column descriptions from column names, types, sample values, statistics, and inferred relationships — but only for the gaps reconciliation identified. Each draft carries a confidence score and shows the grounding signals it used (column name, samples, related columns), so the SME can judge whether the draft is justified. The largest single time saver in the workbench. |
| **Business Glossary Builder** | Extracts likely acronyms and abbreviations from column names (`cust_id`, `dt_birth`, `qty_oh`) and proposes definitions based on context and sample values. Reuses any glossary the client provided as a baseline. SME confirms each entry, edits the definition, or rejects it. |
| **Pattern Catalog** | Detects schema-level patterns: star/snowflake structure, junction tables, audit columns (created_at, updated_by, etc.), and slowly-changing dimensions. SME confirms which patterns are real and which are coincidence. The catalog becomes the agent's "ground truth" for how this dataset works. |
| **KPI Definition Workbook** | A structured template the SME completes: KPI name, business definition, formula, source tables/columns, owner, edge cases, sample queries. Reuses any KPI definitions the client provided as starting points. Formulas and edge cases come from the SME — they have to. |
| **SME Review Workspace** | The interface where the SME confirms or corrects every artifact. Markdown is the source of truth (git-trackable, portable, survives any tooling change). A web UI sits on top for SMEs who don't want to edit markdown by hand — particularly important for non-technical SMEs. Either path writes back to the same markdown files. |
| **Strategy Recommender** | Reads the SME-confirmed schema model and recommends one of three retrieval strategies (Plain Schema, Vector, Knowledge Graph) with reasoning, confidence, and the signals that drove the choice. Runs after the SME review so its input is trusted, not inferred. |

### How the Workflow Flows

```
1. Connect           → YAML config, source connected
2. Discover          → schema introspected, relationships inferred
                       (each item tagged with confidence)
3. Profile           → per-column statistics gathered
                       (sampling / read-only modes honored)
4. Readiness Check   → blocking issues surfaced, severity-tagged
5. Import            → client-provided documentation ingested
                       (CSV/Excel, structured markdown, dbt manifest)
6. Reconcile         → client docs mapped to discovered schema:
                       matches, conflicts, gaps
7. AI Draft Gaps     → descriptions, glossary candidates, patterns,
                       KPI suggestions for what client didn't cover
                       (each draft shows its grounding signals)
8. SME Review        → workbench presents artifacts, prioritized by
                       confidence: confirm imports / resolve conflicts /
                       review drafts / fill remaining gaps
9. Recommender       → runs on confirmed model, recommends retrieval
                       strategy with reasoning + confidence
10. Output           → knowledge bundle written to JSON + markdown,
                       provenance tagged on every artifact
```

The SME never watches a black box. Every step produces an artifact they can read; every draft has confidence indicators and grounding signals; every decision can be overridden.

### The SME Review Workspace

Two ways the SME interacts:

**As a structured markdown bundle** (source of truth). One file per artifact type, organized by domain or table. The SME can edit directly in any editor. Saves are picked up by re-reading the files. Engineer-SMEs and anyone comfortable in a code editor will gravitate here.

**As a web UI on top of the same markdown** (for less technical SMEs — most business SMEs fall in this bucket). Browser-based. Walks the SME through artifacts in order — descriptions, glossary, patterns, KPIs, ambiguous relationships, conflicts. Side-by-side: imported value or AI draft on the left, editable version on the right, with grounding signals visible. Confidence indicators on every item so the reviewer knows what to scrutinize. Bulk actions for the obvious cases ("accept all High-confidence descriptions for this table"). Saves write back to the same markdown files.

The two paths are not separate systems. They're two views of the same source. Switching between them mid-review is fine.

### What the SME Reviews (and What They Don't Need To)

The workbench distinguishes between things that need careful review and things that can be accepted in bulk. The same confidence routing applies whether the source is a client document, an AI draft, or an inferred relationship:

| Confidence | Default behavior | Examples |
|---|---|---|
| **Explicit** | Auto-accepted, surfaced for awareness only | PKs and FKs declared in the schema; client documentation that perfectly matches discovered reality; descriptions where column name, samples, and statistics all line up perfectly |
| **Strong** | Used by default; flagged for spot-check | Inferred FKs with matching name + type + sample-value overlap; client doc entries with minor naming differences from the schema; descriptions where most signals agree |
| **Weak** | Not used silently; SME must decide | Inferred FKs based only on name pattern; ambiguous patterns; columns with conflicting signals; descriptions where grounding signals are thin or contradictory |

Conflicts between client documentation and discovered data are surfaced separately in the workflow with both versions visible — the SME picks the canonical answer. They don't get silently bucketed.

For a 200-table warehouse this means the SME isn't asked to review 200 tables of inferences. They resolve the few dozen weak-confidence items and conflicts, spot-check the strong ones if they want, and the explicit ones flow through. The accelerator earns its keep by routing review effort to where it actually matters.

### Provenance

Every artifact in the bundle carries a provenance marker. Consumers downstream (the Assembler, the agents, future re-runs) need to know where each piece of knowledge came from:

| Source | What it means |
|---|---|
| `client-provided` | Imported directly from client documentation, SME-confirmed as matching reality |
| `client-provided-reconciled` | Imported but adjusted because data showed something different — SME resolved the conflict |
| `ai-drafted` | The accelerator drafted this from the data; SME reviewed and accepted as-is |
| `ai-drafted-edited` | AI draft that the SME modified before accepting |
| `sme-authored` | Written by the SME from scratch (common for KPI formulas, business context, conflict resolutions) |

Provenance has practical consequences:
- Downstream agents can weight knowledge by source quality (`sme-authored` carries more weight than `ai-drafted`)
- Re-runs can re-import client material without overwriting prior SME edits — provenance prevents collisions
- The SME's contribution is visible and attributable, separating their judgment from the AI's drafting

### What Goes In / What Comes Out

**Inputs**:
- Database connection config (YAML/JSON)
- Optional: client-provided knowledge in supported formats (CSV/Excel data dictionaries, structured markdown, dbt manifests)
- Optional: knowledge bundles from prior engagements as starting points
- Optional: profiling constraints (read-only mode, sampling limits)

**Outputs** — the knowledge bundle, provenance-tagged:
- Schema map — tables, columns, types, relationships (SME-confirmed)
- Profiling report — per-column statistics
- Readiness report — issues found, severity, status
- Reconciliation report — matches, conflicts, gaps from client doc imports
- Table and column descriptions — confirmed
- Business glossary — confirmed
- Pattern catalog — confirmed patterns
- KPI definitions — completed workbook entries
- Strategy recommendation — chosen strategy, confidence, reasoning, signals
- Provenance tag on every artifact (`client-provided` / `client-provided-reconciled` / `ai-drafted` / `ai-drafted-edited` / `sme-authored`)
- Everything in JSON (downstream consumption) and markdown (human review)

The bundle schema is namespaced by source from day one, so future support for multi-source engagements is additive rather than a rewrite.

### The Three Retrieval Strategies

The Strategy Recommender chooses one of three. Each fits a distinct kind of schema:

| Strategy | When it fits | What downstream uses it as |
|---|---|---|
| **Plain Schema** | Small schemas (roughly under 15–20 tables), simple relationships, schema text fits cleanly into the prompt. | Schema is injected directly into the agent's prompt context. No indexing layer needed. |
| **Vector** | Medium schemas, mostly flat or simple-join, no strong fact/dimension hierarchy. Many tables but the agent only needs a few per query. | Tables, columns, descriptions, and glossary terms are embedded; the agent retrieves the relevant subset semantically per query. |
| **Knowledge Graph** | Complex schemas with clear fact/dimension structure, conformed dimensions reused across multiple facts, role-playing dimensions, multi-hop joins typical in analytical queries. | Entities, relationships, and patterns are modeled as a graph; the agent traverses the graph for context. |

### How the Recommender Decides

The recommender uses heuristics — not LLM calls — so its output is deterministic, debuggable, and testable. It runs after SME review, so the schema model it reasons over is trusted.

Signals it considers:
- Table count (small → Plain; medium → Vector; large → Vector or KG depending on complexity)
- Fact/dimension structure: presence and clarity of fact and dimension tables, conformed dimensions
- Relationship complexity: average join depth, fan-out from facts to dimensions, presence of role-playing dimensions
- Schema flatness: denormalized vs star vs snowflake vs heavily normalized
- Pattern catalog signals (the SME-confirmed patterns directly inform the choice)
- Imported knowledge: if the client's own documentation explicitly describes the schema as star, snowflake, or KG-shaped, that's a strong signal — high-quality client documentation weights more than AI inference
- Readiness signals: if data quality remains shaky after readiness check, KG quality will suffer; the recommender adjusts accordingly

Decision posture:
- Vector is the **conservative default** when signals are mixed. It's harder to break than KG and rarely worse than Plain at scale.
- KG is recommended only when **multiple strong signals** point that way. A poorly-built KG performs worse than Vector, so the bar is deliberately high.
- Plain is recommended only when the schema is genuinely small enough that any indexing layer would be over-engineering.

Sample recommendation output:

```
Recommended strategy: KNOWLEDGE_GRAPH
Confidence: HIGH

Signals detected:
  - 156 tables: 23 fact tables, 41 dimension tables
  - 12 conformed dimensions (SME-confirmed)
  - 4 role-playing dimension patterns (SME-confirmed)
  - Average join depth for typical analytical query: 4
  - Foreign key validity (post-readiness): 96%
  - Pattern catalog includes: star schema, conformed dimensions, SCD Type 2
  - Client documentation describes schema as Kimball-style warehouse

Reasoning:
  High dimension reuse, multi-fact join patterns, and corroborating
  client documentation all indicate graph traversal will outperform
  semantic retrieval.

Alternatives considered:
  Vector — MEDIUM confidence (would lose explicit conformed dim semantics)
  Plain  — LOW confidence (schema too large for prompt context)

→ Approve recommendation? [Y/n]   Override: [vector|plain|kg]
```

### Re-Runs

Schemas evolve. Client documentation arrives late. Tables get added, renamed, or retired. The workbench treats re-runs as first-class:

- A re-run on a known engagement preserves prior SME confirmations for unchanged items
- Only new, changed, or removed items go back through the AI generators or surface for SME re-review
- Late-arriving client documentation can be re-imported at any point without losing the SME work already done

### Scope

**In scope:**
- Single-source connection per engagement (Postgres / Snowflake / CSV)
- Schema extraction and relationship inference with confidence tagging
- Profiling with operational constraints (sampling, read-only)
- Data readiness check
- Client knowledge import and reconciliation across CSV/Excel, structured markdown, and dbt manifests
- Conflict and gap detection
- AI-drafted table and column descriptions for gaps (with grounding signals)
- Business glossary extraction and definition workflow
- Pattern catalog detection (star/snowflake, junction, audit columns, SCD)
- KPI definition workbook (structured template)
- SME review workspace — markdown source of truth + web UI
- Strategy recommender across Plain, Vector, and Knowledge Graph
- Bulk actions and confidence-based prioritization for large schemas
- Provenance tagging on every artifact
- Re-runs that preserve prior SME work
- Bundle schema namespaced by source for future multi-source support
- Configurable thresholds for all heuristic decisions

**Out of scope:**
- Multi-source engagements (one source per engagement; bundle schema is forward-compatible for future extension)
- Data migration, ETL, or transformation pipelines
- Real-time streaming ingestion
- Data warehouse management
- Unstructured document parsing as a primary input (separate retrieval concern)
- Building the actual representation (handled by Knowledge Representation Assembler)
- Replacing the SME (the entire premise is co-piloting them)
- Domain-specific KPI libraries (each engagement builds its own through the workbook)
- Direct API integrations with enterprise data catalogs (Collibra, Alation, Atlan) — file-based imports first; API integrations added when an engagement actually needs them
- AI-suggested KPI candidates (the workbook is a structured template; AI suggestion is a future enrichment)
- Fully synthesizing knowledge for cryptically-named schemas (e.g., `t1`, `col_a`) — the workbench falls back to "insufficient signal, SME authoring required" rather than fabricating

### Technical Direction

- **Connectors** are config-driven YAML. SQLAlchemy for relational sources; dedicated adapter for Snowflake.
- **Profiling** uses SQL-based sampling on large datasets, full scans on smaller ones. Configurable sample sizes. Read-only and sampling-only modes are first-class so production sources aren't impacted.
- **Client doc imports** are file-based. Each format (CSV/Excel, structured markdown, dbt manifest) has a dedicated parser. Reconciliation uses fuzzy matching on names, types, and sample values to map client docs to schema entities; weak matches surface for SME confirmation rather than being silently accepted.
- **AI generation** (descriptions, glossary suggestions) uses LLMs through the Agent Framework's LLM Gateway. Provider-agnostic. Prompt templates managed through the Agent Framework's prompt registry — we use our own infrastructure.
- **Decisions** (recommender, confidence scoring, readiness severity, reconciliation matching) use heuristics, not LLMs. Deterministic, debuggable, unit-testable. LLMs draft content; rules make decisions.
- **Markdown is the source of truth** for all SME-reviewable artifacts. Git-trackable, portable across engagements, survives tool changes.
- **The web UI is a thin layer** over the markdown bundle. Reads markdown, presents an editing experience, writes markdown back. Stateless — no separate database, no sync drift.
- **Confidence tagging** runs through the entire stack — every inference, draft, recommendation, and import match carries a confidence level so review effort can be prioritized.
- **Provenance tagging** is applied automatically as artifacts move through the pipeline.
- **Bulk operations** (accept all High-confidence items for a table; resolve all conflicts in a domain; etc.) are first-class in the review experience.
- **Prior knowledge import**: existing knowledge bundles from past engagements can be loaded as starting points. Provenance is preserved, so prior SME work is recognized as such.
- **Re-run preservation**: SME-confirmed items remain confirmed across re-runs unless the underlying schema item itself changed.

### Risks

| What could break | What we do about it |
|---|---|
| **AI-drafted descriptions are subtly wrong** — confidently stated, semantically off | Every draft carries a confidence score and shows the grounding signals (column name, samples, related columns) the AI used. SMEs see the *reasoning*, not just the output, making hallucinations easier to spot. SME review is mandatory before output is finalized. The output bundle marks each artifact with provenance — distinguishing `client-provided`, `client-provided-reconciled`, `ai-drafted`, `ai-drafted-edited`, and `sme-authored` — so downstream consumers know the source quality. |
| **Client documentation is stale or wrong** | Reconciliation explicitly compares every imported claim against discovered data. Conflicts surface to the SME with both versions visible. |
| **Naming conventions don't align between client docs and schema** | Fuzzy matching during reconciliation produces candidate maps with confidence levels. Weak matches require SME confirmation before being applied. SME can also explicitly map terms (e.g., "everywhere `cust_id` appears in our docs, treat it as `customer_id`"). |
| **SME review fatigue** — too many items, reviewer skims, quality collapses | Confidence-based prioritization. SME reviews weak-inference and conflict items in detail; strong-inference items get spot-check; explicit items flow through. Bulk-accept actions for obvious cases. The workbench measures review coverage so the bundle is honest about what's confirmed and what's pending. |
| **Insufficient grounding for cryptic schemas** — generic table/column names, no FKs, no client docs | The AI generator falls back gracefully: rather than fabricating descriptions, it marks items as "insufficient signal — SME authoring required" and presents the SME with a blank but structured template. Honest failure beats confident hallucination. |
| **Re-runs lose prior SME work** | Provenance and confirmation status are preserved across runs. Re-runs only re-process what changed. SME-confirmed items remain confirmed unless the underlying schema item itself changed. |
| **Glossary/pattern detection produces noise** — too many spurious entries, SME gives up | Detection thresholds are configurable. Defaults err on the side of fewer, higher-quality suggestions. Better to miss a few and have the SME add them than to drown them in false positives. |
| **KPI workbook becomes a bureaucratic form** — SME fills in fields just to dismiss them | Workbook entries are optional, not required. The accelerator works without KPI definitions; KPIs are an enrichment for downstream agents that benefit from them. |
| **Recommender bias from incomplete review** — SME confirms 60% of artifacts, skips the rest, recommender runs anyway | Recommender output explicitly notes review coverage ("recommendation made on 78% confirmed schema model — Vector confidence reduced from HIGH to MEDIUM accordingly"). |
| **Markdown source of truth gets out of sync with web UI state** | Single-write semantics: web UI changes go straight to markdown files; UI re-reads on the next view. No in-memory state. Stateless UI prevents drift. |
| **Profiling impacts a busy production source** | Operational constraints are honored: read-only mode and sampling-only mode are supported. Defaults are conservative; deeper profiling requires explicit opt-in. |
| **Connector sprawl** | Initial set is intentionally narrow (3 sources). New connectors added only when an engagement actually needs them. |
| **LLM-generated content drifts in quality** as providers/models change | Prompts and few-shot examples are versioned through the Agent Framework's prompt registry. A regression in one model is a swap or a tuned-prompt change, not a code rewrite. |

### Done Criteria

- A new engagement can connect to a data source, the workbench produces the full first-pass artifact set (imports + drafts), and the SME can review and confirm it end-to-end through either the markdown workflow or the web UI
- Client-provided documentation in the supported formats (CSV/Excel, structured markdown, dbt manifest) imports cleanly, reconciles against discovered data, and surfaces conflicts/gaps without silently overwriting either side
- AI-drafted descriptions are good enough that the SME's edits are corrections, not rewrites, in the majority of cases — and where grounding is insufficient, the workbench says so rather than fabricating
- Provenance and confidence are visible on every artifact, throughout the review workflow and in the final bundle
- A re-run preserves prior SME confirmations for unchanged items and only re-processes what changed
- The business glossary captures the terms that actually matter for downstream agents — verified by spot-checking against agent query patterns
- The pattern catalog accurately reflects the schema's real structure when those structures exist
- The KPI workbook is a useful artifact, not paperwork — engineers building agents downstream actually consult it
- The recommender produces strategy choices that hold up under expert scrutiny — when an SME disagrees, it's about the borderline cases, not the obvious ones
- For large schemas (100+ tables), the prioritization mechanism reduces SME review time by directing attention to the items that actually need it
- The output bundle (JSON + markdown, with provenance tags) is consumed cleanly by the Knowledge Representation Assembler with no shape mismatches

---

## DETAILED: Agent Framework Accelerator

### The Problem

Building agents from scratch means re-solving the same problems every time: tool wiring, prompt management, error handling, model routing, memory, multi-agent coordination. The framework provides reusable pieces so teams focus on business logic.

### What It Includes

| Component | What it does |
|---|---|
| **Base Agent Templates** | Pre-wired templates for common patterns: NL2SQL, Conversational QA, Plan-and-Execute, ReAct, Workflow. Each includes tool binding, output parsing, error handling, and logging hooks. NL2SQL is the immediate priority. |
| **Agent Orchestrator** | Patterns for multi-agent coordination: sequential, parallel, hierarchical (supervisor), and router-based (intent classification → agent routing). Includes state management and handoff protocols. Optional — single-agent setups don't need it. |
| **Prompt Management** | File-based prompt registry with versioning, Jinja2 templating, few-shot example banks. Prompts live in a structured directory, tracked in git. No database dependency. Used by the Data Layer accelerator's AI generators as well. |
| **Tool & Function Registry** | Cataloged, tested tool functions: SQL executor (with query safety checks), chart generator, REST API caller, sandboxed code executor, file reader/writer. Standardized interface: name, description, input schema, output schema, execute. |
| **LLM Gateway** | Abstraction over LLM providers (OpenAI, Anthropic, Google, Azure, Ollama). Unified API with fallback chains, rate limiting, token tracking. Switching providers is a config change. Used by both this accelerator and the Data Layer's AI generators. |
| **Memory & Context Manager** | Pluggable memory: buffer (recent N turns), summary (LLM-compressed), sliding window, entity extraction. Storage: in-memory, Redis, or PostgreSQL. Handles context window management automatically. |
| **Retrieval Adapter** | Connects the agent to whichever representation the upstream Knowledge Representation Assembler produced. Same interface regardless of strategy — agents don't need to know whether they're hitting a plain schema bundle, a vector index, or a knowledge graph. |

### What Goes In / What Comes Out

**Inputs**:
- Agent config (YAML/JSON): agent type, model, tool bindings, memory strategy, prompt references, retrieval source
- Orchestration graph config (for multi-agent setups)
- Prompt templates + few-shot examples
- A built representation from the Knowledge Representation Assembler (any strategy)
- Optionally: the knowledge bundle from the Data Layer (descriptions, glossary, KPIs) for grounding

**Outputs**:
- Runnable agent instance with bound tools, managed prompts, memory, error recovery, and logging hooks
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
- Strategy-agnostic retrieval through the unified contract

**Out of scope:**
- Custom agent architectures (use templates as starting point, extend)
- Distributed agents across network clusters
- Automated prompt optimization / tuning
- Domain-specific business logic tools (built per engagement using the standard interface)
- Model fine-tuning
- GUI-based agent builder

### Technical Direction

- **Assembly via config**: Agents defined in YAML/JSON. The framework returns a runnable instance.
- **LangGraph under the hood**: Used for orchestration. The config layer abstracts this, so swapping the engine for a specific engagement means changing the adapter, not rewriting configs or tools.
- **Prompts as files**: Structured directory hierarchy, git-tracked, Jinja2-templated. Few-shot examples stored alongside prompts, loaded based on domain context. The Data Layer accelerator's content-generation prompts live in the same registry.
- **Tool contract**: Standard interface — `name`, `description`, `input_schema`, `output_schema`, `execute`. Makes tools discoverable and composable.
- **Retrieval contract**: A single interface (`retrieve(query, top_k) → list[ContextItem]`) the agent calls regardless of upstream strategy. Vector lookups, graph traversals, and plain schema injection all conform to it.
- **Knowledge bundle integration**: Glossary terms and KPI definitions from the Data Layer can be injected as system context, used as few-shot examples, or surfaced as retrievable context — depending on agent type and configuration.
- **Incremental adoption**: Prompt management works without agent templates. The LLM gateway works without the orchestrator. Use what you need.

### Risks

| What could break | What we do about it |
|---|---|
| **Abstraction becomes the bottleneck** — adds latency, limits capabilities | Keep abstraction thin. If someone needs raw LangGraph or direct API calls, they can drop down. The framework helps; it should never block. |
| **Templates too rigid** — every engagement ends up forking | Inheritance model: base template provides skeleton, config overrides behavior. If >30% needs changing, that's a signal for a new template type. |
| **Prompt management adds overhead without value** | File-based, git-native, no separate service. Near-zero overhead. The value is versioning and cross-project reuse — including reuse by the Data Layer's content generators. |
| **Tool safety** — SQL executor runs something destructive, code executor escapes sandbox | SQL executor defaults to SELECT-only allowlist. Destructive ops require explicit opt-in with confirmation. Code executor uses isolated subprocess with resource limits. |
| **Gateway hides provider features** | Common operations use a unified API. Provider-specific features (structured outputs, vision, function calling variants) are accessible via pass-through. We abstract plumbing, not capability. |
| **Retrieval contract leaks the strategy** — agent code starts branching on "if vector do X, if KG do Y" | The contract returns `ContextItem` objects with a uniform shape. Strategy-specific metadata is exposed through optional fields, not required ones. Agents that need to know can ask; agents that don't, don't. |

### Done Criteria

- New NL2SQL agent assembled from config + prompts in a clean run
- LLM provider switch is a config change
- Adding a tool means adding a definition file, not modifying agent code
- Prompts are versioned, templated, shared across agents and across the Data Layer's AI generators without duplication
- At least two different agent types built from the same framework (proves it's actually reusable)
- The same agent code works against all three retrieval strategies (proves the contract holds)
- Knowledge bundle artifacts (glossary, KPIs) flow into agent context cleanly without bespoke wiring per engagement

---

## How They Connect

The accelerators are independent — any piece can be adopted on its own. The natural flow for an analytics or NL2SQL engagement looks like this:

```
  Client Data Source
       │
       ▼
┌─────────────────────────────────────────────┐
│  DATA LAYER ACCELERATOR                     │──→  Knowledge bundle:
│  (Knowledge Creation Workbench)             │     schema, descriptions,
│                                             │     glossary, patterns, KPIs,
│  Discover + Import client docs +            │     reconciliation report,
│  AI draft gaps → SME review →               │     recommendation,
│  confirmed bundle (provenance tagged)       │     provenance tags
└─────────────────────────────────────────────┘
       │
       ▼  (recommendation drives the next step)
┌─────────────────────────────────────────────┐
│  KNOWLEDGE REPRESENTATION ASSEMBLER         │──→  Built representation:
│  (executes chosen strategy)                 │     plain schema bundle, OR
│                                             │     vector index, OR
└─────────────────────────────────────────────┘     knowledge graph
       │
       ▼
┌─────────────────────────────────────────────┐
│  AGENT FRAMEWORK ACCELERATOR                │──→  Configured agent that
│  (consumes via unified retrieval contract,  │     retrieves through one
│   grounded in glossary + KPI definitions)   │     interface, regardless of
└─────────────────────────────────────────────┘     strategy chosen
       │
       ▼
  Agent serving user queries
```

Three design choices make this pipeline robust:

1. **The recommendation lives separately from the build.** Data Layer recommends; the Assembler executes. The recommender can change without touching strategy implementations, and a strategy can be invoked manually if a team already knows what they want.

2. **The Agent Framework consumes a unified retrieval contract.** Whether the data ended up as a plain schema bundle, a vector index, or a knowledge graph, the agent's code is the same. Switching strategies later doesn't mean rewriting the agent.

3. **The knowledge bundle is strategy-neutral.** Descriptions, glossary, patterns, and KPI definitions are useful regardless of which retrieval strategy is built. The same bundle can serve multiple agents that may use different strategies.

---

## Next Steps

1. Review this scope — push back on anything that's wrong, missing, or over-scoped
2. Confirm the artifact set: imports + descriptions + glossary + patterns + KPI workbook + strategy recommendation
3. Confirm the SME review experience: markdown source of truth + web UI on top
4. Detail the Knowledge Representation Assembler in its own scope document — strategies, the unified retrieval contract, pluggable backends per strategy
5. Define the retrieval contract before strategy implementation begins
6. Define the markdown bundle schema (with source-namespacing baked in for forward compatibility) before AI generators begin producing artifacts
7. Define the provenance tagging schema as part of the bundle contract
8. Remaining accelerators (Conversation/UX, Testing, DevOps, Security) get detailed as work reaches them

---

*Open areas and gaps will be tracked as this document evolves through implementation.*
