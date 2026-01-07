# Recipe Orchestration Pattern

> Phoenix OS Engine - Intent orchestration pattern for Level 2 recipes

---

## ⛔ CRITICAL: Procedural Execution Requirements

**This pattern requires strict sequential execution. Do not optimize for speed.**

### Non-Negotiable Rules

1. **Steps MUST execute in order** — Step 0 before Step 1, Step 1 before Step 2, etc.
2. **Step 0 is MANDATORY** — Initialize STM before any other work. No exceptions.
3. **Auto-verify completion** — Each step must complete successfully before the next begins (no human verification required).
4. **No skipping "setup" steps** — Infrastructure steps (STM, context loading) are not optional overhead.
5. **Pattern compliance over response speed** — A slow, correct execution beats a fast, broken one.

### Why This Matters

- **STM is the execution workspace** — Without it, there's no persistent state for the re-invocation cycle
- **Agents read from STM** — Skipping initialization means agents operate without Vault context
- **Debugging requires state** — When things fail, STM provides the audit trail
- **Pattern integrity** — Partial execution breaks the orchestration contract

### Completion Criteria (Auto-Verified)

Step 0 completes automatically when:
- STM workspace created at `.phoenix-os/stm/{recipe-id}-{timestamp}/`
- `state.md`, `context.md`, `intents.md` files exist
- Radars scanned and matched signals loaded into `context.md`
- Initial state captured

**No human verification required.** Proceed directly to Step 1 after initialization.

---

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

### Step 0: Initialize STM

**Skill**: `phoenix-engine-stm-initialize`

| | |
|---|---|
| **Input** | `recipe_id`: Identifier for this recipe |
| | `signal`: The incoming signal (query, source, metadata) |
| | `user_context`: Known user information (optional) |
| **Output** | `stm_path`: Path to initialized STM workspace |
| | `radar_matches`: Direct and peripheral radar matches |
| | `signals_loaded`: List of signal files loaded into context |
| | `no_radar_match`: Boolean - true if clarify needed |

This step runs **once** at recipe start:

1. Create STM workspace at `.phoenix-os/stm/{recipe-id}-{timestamp}/`
2. **Scan query against all radars** in `@{user-vault}/radars/`
3. **Load mapped signals** from matched radars into `context.md`
4. Capture initial state (query, environment, radar matches)
5. Initialize intent state as `pending_identification`
6. If no radar matches, flag for clarify intent

Reference: @skills/phoenix-engine-stm-initialize/SKILL.md
Architecture: @memory/engine/vault-architecture.md

**STM Workspace Structure:**
```
.phoenix-os/stm/{recipe-id}-{timestamp}/
├── state.md           # Current execution state
├── context.md         # Radar-matched signals from Vault
├── intents.md         # Active intent(s)
├── outputs/           # Outputs from each step
└── evidence/          # Supporting artifacts
```

---

### Step 1: Build Routing Plan

**Agent**: `phoenix:orchestrator`

| | |
|---|---|
| **Input** | `query`: The user's query |
| | `stm_path`: Path to STM workspace (from Step 0) |
| | `context`: Recipe-specific context and constraints |
| **Output** | `routing_plan`: Ordered execution plan |

The orchestrator will:
1. **Read STM context.md** — Radar matches and loaded signals
2. Analyze the query to identify intent(s)
3. Read intent patterns from the recipe's intent domain
4. Look up available agents and their goals from @agents/
5. Match intents to agents with appropriate goals
6. Determine execution strategy (sequential, parallel, dependencies)
7. Order the routing plan per @memory/engine/intents/sequencing-rules.md
8. Return the routing plan to the recipe

**Note**: If `no_radar_match` is true from Step 0, automatically route to clarify intent.

Reference: @memory/engine/schemas/routing-plan-schema.md

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
| | `stm_context`: Relevant signals from STM context.md |
| | `prior_outputs`: Results from completed steps |
| **Output** | `findings`: Key findings or recommendations |
| | `next_steps`: Actionable next steps |
| | `status`: `complete` \| `blocked` \| `needs_clarification` |

**Agent behavior:**
1. Read its definition from @agents/{agent-name}.md
2. **READ STM context.md** for radar-matched signals (do NOT search Vault directly)
3. Use signal content to ground questions and recommendations
4. Decide which skills to invoke based on signal content
5. Execute skills and produce output **derived from signal content**
6. Update STM with results
7. Return findings, next steps, and status

