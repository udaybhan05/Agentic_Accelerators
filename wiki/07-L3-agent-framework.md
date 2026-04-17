# L3 — Agent Framework Layer

[← Back to Wiki Home](README.md) | [← L2 Knowledge & Retrieval](06-L2-knowledge-retrieval.md)

> **Status**: Detailed specification — this is one of the two priority layers with full scope definition.

---

## Purpose

Every agent built from scratch re-solves the same problems: how to wire tools, manage prompts, handle errors, route between models, manage memory, and orchestrate multiple agents. This accelerator provides the reusable building blocks so the team focuses on **business logic, not plumbing**.

---

## Components

### L3.1 — Base Agent Template Library

| | |
|---|---|
| **Priority** | **P0 (demo-critical)** |
| **Type** | Templates |
| **Reuse potential** | 80% — templates reusable; tool sets and prompts are engagement-specific |

**Problem solved**: Every agent is built from scratch — tool binding, memory wiring, error handling, retry logic, output parsing.

**Available templates**:

| Template | Description | Priority |
|---|---|---|
| **NL2SQL Agent** | Natural language → SQL query → formatted results | **P0** (immediate demo need) |
| **Conversational QA** | Multi-turn Q&A over documents or knowledge base | P1 |
| **ReAct Agent** | Reasoning + Acting loop with tool use | P1 |
| **Plan-and-Execute** | Breaks complex tasks into plan steps, executes sequentially | P1 |
| **Workflow Agent** | Follows predefined business process flows | P2 |

**What each template includes**:
- Tool binding (connect to tool registry)
- Memory integration (connect to memory manager)
- Structured output parsing
- Error handling and retry logic
- Logging hooks (connect to observability)

**Technical approach**:
- **Framework-agnostic core** with adapters for LangGraph, CrewAI, AutoGen
- **Declarative agent definition** via YAML/JSON config
- **Inheritance model**: Base template provides skeleton → config overrides behavior
- If >30% of a template needs changing, that signals the need for a **new template type**, not stretching the existing one

**Inputs → Outputs**:
```
Agent config (type, tools, model, memory strategy)  →  Runnable agent instance
```

---

### L3.2 — Agent Orchestrator Framework

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Framework |
| **Reuse potential** | 85% — orchestration patterns are universal |

**Problem solved**: Wiring multi-agent systems (routing, delegation, consensus, error recovery) is complex and re-done for each project.

**Orchestration patterns**:

| Pattern | Description | When to Use |
|---|---|---|
| **Sequential** | Agents execute in order; output of one feeds the next | Linear workflows |
| **Parallel** | Multiple agents run simultaneously; results merged | Independent subtasks |
| **Hierarchical (Supervisor)** | Supervisor agent delegates to specialized sub-agents | Complex, multi-capability tasks |
| **Router-based** | Intent classification routes to the appropriate agent | Multi-purpose assistants |
| **Debate/Consensus** | Multiple agents propose answers; best is selected | High-stakes decisions requiring validation |

**Key capabilities**:
- State management across agents (shared context, handoff protocols)
- Conditional routing based on intent classification or confidence scores
- Human-in-the-loop breakpoints (pause for approval before critical actions)
- Configurable via graph definitions (YAML or programmatic)

**Important design choice**: The orchestrator is **optional**. Single-agent setups do not need it. It exists for genuine multi-agent coordination — not to make simple things complex.

**Inputs → Outputs**:
```
Agent definitions + orchestration graph config
    → Multi-agent system with managed execution flow and state
```

---

### L3.3 — Prompt Management System

| | |
|---|---|
| **Priority** | P1 |
| **Type** | System |
| **Reuse potential** | 90% — system is fully reusable; prompt content is engagement-specific |

**Problem solved**: Prompts are scattered in code, un-versioned, hard to A/B test, and duplicated across projects. Prompts are the "code" of AI applications — having them unmanaged is the #1 maintenance headache.

**Architecture**: **File-based, git-native, no database dependency, no separate service**.

