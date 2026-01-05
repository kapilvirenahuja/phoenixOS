# Orchestration Engine

> The intent detection and routing mechanism for Level 2 recipes

## Overview

The **orchestration engine** is the system that identifies user intents and routes them to appropriate domain agents. It operates via the `phoenix:orchestrator` agent and its 5-skill sequence.

## How It Works

```
User Query + Context
        │
        ▼
┌─────────────────────────────────────────────────────┐
│  1. Pattern Match                                   │
│     Match query against intent patterns from LTM    │
│     Output: candidate_intents with initial scores   │
└─────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────┐
│  2. Boost Confidence                                │
│     Apply context signals to refine scores          │
│     Output: scored_intents with signal breakdown    │
└─────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────┐
│  3. Select Intents                                  │
│     Apply thresholds, resolve dependencies          │
│     Output: selected_intents (max 4)                │
└─────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────┐
│  4. Match Agents                                    │
│     Map intents to available agents                 │
│     Output: agent_assignments with context          │
└─────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────┐
│  5. Build Plan                                      │
│     Construct sequenced routing plan                │
│     Output: routing_plan (JSON per schema)          │
└─────────────────────────────────────────────────────┘
```

## The 5-Skill Sequence

| Step | Skill | Purpose |
|------|-------|---------|
| 1 | `phoenix-orchestrator-pattern-match` | Match query patterns against intent definitions |
| 2 | `phoenix-orchestrator-boost-confidence` | Apply contextual signals to adjust scores |
| 3 | `phoenix-orchestrator-select-intents` | Filter by threshold, resolve dependencies |
| 4 | `phoenix-orchestrator-match-agents` | Map selected intents to domain agents |
| 5 | `phoenix-orchestrator-build-plan` | Construct the execution plan |

## Intent Confidence Thresholds

| Score Range | Action |
|-------------|--------|
| > 0.8 | Select intent |
| 0.5 - 0.8 | Select with confirmation flag |
| < 0.5 | Discard (may trigger clarify intent) |

## Routing Plan Output

The orchestrator produces a routing plan that specifies:

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

## Key Constraints

- **Max 4 intents** per query
- **Max 3 parallel agents** in a single step
- **Max 3 replans** per recipe execution
- Orchestrator **does not perform domain work** — it only routes

## Intent Dependencies

Intents can have relationships that affect sequencing:

| Relationship | Meaning |
|--------------|---------|
| `requires_first` | Must be resolved before this intent |
| `evolves_to` | Common transition after this intent |
| `conflicts_with` | Mutually exclusive intents |

## What the Orchestrator Is NOT

- **Not a domain agent** — Does not answer questions or make decisions
- **Not optional** — All Level 2 recipes use the orchestrator
- **Not bypassable** — Skills must be invoked in sequence

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
