# L5 — Testing & Evaluation Layer

[← Back to Wiki Home](README.md) | [← L4 Conversation & UX](08-L4-conversation-ux.md)

---

## Purpose

> **The most commonly skipped but highest-impact layer for production readiness.**

Testing and evaluation for AI agents is fundamentally different from traditional software testing. Agent outputs are non-deterministic, quality is multidimensional, and regressions can be subtle. This layer provides the framework, tools, and processes to measure agent quality systematically — because **if you can't measure it, don't ship it**.

This layer was specifically called out as something most teams skip but most regret skipping. It is a **P0 priority** alongside the core agent framework.

---

## Components

### L5.1 — Evaluation Framework

| | |
|---|---|
| **Priority** | **P0 (demo-critical)** |
| **Type** | Framework |
| **Reuse potential** | 90% — evaluators are universal; test data is per-engagement |

**Problem solved**: No standardized way to measure agent quality across engagements. Teams ship without knowing if the agent is actually good.

**Built-in evaluators**:

| Evaluator | What it measures | Applies to |
|---|---|---|
| **Correctness** | Exact match or fuzzy match against ground truth | All agents |
| **Faithfulness** | Is the answer grounded in the retrieved context? | RAG agents |
| **Relevance** | Does the answer address the actual question? | All agents |
| **Helpfulness** | Would a user find this answer useful? | All agents |
| **Safety** | Does the output contain harmful or inappropriate content? | All agents |

**SQL-specific evaluators** (for NL2SQL agents):

| Evaluator | What it measures |
|---|---|
| **Execution accuracy** | Does the generated SQL produce the correct results? |
| **Result-set match** | Does the output data match expected output? |
| **Valid SQL check** | Is the generated SQL syntactically and semantically valid? |

**Additional capabilities**:
- **LLM-as-judge evaluator** with configurable rubrics
- **Batch evaluation runner**: Process entire test suite, generate report
- **Evaluation dashboard**: Scores over time, failure categorization, regression alerts
- **Benchmarking**: Compare versions, models, prompts against baseline

**Inputs → Outputs**:
```
Agent outputs + ground truth (or rubric)
    → Scores, reports, failure analysis, regression alerts
```

---

### L5.2 — Test Case Generator

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Tool |
| **Reuse potential** | 85% |

**Problem solved**: Manually writing test cases for conversational agents is slow, biased toward happy paths, and never comprehensive enough.

**Generation methods**:

| Method | Description |
|---|---|
| **Knowledge graph-based** | Generate questions a user would ask given the schema (from L1.3) |
| **Edge case generator** | Ambiguous queries, out-of-scope queries, adversarial inputs |
| **Conversation flow generator** | Multi-turn test scenarios |
| **Template-based** | Fill slots in query templates with real column/table names |

**Key capabilities**:
- LLM-assisted generation from knowledge graph
- Configurable difficulty levels and coverage targets
- Export: JSON, CSV, compatible with L5.1 evaluation framework

**Inputs → Outputs**:
```
Knowledge graph + schema + config  →  Test suite with expected outputs
```

---

### L5.3 — User Simulation Engine

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Engine |
| **Reuse potential** | 85% |

**Problem solved**: Testing multi-turn agents requires human testers clicking through conversations — slow and not scalable.

**Simulation personas**:

| Persona | Behavior Pattern |
|---|---|
| **Novice user** | Simple questions, needs guidance, may misunderstand |
| **Expert user** | Complex queries, domain jargon, expects precision |
| **Adversarial user** | Tries to break the agent, prompt injection, off-topic |

**Key capabilities**:
- Goal-driven conversations (simulate a user trying to achieve a specific outcome)
- Follow-up simulation (tests the agent's multi-turn coherence)
- Automatic success/failure detection against conversation goals
- Transcript logging for review

**Inputs → Outputs**:
```
User personas + conversation goals
    → Simulated conversations + success metrics + transcripts
```

---

### L5.4 — Regression Testing Suite

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Suite |
| **Reuse potential** | 90% |

**Problem solved**: Prompt changes, model upgrades, or knowledge graph edits silently break previously working queries.

**How it works**:

```
┌─────────────────┐     ┌─────────────────┐
│  Golden Test Set │     │  Current Agent   │
│  (curated queries│     │  (latest config) │
│  with verified   │     │                  │
│  outputs)        │     │                  │
└────────┬────────┘     └────────┬─────────┘
         │                       │
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │  Regression Runner    │
         │  (compare outputs)    │
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │  Diff-Based Report    │
         │  • What changed       │
         │  • Severity level     │
         │  • Pass/fail verdict  │
         └───────────────────────┘
```

**Key capabilities**:
- Golden test set management (curated queries with verified outputs)
- Diff-based reporting (what changed from last run)
- **CI/CD integration** (run on PR, block merge if regression detected)
- Severity classification: critical regression vs. minor output variation
- Snapshot testing for structured outputs

**Inputs → Outputs**:
```
Golden test set + current agent  →  Pass/fail report with diffs
```

---

### L5.5 — Prompt/Model A/B Testing Framework

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Framework |
| **Reuse potential** | 95% |

**Problem solved**: No data-driven way to decide between prompt variants or model choices. Decisions are made on gut feeling.

**Key capabilities**:
- Side-by-side evaluation of two configurations on same test set
- Metrics comparison dashboard
- Cost-performance Pareto analysis (quality vs. cost trade-off)
- Automatic winner recommendation based on configurable criteria

**Inputs → Outputs**:
```
Two agent configs + test set  →  Comparative report with recommendation
```

---

## Testing Strategy for NL2SQL Agents

The NL2SQL demo agent exercises a specific testing workflow:

```
1. Knowledge Graph (L1.3) generates test candidates
       │
       ▼
2. Test Case Generator (L5.2) creates test suite
       │  • Natural language questions
       │  • Expected SQL queries
       │  • Expected result sets
       ▼
3. Agent processes test suite
       │
       ▼
4. Evaluation Framework (L5.1) scores results
       │  • SQL execution accuracy
       │  • Result-set match
       │  • Valid SQL check
       │  • Response quality
       ▼
5. Regression Suite (L5.4) compares to baseline
       │  • Did quality improve or degrade?
       │  • Which queries broke?
       ▼
6. Report → Decision
       • Ship / don't ship / investigate
```

---

## Scope Boundaries

| In Scope | Out of Scope |
|---|---|
| Standardized evaluation metrics | Live A/B testing in production (see L5.5 for offline comparison) |
| Automated test case generation | Full mutation testing |
| Regression testing with CI/CD integration | Load/performance testing |
| User simulation for multi-turn testing | Statistical significance testing for tiny sample sizes |
| Offline A/B comparison of agent configs | Flaky-test management infrastructure |

---

[Next: L6 — Observability & DevOps →](10-L6-observability-devops.md)
