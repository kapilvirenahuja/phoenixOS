# Debug Trace: consult-cto Recipe (v2 - Fixed Semantic Algorithm)

> Query: "Help me build something AI-powered"
> Timestamp: 2026-01-07
> Mode: Debug (all steps visible)
> Version: Post-fix (semantic analysis algorithm)

---

## COMPARISON: Before vs After Fix

| Radar | BEFORE (keyword) | AFTER (semantic) | Change |
|-------|------------------|------------------|--------|
| AI/Intelligence | 0.9 direct | 0.8 direct | Minor adjustment |
| Evol. Architecture | **0 none** | **0.4 peripheral** | +0.4 (FIXED) |
| Digital Experience | **0 none** | **0.4 peripheral** | +0.4 (FIXED) |
| Innovation | 0.3 peripheral | 0.3 peripheral | No change |
| Technology | 0.1 none | 0.1 none | No change |
| Purpose | 0 none | 0 none | No change |
| Leadership | 0 none | 0 none | No change |

**Impact**: 2 additional radars matched, 3 more signals loaded, 1 new question generated about AI UX.

---

## STEP 0: Initialize STM

**Skill**: `phoenix-engine-stm-initialize`

### Input
```
query: "Help me build something AI-powered"
recipe_id: consult-cto
vault_path: @{user-vault}/
```

### Semantic Radar Scan (FIXED Algorithm)

#### Step 2b.1: Question Relevance Test

| Radar | Question Tested | Relevance | Score |
|-------|-----------------|-----------|-------|
| AI/Intelligence | "How should AI augment human capabilities?" | Directly about AI | +0.3 |
| AI/Intelligence | "Build vs buy for AI capabilities?" | Directly about building AI | +0.3 |
| Evol. Architecture | "What technical capabilities do we need for AI-native development?" | Building AI requires this | +0.3 |
| Digital Experience | "How do customers expect to interact with AI?" | AI-powered needs UX | +0.2 |
| Innovation | "What new capabilities should we prototype?" | "Build something" relates | +0.1 |

#### Step 2b.2: Focus Overlap Test

| Radar | Focus Statement | Query Term Match | Score |
|-------|-----------------|------------------|-------|
| AI/Intelligence | "AI, ML, intelligent automation" | "AI-powered" explicit | +0.2 |
| Digital Experience | "AI-powered experiences" | "AI-powered" explicit | +0.2 |
| Evol. Architecture | "AI-native development" | "AI-powered" implied | +0.1 |

#### Final Scores

| Radar | Question Score | Focus Score | Total | Match Type |
|-------|----------------|-------------|-------|------------|
| AI/Intelligence | 0.6 | 0.2 | **0.8** | direct |
| Evol. Architecture | 0.3 | 0.1 | **0.4** | peripheral |
| Digital Experience | 0.2 | 0.2 | **0.4** | peripheral |
| Innovation | 0.1 | 0.0 | **0.3** | peripheral |
| Technology | 0.1 | 0.0 | 0.1 | none |
| Purpose | 0.0 | 0.0 | 0.0 | none |
| Leadership | 0.0 | 0.0 | 0.0 | none |

### Signals Loaded to STM

**From AI/Intelligence (direct match @ 0.8)**:

| Signal | Source | Key Content |
|--------|--------|-------------|
| AI Augmentation Principle | `@{user-vault}/signals/ai/augmentation-principle.md` | AI should augment human capabilities, not replace human judgment |
| PCAM Framework | `@{user-vault}/signals/ai/pcam-framework.md` | Four dimensions: Perception, Cognition, Agency, Manifestation |
| Build vs Buy for AI | `@{user-vault}/signals/ai/build-vs-buy.md` | Framework for build/buy decisions |

**From Evolutionary Architecture (peripheral @ 0.4)**:

| Signal | Source | Key Content |
|--------|--------|-------------|
| Composability Framework | `@{user-vault}/signals/architecture/composability.md` | Design for composable AI systems |
| Sustainable Velocity Index | `@{user-vault}/signals/architecture/sustainable-velocity.md` | Maintain development velocity |

**From Digital Experience (peripheral @ 0.4)**:

| Signal | Source | Key Content |
|--------|--------|-------------|
| AI UX Anti-Patterns | `@{user-vault}/signals/ai/ux-anti-patterns.md` | Common mistakes in AI-powered UX |

