---
name: phoenix-orchestrator-boost-confidence
description: Apply context signals to boost confidence scores for candidate intents. Second step in intent identification. Evaluates situational indicators to refine initial pattern match scores.
---

# Boost Confidence

Apply context signals to refine confidence scores for candidate intents.

## When to Use

- **Step 2** of orchestrator skill sequence
- **After**: pattern-match
- **By**: Orchestrator agent only

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `candidates` | Yes | Candidate intents from pattern-match |
| `query` | Yes | User's raw query |
| `intent_domain` | Yes | Path to intent patterns |
| `context` | No | Additional context (conversation history, user info) |

## Instructions

### Step 1: Load Context Signals

For each candidate intent, load its context signals from intent domain:

```
intent.context_signals = [
  "User presents alternatives",
  "Comparative language",
  "Decision framing explicit",
  ...
]
```

### Step 2: Evaluate Signals

For each signal, check if it applies to the query/context:

| Signal | How to Detect |
|--------|---------------|
| "User presents alternatives" | Query contains "or", "vs", "between" |
| "Comparative language" | Query contains comparison words |
| "Decision framing explicit" | Query starts with "Should I", "Which" |
| "Time pressure" | Query mentions deadline, urgency |
| "User has existing plan" | Query references "my plan", "I decided" |

### Step 3: Apply Boost

For each matching signal:
- Boost confidence by 0.05 - 0.1
- Cap total confidence at 1.0

```
For each candidate:
  For each signal that matches:
    candidate.score += 0.05
  candidate.score = min(candidate.score, 1.0)
```

### Step 4: Re-rank Candidates

Sort candidates by updated score descending.

## Outputs

| Output | Description |
|--------|-------------|
| `scored_intents` | Candidates with boosted scores |

```json
{
  "scored_intents": [
    {
      "intent": "decide",
      "score": 0.85,
      "signals_matched": ["User presents alternatives", "Comparative language", "Decision framing explicit"]
    }
  ]
}
```

## Success Criteria

- [ ] All candidate signals evaluated
- [ ] Scores boosted appropriately
- [ ] Candidates re-ranked by score
