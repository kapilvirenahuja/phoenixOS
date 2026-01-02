# Intent Sequencing Rules

> Dynamic sequencing principles for the orchestrator

## Overview

Sequencing is determined **dynamically** by `phoenix:orchestrator` based on intent definitions, not hardcoded matrices. This file provides principles, patterns, and constraints—not specific intent combinations.

## Principles

The orchestrator applies these principles when building routing plans:

1. **Clarify First**: Ambiguity must be resolved before any other intent
2. **Dependencies Before Dependents**: Honor `requires_first` from intent definitions
3. **Decisions Gate Work**: Decisions should precede design or implementation
4. **Create Before Validate**: Can't validate what doesn't exist

## Dynamic Sequencing

The orchestrator derives sequence from each intent's **Related Intents** section:

```
### Related Intents
- **requires_first**: Intents that MUST complete before this one
- **evolves_to**: Intents this one commonly leads to
- **conflicts_with**: Intents that cannot run in parallel
```

### Algorithm

```
For each detected intent:
  1. Check `requires_first` → add dependencies to plan first
  2. Check `conflicts_with` → mark as sequential, not parallel
  3. Check `evolves_to` → inform potential next steps (not automatic)

Build dependency graph → topological sort → routing plan
```

### Example

```
Detected intents: [design, validate]

Intent: design
  requires_first: [clarify] (if requirements vague)
  conflicts_with: []

Intent: validate
  requires_first: [clarify] (if plan not well-defined)
  evolves_to: [design] (if validation reveals need for redesign)

Orchestrator determines:
  - If query is clear → design → validate
  - If query is vague → clarify → design → validate
```

---

## Execution Patterns

### Simple Sequence (Single Pass)

```
Intent A → Output A → Intent B → Output B → Synthesize
```

**When**: Intents have clear dependency, no iteration needed.

### Parallel Execution

```
        ┌→ Intent A → Output A ─┐
Query ──┤                       ├→ Synthesize
        └→ Intent B → Output B ─┘
```

**When**: Intents are independent, no `conflicts_with`, no shared `requires_first`.

### Iterative Cycle (Loop Until Satisfied)

```
Intent A → Output A → Intent B → Output B
    ↑                              │
    └──── (if needs revision) ─────┘
```

**When**: Validation or review may require revision.

### Branching (Conditional Paths)

```
Intent A → Output A
    │
    ├── (condition X) → Intent B
    └── (condition Y) → Intent C
```

**When**: Clarification reveals different user needs.

---

## Output Passing

Outputs flow forward through the sequence:

```
Step 1: intent_a
  Input: query + context
  Output: { result_a: "..." }

Step 2: intent_b
  Input: query + context + prior_outputs.intent_a
  Output: { result_b: "..." }

Synthesis:
  Input: all outputs
  Output: unified response
```

The routing plan captures this via `prior_outputs` in agent inputs.

---

## Constraints

### Cycle Limits

| Limit | Value | Action if exceeded |
|-------|-------|-------------------|
| Max intents per query | 4 | Simplify or break into multiple interactions |
| Max iterations (replan) | 3 | Surface to user for guidance |
| Max parallel agents | 3 | Sequence overflow agents |

### Conflict Resolution

| Conflict Type | Resolution |
|---------------|------------|
| Mutual exclusion (`conflicts_with`) | Sequence by priority |
| Resource conflict | Sequence, don't parallelize |
| Output conflict | Later intent overrides |

Priority is determined by **Intent Detection Priority** in the intent domain file.

---

## STM Updates During Sequencing

After each intent completes, update STM:

```markdown
## STM: Intent Execution State

current_intent: validate
completed_intents: [clarify, design]
pending_intents: []
replan_count: 0
outputs: {
  clarify: { understood_need: "..." },
  design: { architecture: "...", tech_stack: "..." }
}
```

This state informs:
- Replanning decisions
- Output passing to subsequent intents
- Final synthesis

---

**Version**: 2.0.0
**Last Updated**: 2026-01-03
**Part of**: Phoenix OS Engine