### Output
```
STM Workspace: .phoenix-os/stm/consult-cto-debug-v2/
├── state.md    (execution log)
├── context.md  (loaded signals - 6 total)
└── intents.md  (pending identification)

Radars matched: 4 (was 2)
Signals loaded: 6 (was 2)
```

---

## STEP 1a: Identify Intents

**Skill**: `phoenix-engine-identify-intents`

### Input
```
query: "Help me build something AI-powered"
intent_bindings: [
  { intent: "clarify", domain: "@memory/engine/intents/cto-intents.md", agent: "phoenix:strategy-guardian", status: "active" }
]
stm_path: .phoenix-os/stm/consult-cto-debug-v2/
mode: initial
```

### Phase 1: Pattern Matching

| Intent | Pattern Matches | Initial Score |
|--------|-----------------|---------------|
| clarify | "something" (vague), "AI-powered" (buzzword), missing context | 0.65 |

### Phase 2: Confidence Boosting

| Intent | Context Signals Matched | Boost | Final Score |
|--------|-------------------------|-------|-------------|
| clarify | Query lacks specificity (+0.05), Multiple interpretations (+0.05), Key factors unknown (+0.05) | +0.15 | 0.80 |

### Phase 3: Threshold Selection

| Intent | Final Score | Threshold | Decision |
|--------|-------------|-----------|----------|
| clarify | 0.80 | >=0.8 SELECT | SELECTED |

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
intent_bindings: [{ intent: "clarify", agent: "phoenix:strategy-guardian" }]
synthesizer: phoenix:strategy-guardian
```

### Agent Matching

| Intent | Routes To | Available? |
|--------|-----------|------------|
| clarify | phoenix:strategy-guardian | YES |

### Output
```json
{
  "routing_plan": {
    "id": "plan-2026-01-07-v2",
    "steps": [
      {
        "order": 1,
        "agents": [
          {
            "agent": "phoenix:strategy-guardian",
            "intent": "clarify",
            "goal": "Resolve vague 'AI-powered something' into concrete requirements",
            "inputs": {
              "probe_for": ["What problem?", "Who experiences it?", "Why AI?", "What UX expectations?"]
            }
          }
        ]
      }
    ]
  }
}
```

Written to: `outputs/routing-plan.json`

---

## STEP 2: Execute Routing Plan

**Agent**: `phoenix:strategy-guardian`
**Intent**: `clarify`
**Skill Chain**: `analyze-request -> generate-questions`

---

### Skill 1: phoenix-perception-analyze-request

**Domain**: Perception (understanding inputs)

#### Vagueness Analysis

| Indicator | Weight | Match |
|-----------|--------|-------|
| Undefined target | +0.30 | "something" |
| Missing specifics | +0.20 | No scale, no users |
| Missing actor | +0.15 | No user/customer |
| Abstract goals | +0.00 | No "better/good/nice" |

**Vagueness Score: 0.65**

#### 5W1H Analysis

| Dimension | Status |
|-----------|--------|
| WHAT | MISSING - "something" is undefined |
| WHO | MISSING - no user/customer specified |
| WHY | MISSING - no problem statement |
| WHEN | IMPLICIT - no timeline |
| WHERE | IMPLICIT - no context |
| HOW | IMPLICIT - "AI-powered" is approach hint |

#### Buzzword to Signal Mapping

| Buzzword | Signals Found in STM |
|----------|---------------------|
| "AI-powered" | augmentation-principle.md, pcam-framework.md, ux-anti-patterns.md |
| "build" | build-vs-buy.md, composability.md |

#### Output
```json
{
  "vagueness_score": 0.65,
  "buzzwords_detected": ["AI-powered", "build"],
  "missing_dimensions": ["WHAT", "WHO", "WHY"],
  "signal_mapping": {
    "AI-powered": [
      "@{user-vault}/signals/ai/augmentation-principle.md",
      "@{user-vault}/signals/ai/pcam-framework.md",
      "@{user-vault}/signals/ai/ux-anti-patterns.md"
    ]
  }
}
```

---

### Skill 2: phoenix-manifestation-generate-questions

**Domain**: Manifestation (producing outputs)

#### Step 1: Read STM context.md
```
Found signals: 6 (vs 2 in v1)
- augmentation-principle.md
- pcam-framework.md
- build-vs-buy.md
- composability.md
- sustainable-velocity.md
- ux-anti-patterns.md (NEW - from Digital Experience radar)
```

#### Step 2: Generate Signal-Grounded Questions

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

**Question 4 (NEW - only possible with fixed algorithm):**
```
Question: "How do you expect users to interact with this AI-powered capability?"
Signal: @{user-vault}/signals/ai/ux-anti-patterns.md
Why it matters: AI-powered experiences often fall into common UX anti-patterns.
Examples:
  - Conversational interface vs. embedded automation
  - Explicit AI invocation vs. invisible assistance
  - Full autonomy vs. human-in-the-loop approval