```
prompts/
├── system/
│   ├── nl2sql_v1.jinja2
│   ├── nl2sql_v2.jinja2
│   └── conversational_qa_v1.jinja2
├── user/
│   ├── query_template.jinja2
│   └── clarification_template.jinja2
├── few-shot/
│   ├── nl2sql_examples/
│   │   ├── finance_domain.json
│   │   └── healthcare_domain.json
│   └── qa_examples/
│       └── general.json
└── tool-calling/
    ├── sql_executor.jinja2
    └── chart_generator.jinja2
```

**Key capabilities**:
- Centralized prompt store (file-based or DB-backed)
- **Version control with rollback** — prompts are git-tracked
- **Jinja2 template variables** for dynamic interpolation
- Prompt variants for A/B testing
- Few-shot example management (per-domain example banks)
- System prompt, user prompt, and tool-calling prompt separation
- Export/import for cross-project sharing

**Inputs → Outputs**:
```
Prompt key + template variables  →  Rendered prompt string
```

---

### L3.4 — Tool & Function Registry

| | |
|---|---|
| **Priority** | **P0 (demo-critical)** |
| **Type** | Registry |
| **Reuse potential** | 90% |

**Problem solved**: Reusable tools (SQL executor, API caller, calculator, chart generator) are re-implemented per project.

**Pre-built tools**:

| Tool | Description | Safety |
|---|---|---|
| **SQL Executor** | Executes SQL queries against connected databases | SELECT-only allowlist by default; destructive ops require explicit opt-in with confirmation |
| **Chart/Visualization Generator** | Creates charts from data (Plotly/Vega) | N/A |
| **REST API Caller** | Makes HTTP requests to external APIs | Configurable URL allowlist |
| **Python Code Executor** | Runs Python in sandboxed environment | Isolated subprocess with resource limits |
| **File Reader/Writer** | Reads and writes local files | Path restrictions |
| **Web Search** | Searches the web for information | N/A |

**Standardized tool interface (tool contract)**:

```
┌─────────────────────────────────────┐
│  Tool Interface                      │
├─────────────────────────────────────┤
│  name: string                        │
│  description: string                 │
│  input_schema: JSON Schema           │
│  output_schema: JSON Schema          │
│  execute(input) → output             │
└─────────────────────────────────────┘
```

Every tool implements this contract, making tools:
- **Discoverable** — agents can search/filter available tools by capability tags
- **Composable** — output of one tool can be input to another (e.g., SQL execute → chart generate)
- **Testable** — standardized interface enables automated testing

**Adding a new tool** means adding a tool definition file, not modifying agent internals.

---

### L3.5 — LLM Gateway / Model Router

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Gateway |
| **Reuse potential** | 95% — fully provider-agnostic |

**Problem solved**: Hard-coded model dependencies make it painful to switch providers, handle rate limits, or optimize cost.

**Supported providers**:

| Provider | Models |
|---|---|
| **OpenAI** | GPT-4, GPT-4o, GPT-3.5 |
| **Anthropic** | Claude 3.5, Claude 3 |
| **Google** | Gemini Pro, Gemini Ultra |
| **Azure OpenAI** | Azure-hosted OpenAI models |
| **Ollama** | Local open-source models |

**Routing strategies**:

| Strategy | Description |
|---|---|
| **Cost-based** | Route to cheapest model that meets quality threshold |
| **Latency-based** | Route to fastest-responding provider |
| **Capability-based** | Route based on task requirements (vision, function calling, etc.) |
| **Fallback chains** | If primary model fails, automatically route to backup |

**Key capabilities**:
- **Unified API** across all providers — switching providers is a config change
- Rate limiting and request queuing
- Response caching (semantic similarity-based)
- Token usage tracking and cost attribution per agent/task
- **Pass-through mechanism** for provider-specific features (structured outputs, vision, function calling variants)

**Design philosophy**: The gateway abstracts **plumbing**, not **capability**. Provider-specific features remain accessible.

**Inputs → Outputs**:
```
Model request + routing config  →  LLM response with usage metadata
```

---

### L3.6 — Memory & Context Management

| | |
|---|---|
| **Priority** | P1 |
| **Type** | Library |
| **Reuse potential** | 90% |

