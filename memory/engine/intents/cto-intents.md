# CTO Domain Intents

> Intent patterns for CTO-level strategic and technical guidance

---

## Architecture: Separation of Concerns

```
User Query
    │
    ├─────────────────────┬─────────────────────┐
    │                     │                     │
    ▼                     ▼                     │
RADAR SCANNING       INTENT DETECTION           │
(Step 0)             (Step 1)                   │
    │                     │                     │
    ▼                     ▼                     │
STM context.md       Routing Plan               │
(signals loaded)     (intents identified)       │
    │                     │                     │
    └─────────────────────┴─────────────────────┘
                          │
                          ▼
                    AGENTS EXECUTE
                    (Read from STM + serve intent)
```

### Two Independent Processes

| Process | Location | Purpose | References |
|---------|----------|---------|------------|
| **Radar Scanning** | `initialize-stm` skill | Load relevant signals into STM | `{user-vault}/radars/` |
| **Intent Detection** | `orchestrator` agent | Identify what user wants | This file |

**These processes are independent.** Intents do NOT reference radars or signals. Radars do NOT reference intents.

### What Each Provides

| Component | Provides |
|-----------|----------|
| **Intents** (this file) | Detection patterns + question framing structure (the SHAPE) |
| **Radars** | Keywords for matching + signal mappings |
| **Signals** | Actual knowledge content (the CONTENT) |
| **STM** | Pre-filtered signals for this session |

---

## Intent: clarify

### Patterns
- Vague or ambiguous queries
- Buzzwords without specifics ("AI-powered", "scalable", "modern")
- Missing critical context
- Too broad to act on ("help me with my system")

### Context Signals
- Query lacks specificity
- Multiple interpretations possible
- Key decision factors unknown
- User seems uncertain
- No radar matches (triggers clarify automatically)

### Routes To
Agent: `phoenix:strategy-guardian`

### Framing Structure

**Goal**: Resolve ambiguity and deliver strategic framing

**Question shapes** (used to gather information):
- "What specifically...?" — narrow scope
- "Who/what/when/where...?" — fill gaps
- "What does [term] mean to you?" — define terms
- "What would success look like?" — clarify outcomes

**Input complete when**: User provides concrete specifics for WHAT, WHO, WHY, and CONSTRAINTS

---

### Output Schema

**Required Deliverables**:
1. `resolved_request` — Structured summary of what user wants (WHAT, WHO, WHY, CONSTRAINTS)
2. `strategic_framing` — CTO perspective on what this means (not echoing input, adding insight)
3. `key_decisions` — Trade-offs or choices that will shape the approach
4. `recommended_action` — ONE concrete next step the user can take

**Output Format**:

```markdown
## Strategic Brief: {one-line summary}

### What You're Building

| Dimension | Value |
|-----------|-------|
| WHAT | {description of what to build/do} |
| WHO | {target users/customers} |
| WHY | {problem being solved + success metric} |
| CONSTRAINTS | {limitations, requirements, boundaries} |

### Strategic Framing

{CTO perspective — what this really means, what the core challenge is,
what matters most. NOT just echoing the user's input back.}

### Key Decisions Ahead

1. **{Decision 1}**: {Option A} vs {Option B}
   - {Why this matters}
2. **{Decision 2}**: {Option A} vs {Option B}
   - {Why this matters}

### Recommended First Action

{Concrete, specific step the user can take immediately.
NOT "let me know if you want..." but "Start by doing X, specifically Y."}
```

---

### Completion Criteria

Output is complete when:
- All 4 dimensions (WHAT, WHO, WHY, CONSTRAINTS) are documented
- Strategic framing adds insight beyond user input
- At least 1 key decision is identified
- Recommended action is specific and immediately actionable

**No user interaction during execution.** Agent executes until output is complete.

---

### Routing

After output is delivered, present routing options **based on active intent bindings from the recipe**.

**⚠️ CRITICAL: Do NOT hardcode routing options. Options must be derived from recipe bindings.**

```
### What's Next?

For each intent in recipe.intent_bindings where status == "active":
  - **[{intent}]** — {intent.description from this file}

Always include:
  - **[Done]** — I have what I need
```

**Example** (if only `clarify` and `validate` are active):
```
### What's Next?

- **[Validate]** — Stress-test this approach
- **[Done]** — I have what I need
```

**Routing rules**:
- User selects next intent (no auto-routing)
- If user selects [Done], conversation ends with deliverable
- **⛔ Do NOT present intents that are not in recipe.intent_bindings**
- **⛔ Do NOT present intents where status != "active"**
- If user requests an unavailable intent, inform them and list available options

### Related Intents
- **evolves_to**: `design`, `validate`, `decide`, `consult`, or `advise` (user choice)
- **requires_first**: None (this IS the first step)
- **conflicts_with**: All others (clarify must resolve first)

---

## Intent: decide

### Patterns
- "Should I use X or Y..."
- "Build vs buy..."
- "What should I choose between..."
- "Help me decide..."
- "Trade-offs between..."
- "Which option is better..."
- "Pros and cons of..."

### Context Signals
- User presents alternatives
- Comparative language
- Decision framing explicit
- Time pressure or commitment imminent

### Routes To
Agent: `phoenix:strategy-guardian`

### Framing Structure

**Goal**: Guide decision between options with clear rationale

**Question shapes**:
- "What criteria matter most for this decision?" — establish framework
- "What are the constraints?" — understand boundaries
- "What happens if you choose wrong?" — assess reversibility
- "Who else is affected by this decision?" — identify stakeholders

**Success criteria**: User can make informed decision with stated trade-offs

