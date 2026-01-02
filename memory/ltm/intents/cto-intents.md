# CTO Domain Intents

> Intent patterns for CTO-level strategic and technical guidance

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

### Routes To
Agent: `phoenix:strategy-guardian`
Skills: `consult:clarify-requirements`

### Context Enrichment
- **user_needs**: Help articulating what they actually want
- **expects**: Focused questions that unblock decision-making
- **probe_for**:
  - "AI-powered" → What problem does AI solve?
  - "Scalable" → What's your target user count?
  - "Fast" → What latency is acceptable?
  - "Modern" → What feels outdated today?

### Related Intents
- **evolves_to**: `design`, `validate`, `decide`, `consult`, or `advise` (once clarified)
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
Skills: `consult:analyze-request`, `validate:challenge-assumptions`

### Context Enrichment
- **user_needs**: Clear recommendation with rationale
- **expects**: Decision framework, trade-off analysis, recommendation with confidence level
- **probe_for**: Decision criteria, constraints, reversibility, timeline, who else is involved

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
Skills: `validate:challenge-assumptions`, `validate:detect-antipatterns`, `validate:generate-report`

### Context Enrichment
- **user_needs**: Honest assessment of risks and blind spots
- **expects**: Validation report with verdict (GOOD / NEEDS WORK / BAD), specific concerns, alternatives
- **probe_for**: Timeline, team size, budget, constraints, success criteria

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
Skills: `consult:analyze-request`, `consult:clarify-requirements`, `consult:synthesize-response`

### Context Enrichment
- **user_needs**: Practical guidance to unblock progress
- **expects**: Step-by-step approach, concrete recommendations, actionable next steps
- **probe_for**: What's been tried, current blockers, constraints, desired outcome

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
Agent: `phoenix:advisor` (TBD) or `phoenix:strategy-guardian`
Skills: `advise:provide-perspective`, `advise:assess-trends`, `advise:strategic-counsel`

### Context Enrichment
- **user_needs**: Informed perspective from experienced CTO lens
- **expects**: Nuanced viewpoint, industry context, strategic considerations
- **probe_for**: Why asking now, what decision this might inform, specific concerns

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
**Skills**: `architect:analyze-requirements`, `architect:propose-options`, `architect:document-decision`, `architect:identify-risks`

#### ux-design
**Patterns**: User experience, user flows, interface design, onboarding
**Example**: "Design the onboarding flow", "How should the user journey work?"
**Routes To**: Agent `phoenix:ux-architect` (TBD)
**Skills**: `ux:map-user-journey`, `ux:design-flow`, `ux:validate-experience`

#### strategy-design
**Patterns**: Business strategy, go-to-market, product strategy, competitive positioning
**Example**: "Design our go-to-market", "How should we position this product?"
**Routes To**: Agent `phoenix:strategist` (TBD)
**Skills**: `strategy:analyze-market`, `strategy:design-gtm`, `strategy:competitive-positioning`

### Context Enrichment
- **user_needs**: System design guidance with clear rationale
- **expects**: Architecture recommendation, trade-offs analysis, implementation roadmap
- **probe_for**: Scale requirements, constraints, team capabilities, timeline, existing systems

### Related Intents
- **evolves_to**: `validate` (after design is proposed)
- **requires_first**: `clarify` (if requirements are vague)
- **conflicts_with**: None

---

## Intent Detection Priority

When multiple intents could match, use this priority:

1. **clarify** - Always resolve ambiguity first
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

**Version**: 1.1.0
**Last Updated**: 2026-01-02
