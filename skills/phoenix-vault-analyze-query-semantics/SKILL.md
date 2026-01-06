---
name: phoenix-vault-analyze-query-semantics
description: Semantically analyze query against all radars to determine relevance. Replaces keyword matching with LLM-based semantic understanding.
---

# Analyze Query Semantics

Use LLM to semantically analyze a query against all radar definitions to determine relevance. This replaces literal keyword matching with contextual understanding.

## When to Use

- **Called by**: `phoenix-context-initialize-stm` skill (Step 2a)
- **Before**: Loading signals from radars
- **Purpose**: Identify which radars are semantically relevant to the query

## Position in Flow

```
Query arrives
      │
      ▼
┌─────────────────────────────────┐
│  initialize-stm                 │
│  Step 0: Create workspace       │
└─────────────────────────────────┘
      │
      ▼
┌─────────────────────────────────┐
│  analyze-query-semantics        │  ◄── YOU ARE HERE
│  Step 2a: Semantic analysis     │
│  (Replaces keyword matching)    │
└─────────────────────────────────┘
      │
      ▼ radar_matches
┌─────────────────────────────────┐
│  initialize-stm                 │
│  Step 2b: Load signals          │
│  Step 2c: Graph traversal       │
└─────────────────────────────────┘
```

---

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `query` | Yes | The user's raw query (verbatim) |
| `radars_path` | Yes | Path to radars directory (default: `{user-vault}/radars/`) |

---

## Instructions

### Step 1: Load All Radar Definitions

Read all radar files from `{radars_path}/*.md`:

For each radar, extract:
- **Name**: Radar name (from filename)
- **Focus**: What this radar monitors
- **Questions**: Strategic questions this radar asks

```markdown
Example radar structure:

# AI / Intelligence Radar

> Artificial intelligence, machine learning, agentic systems

## Focus
AI model advancements, agentic system design (PCAM), AI-native SDLC transformation...

## Questions
- How can AI augment our capabilities without replacing human judgment?
- What PCAM dimension needs strengthening?
- What risks or biases should we consider?
```

**Store for each radar:**
```json
{
  "name": "AI / Intelligence",
  "file": "ai-intelligence.md",
  "focus": "AI model advancements, agentic system design...",
  "questions": [
    "How can AI augment our capabilities?",
    "What PCAM dimension needs strengthening?",
    "What risks or biases should we consider?"
  ]
}
```

---

### Step 2: Semantic Relevance Analysis

For each radar, use LLM to analyze semantic relevance to the query.

**Prompt template:**

```
Query: "{query}"

Radar: {radar_name}
Focus: {radar_focus}
Questions this radar asks:
{radar_questions}

Task: Evaluate how semantically relevant this radar is to the query.

Consider:
1. Does the query's intent align with this radar's focus?
2. Would this radar's strategic questions help address the query?
3. Does the query domain overlap with this radar's domain?

Score relevance from 0.0 to 1.0:
- 1.0 = Directly addresses this radar's focus
- 0.7-0.9 = Strong relevance, important context
- 0.4-0.6 = Moderate relevance, peripheral context
- 0.1-0.3 = Weak relevance, tangential
- 0.0 = No relevance

Output format:
{
  "relevance": <float 0.0-1.0>,
  "reasoning": "<1-2 sentence explanation>",
  "matched_aspects": ["<aspect 1>", "<aspect 2>"]
}
```

---

### Step 3: Classify Match Types

Based on relevance scores, classify each radar:

| Relevance Score | Match Type | Confidence | Treatment |
|----------------|------------|------------|-----------|
| >= 0.6 | `direct` | Score as-is | Load all signals from this radar |
| 0.3 - 0.59 | `peripheral` | 0.4 (fixed) | Include in graph traversal |
| < 0.3 | `none` | 0.0 | Exclude unless reached via graph traversal |

**Rationale:**
- **Direct matches** (>= 0.6): High relevance, load signals immediately
- **Peripheral matches** (0.3-0.59): Moderate relevance, include but don't load yet
- **No match** (< 0.3): Low relevance, only load if connected via signals

---

### Step 4: Set No Match Flag

If no radars score >= 0.3:
```json
{
  "no_radar_match": true
}
```

This triggers the `clarify` intent automatically.

---

## Outputs

| Output | Type | Description |
|--------|------|-------------|
| `direct_matches` | array | Radars with relevance >= 0.6 |
| `peripheral_matches` | array | Radars with relevance 0.3-0.59 |
| `no_matches` | array | Radars with relevance < 0.3 |
| `no_radar_match` | boolean | True if no radars scored >= 0.3 |

**Output Schema:**

