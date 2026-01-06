# Consult Skills Overview

> The perception-generation-synthesis chain for clarifying vague requirements

---

## The Three Skills

| Skill | Phase | Purpose |
|-------|-------|---------|
| `consult:analyze-request` | **Perception** | Understand what we're dealing with |
| `consult:clarify-requirements` | **Generation** | Produce signal-grounded questions |
| `consult:synthesize-response` | **Synthesis** | Wrap up and route forward |

---

## Execution Flow

```
                    ┌─────────────────────────────────────────┐
                    │           USER QUERY                    │
                    │   "Help me build something AI-powered"  │
                    └─────────────────────────────────────────┘
                                       │
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                    CONSULT:ANALYZE-REQUEST                             │  │
│  │                         (Perception)                                    │  │
│  │                                                                        │  │
│  │  Inputs:                                                               │  │
│  │  • query: "Help me build something AI-powered"                         │  │
│  │  • stm_path: .phoenix-os/stm/consult-cto-xxx/                          │  │
│  │  • intent: clarify                                                     │  │
│  │                                                                        │  │
│  │  Process:                                                              │  │
│  │  1. Parse query → verbs: [help, build], nouns: [something]             │  │
│  │  2. Detect vagueness → score: 0.7 (high)                               │  │
│  │  3. Detect buzzwords → ["AI-powered"]                                  │  │
│  │  4. Missing dimensions → [WHAT, WHO, WHY, HOW]                         │  │
│  │  5. Map to STM signals → augmentation-principle.md, pcam.md            │  │
│  │  6. Recommend action → clarify                                         │  │
│  │                                                                        │  │
│  │  Output: analysis object                                               │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                      │
│                                       ▼                                      │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                  CONSULT:CLARIFY-REQUIREMENTS                          │  │
│  │                         (Generation)                                    │  │
│  │                                                                        │  │
│  │  Inputs:                                                               │  │
│  │  • analysis: (from analyze-request)                                    │  │
│  │  • stm_path: .phoenix-os/stm/consult-cto-xxx/                          │  │
│  │  • engine_intent_path: @memory/engine/intents/cto-intents.md           │  │
│  │  • max_questions: 4                                                    │  │
│  │                                                                        │  │
│  │  Process:                                                              │  │
│  │  1. Load Engine question shapes → "What specifically...?"              │  │
│  │  2. Load STM signals → augmentation-principle.md content               │  │
│  │  3. Generate buzzword questions → grounded in signals                  │  │
│  │  4. Generate dimension questions → for WHAT, WHO, WHY, HOW             │  │
│  │  5. Prioritize by impact                                               │  │
│  │  6. Apply question limit                                               │  │
│  │                                                                        │  │
│  │  Output: questions[], status: needs_clarification                      │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                      │
│                                       ▼                                      │
│                    ┌─────────────────────────────────────────┐               │
│                    │         PRESENT TO USER                 │               │
│                    │                                         │               │
│                    │  Q1: What capability should AI augment? │               │
│                    │  Q2: Who are the users?                 │               │
│                    │  Q3: What problem does this solve?      │               │
│                    │  Q4: What constraints exist?            │               │
│                    └─────────────────────────────────────────┘               │
│                                       │                                      │
│                                       ▼                                      │
│                    ┌─────────────────────────────────────────┐               │
│                    │          USER RESPONDS                  │               │
│                    │                                         │               │
│                    │  "I want to help support agents..."     │               │
│                    └─────────────────────────────────────────┘               │
│                                       │                                      │
│                                       ▼                                      │
│                    ┌─────────────────────────────────────────┐               │
│                    │     PHOENIX-CONTEXT-UPDATE-STM          │               │
│                    │     (Capture response in STM)           │               │
│                    └─────────────────────────────────────────┘               │
│                                       │                                      │
│                                       ▼                                      │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                   CONSULT:SYNTHESIZE-RESPONSE                          │  │
│  │                          (Synthesis)                                    │  │
│  │                                                                        │  │
│  │  Inputs:                                                               │  │
│  │  • original_query: "Help me build something AI-powered"                │  │
│  │  • stm_path: .phoenix-os/stm/consult-cto-xxx/                          │  │
│  │  • questions_asked: [Q1, Q2, Q3, Q4]                                   │  │
│  │  • user_responses: (from STM)                                          │  │
│  │  • clarification_round: 1                                              │  │
│  │                                                                        │  │
│  │  Process:                                                              │  │
│  │  1. Load all context from STM                                          │  │
│  │  2. Evaluate response completeness → 0.85                              │  │
│  │  3. Extract resolved understanding                                     │  │
│  │  4. Determine next intent → design (confidence: 0.9)                   │  │
│  │  5. Check if re-clarification needed → No                              │  │
│  │  6. Generate executive summary                                         │  │
│  │                                                                        │  │
│  │  Output: status: clarified, next_intent: design                        │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                      │
└───────────────────────────────────────┼──────────────────────────────────────┘
                                        │
                    ┌───────────────────┴───────────────────┐
                    │                                       │
                    ▼                                       ▼
        ┌───────────────────────┐             ┌───────────────────────┐
        │   STILL VAGUE?        │             │   CLARIFIED?          │
        │                       │             │                       │
        │   Re-run analyze      │             │   Route to next       │
        │   (max 3 rounds)      │             │   intent (design)     │
        └───────────────────────┘             └───────────────────────┘
```

