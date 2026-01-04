---
name: context-identify-intent
description: Classify user query into intent(s) using LTM patterns. Use early in Level 2+ recipes (Step 2) and re-invoke when agents need clarity about which intent they're serving. Reads intent patterns from LTM, matches against query, handles multi-intent sequencing, and enriches context for downstream agents.
---

# Identify Intent

Detect user intent(s) from query and context. Maps raw expression to actionable intent classification that drives routing and enriches agent context.

## When to Use

- **Step 2**: Early in Level 2+ recipes, after STM initialization
- **Re-invocable**: Yes - agents can call when unsure which intent they're serving
- **By**: Recipe (initial) or Agent (subsequent)

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `query` | Yes | User's raw query |
| `stm_path` | Yes | Path to current STM workspace |
| `context` | No | Additional context (conversation history, prior outputs) |
| `hint` | No | Agent's hypothesis about intent (for re-invocation) |

## Instructions

### Step 1: Load Intent Patterns from LTM

Read intent definitions from `memory/ltm/intents/`:
- `{domain}-intents.md` - Domain-specific intents (e.g., `cto-intents.md`)
- `sequencing-rules.md` - Multi-intent ordering principles

### Step 2: Pattern Matching

For each intent in LTM:

1. Match query against defined patterns
2. Check context signals
3. Calculate confidence score (0.0 - 1.0)

```markdown
## Pattern Match Results

| Intent | Pattern Matches | Context Signals | Confidence |
|--------|-----------------|-----------------|------------|
| design | 2/7 | 1/4 | 0.43 |
| validate | 5/8 | 3/4 | 0.81 |
| clarify | 1/4 | 2/3 | 0.50 |
```

### Step 3: Apply Confidence Thresholds

| Confidence | Action |
|------------|--------|
| > 0.8 | Accept intent |
| 0.5 - 0.8 | Accept with confirmation flag |
| < 0.5 | Trigger `clarify` intent |

### Step 4: Handle Multiple Intents

If multiple intents detected above threshold:

1. Load sequencing rules from LTM
2. Determine order based on dependencies
3. Check for conflicts
4. Build execution sequence

Example sequence resolution:
```
Detected: [design, validate]
Rule: "Can't validate what doesn't exist"
Sequence: design → validate
```

### Step 5: Build Enrichment Context

For each detected intent, extract from LTM:
- `user_needs` - What they're trying to accomplish
- `expects` - Expected output format
- `probe_for` - Information to gather if missing
- `routes_to` - Agent and skills to invoke

### Step 6: Update STM

Write to `{stm_path}/intents.md`:

```markdown
## Intent State

**Status**: identified
**Timestamp**: {ISO timestamp}

### Detected Intents
| Intent | Confidence | Flags |
|--------|------------|-------|
| validate | 0.81 | none |

### Sequence
1. validate

### Enrichment
- user_needs: Honest assessment of risks and blind spots
- expects: Validation report with verdict
- probe_for: Timeline, team size, constraints

### Routes To
Agent: phoenix:strategy-guardian
Skills: validate:challenge-assumptions, validate:detect-antipatterns
```

## Outputs

| Output | Description |
|--------|-------------|
| `intents` | Array of detected intent(s) with confidence scores |
| `sequence` | Ordered sequence if multiple intents |
| `enrichment` | Context enrichment for detected intent(s) |
| `needs_clarification` | True if confidence too low to proceed |

## Re-Invocation by Agents

Agents can re-invoke this skill when:
- Conversation has evolved and intent may have shifted
- Unsure which intent they're serving
- User's response suggests different intent

Include `hint` parameter with agent's hypothesis:
```json
{
  "query": "original query",
  "stm_path": "...",
  "context": { "conversation_so_far": [...] },
  "hint": "I think this might be 'decide' not 'validate'"
}
```

## Success Criteria

- [ ] At least one intent identified (or clarify triggered)
- [ ] Confidence scores calculated for all matches
- [ ] Multi-intent sequencing resolved (if applicable)
- [ ] STM intents.md updated with results
- [ ] Enrichment context available for agents

## Edge Cases

| Case | Handling |
|------|----------|
| No intent matches | Return `clarify` as default |
| All low confidence | Return `needs_clarification: true` |
| Conflicting intents | Use priority order from LTM |
| Agent hint provided | Weight hint in confidence scoring |
