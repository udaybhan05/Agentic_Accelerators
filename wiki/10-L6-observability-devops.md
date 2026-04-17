# L6 — Observability & DevOps Layer

[← Back to Wiki Home](README.md) | [← L5 Testing & Evaluation](09-L5-testing-evaluation.md)

---

## Purpose

Production readiness infrastructure that every engagement needs but rarely builds properly. This layer provides structured logging, cost visibility, and deployment automation so that agent systems are operable, debuggable, and cost-efficient from day one.

---

## Components

### L6.1 — Logging & Tracing System

| | |
|---|---|
| **Priority** | P1 |
| **Type** | System |
| **Reuse potential** | 95% |

**Problem solved**: Debugging agent failures in production is nearly impossible without structured logs and trace IDs. When a query produces a wrong answer, you need to trace the full execution chain — from the user query through retrieval, LLM calls, tool execution, and response generation.

**Trace propagation model**:

```
User Query
  │  trace_id: abc-123
  │
  ├─→ Retrieval Step
  │     trace_id: abc-123, step: retrieval
  │     latency: 120ms, results: 5
  │
  ├─→ LLM Call
  │     trace_id: abc-123, step: llm_call
  │     model: gpt-4o, tokens_in: 2400, tokens_out: 350
  │     latency: 1200ms, cost: $0.032
  │
  ├─→ Tool Execution (SQL)
  │     trace_id: abc-123, step: tool_exec
  │     tool: sql_executor, query: SELECT...
  │     rows_returned: 42, latency: 85ms
  │
  └─→ Response Generation
        trace_id: abc-123, step: response
        format: table, total_latency: 1620ms
```

**Structured log format**: Every log entry includes:
- Timestamp
- Trace ID (request-level, propagated through entire chain)
- Agent ID
- Step identifier
- Latency
- Token count (for LLM calls)
- Cost (for LLM calls)

**Additional capabilities**:
- Conversation session logging (full turn-by-turn with metadata)
- **PII redaction** in logs (configurable patterns)
- Integration hooks for: stdout, file, OpenTelemetry, Datadog, CloudWatch

**Inputs → Outputs**:
```
Agent execution  →  Structured logs + traces
```

---

### L6.2 — Cost Tracking & Optimization

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Dashboard |
| **Reuse potential** | 95% |

**Problem solved**: LLM costs spiral without visibility. There is no per-query or per-agent cost attribution, making optimization impossible.

**Cost attribution hierarchy**:

```
Organization total cost
  └─→ Per-agent cost
       └─→ Per-tool cost
            └─→ Per-model cost
                 └─→ Per-request cost (input + output tokens)
```

**Key capabilities**:
- Per-request token counting (input + output) with cost calculation
- Per-agent, per-tool, per-model cost aggregation
- Cost dashboard: daily/weekly trends, top-cost queries, cost per conversation
- **Optimization alerts**: "Switching to model X would save Y% with Z% quality impact"
- **Budget guardrails**: Hard/soft limits per agent or per user

**Inputs → Outputs**:
```
Agent execution metadata  →  Cost reports, optimization suggestions
```

---

### L6.3 — CI/CD & Deployment Templates

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Templates |
| **Reuse potential** | 90% |

**Problem solved**: Deploying agent systems (containers, configs, secrets, health checks) is re-engineered per engagement.

**What's provided**:

| Asset | Description |
|---|---|
| **Dockerfile templates** | Multi-stage, optimized for Python ML workloads |
| **Docker Compose** | Local multi-service development setup |
| **Makefile** | Standard targets: `make dev`, `make test`, `make deploy`, `make help` |
| **CI pipeline templates** | GitHub Actions and GitLab CI |
| **Health check endpoints** | Readiness and liveness probes |
| **Environment config management** | dev / staging / prod separation |

**Local development workflow**:
- `make help` — Lists all available operations
- `make dev` — Start local development environment
- `make dev fresh` — Clean start with fresh environment
- `make test` — Run test suite
- `make deploy` — Deploy to target environment

**Inputs → Outputs**:
```
Project config  →  Ready-to-use deployment pipeline
```

**Scope boundaries**:
- In: Deployment templates, CI/CD configs, health checks
- Out: Cloud infrastructure provisioning (Terraform, CloudFormation, etc.)

---

## How L6 Integrates

```
All Layers ──→ L6.1 Logging (every component emits structured logs)
L3.5 LLM Gateway ──→ L6.2 Cost Tracking (token usage from every LLM call)
L6.3 CI/CD ──→ L5.4 Regression Testing (run tests on PR, block merge on regression)
L6.1 Logging ──→ L7.3 Audit Logger (structured logs feed compliance audit trail)
```

---

[Next: L7 — Security & Governance →](11-L7-security-governance.md)
