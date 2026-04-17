# Risk Analysis & Mitigation Strategies

[← Back to Wiki Home](README.md) | [← Technical Decisions](14-technical-decisions.md)

---

## Purpose

This document provides a comprehensive risk register for the accelerator platform. Each risk is categorized, assessed for likelihood and impact, and paired with specific mitigation strategies.

---

## Risk Categories

| Category | Description |
|---|---|
| **Technical** | Risks related to technology choices, implementation complexity |
| **Architectural** | Risks related to system design, coupling, scalability |
| **Process** | Risks related to development workflow, team coordination |
| **Adoption** | Risks related to team adoption and usage of accelerators |
| **Data** | Risks related to client data characteristics and quality |

---

## Risk Register

### Technical Risks

#### T1: Framework Becomes the Bottleneck

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | High |
| **Category** | Technical |

**Description**: The abstraction layer over LangGraph adds latency or limits what agents can do, forcing teams to work around the framework rather than through it.

**Mitigation**:
- Keep the abstraction layer thin — minimal overhead
- Provide escape hatches: developers can drop down to raw LangGraph or direct API calls at any point
- The framework helps; it should never block
- Monitor: if teams routinely bypass the framework, it signals the abstraction is too thick

---

#### T2: Template Rigidity

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | Medium |

**Description**: Agent templates are too opinionated, and every engagement ends up forking the template because it doesn't bend enough.

**Mitigation**:
- Inheritance model: base template provides skeleton, config overrides behavior
- **30% rule**: If more than 30% of a template needs changing, create a new template type rather than stretching
- Track forking frequency — frequent forks signal missing template variants

---

#### T3: Tool Safety Gaps

| | |
|---|---|
| **Likelihood** | Low |
| **Impact** | Critical |

**Description**: SQL executor runs a destructive query, or code executor escapes sandbox, causing data loss or security breach.

**Mitigation**:
- SQL executor: SELECT-only allowlist by default; destructive ops require explicit configuration opt-in with confirmation step
- Code executor: isolated subprocess with resource limits (memory, CPU, execution time)
- All tool executions logged with full audit trail
- Regular security review of tool implementations

---

#### T4: LLM Gateway Hides Provider Features

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | Medium |

**Description**: The unified API abstracts away provider-specific features that teams need (structured outputs, vision capabilities, specific function calling variants).

**Mitigation**:
- Gateway exposes common API for common operations
- **Pass-through mechanism** for provider-specific features — capability is not abstracted away, only plumbing
- Document which features are unified vs. pass-through

---

### Architectural Risks

#### A1: Connector Sprawl

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | Medium |

**Description**: Trying to support every database and API becomes a maintenance burden that dilutes focus.

**Mitigation**:
- Start with the 4–5 most common databases in the client base
- Add new connectors **only** when an actual engagement demands it
- Each new connector must have a named engagement that needs it

---

#### A2: Over-Engineering the Orchestrator

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | Low |

**Description**: Building complex orchestration patterns (debate, consensus, hierarchical) before they're needed, adding complexity without value.

**Mitigation**:
- Orchestrator is **optional** — single-agent setups don't use it
- Build orchestration patterns as real multi-agent use cases emerge
- Start with Sequential and Router patterns (the simplest); add Hierarchical and Debate when demanded

---

#### A3: Platform Creep

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | High |

**Description**: Accelerators evolve into a "platform" with central services, admin UIs, and deployment infrastructure that nobody asked for.

**Mitigation**:
- Enforce the "composable building blocks, not a platform" principle
- No shared runtime or central registry required
- Every component must be independently installable and usable
- Regular scope review against the design principles

---

### Data Risks

#### D1: False Confidence from Automated Profiling

| | |
|---|---|
| **Likelihood** | High |
| **Impact** | Medium |

**Description**: Automated data profiles miss semantic issues (e.g., a "status" column with inconsistent string values like "active", "Active", "ACTIVE", "enabled").

**Mitigation**:
- Quality reports include **"human review needed"** flags for ambiguous findings
- Profiling explicitly states it informs decision-making but does not replace domain understanding
- Knowledge graph assembler includes interactive preview-edit-commit flow for human validation

---

#### D2: Performance on Large Databases

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | Medium |

