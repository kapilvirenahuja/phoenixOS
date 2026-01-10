# Orchestration

> The execution engine: intent detection, routing, and recipe execution

---

## Overview

Orchestration is the system that:
1. **Detects intents** — Pattern match user query to intent
2. **Builds routing plan** — Map intents to agents
3. **Executes recipes** — Run the Step 0-3 flow

---

## The Orchestration Flow

```
Signal: /recipe-name "query"
        │
        ▼
┌─────────────────────────────────────────────────────────────┐
│  Step 0: Initialize STM                                      │
│  ├─ Create workspace: .phoenix-os/stm/{id}-{timestamp}/      │
│  ├─ Scan query against radars                                │
│  ├─ Load matched signals into context.md                     │
│  └─ Initialize state.md, intents.md                          │
└─────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────┐
│  Step 1: Build Routing Plan                                  │
│  ├─ Invoke orchestrator agent                                │
│  ├─ Identify intents from query + context                    │
│  ├─ Map intents to agents                                    │
│  └─ Construct sequenced routing plan                         │
└─────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────┐
│  Step 2: Execute Routing Plan                                │
│  ├─ For each step in plan:                                   │
│  │   ├─ Invoke domain agent with goal                        │
│  │   ├─ Agent reads STM (signals + context)                  │
│  │   ├─ Agent invokes skill chain                            │
│  │   └─ Agent updates STM with output                        │
│  └─ Handle roadblocks (clarification, blocked, error)        │
└─────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────┐
│  Step 3: Synthesize Response                                 │
│  ├─ Designated synthesizer consolidates outputs              │
│  └─ Generate final response to user                          │
└─────────────────────────────────────────────────────────────┘
```

---

## The Orchestrator Agent

The `phoenix:orchestrator` agent handles Steps 1 via a 2-skill sequence:

| Step | Skill | Purpose |
|------|-------|---------|
| 1 | `phoenix-engine-identify-intents` | Pattern match, boost confidence, select intents |
| 2 | `phoenix-engine-build-plan` | Map to agents, construct routing plan |

```
User Query + Context
        │
        ▼
┌─────────────────────────────────────────────────────────────┐
│  1. Identify Intents                                         │
│     - Pattern match against intent definitions               │
│     - Boost confidence with context signals                  │
│     - Apply thresholds, resolve dependencies                 │
│     Output: selected_intents (max 4)                         │
└─────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────┐
│  2. Build Plan                                               │
│     - Map intents to available agents                        │
│     - Extract context enrichment                             │
│     - Construct sequenced routing plan                       │
│     Output: routing_plan (JSON per schema)                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Intent Confidence Thresholds

| Score Range | Action |
|-------------|--------|
| >= 0.8 | Select intent |
| 0.5 - 0.8 | Select with confirmation flag |
| < 0.5 | Discard (may trigger clarify intent) |

---

## Routing Plan Output

```json
{
  "steps": [
    {
      "step": 1,
      "agent": "phoenix:strategy-guardian",
      "intent": "clarify",
      "goal": "...",
      "depends_on": []
    }
  ],
  "execution_mode": "sequential",
  "synthesizer": "phoenix:strategy-guardian",
  "metadata": {
    "intent_domain": "cto",
    "replan_count": 0
  }
}
```

---

## Response Gates

Recipes only output to user when a gate is reached:

| Gate | Condition | Action |
|------|-----------|--------|
| **Clarification** | Agent returns `needs_clarification` | Present questions to user |
| **Blocked** | Agent returns `blocked` | Explain blocker, request action |
| **Synthesis** | All steps complete | Present final synthesis |
| **Error** | Unrecoverable failure | Explain failure, suggest recovery |

**Steps 0, 1, 2 execute silently.** No status updates between steps.

---

## Skill Chains

Domain agents invoke skill chains:

```
Strategy-Guardian (clarify intent):
  phoenix-perception-analyze-request
    -> phoenix-manifestation-generate-questions
    -> phoenix-cognition-evaluate-understanding

Orchestrator (routing):
  phoenix-engine-identify-intents
    -> phoenix-engine-build-plan
```

Skills are mandatory. Agents MUST invoke their defined skill chains.

---

## Key Constraints

- **Max 4 intents** per query
- **Max 3 parallel agents** in a single step
- **Max 3 replans** per recipe execution
- Orchestrator **does not perform domain work** — it only routes

---

## Intent Dependencies

| Relationship | Meaning |
|--------------|---------|
| `requires_first` | Must be resolved before this intent |
| `evolves_to` | Common transition after this intent |
| `conflicts_with` | Mutually exclusive intents |

---

## What Orchestration Is NOT

- **Not a domain agent** — Does not answer questions or make decisions
- **Not optional** — All Level 2 recipes use orchestration
- **Not bypassable** — Steps must execute in order

---

## Related

-> [Recipes](recipes.md) — What orchestration executes
-> [Intents](intents.md) — What orchestration detects
-> [Agent Architecture](agent-architecture.md) — What orchestration routes to
-> [Memory](memory.md) — What orchestration reads/writes

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
