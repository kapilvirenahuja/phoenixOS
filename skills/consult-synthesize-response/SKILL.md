---
name: consult-synthesize-response
description: Synthesize gathered information into executive summary with clear next steps. Final skill in the clarify chain - consolidates understanding and routes to appropriate next intent.
---

# Synthesize Response

Consolidate all gathered information into an executive summary, determine the resolved understanding, and route to the appropriate next intent. This is the **synthesis** phase - wrapping up clarification and moving forward.

## When to Use

- **After**: User has responded to clarifying questions
- **After**: `phoenix-context-update-stm` has captured the response
- **Trigger**: Clarification round complete, ready to synthesize
- **By**: `phoenix:strategy-guardian` agent (or designated synthesizer)

## Position in Chain

```
┌─────────────────────────────────┐
│  consult:analyze-request        │
└─────────────────────────────────┘
      │
      ▼
┌─────────────────────────────────┐
│  consult:clarify-requirements   │
└─────────────────────────────────┘
      │
      ▼ questions presented
┌─────────────────────────────────┐
│  User responds                  │
│  phoenix-context-update-stm     │
└─────────────────────────────────┘
      │
      ▼ updated STM
┌─────────────────────────────────┐
│  consult:synthesize-response    │  ◄── YOU ARE HERE
│  (Synthesis: Wrap up and route) │
└─────────────────────────────────┘
      │
      ├─────────────────────────────────────────────┐
      │                                             │
      ▼ Still vague?                                ▼ Clarified?
┌─────────────────────────┐           ┌─────────────────────────────┐
│  Re-run analyze-request │           │  Route to next intent       │
│  (max 3 rounds)         │           │  (design, decide, validate) │
└─────────────────────────┘           └─────────────────────────────┘
```

---

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `original_query` | Yes | The user's original query |
| `stm_path` | Yes | Path to STM workspace (contains all context) |
| `questions_asked` | Yes | Questions that were presented to user |
| `user_responses` | Yes | User's responses (from STM context.md) |
| `clarification_round` | Yes | Which round of clarification this is (1, 2, 3) |
| `max_rounds` | No | Maximum clarification rounds before forcing proceed (default: 3) |

---

## Instructions

### Step 1: Load All Context from STM

Read from STM workspace:

```
{stm_path}/
├── context.md      → Original signals + user responses
├── state.md        → Execution state, interaction log
├── intents.md      → Intent history
└── outputs/        → Previous analysis outputs
```

**Extract:**
- Original query
- Radar matches and loaded signals
- All user responses with timestamps
- Extracted facts from each response
- Current intent state

---

### Step 2: Evaluate Response Completeness

For each question that was asked, evaluate if the response adequately addresses it:

| Evaluation | Criteria | Score |
|------------|----------|-------|
| **Complete** | Specific, actionable answer provided | 1.0 |
| **Partial** | Some information, but still gaps | 0.5 |
| **Deflected** | User avoided or redirected | 0.2 |
| **Unanswered** | No response to this question | 0.0 |

**Response Completeness Score:**

```
completeness = sum(question_scores) / question_count
```

| Score | Interpretation |
|-------|----------------|
| 0.8 - 1.0 | Fully clarified, ready to proceed |
| 0.5 - 0.8 | Mostly clarified, can proceed with stated assumptions |
| 0.3 - 0.5 | Partially clarified, may need another round |
| 0.0 - 0.3 | Not clarified, definitely need another round |

---

### Step 3: Extract Resolved Understanding

Consolidate what we now know:

```markdown
## Resolved Understanding

### The Request
[One sentence summary of what user wants]

### Key Facts Gathered
| Dimension | Value | Source |
|-----------|-------|--------|
| WHAT | [specific target] | User response to Q1 |
| WHO | [users/customers] | User response to Q2 |
| WHY | [problem being solved] | User response to Q3 |
| HOW | [constraints] | User response to Q4 |

### Assumptions Made
[List any assumptions we're proceeding with due to incomplete responses]

### Still Unknown
[List anything we couldn't clarify but will proceed without]
```

