# L0 — Client Onboarding Layer

[← Back to Wiki Home](README.md) | [← Architecture Overview](03-architecture-overview.md)

---

## Purpose

The glue layer that ties everything together for a new engagement. When a new client comes in, the team needs structured processes and tools to assess data readiness, make architectural decisions, and scaffold the project. This layer ensures that onboarding follows a repeatable, high-quality process rather than ad-hoc improvisation.

---

## Components

### L0.1 — Data Readiness Assessment Playbook

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Playbook (not code — structured knowledge asset) |
| **Reuse potential** | 90% — questionnaire and rubric are universal; only scoring weights adjust per domain |

**Problem solved**: Teams jump into building agents without understanding if the client's data can actually support the desired use case. This leads to wasted effort and failed demos.

**What it provides**:
- Checklist-driven assessment across five dimensions: **completeness**, **freshness**, **structure**, **accessibility**, and **sensitivity**
- Scoring model that outputs a readiness grade: **Red** (not ready) / **Amber** (needs work) / **Green** (ready)
- Gap analysis report with remediation recommendations
- Decision matrix: what agent patterns are viable vs. not viable given current data state

**Inputs → Outputs**:
```
Client data catalog + sample extracts + DB access details
    → Readiness scorecard + gap report + recommended architecture pattern
```

**Scope boundaries**:
- In: Structured assessment, scoring, gap analysis, recommendations
- Out: Actual data migration or remediation work

---

### L0.2 — Engagement Kickoff Wizard

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Tool (CLI or web-based) |
| **Reuse potential** | 95% — template-driven, only metadata changes |

**Problem solved**: Every engagement reinvents the setup — environment provisioning, repo scaffolding, tool access, team onboarding docs. This wastes the first 1–2 weeks.

**What it provides**:
- Generates project repository from templates (monorepo or multi-repo)
- Provisions configuration files (`.env`, `docker-compose`, `Makefile`)
- Scaffolds folder structure per selected accelerators
- Creates onboarding README with team playbook links
- Generates initial task board with standard task breakdown

**Inputs → Outputs**:
```
Engagement name + client domain + selected accelerators + team roster
    → Ready-to-develop project scaffold
```

**Development approach**: `Makefile` with standard targets (`make dev`, `make test`, `make deploy`, `make help`) is the expected interface for local development. The `make help` command lists all available operations. `make dev fresh` provides a clean local environment setup.

**Scope boundaries**:
- In: Project scaffolding, config generation, documentation templates
- Out: Client-side infrastructure setup

---

### L0.3 — Architecture Decision Playbook

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Playbook (structured knowledge asset) |
| **Reuse potential** | 85% — patterns are universal; domain-specific overlays needed |

**Problem solved**: Architects re-derive the same agent architecture trade-offs for every engagement — single vs. multi-agent, RAG vs. fine-tuning, etc.

**What it provides**:
- Decision trees for: agent topology, retrieval strategy, model selection, memory strategy
- Reference architecture diagrams for common patterns:
  - NL2SQL (natural language to SQL)
  - Document Q&A
  - Workflow automation
- Anti-pattern catalog with failure examples
- Cost-performance trade-off matrices

**Inputs → Outputs**:
```
Use case description + data readiness grade + latency/cost constraints
    → Recommended architecture pattern + justification
```

**This playbook is particularly valuable** for setting client expectations about what agents can and cannot do with the current state of their data. It provides meaningful insights on how to look at data from an agent perspective.

**Scope boundaries**:
- In: Decision frameworks, reference architectures, trade-off analysis
- Out: Actual implementation (this is a knowledge asset, not a tool)

---

## How L0 Feeds Other Layers

```
L0.1 Data Readiness Assessment
    │
    ├──→ L1 (Data Foundation): Identifies which connectors and profiling are needed
    ├──→ L0.3 (Architecture Decisions): Readiness grade constrains viable patterns
    │
L0.3 Architecture Decision Playbook
    │
    ├──→ L3 (Agent Framework): Determines agent topology and template selection
    ├──→ L2 (Knowledge & Retrieval): Determines retrieval strategy
    │
L0.2 Engagement Kickoff Wizard
    │
    └──→ All layers: Scaffolds project structure with selected accelerators
```

---

[Next: L1 — Data Foundation →](05-L1-data-foundation.md)
