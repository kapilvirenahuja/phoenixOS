---
name: phoenix-orchestrator-pattern-match
description: Match user query against intent patterns from LTM. First step in intent identification. Scans query for pattern matches and returns candidate intents with initial confidence scores.
---

# Pattern Match

Match user query against intent patterns to identify candidate intents.

## When to Use

- **Step 1** of orchestrator skill sequence
- **By**: Orchestrator agent only

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `query` | Yes | User's raw query |
| `intent_domain` | Yes | Path to intent patterns (e.g., `@memory/engine/intents/cto-intents.md`) |
| `enabled_intents` | Yes | List of intents enabled by recipe |

## Instructions

### Step 1: Load Intent Patterns

Read the intent domain file and extract patterns for each enabled intent:

```
For each intent in enabled_intents:
  Load intent.patterns from intent_domain
```

### Step 2: Scan Query

For each pattern in each intent:

1. Check if pattern matches query (exact, partial, semantic)
2. Calculate match strength (0.0 - 1.0)

**Match Types:**
| Type | Example | Weight |
|------|---------|--------|
| Exact phrase | "Should I use X or Y" | 0.9 |
| Partial match | Contains "should I" | 0.6 |
| Semantic similarity | Similar meaning | 0.4 |

### Step 3: Calculate Initial Score

```
For each intent:
  matched_patterns = count of patterns that matched
  total_patterns = total patterns for intent

  initial_score = (matched_patterns / total_patterns) * max_match_weight
```

### Step 4: Filter Candidates

Only include intents with score > 0.3 as candidates.

## Outputs

| Output | Description |
|--------|-------------|
| `candidates` | Array of candidate intents with scores |

```json
{
  "candidates": [
    { "intent": "decide", "score": 0.7, "matched_patterns": ["Should I use X or Y"] },
    { "intent": "consult", "score": 0.35, "matched_patterns": ["Help me with"] }
  ]
}
```

## Success Criteria

- [ ] All enabled intents scanned
- [ ] Candidates have scores > 0.3
- [ ] Matched patterns recorded for transparency
