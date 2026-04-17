# Architecture Overview

[← Back to Wiki Home](README.md) | [← Design Principles](02-design-principles.md)

---

## Layer Architecture

The system is organized into 8 layers (L0–L7), each addressing a distinct concern in the AI agent lifecycle. Layers are **independent** — each can be developed, adopted, and deployed separately.

```
┌─────────────────────────────────────────────────────────────────┐
│                  L0 · CLIENT ONBOARDING LAYER                   │
│            (Assessment, playbooks, onboarding wizard)           │
├─────────────────────────────────────────────────────────────────┤
│                  L1 · DATA FOUNDATION LAYER                     │
│        (Connectors, profiling, knowledge graph, vectors)        │
├─────────────────────────────────────────────────────────────────┤
│                  L2 · KNOWLEDGE & RETRIEVAL LAYER               │
│          (RAG pipelines, retrieval strategies, re-ranking)      │
├─────────────────────────────────────────────────────────────────┤
│                  L3 · AGENT FRAMEWORK LAYER                     │
│    (Templates, orchestration, tools, prompts, memory, LLM GW)  │
├─────────────────────────────────────────────────────────────────┤
│                  L4 · CONVERSATION & UX LAYER                   │
│     (Behavior config, response formats, follow-up engine, UI)   │
├─────────────────────────────────────────────────────────────────┤
│                  L5 · TESTING & EVALUATION LAYER                │
│     (Eval framework, test generation, regression, benchmarks)   │
├─────────────────────────────────────────────────────────────────┤
│                  L6 · OBSERVABILITY & DEVOPS LAYER              │
│       (Logging, tracing, CI/CD, deployment, cost tracking)      │
├─────────────────────────────────────────────────────────────────┤
│                  L7 · SECURITY & GOVERNANCE LAYER               │
│       (Guardrails, PII handling, auth, audit, compliance)       │
└─────────────────────────────────────────────────────────────────┘
```

---

## Layer Summary

| Layer | Name | Focus | # Components | Status |
|---|---|---|---|---|
| L0 | Client Onboarding | Assessment, playbooks, scaffolding | 3 | Planned |
| L1 | Data Foundation | Data connection, profiling, knowledge graphs | 5 | **Detailed** |
| L2 | Knowledge & Retrieval | RAG, re-ranking, document processing | 3 | Planned |
| L3 | Agent Framework | Agent templates, orchestration, tools, LLM | 6 | **Detailed** |
| L4 | Conversation & UX | Behavior, rendering, UI | 6 | Planned |
| L5 | Testing & Evaluation | Quality metrics, test gen, regression | 5 | Planned |
| L6 | Observability & DevOps | Logging, cost, CI/CD | 3 | Future |
| L7 | Security & Governance | Guardrails, auth, audit | 3 | Future |
| | | **Total** | **34** | |

---

## Component Inventory

### L0 — Client Onboarding (3 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L0.1 | Data Readiness Assessment Playbook | Playbook | P1 | 90% |
| L0.2 | Engagement Kickoff Wizard | Tool | P2 | 95% |
| L0.3 | Architecture Decision Playbook | Playbook | P1 | 85% |

### L1 — Data Foundation (5 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L1.1 | Data Connector Library | Library | P1 | 95% |
| L1.2 | Schema Discovery & Data Profiling Engine | Engine | **P0** | 90% |
| L1.3 | Knowledge Graph Generation Assembler | Tool | **P0** | 80% |
| L1.4 | Vector Store Management Toolkit | Toolkit | P1 | 90% |
| L1.5 | Synthetic & Sample Data Generator | Tool | P2 | 85% |

### L2 — Knowledge & Retrieval (3 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L2.1 | RAG Pipeline Templates | Templates | P1 | 85% |
| L2.2 | Re-ranking & Filtering Strategy Library | Library | P2 | 95% |
| L2.3 | Document Processing Pipeline | Pipeline | P1 | 95% |

### L3 — Agent Framework (6 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L3.1 | Base Agent Template Library | Templates | **P0** | 80% |
| L3.2 | Agent Orchestrator Framework | Framework | P1 | 85% |
| L3.3 | Prompt Management System | System | P1 | 90% |
| L3.4 | Tool & Function Registry | Registry | **P0** | 90% |
| L3.5 | LLM Gateway / Model Router | Gateway | P1 | 95% |
| L3.6 | Memory & Context Management | Library | P1 | 90% |

### L4 — Conversation & UX (6 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L4.1 | Conversation Behavior Configuration | Config | P1 | 85% |
| L4.2 | Conversation Design Starter Playbook | Playbook | P1 | 90% |
| L4.3 | Response Format & Rendering Engine | Engine | **P0** | 85% |
| L4.4 | Follow-Up Question Engine | Engine | P1 | 85% |
| L4.5 | UI Component Library | Library | P1 | 80% |
| L4.6 | Channel Adapter Kit | Kit | P2 | 95% |

