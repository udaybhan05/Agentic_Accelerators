# Agentic AI Application Accelerators — Wiki

> A complete, self-sufficient knowledge base for the Agentic AI Application Accelerators platform.
> Any developer — regardless of prior familiarity — can understand, extend, and maintain this system using only this documentation.

---

## Quick Navigation

### Foundation

| Document | Description |
|---|---|
| [System Overview & Vision](01-system-overview.md) | What the platform is, why it exists, and its strategic objectives |
| [Design Principles](02-design-principles.md) | Core philosophy and anti-patterns that guide all architectural decisions |
| [Architecture Overview](03-architecture-overview.md) | Full 8-layer architecture with visual diagrams and component relationships |

### Layer Documentation

| Layer | Document | Priority Components |
|---|---|---|
| L0 | [Client Onboarding](04-L0-client-onboarding.md) | Assessment Playbook, Kickoff Wizard, Architecture Decision Playbook |
| L1 | [Data Foundation](05-L1-data-foundation.md) | Connectors, Schema Discovery, Data Profiling, Knowledge Graph, Vector Store |
| L2 | [Knowledge & Retrieval](06-L2-knowledge-retrieval.md) | RAG Pipelines, Re-ranking, Document Processing |
| L3 | [Agent Framework](07-L3-agent-framework.md) | Agent Templates, Orchestrator, Prompt Management, Tool Registry, LLM Gateway |
| L4 | [Conversation & UX](08-L4-conversation-ux.md) | Behavior Config, Response Rendering, Follow-Up Engine, UI Components |
| L5 | [Testing & Evaluation](09-L5-testing-evaluation.md) | Eval Framework, Test Generation, Regression, User Simulation |
| L6 | [Observability & DevOps](10-L6-observability-devops.md) | Logging, Tracing, Cost Tracking, CI/CD |
| L7 | [Security & Governance](11-L7-security-governance.md) | Guardrails, Auth/RBAC, Audit & Compliance |

### Cross-Cutting Concerns

| Document | Description |
|---|---|
| [Data Flow & Integration Patterns](12-data-flow-and-integration.md) | How data moves through the system; inter-layer dependencies and integration points |
| [P0 Demo-Critical Path](13-demo-critical-path.md) | The 6 accelerators required for the analytics/NL2SQL demo agent |
| [Technical Decisions & Rationale](14-technical-decisions.md) | Key technology choices, trade-offs, and the reasoning behind each |
| [Risk Analysis & Mitigation](15-risk-analysis.md) | Comprehensive risk register with mitigation strategies |
| [Glossary & Roadmap](16-glossary-and-roadmap.md) | Terminology definitions and project evolution plan |

---

## How to Use This Wiki

1. **New to the project?** Start with [System Overview](01-system-overview.md), then [Design Principles](02-design-principles.md), then [Architecture Overview](03-architecture-overview.md).
2. **Working on a specific layer?** Go directly to the relevant layer document (L0–L7).
3. **Need to understand data flow?** See [Data Flow & Integration Patterns](12-data-flow-and-integration.md).
4. **Building the demo?** Start with [P0 Demo-Critical Path](13-demo-critical-path.md).
5. **Making a technology decision?** Check [Technical Decisions & Rationale](14-technical-decisions.md).
6. **Assessing risks?** See [Risk Analysis & Mitigation](15-risk-analysis.md).

---

## Document Status

| Document | Status |
|---|---|
| System Overview | Complete |
| Design Principles | Complete |
| Architecture Overview | Complete |
| L1 Data Foundation | Detailed |
| L3 Agent Framework | Detailed |
| L0, L2, L4–L7 | High-level scope defined |
| Demo-Critical Path | Defined (6 P0 accelerators) |

---

*This wiki is a living knowledge base. Each section will be expanded as implementation progresses.*
