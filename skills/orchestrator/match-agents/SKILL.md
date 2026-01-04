---
name: orchestrator-match-agents
description: Map selected intents to available agents. Fourth step in orchestration. Verifies agent availability, extracts context enrichment for agent inputs.
---

# Match Agents

Map selected intents to available agents and prepare inputs.

## When to Use

- **Step 4** of orchestrator skill sequence
- **After**: select-intents
- **By**: Orchestrator agent only

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `selected_intents` | Yes | Intents from select-intents |
| `intent_domain` | Yes | Path to intent patterns (for Routes To) |
| `available_agents` | Yes | Agents registered for this recipe |

## Instructions

### Step 1: Load Routing Info

For each selected intent, load from intent domain:

```
intent.routes_to = {
  agent: "phoenix:strategy-guardian",
  skills: ["consult:analyze-request", "validate:challenge-assumptions"]
}

intent.context_enrichment = {
  user_needs: "Clear recommendation with rationale",
  expects: "Decision framework, trade-off analysis",
  probe_for: ["Decision criteria", "constraints", "timeline"]
}
```

### Step 2: Verify Agent Availability

For each intent:

```
If intent.routes_to.agent NOT IN available_agents:
  Mark intent as blocked
  Set blocked_reason = "Agent not available"
  Suggest fallback if exists
```

### Step 3: Extract Context Enrichment

For each available agent assignment:

```
agent_input = {
  intent: intent.name,
  goal: derive_goal_from_query(query, intent),
  user_needs: intent.context_enrichment.user_needs,
  expects: intent.context_enrichment.expects,
  probe_for: intent.context_enrichment.probe_for
}
```

### Step 4: Build Assignments

Create agent assignments for each intent:

```json
{
  "intent": "decide",
  "agent": "phoenix:strategy-guardian",
  "available": true,
  "inputs": {
    "user_needs": "...",
    "expects": "...",
    "probe_for": [...]
  }
}
```

## Outputs

| Output | Description |
|--------|-------------|
| `agent_assignments` | Mapped intents to agents |

```json
{
  "agent_assignments": [
    {
      "intent": "decide",
      "agent": "phoenix:strategy-guardian",
      "available": true,
      "inputs": {
        "goal": "Help user decide between Postgres and MongoDB",
        "user_needs": "Clear recommendation with rationale",
        "expects": "Decision framework, trade-off analysis",
        "probe_for": ["Decision criteria", "constraints", "timeline"]
      }
    }
  ],
  "blocked": []
}
```

## Success Criteria

- [ ] All intents mapped to agents
- [ ] Unavailable agents flagged as blocked
- [ ] Context enrichment extracted
- [ ] Goal derived from query
