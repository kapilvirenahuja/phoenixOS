# Routing Plan Schema

> Phoenix OS Engine - Schema for orchestrator routing plans

## Overview

This document defines the structure of routing plans returned by `phoenix:orchestrator`. All orchestrators must return plans conforming to this schema.

## Schema

```
{
  id: string,                    // Unique plan identifier
  created_at: timestamp,         // When plan was created
  query: string,                 // Original user query
  confidence: number,            // 0.0 - 1.0, overall confidence in plan

  steps: [
    {
      order: number,             // Execution order (1, 2, 3...)
      mode: "sequential" | "parallel",
      depends_on: [number],      // Orders of steps this depends on (optional)

      agents: [
        {
          agent: string,         // Agent registration (e.g., "phoenix:architect")
          intent: string,        // Intent being handled
          goal: string,          // Goal for this invocation
          inputs: object,        // Additional inputs for the agent (optional)
        }
      ]
    }
  ],

  synthesizer: {
    agent: string,               // Agent responsible for final synthesis
    inputs: object               // Additional inputs for synthesis (optional)
  },

  metadata: {
    intent_domain: string,       // Intent domain reference
    sequencing_rules: string,    // Sequencing rules reference
    replan_count: number         // How many times plan was rebuilt (0 initially)
  }
}
```

## Example

```json
{
  "id": "plan-2026-01-02-abc123",
  "created_at": "2026-01-02T10:30:00Z",
  "query": "Help me design an API layer and validate my approach",
  "confidence": 0.85,

  "steps": [
    {
      "order": 1,
      "mode": "sequential",
      "depends_on": [],
      "agents": [
        {
          "agent": "phoenix:strategy-guardian",
          "intent": "clarify",
          "goal": "Ensure requirements are clear before design"
        }
      ]
    },
    {
      "order": 2,
      "mode": "parallel",
      "depends_on": [1],
      "agents": [
        {
          "agent": "phoenix:architect",
          "intent": "tech-design",
          "goal": "Design API layer architecture"
        },
        {
          "agent": "phoenix:ux-architect",
          "intent": "ux-design",
          "goal": "Design API developer experience"
        }
      ]
    },
    {
      "order": 3,
      "mode": "sequential",
      "depends_on": [2],
      "agents": [
        {
          "agent": "phoenix:strategy-guardian",
          "intent": "validate",
          "goal": "Validate the proposed design"
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

## Execution Modes

| Mode | Behavior |
|------|----------|
| `sequential` | Execute agents in the step one by one, in array order |
| `parallel` | Execute all agents in the step concurrently |

## Dependencies

The `depends_on` field specifies which steps must complete before this step can begin.

- Empty array `[]` means no dependencies (can start immediately)
- `[1]` means step 1 must complete first
- `[1, 2]` means both step 1 and step 2 must complete first

## Replanning

When a roadblock occurs and is resolved:

1. Orchestrator is re-invoked
2. New plan is generated with `metadata.replan_count` incremented
3. New plan may have different steps based on new context
4. Recipe continues with new plan from Step 2

---

**Version**: 1.0.0
**Last Updated**: 2026-01-02
**Part of**: Phoenix OS Engine
