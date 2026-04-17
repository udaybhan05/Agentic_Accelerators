# L7 — Security & Governance Layer

[← Back to Wiki Home](README.md) | [← L6 Observability & DevOps](10-L6-observability-devops.md)

---

## Purpose

Non-negotiable for enterprise clients, often bolted on as an afterthought. This layer provides guardrails, access control, and audit capabilities that are designed to be integrated from the start — not retrofitted after deployment.

---

## Components

### L7.1 — Guardrails & Safety Engine

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Engine |
| **Reuse potential** | 90% |

**Problem solved**: Agents can hallucinate, leak sensitive information, execute harmful queries, or go off-topic without proper guardrails.

**Two-stage guardrail architecture**:

```
User Input
    │
    ▼
┌───────────────────────────────┐
│  INPUT GUARDRAILS             │
│  • Prompt injection detection │
│  • Topic boundary enforcement │
│  • PII detection in user input│
└───────────────┬───────────────┘
                │
                ▼
        Agent Processing
                │
                ▼
┌───────────────────────────────┐
│  OUTPUT GUARDRAILS            │
│  • Hallucination flag         │
│    (when agent isn't confident)│
│  • Sensitive data leakage     │
│    prevention                 │
│  • SQL safety checks          │
│    (no DROP, DELETE, UPDATE   │
│     without approval)         │
└───────────────┬───────────────┘
                │
                ▼
        Validated Response
```

**Key capabilities**:
- All guardrails configurable via YAML (easy to adjust per client's risk tolerance)
- **Bypass approval workflow** for human-in-the-loop override when guardrails block legitimate requests

**SQL safety checks** (critical for NL2SQL agents):
- Default: SELECT-only allowlist
- Destructive operations (DROP, DELETE, UPDATE, INSERT) require explicit configuration opt-in
- Confirmation step before execution of any approved destructive operation

**Inputs → Outputs**:
```
Agent input/output  →  Validated input/output OR blocked with explanation
```

---

### L7.2 — Authentication & Authorization Patterns

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Patterns |
| **Reuse potential** | 85% |

**Problem solved**: Agents need to respect data access controls — not every user should see every table or metric.

**RBAC template**:

| Role | Permissions | Example Restrictions |
|---|---|---|
| **Admin** | Full access to all data, configuration, and audit logs | No restrictions |
| **Analyst** | Query access to approved tables/columns | Cannot access PII columns, salary data |
| **Viewer** | Read-only access to pre-approved reports | Cannot run ad-hoc queries |

**Key capabilities**:
- Role-based access control templates
- **Data-level access control**: Restrict specific tables or columns per role
- Session management patterns
- API key and OAuth integration templates
- **Audit trail**: Who asked what, when, and what was returned

**Inputs → Outputs**:
```
User identity + query  →  Access-filtered agent response
```

---

### L7.3 — Audit & Compliance Logger

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Logger |
| **Reuse potential** | 90% |

**Problem solved**: Enterprise clients require audit trails for regulatory compliance. Building this after the fact is expensive and error-prone.

**What gets logged**:

| Category | Data Captured |
|---|---|
| **Conversation logs** | Immutable records with timestamps and user identity |
| **Decision audit trail** | Why the agent chose a particular action or tool |
| **Data access logging** | Which tables and columns were queried |
| **Tool execution records** | What tools were invoked, with what parameters |

**Key capabilities**:
- **Immutable** conversation logs (append-only, tamper-evident)
- Export formats: JSON, CSV for compliance review
- **Retention policy management**: Auto-archive and auto-delete based on configurable retention periods

**Inputs → Outputs**:
```
Agent interactions  →  Immutable audit records
```

---

## Security Model for NL2SQL Agents

The NL2SQL use case requires a specific security posture:

```
                        User Query
                            │
                            ▼
                    ┌───────────────┐
                    │ Authentication │  ← Who is this user?
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ Authorization  │  ← What tables/columns
                    │ (RBAC)        │     can they access?
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ Input Guards   │  ← Prompt injection?
                    │               │     Off-topic?
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ Agent + SQL   │  ← Generate query
                    │ Generation    │     (SELECT only)
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ SQL Safety    │  ← No destructive ops
                    │ Check         │     Approved tables only
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ Output Guards  │  ← PII filtering
                    │               │     Hallucination check
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ Audit Log     │  ← Record everything
                    └───────┬───────┘
                            │
                            ▼
                    Validated Response
```

---

## Scope Boundaries

| In Scope | Out of Scope |
|---|---|
| Input/output validation and safety filtering | Model-level safety training |
| RBAC templates and access control patterns | Identity provider setup (IdP) |
| Immutable audit logging | Compliance certification processes |
| Configurable guardrails via YAML | Real-time threat detection |
| SQL safety checks for NL2SQL agents | Network security and infrastructure hardening |

---

[Next: Data Flow & Integration Patterns →](12-data-flow-and-integration.md)
