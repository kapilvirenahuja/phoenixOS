# Debug Trace: consult-cto Recipe

> Query: "Help me build something AI-powered"
> Timestamp: 2026-01-07
> Mode: Debug (all steps visible)

---

## STEP 0: Initialize STM

**Skill**: `phoenix-engine-stm-initialize`

### Input
```
query: "Help me build something AI-powered"
recipe_id: consult-cto
```

### Radar Scan Results

| Radar | Match Type | Keywords Matched |
|-------|------------|------------------|
| AI/Intelligence | ✅ DIRECT | "AI-powered" |
| Innovation | ⚠️ WEAK | "build" (partial) |
| Technology | ❌ NO MATCH | - |
| Leadership | ❌ NO MATCH | - |
| Evolutionary Arch. | ❌ NO MATCH | - |
| Digital Experience | ❌ NO MATCH | - |
| Purpose | ❌ NO MATCH | - |

### Signals Loaded to STM

**Signal 1: AI Augmentation Principle**
- Source: `@{user-vault}/signals/ai/augmentation-principle.md`
- Content: AI should augment human capabilities, not replace human judgment for critical decisions.
- Implications:
  - AI projects should start with "what human capability are we augmenting?"
  - Avoid AI-for-AI's-sake projects
  - Success metric: humans become more effective, not obsolete

**Signal 2: PCAM Framework**
- Source: `@{user-vault}/signals/ai/pcam-framework.md`
- Content: Four dimensions define agentic AI systems:
  1. Perception — What signals can it sense?
  2. Cognition — How does it reason?
  3. Agency — What actions can it take?
  4. Manifestation — What outputs does it produce?
- Implications:
  - Use PCAM to evaluate any "AI-powered" proposal
  - Weak in any dimension = weak overall system
  - Most "AI features" are perception-only (no real agency)

### Output
```
STM Workspace: .phoenix-os/stm/consult-cto-debug/
├── state.md    (execution log)
└── context.md  (loaded signals)
```

---

## STEP 1a: Identify Intents

**Skill**: `phoenix-engine-identify-intents`

### Input
```
query: "Help me build something AI-powered"
intent_domain_path: @memory/engine/intents/cto-intents.md
enabled_intents: [clarify, decide, validate, consult, advise]
stm_path: .phoenix-os/stm/consult-cto-debug/
mode: initial
```

### Phase 1: Pattern Matching

| Intent | Pattern Matches | Initial Score |
|--------|-----------------|---------------|
| clarify | ✅ "something" (vague), ✅ "AI-powered" (buzzword), ✅ missing context | 0.65 |
| consult | ⚠️ "Help me" (partial) | 0.35 |
| decide | ❌ No "X or Y", no trade-offs | 0.10 |
| validate | ❌ No existing plan mentioned | 0.05 |
| advise | ❌ Not asking for perspective | 0.05 |

### Phase 2: Confidence Boosting

| Intent | Context Signals Matched | Boost | Final Score |
|--------|-------------------------|-------|-------------|
| clarify | ✅ Query lacks specificity (+0.05), ✅ Multiple interpretations (+0.05), ✅ Key factors unknown (+0.05) | +0.15 | 0.80 |
| consult | ⚠️ "Help me" (weak) | +0.05 | 0.40 |

### Phase 3: Threshold Selection

| Intent | Final Score | Threshold | Decision |
|--------|-------------|-----------|----------|
| clarify | 0.80 | ≥0.8 SELECT | ✅ SELECTED |
| consult | 0.40 | <0.5 DISCARD | ❌ DISCARDED |
| decide | 0.10 | <0.5 DISCARD | ❌ DISCARDED |
| validate | 0.05 | <0.5 DISCARD | ❌ DISCARDED |
| advise | 0.05 | <0.5 DISCARD | ❌ DISCARDED |

### Output
```json
{
  "selected_intents": [
    {
      "intent": "clarify",
      "confidence": 0.80,
      "needs_confirmation": false,
      "matched_patterns": ["vague query", "buzzword: AI-powered", "missing context"],
      "rationale": "Query contains vague terms ('something'), buzzword ('AI-powered'), and lacks WHO/WHY context"
    }
  ]
}
```

---

## STEP 1b: Build Routing Plan

**Skill**: `phoenix-engine-build-plan`

### Input
```
selected_intents: [clarify @ 0.80]
query: "Help me build something AI-powered"
intent_domain_path: @memory/engine/intents/cto-intents.md
available_agents: [orchestrator, strategy-guardian]
synthesizer: phoenix:strategy-guardian
```

### Phase 1: Agent Matching

| Intent | Routes To (from domain) | Available? |
|--------|-------------------------|------------|
| clarify | strategy-guardian | ✅ YES |

