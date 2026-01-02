# Recipe Orchestration Pattern

> Phoenix OS Engine - Intent orchestration pattern for Level 2 recipes

## Overview

This document defines the orchestration pattern for **Level 2 (non-autonomous) recipes** that require **intent identification and routing**. Not all recipes use this pattern—only those that need to classify user intent and route to specialized agents.

### When to Use This Pattern

Use this pattern when the recipe:

- Needs to **classify user intent** before acting
- Routes to **different agents** based on intent
- Requires **multi-step orchestration** with dependencies
- Is **Level 2 (non-autonomous)** — guided by user input

**Examples**: `consult-cto`, `consult-architect`

### When NOT to Use This Pattern

Do not use this pattern when the recipe:

- Has a **fixed execution flow** with no intent routing
- Executes a **single task** directly without classification
- Is **Level 1 (direct execution)** — simple task, predictable path
- Is **Level 3 (autonomous)** — self-directing with own orchestration

**Examples**: Simple utility recipes, autonomous agent recipes

---

Recipes that inherit this pattern reference it and define their specializations (context, constraints, intent domain).

## Execution Flow

### Step 1: Build Routing Plan

**Agent**: `phoenix:orchestrator`

| | |
|---|---|
| **Input** | `query`: The user's query |
| | `context`: Recipe-specific context and constraints |
| **Output** | `routing_plan`: Ordered execution plan |

The orchestrator will:
1. Analyze the query to identify intent(s)
2. Read intent patterns from the recipe's intent domain
3. Look up available agents and their goals from @agents/
4. Match intents to agents with appropriate goals
5. Determine execution strategy (sequential, parallel, dependencies)
6. Order the routing plan per @memory/ltm/intents/sequencing-rules.md
7. Return the routing plan to the recipe

Reference: @memory/ltm/engine/flows/routing-plan-schema.md

---

### Step 2: Execute Routing Plan

Recipe executes `routing_plan` from Step 1, following the orchestrator's execution strategy.

**For each step in routing_plan.steps (in order):**

| Mode | Recipe Action |
|------|---------------|
| `sequential` | Execute agents one by one, wait for each to complete |
| `parallel` | Execute all agents in the step concurrently, wait for all to complete |

**For each agent invocation:**

| | |
|---|---|
| **Input** | `query`: The user's query |
| | `intent`: The intent this agent is handling |
| | `goal`: The goal for this invocation |
| | `prior_outputs`: Results from completed steps |
| **Output** | `findings`: Key findings or recommendations |
| | `next_steps`: Actionable next steps |
| | `status`: `complete` \| `blocked` \| `needs_clarification` |

**Agent behavior:**
1. Read its definition from @agents/{agent-name}.md
2. Read memory (STM + LTM) to build context
3. Decide which skills to invoke
4. Execute skills and produce output
5. Update STM with results
6. Return findings, next steps, and status

---

### Step 3: Handle Roadblocks

If any agent returns `status: blocked` or `status: needs_clarification`:

| Status | Recipe Action |
|--------|---------------|
| `needs_clarification` | Present clarifying questions to user, await response |
| `blocked` | Log blocker, attempt resolution or escalate to user |
| `complete` | Continue to next agent in routing plan |

**Re-invocation cycle:**

Once a roadblock is resolved (e.g., user provides clarification):

1. Re-invoke `phoenix:orchestrator` with updated context
2. Orchestrator re-analyzes with new information
3. Orchestrator returns updated `routing_plan`
4. Recipe continues execution from Step 2

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Step 1: Build Plan ──► Step 2: Execute ──► Step 3: Handle │
│       ▲                                          │          │
│       │                                          │          │
│       └────────── Re-invoke on resolution ◄──────┘          │
│                                                             │
│  Cycle continues until routing_plan executes without        │
│  roadblocks or all intents are confidently resolved         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Step 4: Synthesize Response

After all agents in routing plan complete, synthesize final response.

**Agent**: Designated synthesizer from recipe (default: last agent in plan)

| | |
|---|---|
| **Input** | `routing_plan`: The executed plan |
| | `all_outputs`: Findings and next steps from all agents |
| **Output** | Executive summary containing: |
| | 1. Detected intents and confidence |
| | 2. Key findings or recommendations |
| | 3. Specific next steps |

---

## Recipe Specializations

Recipes that inherit this pattern define:

| Specialization | Description | Example |
|----------------|-------------|---------|
| `context` | Domain-specific framing | "CTO advisor across 7 Strategic Radars" |
| `intent_domain` | Which intents this recipe handles | `@memory/ltm/intents/cto-intents.md` |
| `constraints` | Rules and boundaries | Communication style, scope limits |
| `synthesizer` | Agent responsible for final synthesis | `phoenix:strategy-guardian` |

---

## How to Inherit

In your recipe:

```markdown
## Execution Flow

Inherits: @memory/ltm/engine/flows/recipe-orchestration-pattern.md

### Specializations

- **Intent Domain**: @memory/ltm/intents/{domain}-intents.md
- **Synthesizer**: phoenix:{agent-name}
- **Constraints**: [List recipe-specific constraints]
```

---

**Version**: 1.0.0
**Last Updated**: 2026-01-02
**Part of**: Phoenix OS Engine
