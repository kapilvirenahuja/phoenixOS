---
name: orchestrator-select-intents
description: Apply confidence thresholds to select final intents. Third step in intent identification. Filters candidates, resolves dependencies, handles multi-intent queries.
---

# Select Intents

Apply thresholds and resolve dependencies to select final intents.

## When to Use

- **Step 3** of orchestrator skill sequence
- **After**: boost-confidence
- **By**: Orchestrator agent only

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `scored_intents` | Yes | Candidates with boosted scores |
| `intent_domain` | Yes | Path to intent patterns (for Related Intents) |

## Instructions

### Step 1: Apply Confidence Thresholds

| Confidence | Action |
|------------|--------|
| > 0.8 | Select intent |
| 0.5 - 0.8 | Select with `needs_confirmation: true` |
| < 0.5 | Discard |

If all intents discarded, select `clarify` as default.

### Step 2: Check Dependencies

For each selected intent, load Related Intents:

```
intent.related_intents = {
  requires_first: ["clarify"],
  evolves_to: ["design", "validate"],
  conflicts_with: []
}
```

**Dependency Rules:**
- If `requires_first` is not in selected intents, add it
- If `conflicts_with` is in selected intents, resolve by priority

### Step 3: Handle Multi-Intent

If multiple intents selected:

1. Check for composite query ("decide X and then design Y")
2. Apply Intent Detection Priority from intent domain
3. Sequence using `evolves_to` relationships

**Priority Order** (from cto-intents):
1. clarify
2. decide
3. validate
4. consult
5. advise
6. design

### Step 4: Validate Selection

- Max 4 intents per query
- If more, ask user to narrow scope

## Outputs

| Output | Description |
|--------|-------------|
| `selected_intents` | Final intent selection |

```json
{
  "selected_intents": [
    {
      "intent": "clarify",
      "confidence": 0.85,
      "needs_confirmation": false,
      "added_by": "dependency"
    },
    {
      "intent": "decide",
      "confidence": 0.85,
      "needs_confirmation": false,
      "added_by": "detection"
    }
  ]
}
```

## Success Criteria

- [ ] Thresholds applied correctly
- [ ] Dependencies resolved
- [ ] Max 4 intents enforced
- [ ] Selection explained (detection vs dependency)