---

### Step 4: Determine Next Intent

Based on resolved understanding, identify what the user actually needs:

**Intent Detection from Clarified Request:**

| If clarified request looks like... | Route to Intent |
|-----------------------------------|-----------------|
| "Build X for Y users solving Z problem" | `design` |
| "Choose between X and Y" | `decide` |
| "Review/validate my plan for X" | `validate` |
| "I'm stuck on X, help me" | `consult` |
| "What do you think about X trend" | `advise` |
| Still too vague after 3 rounds | `consult` (with stated assumptions) |

**Apply intent detection patterns from Engine:**

```
Read @memory/engine/intents/cto-intents.md
Match clarified understanding against intent patterns
Return highest-confidence match
```

---

### Step 5: Check for Re-clarification Need

**Decision tree:**

```
If completeness_score < 0.5 AND clarification_round < max_rounds:
  → Return: needs_reclarification
  → Generate: focused follow-up questions (only for gaps)

If completeness_score < 0.5 AND clarification_round >= max_rounds:
  → Return: proceed_with_assumptions
  → Document: what we're assuming and why

If completeness_score >= 0.5:
  → Return: clarified
  → Route to: next_intent
```

---

### Step 6: Generate Executive Summary

**Summary Structure:**

```markdown
## Executive Summary

### What We Understood
[2-3 sentences capturing the clarified request]

### Key Insights
- [Insight 1 from signals + user responses]
- [Insight 2]
- [Insight 3]

### Signals Used
| Signal | How It Applied |
|--------|----------------|
| [signal path] | [how it informed questions/understanding] |

### Next Steps
1. [Immediate next action]
2. [Following action]
3. [Future consideration]

### Routing
**Next Intent**: [design | decide | validate | consult | advise]
**Agent**: [phoenix:strategy-guardian | phoenix:architect | etc.]
**Confidence**: [0.0-1.0]
```

---

### Step 7: Handle Edge Cases

**User refuses to clarify:**

```markdown
If user explicitly refuses ("just do it", "I don't know"):
  → Document refusal
  → State assumptions explicitly
  → Proceed with stated caveats
  → Lower confidence in next intent
```

**Contradictions detected:**

```markdown
If user response contradicts earlier statement:
  → Surface contradiction explicitly
  → Ask user to resolve (this is a mini-clarification)
  → Do not proceed until resolved
```

**Scope changed:**

```markdown
If user response reveals fundamentally different request:
  → Flag scope_change = true
  → Re-run full orchestration from Step 1
  → Do not synthesize based on original query
```

---

## Outputs

| Output | Type | Description |
|--------|------|-------------|
| `status` | enum | `clarified` \| `needs_reclarification` \| `proceed_with_assumptions` \| `scope_changed` |
| `completeness_score` | float | 0.0-1.0 score of how well questions were answered |
| `resolved_understanding` | object | Structured understanding of the request |
| `assumptions` | array | Assumptions made due to incomplete answers |
| `unknowns` | array | Things we couldn't determine |
| `executive_summary` | string | Markdown summary for presentation |
| `next_intent` | object | Recommended next intent with confidence |
| `next_steps` | array | Actionable next steps |
| `signals_used` | array | Signals that informed the synthesis |

**Output Schema:**

