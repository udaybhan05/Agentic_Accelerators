# L4 — Conversation & UX Layer

[← Back to Wiki Home](README.md) | [← L3 Agent Framework](07-L3-agent-framework.md)

---

## Purpose

This layer defines how the agent looks, feels, and behaves from the end-user perspective. It covers everything from conversation behavior and response formatting to UI components and channel integration. The goal is that conversation experience design becomes a **configurable, repeatable process** rather than ad-hoc implementation.

---

## Components

### L4.1 — Conversation Behavior Configuration

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Config |
| **Reuse potential** | 85% — framework reusable; config content is per-client |

**Problem solved**: Agent personality, tone, guardrails, and behavior are hard-coded and differ wildly across engagements.

**Configuration scope** (all via YAML/JSON):

| Category | What's configurable |
|---|---|
| **Persona & tone** | Formal enterprise, casual assistant, technical expert, etc. |
| **Greeting behavior** | Initial message, welcome flow |
| **Error messages** | How the agent communicates failures |
| **Clarification strategies** | How and when the agent asks for more information |
| **Escalation triggers** | Conditions for handoff to human or elevated action |
| **Topic boundaries** | What the agent will and won't discuss |
| **Refusal templates** | How the agent declines out-of-scope requests |
| **Sensitivity filters** | Content filtering rules |
| **Domain terminology** | Glossary that the agent respects in conversations |
| **Multi-language support** | Language-specific behavior settings |

**Behavior profiles** (pre-built starting points):
- `formal-enterprise` — Precise, professional, citations-heavy
- `casual-assistant` — Friendly, concise, conversational
- `technical-expert` — Detailed, accurate, jargon-appropriate

**Design rationale**: Rather than keeping behavior platform-specific, everything is made platform-agnostic through declarative YAML/JSON configuration. This pattern was validated in a prior auto-code-generator implementation.

**Inputs → Outputs**:
```
Behavior config file  →  Agent behavior constraints and personality
```

---

### L4.2 — Conversation Design Starter Playbook

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Playbook (not code — documented knowledge asset) |
| **Reuse potential** | 90% — patterns are universal; examples need domain adaptation |

**Problem solved**: Architects and clients lack a shared vocabulary for discussing how an agent should converse. This playbook bridges that communication gap.

**What it provides**:

| Section | Content |
|---|---|
| **Conversation pattern catalog** | Single-turn Q&A, multi-turn drill-down, guided workflow, proactive insights, clarification loops |
| **Context window strategy guide** | When to summarize, when to truncate, cost implications of different strategies |
| **UX guidelines** | When to show charts vs. text, progressive disclosure, confidence indicators |
| **Failure mode playbook** | What happens when agent doesn't know, hallucinates, or misunderstands |
| **Sample conversation scripts** | Ready-to-demo scripts per pattern (conversation flow examples) |

**This is not a technical accelerator** — it is a documented playbook that can be shared directly with clients. It sets realistic expectations about agent capabilities, the ups and downsides of comprehensive context management, and different conversation patterns that can be achieved.

**Inputs → Outputs**:
```
Use case description  →  Recommended conversation patterns + sample scripts
```

---

### L4.3 — Response Format & Rendering Engine

| | |
|---|---|
| **Priority** | **P0 (demo-critical)** |
| **Type** | Engine |
| **Reuse potential** | 85% |

**Problem solved**: Agents return raw text; formatting tables, charts, drill-down links, and rich cards is reimplemented each time.

**Response types**:

| Type | When Used | Example |
|---|---|---|
| **Plain text** | Simple answers | "Revenue was $1.2M last quarter" |
| **Markdown table** | Tabular data | Multi-column comparison results |
| **Interactive chart** | Time series, distributions | Plotly/Vega visualizations |
| **Summary card** | Single-value highlights | KPI cards with trend indicators |
| **Step-by-step breakdown** | Complex explanations | Query explanation walkthrough |

**Key capabilities**:
- **Auto-format selection** based on data shape:
  - Single value → Summary card
  - Tabular data → Markdown table
  - Time-series → Chart
  - Multi-step → Step-by-step breakdown
- **Drill-down link generation**: Follow-up questions embedded in response
- **Citation/source attribution** formatting
- **Export options**: PDF, CSV, clipboard-friendly

**Inputs → Outputs**:
```
Agent raw output + format config  →  Rendered, structured response
```

---

### L4.4 — Follow-Up Question Engine

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Engine |
| **Reuse potential** | 85% |

**Problem solved**: Users often don't know what to ask next; agents feel passive instead of guiding exploration.

**Follow-up strategies**:

| Strategy | Description | Example |
|---|---|---|
| **Drill-down** | Deeper into same topic | "Which region contributed most to this?" |
| **Pivot** | Related different angle | "How does this compare to last year?" |
| **Compare** | Cross-dimension analysis | "How do these metrics differ by product?" |
| **Validate** | Check an assumption | "Is this trend consistent across all segments?" |

**Key capabilities**:
- Context-aware follow-up generation (based on current query, data, and conversation history)
- Configurable: max suggestions, style (question vs. button label), relevance threshold
- **Knowledge graph-aware**: suggest questions that explore related entities from L1.3

**Inputs → Outputs**:
```
Current query + response + knowledge graph  →  Ranked follow-up suggestions
```

---

### L4.5 — UI Component Library

| | |
|---|---|
| **Priority** | P1 (P0 for basic chat component for demo) |
| **Type** | Library |
| **Reuse potential** | 80% |

**Problem solved**: Every engagement builds its own chat interface, chart components, and admin panels from scratch.

**Component catalog**:

| Component | Features |
|---|---|
| **Chat Interface** | Streaming support, markdown rendering, code blocks |
| **Chart/Visualization** | Auto-generated from agent output (Plotly/Vega) |
| **Data Table** | Sortable, filterable, exportable |
| **Feedback Widget** | Thumbs up/down + optional comment |
| **Admin Panel — Prompt Editor** | Edit and version prompts in browser |
| **Admin Panel — Conversation Log Viewer** | Browse and search past conversations |
| **Admin Panel — Eval Dashboard** | Quality scores and trends |

**Framework**: React (primary), with adapter patterns for other frameworks.

**Inputs → Outputs**:
```
Agent response payload  →  Rendered UI components
```

---

### L4.6 — Channel Adapter Kit

| | |
|---|---|
| **Priority** | P2 |
| **Type** | Kit |
| **Reuse potential** | 95% |

**Problem solved**: Clients want agents accessible from Slack, Teams, email, or embedded web — each requiring custom integration.

**Supported channels**:

| Channel | Protocol | Rendering |
|---|---|---|
| **Web** | REST / WebSocket | Full markdown + charts |
| **Slack** | Slack API | Block Kit |
| **Microsoft Teams** | Bot Framework | Adaptive Cards |
| **Email** | SMTP / IMAP | HTML email |

**Key capabilities**:
- Unified message format (normalize inbound, format outbound per channel)
- Channel-specific rendering adaptation
- Authentication bridge (map channel user identity to agent session)

**Out of scope**: Voice and telephony channels.

---

## Layer Integration

```
L3 Agent Framework ──→ L4.1 Behavior Config (applies to agent output)
L3 Agent Framework ──→ L4.3 Response Rendering (formats agent output)
L1.3 Knowledge Graph ──→ L4.4 Follow-Up Engine (entity-aware suggestions)
L4.3 Response Rendering ──→ L4.5 UI Components (renders in frontend)
L4.1 Behavior Config ──→ L4.6 Channel Adapters (tone/format per channel)
```

---

[Next: L5 — Testing & Evaluation →](09-L5-testing-evaluation.md)