### L5 — Testing & Evaluation (5 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L5.1 | Evaluation Framework | Framework | **P0** | 90% |
| L5.2 | Test Case Generator | Tool | P1 | 85% |
| L5.3 | User Simulation Engine | Engine | P2 | 85% |
| L5.4 | Regression Testing Suite | Suite | P1 | 90% |
| L5.5 | Prompt/Model A/B Testing Framework | Framework | P2 | 95% |

### L6 — Observability & DevOps (3 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L6.1 | Logging & Tracing System | System | P1 | 95% |
| L6.2 | Cost Tracking & Optimization | Dashboard | P2 | 95% |
| L6.3 | CI/CD & Deployment Templates | Templates | P1 | 90% |

### L7 — Security & Governance (3 components)

| ID | Component | Type | Priority | Reuse % |
|---|---|---|---|---|
| L7.1 | Guardrails & Safety Engine | Engine | P1 | 90% |
| L7.2 | Authentication & Authorization Patterns | Patterns | P2 | 85% |
| L7.3 | Audit & Compliance Logger | Logger | P2 | 90% |

---

## Priority Distribution

```
P0 (Demo-Critical):  6 components  ████████████
P1 (High):          18 components  ████████████████████████████████████
P2 (Nice-to-have):  10 components  ████████████████████
```

| Priority | Count | Focus |
|---|---|---|
| **P0** | 6 | Must-have for NL2SQL demo agent |
| **P1** | 18 | High value; build as capacity allows |
| **P2** | 10 | Nice-to-have; defer until needed |

---

## Inter-Layer Dependencies

While layers are independently adoptable, they are most powerful when combined. The natural data flow for an analytics/NL2SQL engagement:

```
                    L0 · CLIENT ONBOARDING
                    (Assessment, scaffolding)
                           │
                           ▼
    ┌──────────────────────────────────────────┐
    │           L1 · DATA FOUNDATION            │
    │  ┌─────────┐  ┌──────────┐  ┌─────────┐ │
    │  │Connector │→ │ Schema   │→ │ Quality │ │
    │  │ Library  │  │Discovery │  │Assessor │ │
    │  └─────────┘  └──────────┘  └─────────┘ │
    │              ↓                            │
    │  ┌──────────────────────┐                │
    │  │ Knowledge Graph      │                │
    │  │ Generation Assembler │                │
    │  └──────────────────────┘                │
    └──────────────────────────────────────────┘
                           │
              Schema + Profiles + Knowledge Graph
                           │
                           ▼
    ┌──────────────────────────────────────────┐
    │           L3 · AGENT FRAMEWORK            │
    │  ┌─────────┐  ┌──────────┐  ┌─────────┐ │
    │  │ Agent   │  │  Prompt  │  │  Tool   │ │
    │  │Template │← │Management│  │Registry │ │
    │  └─────────┘  └──────────┘  └─────────┘ │
    │       ↑                                   │
    │  ┌─────────┐  ┌──────────┐               │
    │  │  LLM   │  │ Memory & │               │
    │  │Gateway  │  │ Context  │               │
    │  └─────────┘  └──────────┘               │
    └──────────────────────────────────────────┘
                           │
                    Runnable Agent
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
    ┌──────────┐  ┌──────────┐  ┌──────────┐
    │ L4 · UX  │  │L5 · TEST │  │L6 · OPS  │
    └──────────┘  └──────────┘  └──────────┘
              │            │            │
              └────────────┼────────────┘
                           ▼
                  ┌──────────────┐
                  │L7 · SECURITY │
                  └──────────────┘
```

---

## Component Type Classification

| Type | Description | Examples |
|---|---|---|
| **Library** | Collection of reusable functions/classes | Data Connectors, Re-ranking Strategies |
| **Engine** | Processing system with defined input/output | Schema Discovery, Evaluation Framework |
| **Framework** | Structural foundation with extension points | Agent Orchestrator, A/B Testing |
| **Templates** | Pre-built starting points for common patterns | Agent Templates, RAG Pipelines, CI/CD |
| **Tool** | Standalone utility for a specific task | Knowledge Graph Assembler, Test Generator |
| **System** | Integrated subsystem with its own lifecycle | Prompt Management, Logging & Tracing |
| **Playbook** | Documented knowledge asset (not code) | Data Readiness Assessment, Conversation Design |
| **Config** | Declarative behavior definition | Conversation Behavior, Quality Rules |

---

[Next: L0 — Client Onboarding →](04-L0-client-onboarding.md)