```

#### Output
```json
{
  "question_blocks": [
    {
      "question": "What specific problem are you trying to solve?",
      "signal_path": "@{user-vault}/signals/ai/augmentation-principle.md",
      "why_it_matters": "AI should augment human capabilities, not be added for its own sake."
    },
    {
      "question": "Who experiences this problem today?",
      "signal_path": null,
      "why_it_matters": "Understanding current workflow reveals if AI is right solution."
    },
    {
      "question": "Why do you believe AI is the right approach?",
      "signal_path": "@{user-vault}/signals/ai/pcam-framework.md",
      "why_it_matters": "Most AI features are perception-only with no real agency."
    },
    {
      "question": "How do you expect users to interact with this AI-powered capability?",
      "signal_path": "@{user-vault}/signals/ai/ux-anti-patterns.md",
      "why_it_matters": "AI-powered experiences often fall into common UX anti-patterns."
    }
  ],
  "signal_coverage": 3,
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

**4. How do you expect users to interact with this AI-powered capability?** (NEW)

> **Source**: `@{user-vault}/signals/ai/ux-anti-patterns.md`
>
> **Why it matters**: AI-powered experiences often fall into common UX anti-patterns. The interaction model shapes the entire solution.

Examples:
- Conversational interface vs. embedded automation
- Explicit AI invocation vs. invisible assistance
- Full autonomy vs. human-in-the-loop approval

---

## EXECUTION SUMMARY

| Step | Skill(s) | Key Output |
|------|----------|------------|
| Step 0 | stm-initialize | **4 radars matched** (was 2), **6 signals loaded** (was 2) |
| Step 1a | identify-intents | clarify @ 0.80 selected |
| Step 1b | build-plan | 1 step, 1 agent (strategy-guardian) |
| Step 2 | analyze-request, generate-questions | **4 signal-grounded questions** (was 3) |
| Gate | - | needs_clarification -> present questions |

### Improvement from Fix

| Metric | Before (v1) | After (v2) | Delta |
|--------|-------------|------------|-------|
| Radars matched | 2 | 4 | +2 |
| Signals loaded | 2 | 6 | +4 |
| Questions generated | 3 | 4 | +1 |
| UX-related guidance | None | ux-anti-patterns.md | NEW |

### STM Workspace

```
.phoenix-os/stm/consult-cto-debug-v2/
├── state.md
├── context.md
├── debug-trace.md (this file)
└── outputs/
    ├── routing-plan.json
    └── questions.json
```

---

## FIX APPLIED

**Defect**: `phoenix-engine-stm-initialize` said "do semantic analysis" but didn't specify HOW, causing executors to fall back to keyword matching.

**Fix Applied**: Added explicit 2-step algorithm:

```markdown
#### Step 2b.1: Question Relevance Test (Primary)

For each radar's strategic questions, ask:
> "If the user's query were fully answered, would this question also need to be answered?"

| Result | Score Contribution |
|--------|-------------------|
| Question is directly about the query topic | +0.3 |
| Question would naturally arise while addressing query | +0.2 |
| Question is tangentially related | +0.1 |

#### Step 2b.2: Focus Overlap Test (Secondary)

| Result | Score Contribution |
|--------|-------------------|
| Query term explicitly in Focus | +0.2 |
| Query concept implied by Focus | +0.1 |
```

**Result**: "AI-powered" now correctly matches:
- Evolutionary Architecture (asks about AI-native technical capabilities)
- Digital Experience (mentions AI-powered experiences in Focus)

---

## NEXT STEPS (if user responds)

```
User responds
      |
      v
Step 3a: phoenix-engine-stm-update
      | (capture response, extract facts)
      v
Step 3b: phoenix-cognition-evaluate-understanding
      | (calculate completeness_score)
      v
completeness_score >= 0.8?
      |
      +-- YES -> Re-evaluate intent (may shift to decide/design/validate)
      |          -> Step 4: Synthesis (Strategic Brief)
      |
      +-- NO  -> Loop: generate more questions (max 3 rounds)
```