---

## Data Flow Between Skills

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           analyze-request                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│ OUTPUT:                                                                      │
│                                                                             │
│ {                                                                           │
│   "vagueness_score": 0.7,                                                   │
│   "buzzwords_detected": [                                                   │
│     {"term": "AI-powered", "signal": "augmentation-principle.md"}           │
│   ],                                                                        │
│   "missing_dimensions": ["WHAT", "WHO", "WHY", "HOW"],                      │
│   "complexity": "complex",                                                  │
│   "signal_mapping": {...},                                                  │
│   "recommended_action": "clarify"                                           │
│ }                                                                           │
└─────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        │ Feeds into
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         clarify-requirements                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│ RECEIVES: analysis object                                                   │
│ READS: STM context.md (signals), Engine cto-intents.md (shapes)             │
│                                                                             │
│ OUTPUT:                                                                      │
│                                                                             │
│ {                                                                           │
│   "questions": [                                                            │
│     {                                                                       │
│       "question": "What specific capability should AI augment?",            │
│       "signal_path": "@{user-vault}/signals/ai/augmentation-principle.md",  │
│       "why_it_matters": "AI should augment capabilities, not replace...",   │
│       "examples": [...]                                                     │
│     },                                                                      │
│     ...                                                                     │
│   ],                                                                        │
│   "status": "needs_clarification"                                           │
│ }                                                                           │
└─────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        │ User responds
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         synthesize-response                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│ RECEIVES: questions_asked, user_responses (from STM)                        │
│ READS: Full STM workspace                                                   │
│                                                                             │
│ OUTPUT:                                                                      │
│                                                                             │
│ {                                                                           │
│   "status": "clarified",                                                    │
│   "completeness_score": 0.85,                                               │
│   "resolved_understanding": {                                               │
│     "summary": "User wants AI support assistant for agents...",             │
│     "dimensions": {"WHAT": "...", "WHO": "...", "WHY": "...", "HOW": "..."}  │
│   },                                                                        │
│   "next_intent": {"intent": "design", "confidence": 0.9},                   │
│   "next_steps": [...]                                                       │
│ }                                                                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Key Design Principles

### 1. Separation of Concerns

| Skill | Concern | Does NOT Do |
|-------|---------|-------------|
| analyze-request | Understanding | Generate questions |
| clarify-requirements | Question generation | Evaluate responses |
| synthesize-response | Wrap-up and routing | Parse new queries |

### 2. Signal-Grounded Protocol

All three skills follow the same protocol:

```
1. Read STM context.md (pre-loaded signals from radar scanning)
2. Read Engine for structure (question shapes, intent patterns)
3. Generate output grounded in signal content
4. Cite signal sources where relevant
5. State "No signal found" when appropriate (don't force citations)
```

### 3. Composability

Skills can be chained or used independently:

```python
# Full chain
analysis = analyze_request(query, stm_path, intent)
questions = clarify_requirements(analysis, stm_path, engine_path, max_questions)
# ... user responds ...
synthesis = synthesize_response(query, stm_path, questions, responses)

# Skip analysis (if already know what to ask)
questions = clarify_requirements(
    analysis={"buzzwords_detected": [...], "missing_dimensions": [...]},
    ...
)

# Re-analyze after response (if still vague)
new_analysis = analyze_request(user_response, stm_path, intent)
```

### 4. Testability

Each skill has:
- Clear inputs (can be mocked)
- Deterministic processing (same input → same output)
- Structured outputs (can be validated against schema)
- Success criteria (can be verified)

---

## Integration with Agent

The `phoenix:strategy-guardian` agent orchestrates these skills:

```markdown
## Agent Flow for Clarify Intent

1. Receive query + intent from orchestrator
2. Invoke `consult:analyze-request`
   - If recommended_action == "proceed": skip to step 5
   - If recommended_action == "clarify": continue
3. Invoke `consult:clarify-requirements`
4. Present questions to user (return status: needs_clarification)
5. [User responds]
6. Invoke `phoenix-context-update-stm` to capture response
7. Invoke `consult:synthesize-response`
   - If status == "needs_reclarification" AND round < 3: go to step 2
   - If status == "clarified": return to orchestrator with next_intent
   - If status == "proceed_with_assumptions": return with assumptions stated
```

---

## Error Recovery

| Failure Point | Recovery |
|---------------|----------|
| analyze-request fails | Default to full clarify (ask all 5W1H) |
| clarify-requirements fails | Use Engine shapes without signal grounding |
| synthesize-response fails | Default to consult intent with low confidence |
| User refuses to answer | Proceed with explicit assumptions |
| Max rounds exceeded | Force proceed, document unknowns |

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