### Context Enrichment (from cto-intents.md)
```
goal: "Resolve ambiguity and deliver strategic framing"
user_needs: "Clear understanding of WHAT, WHO, WHY, CONSTRAINTS"
expects: "Strategic Brief with resolved requirements"
probe_for: ["Specifics about 'something'", "Why AI?", "Who is the user?"]
```

### Output
```json
{
  "routing_plan": {
    "id": "plan-2026-01-07-debug",
    "query": "Help me build something AI-powered",
    "confidence": 0.80,
    "steps": [
      {
        "order": 1,
        "mode": "sequential",
        "depends_on": [],
        "agents": [
          {
            "agent": "phoenix:strategy-guardian",
            "intent": "clarify",
            "goal": "Resolve vague 'AI-powered something' into concrete requirements",
            "inputs": {
              "user_needs": "Clear understanding of WHAT, WHO, WHY, CONSTRAINTS",
              "expects": "Strategic Brief with resolved requirements",
              "probe_for": ["What problem?", "Who experiences it?", "Why AI?"]
            }
          }
        ]
      }
    ],
    "synthesizer": {
      "agent": "phoenix:strategy-guardian"
    }
  }
}
```

Written to: `outputs/routing-plan.json`

---

## STEP 2: Execute Routing Plan

**Agent**: `phoenix:strategy-guardian`
**Intent**: `clarify`
**Skill Chain**: `analyze-request → generate-questions`

### Agent Input
```
query: "Help me build something AI-powered"
intent: clarify
goal: Resolve vague 'AI-powered something' into requirements
stm_path: .phoenix-os/stm/consult-cto-debug/
```

---

### Skill 1: phoenix-perception-analyze-request

**Domain**: Perception (understanding inputs)

#### Step 1: Tokenize and Parse
```
verbs: [help, build]
nouns: [something]
modifiers: [AI-powered]
structure: request + undefined_target + buzzword_modifier
```

#### Step 2: Detect Vagueness Indicators

| Indicator | Weight | Match |
|-----------|--------|-------|
| Undefined target | +0.30 | ✅ "something" |
| Missing specifics | +0.20 | ✅ No scale, no users |
| Missing actor | +0.15 | ✅ No user/customer |
| Abstract goals | +0.00 | ❌ No "better/good/nice" |
| Pronoun no referent | +0.00 | ❌ No pronouns |

**Vagueness Score: 0.65**

#### Step 3: Detect Buzzwords

| Buzzword | Problem |
|----------|---------|
| "AI-powered" | No specific problem AI solves |
| "build" | Scope unclear: MVP? Production? Prototype? |

#### Step 4: 5W1H Analysis

| Dimension | Status |
|-----------|--------|
| WHAT | ❌ MISSING — "something" is undefined |
| WHO | ❌ MISSING — no user/customer specified |
| WHY | ❌ MISSING — no problem statement |
| WHEN | ⚠️ IMPLICIT — no timeline mentioned |
| WHERE | ⚠️ IMPLICIT — no context |
| HOW | ⚠️ IMPLICIT — "AI-powered" is approach hint |

#### Step 5: Map Buzzwords to STM Signals

| Buzzword | Signal Found |
|----------|--------------|
| "AI-powered" | ✅ augmentation-principle.md |
| "AI-powered" | ✅ pcam-framework.md |

#### Output
```json
{
  "vagueness_score": 0.65,
  "buzzwords_detected": ["AI-powered", "build"],
  "missing_dimensions": ["WHAT", "WHO", "WHY"],
  "complexity": "undefined",
  "signal_mapping": {
    "AI-powered": [
      "@{user-vault}/signals/ai/augmentation-principle.md",
      "@{user-vault}/signals/ai/pcam-framework.md"
    ]
  },
  "recommended_action": "clarify"
}
```

---

### Skill 2: phoenix-manifestation-generate-questions

**Domain**: Manifestation (producing outputs)

#### Step 1: Read STM context.md
```
Found signals:
- augmentation-principle.md
- pcam-framework.md
```

#### Step 2: Read Engine for question framing
From cto-intents.md clarify intent:
- "What specifically...?" — narrow scope
- "Who/what/when...?" — fill gaps
- "What does [term] mean to you?" — define terms

#### Step 3: Generate signal-grounded questions

**Question 1:**
```
Question: "What specific problem are you trying to solve?"
Signal: @{user-vault}/signals/ai/augmentation-principle.md
Why it matters: AI should augment human capabilities, not be added for its own sake.
Examples:
  - Reducing manual data entry time by 50%
  - Automating customer support for common queries
  - Predicting equipment failures before they happen
```

