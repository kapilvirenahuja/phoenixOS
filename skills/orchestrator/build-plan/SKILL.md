---
name: orchestrator-build-plan
description: Construct routing plan from agent assignments. Final step in orchestration. Applies sequencing rules, determines execution mode, outputs plan per schema.
---

# Build Plan

Construct the routing plan for recipe execution.

## When to Use

- **Step 5** of orchestrator skill sequence
- **After**: match-agents
- **By**: Orchestrator agent only

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `agent_assignments` | Yes | Mapped agents from match-agents |
| `query` | Yes | Original user query |
| `sequencing_rules` | Yes | Path to sequencing rules (`@memory/ltm/intents/sequencing-rules.md`) |
| `synthesizer` | Yes | Synthesizer agent from recipe |
| `replan_count` | No | Current replan count (default 0) |

## Instructions

### Step 1: Build Dependency Graph

From agent assignments and Related Intents:

```
For each assignment:
  Load requires_first, conflicts_with from intent
  Add edges to dependency graph
```

### Step 2: Topological Sort

Order assignments by dependencies:

```
clarify → decide → validate → design
```

### Step 3: Determine Execution Mode

For each step in sorted order:

| Condition | Mode |
|-----------|------|
| Has `requires_first` dependency | `sequential` |
| Has `conflicts_with` in same step | `sequential` |
| Independent, no conflicts | `parallel` |

**Constraints from sequencing rules:**
- Max 3 parallel agents
- Max 4 intents total
- Max 3 replans

### Step 4: Set Dependencies

For each step, set `depends_on`:

```
Step 1: depends_on = []
Step 2: depends_on = [1] (if requires step 1)
Step 3: depends_on = [1, 2] (if requires both)
```

### Step 5: Construct Plan

Build routing plan per schema:

```json
{
  "id": "plan-{timestamp}-{hash}",
  "created_at": "{ISO timestamp}",
  "query": "{original query}",
  "confidence": {average of intent confidences},
  "steps": [...],
  "synthesizer": {...},
  "metadata": {...}
}
```

### Step 6: Validate Plan

- All blocked intents excluded
- Dependencies satisfied
- Within limits

## Outputs

| Output | Description |
|--------|-------------|
| `routing_plan` | Complete execution plan |

```json
{
  "id": "plan-2026-01-04-abc123",
  "created_at": "2026-01-04T20:00:00Z",
  "query": "Should I use Postgres or MongoDB?",
  "confidence": 0.85,
  "steps": [
    {
      "order": 1,
      "mode": "sequential",
      "depends_on": [],
      "agents": [
        {
          "agent": "phoenix:strategy-guardian",
          "intent": "decide",
          "goal": "Help user decide between Postgres and MongoDB",
          "inputs": {
            "user_needs": "Clear recommendation with rationale",
            "expects": "Decision framework, trade-off analysis",
            "probe_for": ["Decision criteria", "constraints", "timeline"]
          }
        }
      ]
    }
  ],
  "synthesizer": {
    "agent": "phoenix:strategy-guardian",
    "inputs": {}
  },
  "metadata": {
    "intent_domain": "@memory/ltm/intents/cto-intents.md",
    "sequencing_rules": "@memory/ltm/intents/sequencing-rules.md",
    "replan_count": 0
  }
}
```

Reference: `@memory/ltm/engine/flows/routing-plan-schema.md`

## Success Criteria

- [ ] Plan conforms to routing-plan-schema
- [ ] Dependencies correctly ordered
- [ ] Execution modes appropriate
- [ ] Within constraints (max 4 intents, max 3 parallel)