**Problem solved**: Managing short-term conversation history, long-term user preferences, and shared agent state is ad-hoc. Without managed memory, agents either run out of context window or send too much irrelevant history.

**Memory types**:

| Type | How it works | Best for |
|---|---|---|
| **Buffer** | Stores recent N turns verbatim | Short conversations |
| **Summary** | LLM-compressed conversation summary | Long conversations |
| **Window** | Sliding window of recent context | Moderate conversations |
| **Entity** | Extracts and tracks named entities across turns | Entity-heavy domains |
| **Vector** | Semantic lookup of relevant past context | Very long histories |

**Storage backends**:

| Backend | Use Case |
|---|---|
| **In-memory** | Development, testing, ephemeral sessions |
| **Redis** | Production with fast access requirements |
| **PostgreSQL** | Production with persistence and query needs |
| **File-based** | Simple deployments, debugging |

**Key capabilities**:
- Shared memory for multi-agent systems
- Automatic context window management (truncation strategies when approaching token limits)
- Session persistence and restoration

**Inputs → Outputs**:
```
Conversation turns + memory config  →  Managed context for LLM calls
```

---

## Technical Architecture

### Config-Driven Assembly

The fundamental pattern is **agents defined in config, assembled by the framework**:

```yaml
agent:
  name: analytics-agent
  type: nl2sql
  model:
    provider: openai
    model: gpt-4o
    fallback: anthropic/claude-3-5-sonnet
  tools:
    - sql_executor
    - chart_generator
  memory:
    type: buffer
    max_turns: 20
    backend: redis
  prompts:
    system: prompts/system/nl2sql_v2.jinja2
    few_shot: prompts/few-shot/nl2sql_examples/finance_domain.json
```

### Framework Under the Hood

- **LangGraph** as the primary orchestration engine — proven state management, flexible graph-based orchestration
- The **config layer abstracts this** — if swapping to a different engine for a specific engagement, only the adapter changes, not the configs or tools
- **Gradual adoption**: Components can be used independently

---

## Scope Boundaries

| In Scope | Explicitly Out of Scope |
|---|---|
| Agent templates for common patterns | Fully custom agent architectures (use templates as starting point) |
| Multi-agent orchestration patterns | Distributed agents across network clusters |
| Centralized prompt management with versioning | Automated prompt optimization / prompt tuning |
| Pre-built tool functions with standard interfaces | Domain-specific business logic tools (built per engagement) |
| LLM provider abstraction and routing | Model fine-tuning or training |
| Pluggable memory backends | Custom memory compression models |
| Configuration-driven agent assembly | GUI-based agent builder (future consideration) |

---

## Success Criteria

This layer is "done enough" when:

1. A new **NL2SQL agent can be assembled from config + prompts in under a day** (not a week)
2. Switching the underlying **LLM provider is a config change**, not a code change
3. Adding a new tool to an agent is **adding a tool definition file**, not modifying agent internals
4. Prompts are **version-controlled, templated, and shared** across agents without duplication
5. At least **two different agent types** (e.g., NL2SQL + Conversational QA) are built from the same framework, proving reusability

---

## Risks and Mitigations

| Risk | Mitigation |
|---|---|
| **Framework becomes the bottleneck** — abstraction adds latency or limits capabilities | Keep abstraction thin. If someone needs raw LangGraph or direct API calls, they can drop down. The framework helps; it never blocks. |
| **Template rigidity** — every engagement forks | Inheritance model: base template provides skeleton, config overrides behavior. >30% change = new template type. |
| **Prompt management overhead** | File-based, git-native, no database dependency. Near-zero overhead. Value = versioning and reuse. |
| **Tool safety gaps** — SQL executor runs destructive query | SQL executor: SELECT-only allowlist by default. Destructive ops require explicit config-level opt-in with confirmation. Code executor: isolated subprocess with resource limits. |
| **Gateway hides provider features** | Common operations use unified API. Provider-specific features accessible via pass-through. Plumbing is abstracted; capability is not. |
| **Over-engineering for single-agent use** | Orchestrator is optional. Single agents use templates directly. |

---

[Next: L4 — Conversation & UX →](08-L4-conversation-ux.md)