```json
{
  "status": "clarified",
  "completeness_score": 0.85,
  "resolved_understanding": {
    "summary": "User wants to build an AI-powered customer support assistant that helps agents resolve tickets faster",
    "dimensions": {
      "WHAT": "AI assistant for customer support",
      "WHO": "Internal support agents (not customers directly)",
      "WHY": "Reduce ticket resolution time, improve agent efficiency",
      "HOW": "API-first approach, integrate with existing Zendesk"
    }
  },
  "assumptions": [
    "Scale: Assumed <100 agents based on 'small team' mention",
    "Timeline: Not specified, assuming standard priority"
  ],
  "unknowns": [
    "Budget constraints",
    "Specific Zendesk plan/limitations"
  ],
  "executive_summary": "## Executive Summary\n\n### What We Understood\nYou want to build an AI assistant...",
  "next_intent": {
    "intent": "design",
    "sub_intent": "tech-design",
    "confidence": 0.9,
    "agent": "phoenix:architect",
    "rationale": "User has clear requirements and is ready for architecture guidance"
  },
  "next_steps": [
    "Design the AI assistant architecture (LLM integration pattern)",
    "Define the Zendesk integration approach",
    "Prototype with a subset of ticket types"
  ],
  "signals_used": [
    {
      "path": "@{user-vault}/signals/ai/augmentation-principle.md",
      "usage": "Framed AI as augmenting agent capabilities, not replacing agents"
    },
    {
      "path": "@{user-vault}/signals/ai/llm-integration-patterns.md",
      "usage": "Informed recommendation to start with RAG for knowledge retrieval"
    }
  ]
}
```

---

## Reclarification Output (when status = needs_reclarification)

If another round is needed:

```json
{
  "status": "needs_reclarification",
  "completeness_score": 0.4,
  "gaps_remaining": [
    {
      "dimension": "WHO",
      "original_question": "Who are the users?",
      "response_evaluation": "partial",
      "gap": "User said 'support team' but didn't specify size or skill level"
    }
  ],
  "follow_up_questions": [
    {
      "question": "How many support agents will use this? What's their technical skill level?",
      "why_needed": "Affects UX complexity and training requirements"
    }
  ],
  "round": 2,
  "max_rounds": 3
}
```

---

## Success Criteria

- [ ] All user responses evaluated for completeness
- [ ] Resolved understanding documented with sources
- [ ] Assumptions explicitly stated
- [ ] Unknowns acknowledged (not hidden)
- [ ] Next intent identified with confidence score
- [ ] Executive summary generated
- [ ] Signals used documented with how they applied
- [ ] Clear next steps provided

---

## Error Handling

| Error | Action |
|-------|--------|
| STM path not found | Fail with "Cannot synthesize without STM context" |
| No user responses in STM | Return `status: needs_reclarification` with original questions |
| Intent detection fails | Default to `consult` intent with low confidence |
| Contradictions unresolvable | Surface to user, pause synthesis |

---

## Integration Notes

### Updating STM After Synthesis

Write synthesis output to STM:

```
{stm_path}/outputs/synthesis-{timestamp}.json
```

Update `{stm_path}/state.md`:

```markdown
| Timestamp | Step | Status | Notes |
|-----------|------|--------|-------|
| {ts} | Synthesis | complete | Clarified, routing to design intent |
```

Update `{stm_path}/intents.md`:

```markdown
## Intent Resolution [{timestamp}]

**Original**: clarify (confidence: 1.0)
**Resolved to**: design (confidence: 0.9)
**Reason**: User provided clear requirements for AI support assistant
**Next agent**: phoenix:architect
```

### Handing Off to Next Intent

The orchestrator receives the synthesis output and:

1. Reads `next_intent` from synthesis
2. Updates routing plan
3. Invokes appropriate agent with:
   - `resolved_understanding` as context
   - `signals_used` for continued grounding
   - `assumptions` for the agent to validate or challenge

---

## Clarification Loop Limits

| Round | Action |
|-------|--------|
| 1 | Full clarification (all missing dimensions) |
| 2 | Targeted follow-up (only remaining gaps) |
| 3 | Final attempt (most critical gap only) |
| 4+ | Force proceed with explicit assumptions |

**After max rounds:**

```markdown
⚠️ Proceeding with assumptions

We asked 3 rounds of clarifying questions but the following remains unclear:
- [Unknown 1]
- [Unknown 2]

We're proceeding with these assumptions:
- [Assumption 1]
- [Assumption 2]

These assumptions may need revisiting if they prove incorrect.
```

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