**⛔ Signal-Grounding Requirement:**
- Agents MUST read from STM context.md, not search Vault directly
- All questions, recommendations, and analysis must trace to loaded signals
- If no relevant signal exists in STM, agent must state "No signal found for X"
- Cite signal source: `@{user-vault}/signals/{path}` from STM context

---

### Step 3: Handle Roadblocks

If any agent returns `status: blocked` or `status: needs_clarification`:

| Status | Recipe Action |
|--------|---------------|
| `needs_clarification` | Present clarifying questions directly in text (see format below), await response |
| `blocked` | Log blocker, attempt resolution or escalate to user |
| `complete` | Continue to next agent in routing plan |

**Clarification Question Format** (present in text, do NOT use AskUserQuestion tool):

For each question, include:
- The question itself
- **Signal source** — which signal file the question derives from
- **Radar** — which radar matched
- **Why it matters** — what decision or direction this unblocks
- **Examples** — concrete options or scenarios to help the user respond

**Re-invocation cycle:**

Once a roadblock is resolved (e.g., user provides clarification):

1. **Update STM** with user response (see Step 3a)
2. Re-invoke `phoenix:orchestrator` with updated context
3. Orchestrator re-analyzes with new information
4. Orchestrator returns updated `routing_plan`
5. Recipe continues execution from Step 2

---

### Step 3a: Update STM on User Response

**Skill**: `phoenix-engine-stm-update`

**Trigger**: After **every** user response during recipe execution.

| | |
|---|---|
| **Input** | `stm_path`: Path to current STM workspace |
| | `user_response`: The user's latest input |
| | `current_state`: Current execution state |
| **Output** | `updated`: Boolean - whether STM was modified |
| | `changes`: List of what changed |

**Evaluation Criteria** (check each):

| Check | Action if True |
|-------|----------------|
| New information provided? | Append to `context.md` |
| Clarification received? | Update `intents.md` confidence, mark clarified |
| Decision made? | Log to `state.md`, update intent status |
| Scope changed? | Flag for re-routing, update `state.md` |
| Contradiction detected? | Flag conflict in `context.md`, surface to agent |

**Update Protocol:**

```markdown
## context.md - Append Block

### User Response [{timestamp}]

**In response to**: {what was asked}
**Content**: {user's response}
**Impact**: {new_info | clarification | decision | scope_change | none}
**Extracted facts**:
- {fact 1}
- {fact 2}
```

**Always update `state.md`:**

```markdown
## Interaction Log

| Timestamp | Type | Summary |
|-----------|------|---------|
| {ts} | user_response | {brief summary} |
```

Reference: @skills/phoenix-engine-stm-update/SKILL.md

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Step 0: Init STM ──► Step 1: Build Plan ──► Step 2: Execute│
│   (radar scan)            ▲                       │         │
│                           │                       ▼         │
│                           │              Step 3: Handle     │
│                           │                       │         │
│                           │              Step 3a: Update STM│
│                           │                       │         │
│                           └─── Re-invoke on resolution ◄────┘
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
| | 2. Radar matches and signals used |
| | 3. Key findings or recommendations |
| | 4. Specific next steps |

---

## Recipe Specializations

Recipes that inherit this pattern define:

| Specialization | Description | Example |
|----------------|-------------|---------|
| `context` | Domain-specific framing | "CTO advisor across 7 Strategic Radars" |
| `intent_domain` | Which intents this recipe handles | `@memory/engine/intents/cto-intents.md` |
| `constraints` | Rules and boundaries | Communication style, scope limits |
| `synthesizer` | Agent responsible for final synthesis | `phoenix:strategy-guardian` |

---

## How to Inherit

In your recipe:

```markdown
## Execution Flow

Inherits: @memory/engine/flows/recipe-orchestration-pattern.md

### Specializations

- **Intent Domain**: @memory/engine/intents/{domain}-intents.md
- **Synthesizer**: phoenix:{agent-name}
- **Constraints**: [List recipe-specific constraints]
```

---

**Version**: 2.0.0
**Last Updated**: 2026-01-05
**Part of**: Phoenix OS Engine