```json
{
  "direct_matches": [
    {
      "radar": "AI / Intelligence",
      "file": "ai-intelligence.md",
      "relevance": 0.9,
      "reasoning": "Query explicitly mentions 'AI-powered', directly aligns with AI/ML focus",
      "matched_aspects": ["AI implementation", "capability augmentation"]
    },
    {
      "radar": "Innovation",
      "file": "innovation.md",
      "relevance": 0.7,
      "reasoning": "Query says 'build something', aligns with prototyping and new development",
      "matched_aspects": ["building new things", "rapid prototyping"]
    }
  ],
  "peripheral_matches": [
    {
      "radar": "Purpose / Why",
      "file": "purpose.md",
      "relevance": 0.5,
      "reasoning": "'Help me' suggests seeking strategic direction",
      "matched_aspects": ["strategic guidance"]
    },
    {
      "radar": "Technology",
      "file": "technology.md",
      "relevance": 0.4,
      "reasoning": "'Build' implies technology selection and implementation decisions",
      "matched_aspects": ["build vs buy decisions"]
    }
  ],
  "no_matches": [
    {
      "radar": "Digital Experience",
      "file": "digital-experience.md",
      "relevance": 0.1,
      "reasoning": "No UX or customer experience context in query"
    }
  ],
  "no_radar_match": false
}
```

---

## Success Criteria

- [ ] All radar files loaded and parsed
- [ ] Semantic relevance calculated for each radar (0.0-1.0)
- [ ] Match types classified (direct, peripheral, none)
- [ ] No match flag set correctly
- [ ] Reasoning provided for each radar

---

## Error Handling

| Error | Action |
|-------|--------|
| Radar file unreadable | Warn, skip that radar, continue |
| Radar missing Focus/Questions | Use filename only, assign low relevance |
| LLM semantic analysis fails | Fall back to keyword matching for that radar |
| All semantic analyses fail | Fall back to full keyword matching, log error |

---

## Example Execution

**Input:**
```
query: "Help me build something AI-powered"
radars_path: "{user-vault}/radars/"
```

**LLM Analysis (per radar):**

```
Radar: AI / Intelligence
Focus: AI model advancements, agentic systems, PCAM framework
→ Relevance: 0.9 (direct match)
→ Reasoning: Query explicitly mentions "AI-powered"

Radar: Innovation
Focus: New ideas, prototyping, disruptive approaches
→ Relevance: 0.7 (direct match)
→ Reasoning: "Build something" aligns with prototyping

Radar: Purpose / Why
Focus: Mission, vision, strategic intent
→ Relevance: 0.5 (peripheral)
→ Reasoning: "Help me" suggests seeking strategic direction

Radar: Technology
Focus: Tools, platforms, build vs buy
→ Relevance: 0.4 (peripheral)
→ Reasoning: "Build" implies technology decisions

Radar: Leadership
Focus: Team, culture, organizational effectiveness
→ Relevance: 0.2 (no match)
→ Reasoning: No team or organizational context

Radar: Evolutionary Architecture
Focus: System design, technical architecture
→ Relevance: 0.2 (no match)
→ Reasoning: No architectural context yet

Radar: Digital Experience
Focus: Customer experience, UX
→ Relevance: 0.1 (no match)
→ Reasoning: No UX or customer context
```

**Output:**
```json
{
  "direct_matches": [
    {
      "radar": "AI / Intelligence",
      "file": "ai-intelligence.md",
      "relevance": 0.9,
      "reasoning": "Query explicitly mentions 'AI-powered', directly aligns with AI/ML focus",
      "matched_aspects": ["AI implementation", "capability augmentation"]
    },
    {
      "radar": "Innovation",
      "file": "innovation.md",
      "relevance": 0.7,
      "reasoning": "Query says 'build something', aligns with prototyping and new development",
      "matched_aspects": ["building new things", "rapid prototyping"]
    }
  ],
  "peripheral_matches": [
    {
      "radar": "Purpose / Why",
      "file": "purpose.md",
      "relevance": 0.5,
      "reasoning": "'Help me' suggests seeking strategic direction",
      "matched_aspects": ["strategic guidance"]
    },
    {
      "radar": "Technology",
      "file": "technology.md",
      "relevance": 0.4,
      "reasoning": "'Build' implies technology selection and implementation decisions",
      "matched_aspects": ["build vs buy decisions"]
    }
  ],
  "no_matches": [
    {
      "radar": "Leadership",
      "file": "leadership.md",
      "relevance": 0.2,
      "reasoning": "No team or organizational context"
    },
    {
      "radar": "Evolutionary Architecture",
      "file": "evolutionary-architecture.md",
      "relevance": 0.2,
      "reasoning": "No architectural context yet"
    },
    {
      "radar": "Digital Experience",
      "file": "digital-experience.md",
      "relevance": 0.1,
      "reasoning": "No UX or customer context"
    }
  ],
  "no_radar_match": false
}
```

---

## Integration Notes

### Replacing Keyword Matching

**Old approach** (keyword matching):
```python
if "AI-powered" in query_tokens:
    match = "AI/Intelligence radar"
```

**New approach** (semantic matching):
```python
for radar in radars:
    relevance = llm_analyze_semantic_relevance(query, radar)
    if relevance >= 0.6:
        direct_matches.append(radar)
```

### Benefits

1. **Captures intent, not just keywords**: "Help me build something AI-powered" now matches Innovation and Purpose radars
2. **More flexible**: Works with paraphrasing, synonyms, implied meaning
3. **Explainable**: Provides reasoning for each match
4. **Graceful degradation**: Falls back to keyword matching if LLM fails

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
