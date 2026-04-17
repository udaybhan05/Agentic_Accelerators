# Design Principles

[← Back to Wiki Home](README.md) | [← System Overview](01-system-overview.md)

---

## Why Principles Matter Here

Accelerator initiatives fail in predictable ways. These principles exist because those failure modes have been observed repeatedly across AI agent projects. Each principle is the **inversion** of a known failure pattern.

---

## Core Principles

### 1. Composable Building Blocks, Not a Platform

**Failure mode it prevents**: Accelerators become a "platform" nobody asked for.

Every accelerator is independently adoptable and ejectable. If a team does not need the prompt management system, they skip it. If they only need the data profiler, they use the data profiler. There is no central runtime, no required startup sequence, and no "all-or-nothing" adoption model.

**In practice**:
- The LLM Gateway works without the Agent Templates
- Prompt Management works without the Orchestrator
- The Data Profiler works without the Knowledge Graph Assembler
- Any component can be replaced with an alternative implementation

---

### 2. Useful on Day One

**Failure mode it prevents**: Over-abstraction makes nothing actually useful on real projects.

If a component is not useful on the current engagement within the current sprint, it is not ready. Accelerators are not theoretical frameworks — they solve concrete, immediate problems that the team faces today.

**Validation test**: Can this component save time on the engagement that is happening right now?

---

### 3. Configuration Over Code

**Failure mode it prevents**: Hard-coded assumptions break on the next client.

Behavior lives in YAML/JSON configuration, not in code. Switching databases, changing LLM providers, adjusting conversation tone, modifying quality rules — these are all configuration changes, not code rewrites.

**In practice**:
- Agent definition: YAML specifying type, model, tools, memory, prompts
- Data connections: YAML/JSON connection configs
- Conversation behavior: YAML persona, tone, guardrail settings
- Quality rules: Pluggable rule definitions in config

---

### 4. Measure It or Don't Ship It

**Failure mode it prevents**: Teams skip testing because "it's a chatbot."

Evaluation is a first-class concern, not something bolted on later. Every agent has measurable quality metrics. The testing and evaluation layer is explicitly called out as the **most commonly skipped but highest-impact** layer for production readiness.

**In practice**:
- Built-in evaluators for correctness, faithfulness, relevance, safety
- SQL-specific evaluators for NL2SQL agents
- Regression testing integrated into CI/CD
- Quality dashboards tracking scores over time

---

### 5. Built Through Real Projects

**Failure mode it prevents**: Accelerators decay because they were built in a vacuum.

Accelerators are **extracted from working implementations**, not theorized in isolation. Theory-first accelerators rot. The pattern is: build it for one engagement, see it repeated, then extract it as a reusable component.

**Validation test**: Has this pattern been proven on at least one real engagement before being generalized?

---

### 6. Start Narrow, Widen with Evidence

**Failure mode it prevents**: A 2-week piece of work balloons into 2 months.

Build for the current use case first. Generalize only when a second use case actually shows up and demands it. Premature generalization is one of the most expensive mistakes in accelerator development.

**In practice**:
- Data connectors: Start with 4–5 most common databases, add when a real engagement needs more
- Agent templates: NL2SQL first (the demo need), then expand
- Orchestration patterns: Simple patterns first, complex ones when real multi-agent use cases emerge

---

## How Principles Map to Architecture

| Principle | Architectural Consequence |
|---|---|
| Composable blocks | Each layer is independent; components have clean interfaces |
| Useful on day one | P0 prioritization; demo-critical path drives what gets built first |
| Config over code | YAML/JSON configs at every layer; no hard-coded client assumptions |
| Measure or don't ship | L5 (Testing & Evaluation) is a P0 priority, not an afterthought |
| Built from real work | Accelerators extracted from the current NL2SQL analytics engagement |
| Start narrow | Focused initial scope; explicit "out of scope" boundaries on every component |

---

## Anti-Patterns to Avoid

These are the warning signs that an accelerator is going off track:

| Anti-Pattern | What it looks like | What to do instead |
|---|---|---|
| **Platform creep** | Adding orchestration layers, service meshes, or admin UIs nobody asked for | Keep it as a library/toolkit. Add infrastructure only when proven necessary. |
| **Abstraction for abstraction's sake** | The abstraction layer adds latency and limits what developers can do | Keep abstractions thin. If someone needs to drop down to raw API calls, they can. |
| **Template rigidity** | Every engagement ends up forking the template because it does not bend | Use config overrides. If >30% needs changing, create a new template type. |
| **Gold plating** | Spending weeks on edge cases that may never occur | Ship the 80% case. Handle edge cases when they actually happen. |
| **Connector sprawl** | Trying to support every database and API before anyone asks | Start with the most common. Add when a real engagement demands it. |
| **Theory-first design** | Building components based on what might be needed, not what is needed | Extract patterns from working code. If it has not been needed yet, it is not ready. |

---

## Decision Framework

When making architectural or design decisions, evaluate against these questions in order:

1. **Does this solve a real problem on the current engagement?** If no, defer it.
2. **Is it independently adoptable?** If using it requires adopting other components, reconsider the coupling.
3. **Can behavior be changed via config?** If changing behavior requires code changes, extract it to config.
4. **Can it be measured?** If there is no way to evaluate whether it works, define metrics first.
5. **Is it narrow enough?** If the scope covers hypothetical future needs, cut back to the immediate need.

---

[Next: Architecture Overview →](03-architecture-overview.md)