**Question 2:**
```
Question: "Who experiences this problem today, and what do they currently do instead?"
Signal: null (logical reasoning)
Why it matters: Understanding the current workflow reveals whether AI is the right solution.
Examples:
  - Support agents manually categorizing tickets
  - Analysts spending 4 hours/week on reports
  - Customers waiting 24 hours for responses
```

**Question 3:**
```
Question: "Why do you believe AI is the right approach?"
Signal: @{user-vault}/signals/ai/pcam-framework.md
Why it matters: Most "AI features" are perception-only with no real agency.
Examples:
  - Pattern recognition at scale humans can't match
  - Real-time decisions faster than human reaction
  - Learning from data to improve over time
```

#### Output
```json
{
  "question_blocks": [
    {
      "question": "What specific problem are you trying to solve?",
      "signal_path": "@{user-vault}/signals/ai/augmentation-principle.md",
      "why_it_matters": "AI should augment human capabilities, not be added for its own sake.",
      "examples": ["Reducing manual data entry time by 50%", "Automating customer support", "Predicting equipment failures"]
    },
    {
      "question": "Who experiences this problem today, and what do they currently do instead?",
      "signal_path": null,
      "why_it_matters": "Understanding the current workflow reveals whether AI is the right solution.",
      "examples": ["Support agents manually categorizing tickets", "Analysts spending 4 hours/week on reports"]
    },
    {
      "question": "Why do you believe AI is the right approach?",
      "signal_path": "@{user-vault}/signals/ai/pcam-framework.md",
      "why_it_matters": "Most 'AI features' are perception-only with no real agency.",
      "examples": ["Pattern recognition at scale", "Real-time decisions", "Learning from data"]
    }
  ],
  "signal_coverage": 2,
  "status": "needs_clarification"
}
```

Written to: `outputs/questions.json`

---

## RESPONSE GATE: needs_clarification

**Gate condition met**: Agent returned `status: needs_clarification`

### User-Facing Output

---

Your query "Help me build something AI-powered" is too vague to act on. Before I can help, I need to understand what you're actually trying to accomplish.

**1. What specific problem are you trying to solve?**

> **Source**: `@{user-vault}/signals/ai/augmentation-principle.md`
>
> **Why it matters**: AI should augment human capabilities, not be added for its own sake. Without a clear problem, you risk building AI-for-AI's-sake.

Examples:
- Reducing manual data entry time by 50%
- Automating customer support for common queries
- Predicting equipment failures before they happen

**2. Who experiences this problem today, and what do they currently do instead?**

> **Why it matters**: Understanding the current workflow reveals whether AI is the right solution or an over-engineered one.

Examples:
- Support agents manually categorizing tickets
- Analysts spending 4 hours/week on reports
- Customers waiting 24 hours for responses

**3. Why do you believe AI is the right approach?**

> **Source**: `@{user-vault}/signals/ai/pcam-framework.md`
>
> **Why it matters**: Most "AI features" are perception-only with no real agency. PCAM framework asks: What will this AI actually *do*?

Examples:
- Pattern recognition at scale humans can't match
- Real-time decisions faster than human reaction
- Learning from data to improve over time

---

## EXECUTION SUMMARY

| Step | Skill(s) | Key Output |
|------|----------|------------|
| Step 0 | stm-initialize | 1 radar matched, 2 signals loaded |
| Step 1a | identify-intents | clarify @ 0.80 selected |
| Step 1b | build-plan | 1 step, 1 agent (strategy-guardian) |
| Step 2 | analyze-request, generate-questions | 3 signal-grounded questions |
| Gate | - | needs_clarification → present questions |

### Totals

| Metric | Count |
|--------|-------|
| Skills invoked | 5 |
| Engine reads | 3 |
| Vault reads (radars) | 7 |
| Vault reads (signals) | 2 |
| STM writes | 4 |
| User outputs | 1 |

### STM Workspace

```
.phoenix-os/stm/consult-cto-debug/
├── state.md
├── context.md
├── debug-trace.md (this file)
└── outputs/
    ├── routing-plan.json
    └── questions.json
```

---

## NEXT STEPS (if user responds)

```
User responds
      │
      ▼
Step 3a: phoenix-engine-stm-update
      │ (capture response, extract facts)
      ▼
Step 3b: phoenix-cognition-evaluate-understanding
      │ (calculate completeness_score)
      ▼
completeness_score ≥ 0.8?
      │
      ├─ YES → Re-evaluate intent (may shift to decide/design/validate)
      │        → Step 4: Synthesis (Strategic Brief)
      │
      └─ NO  → Loop: generate more questions (max 3 rounds)
```