**Description**: Profiling 500+ tables with billions of rows is slow and expensive, potentially timing out or consuming excessive resources.

**Mitigation**:
- **Sampling-based profiling** by default — profile on representative samples, not full table scans
- Configurable sample sizes
- Option to scope profiling to specific schemas or tables (schema-filtered)
- Progress indicators for long-running profiling jobs

---

#### D3: Knowledge Graph Quality

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | High |

**Description**: LLM-generated descriptions and patterns in the knowledge graph are inaccurate or misleading, causing the NL2SQL agent to generate wrong queries.

**Mitigation**:
- **Interactive preview-edit-commit flow** — human experts review and correct before committing
- Knowledge graph is never auto-committed without review
- Evaluation framework (L5.1) measures query accuracy, providing feedback loop on knowledge graph quality
- Iterative refinement: knowledge graph improves as errors are identified and corrected

---

### Process Risks

#### P1: Scope Creep — 2 Weeks → 2 Months

| | |
|---|---|
| **Likelihood** | High |
| **Impact** | High |

**Description**: Accelerator development expands beyond the immediate need, attempting to cover hypothetical future requirements.

**Mitigation**:
- **"Start narrow, widen with evidence"** principle enforced at every scope review
- Each accelerator has explicit **"in scope" and "out of scope"** boundaries
- Build for the current use case first; generalize only when a second use case demands it
- P0 / P1 / P2 prioritization with strict discipline on deferring P2 items

---

#### P2: Quality Scorecard Without Actionable Guidance

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | Medium |

**Description**: Data quality scores are reported as numbers without context — "completeness: 72%" tells you nothing actionable.

**Mitigation**:
- Every quality finding must include three elements:
  1. **What is wrong** — the specific issue
  2. **Why it matters for agents** — the impact on agent performance
  3. **What to do about it** — actionable remediation steps
- Reports must pass the "so what?" test — if a finding doesn't lead to an action, it's not useful

---

### Adoption Risks

#### AD1: Prompt Management Overhead

| | |
|---|---|
| **Likelihood** | Low |
| **Impact** | Medium |

**Description**: Centralizing prompts adds process overhead without adding enough value, causing teams to bypass the system.

**Mitigation**:
- System is file-based, git-native, no database dependency, no separate service
- Overhead is near-zero — it works with the existing development workflow
- Value proposition is clear: versioning, rollback, cross-project reuse, A/B testing

---

#### AD2: Accelerators Decay Through Non-Use

| | |
|---|---|
| **Likelihood** | Medium |
| **Impact** | High |

**Description**: Accelerators are built once and then rot because they were designed in isolation and no engagement actually uses them.

**Mitigation**:
- **"Built through real projects"** principle — accelerators are extracted from working implementations, not theorized
- Every accelerator must be proven on at least one real engagement before being generalized
- Accelerators that are not used within two engagement cycles are candidates for deprecation

---

## Risk Heat Map

```
                    IMPACT
                Low    Medium    High    Critical
            ┌─────────┬─────────┬─────────┬─────────┐
    High    │         │  D1     │  P1     │         │
            │         │  P2     │         │         │
            ├─────────┼─────────┼─────────┼─────────┤
 L  Medium  │  A2     │  A1     │  T1     │         │
 I          │         │  T2,T4  │  A3     │         │
 K          │         │  D2     │  AD2    │         │
 E          │         │         │  D3     │         │
 L  ├───────┼─────────┼─────────┼─────────┼─────────┤
 I  Low     │         │  AD1    │         │  T3     │
 H          │         │         │         │         │
 O          │         │         │         │         │
 O          │         │         │         │         │
 D  └───────┴─────────┴─────────┴─────────┴─────────┘
```

---

## Top 5 Risks to Watch

1. **P1: Scope Creep** — Most likely and most impactful process risk
2. **D3: Knowledge Graph Quality** — Directly impacts demo agent quality
3. **T1: Framework Bottleneck** — Could slow down all teams if abstraction is too thick
4. **A3: Platform Creep** — Subtle drift that's hard to reverse
5. **T3: Tool Safety** — Low likelihood but catastrophic impact

---

[Next: Glossary & Roadmap →](16-glossary-and-roadmap.md)