### Related Intents
- **evolves_to**: `design` (after decision made)
- **requires_first**: `clarify` (if options not well-defined)
- **conflicts_with**: None

---

## Intent: validate

### Patterns
- "Is my plan solid..."
- "Review my approach..."
- "What do you think of..."
- "Is this a good idea..."
- "Challenge my assumptions..."
- "What could go wrong with..."
- "Stress test my..."
- "Am I missing anything..."

### Context Signals
- User has an existing plan or approach
- Seeking confirmation or critique
- Past-tense or present-tense framing ("I'm planning to", "I decided to")
- Mentions specific technology choices already made

### Routes To
Agent: `phoenix:strategy-guardian`

### Framing Structure

**Goal**: Identify risks, blind spots, and weaknesses in proposed approach

**Question shapes**:
- "What assumptions are you making?" — surface hidden assumptions
- "What could cause this to fail?" — identify risks
- "What are you NOT doing?" — find gaps
- "What's your fallback if X doesn't work?" — test resilience

**Success criteria**: User understands risks and has mitigation strategies

### Related Intents
- **evolves_to**: `design` (if validation reveals need for redesign)
- **requires_first**: `clarify` (if plan is not well-defined)
- **conflicts_with**: None

---

## Intent: consult

### Patterns
- "Help me with..."
- "I'm stuck on..."
- "How do I solve..."
- "I need guidance on..."
- "Walk me through..."
- "What's the best way to handle..."
- "I'm having trouble with..."

### Context Signals
- User has a specific problem or challenge
- Seeking hands-on guidance
- Implementation-focused language
- May have attempted solutions already

### Routes To
Agent: `phoenix:strategy-guardian`

### Framing Structure

**Goal**: Provide practical guidance to unblock progress

**Question shapes**:
- "What have you tried so far?" — understand current state
- "What specifically is blocking you?" — pinpoint the issue
- "What constraints are you working within?" — understand boundaries
- "What would 'solved' look like?" — clarify desired outcome

**Success criteria**: User has actionable next steps to make progress

### Related Intents
- **evolves_to**: `design` (if problem requires new architecture), `validate` (if solution needs review)
- **requires_first**: `clarify` (if problem is not well-defined)
- **conflicts_with**: None

---

## Intent: advise

### Patterns
- "What's your perspective on..."
- "What do you think about..."
- "Should I be worried about..."
- "What trends should I watch..."
- "Give me your take on..."
- "What would you recommend for..."
- "How do you see X evolving..."
- "What's your opinion on..."

### Context Signals
- Seeking strategic perspective, not tactical help
- Forward-looking or trend-related
- No immediate decision or problem to solve
- Values CTO experience and judgment

### Routes To
Agent: `phoenix:advisor` or `phoenix:strategy-guardian`

### Framing Structure

**Goal**: Provide informed strategic perspective

**Question shapes**:
- "Why are you asking this now?" — understand context
- "What decision might this inform?" — connect to action
- "What's your current thinking?" — understand baseline
- "What specifically concerns you?" — focus the advice

**Success criteria**: User has strategic perspective to inform thinking

### Related Intents
- **evolves_to**: `decide` (if advice leads to decision), `design` (if advice sparks new initiative)
- **requires_first**: None
- **conflicts_with**: None

---

## Intent: design

### Patterns
- "How should I build..."
- "What architecture should I use..."
- "Design a system for..."
- "What's the best approach to..."
- "Help me architect..."
- "What technology stack..."
- "How would you structure..."

### Context Signals
- User is starting a new project or feature
- No existing implementation mentioned
- Forward-looking language ("should", "would", "will")
- Scale or requirements mentioned

### Routes To
Sub-intent classification → then routes to appropriate agent

### Sub-Intents

#### tech-design
**Patterns**: Technical architecture, system design, API design, infrastructure
**Example**: "Design my API layer", "What database architecture should I use?"
**Routes To**: Agent `phoenix:architect`

#### ux-design
**Patterns**: User experience, user flows, interface design, onboarding
**Example**: "Design the onboarding flow", "How should the user journey work?"
**Routes To**: Agent `phoenix:ux-architect`

#### strategy-design
**Patterns**: Business strategy, go-to-market, product strategy, competitive positioning
**Example**: "Design our go-to-market", "How should we position this product?"
**Routes To**: Agent `phoenix:strategist`

### Framing Structure

**Goal**: Provide design guidance with clear rationale

**Question shapes**:
- "What scale are you designing for?" — understand constraints
- "Who needs to maintain this?" — understand team capabilities
- "What's already in place?" — understand integration points
- "What's non-negotiable vs. flexible?" — understand priorities

**Success criteria**: User has actionable design direction with understood trade-offs

### Related Intents
- **evolves_to**: `validate` (after design is proposed)
- **requires_first**: `clarify` (if requirements are vague)
- **conflicts_with**: None

---

## Intent Detection Priority

When multiple intents could match, use this priority:

1. **clarify** - Always resolve ambiguity first (also triggered when no radar matches)
2. **decide** - Decision gates block other work
3. **validate** - Review before building
4. **consult** - Address specific problems
5. **advise** - Provide strategic perspective
6. **design** - Create after decisions and validation

---

## Intent Confidence Thresholds

| Confidence | Action |
|------------|--------|
| > 0.8 | Proceed with detected intent |
| 0.5 - 0.8 | Confirm intent with user |
| < 0.5 | Trigger `clarify` intent |

---

**Version**: 4.1.0
**Last Updated**: 2026-01-13
**Changes**: Fixed clarify routing section - options now derived from recipe bindings, not hardcoded. Added explicit warnings against presenting unavailable intents.
