# Consult CTO - Recipe Scope

> Complete implementation scope for the `consult-cto` recipe

## Overview

The `consult-cto` recipe provides CTO-level strategic and technical guidance. It routes queries through intent classification to specialized agents that leverage domain-specific skills.

## Execution Flow

```
User Query
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│                    consult-cto Recipe                        │
│                recipes/consult-cto.md                        │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 1: Initialize Context                                   │
│                                                              │
│   Skill: context:initialize-stm                              │
│   - Create STM workspace structure                           │
│   - Load relevant LTM patterns                               │
│   - Capture initial state                                    │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 2: Identify Intent                                      │
│                                                              │
│   Skill: context:identify-intent                             │
│   Memory: @memory/ltm/intents/cto-intents.md                 │
│                                                              │
│   Intents (6):                                               │
│   - clarify  → Resolve ambiguity                             │
│   - decide   → Choose between options                        │
│   - validate → Stress-test existing plans                    │
│   - consult  → Seek help on a problem                        │
│   - advise   → Strategic counsel/perspective                 │
│   - design   → Create something new (→ sub-intents)          │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 3: Route to Agent                                       │
└─────────────────────────────────────────────────────────────┘
    │
    ├─────────────────────────────────────────────────────────┐
    │                                                         │
    ▼                                                         ▼
┌─────────────────────────┐                    ┌──────────────────────────┐
│   clarify / decide /    │                    │         design           │
│   validate / consult    │                    │            │             │
│                         │                    │            ▼             │
│            │            │                    │   Sub-intent Detection   │
│            ▼            │                    │            │             │
│  phoenix:strategy-      │                    │    ┌───────┼───────┐     │
│  guardian               │                    │    ▼       ▼       ▼     │
└─────────────────────────┘                    │  tech   ux    strategy   │
                                               └──────────────────────────┘
    │                                                   │
    ▼                                                   ▼
┌─────────────────────────┐                    ┌──────────────────────────┐
│         advise          │                    │     Design Sub-Intents    │
│            │            │                    │                          │
│            ▼            │                    │  tech-design             │
│  phoenix:advisor (TBD)  │                    │    → phoenix:architect   │
│  or strategy-guardian   │                    │                          │
└─────────────────────────┘                    │  ux-design               │
                                               │    → phoenix:ux-architect│
                                               │      (TBD)               │
                                               │                          │
                                               │  strategy-design         │
                                               │    → phoenix:strategist  │
                                               │      (TBD)               │
                                               └──────────────────────────┘
```

## Intent Model

### Top-Level Intents

| Intent | Description | Example Query | Routes To |
|--------|-------------|---------------|-----------|
| `clarify` | Resolve ambiguity | "I need to scale" (vague) | `phoenix:strategy-guardian` |
| `decide` | Choose between options | "Postgres or MongoDB?" | `phoenix:strategy-guardian` |
| `validate` | Stress-test existing plans | "Is my architecture solid?" | `phoenix:strategy-guardian` |
| `consult` | Seek help on a problem | "Help me with authentication" | `phoenix:strategy-guardian` |
| `advise` | Strategic counsel/perspective | "What's your take on AI agents?" | `phoenix:advisor` (TBD) |
| `design` | Create something new | "How should I build..." | → Sub-intent |

### Design Sub-Intents

| Sub-Intent | Description | Example | Routes To |
|------------|-------------|---------|-----------|
| `tech-design` | Technical architecture | "Design my API layer" | `phoenix:architect` |
| `ux-design` | User experience | "Design the onboarding flow" | `phoenix:ux-architect` (TBD) |
| `strategy-design` | Business/product strategy | "Design our go-to-market" | `phoenix:strategist` (TBD) |

### Intent Detection Priority

When multiple intents could match:

```
1. clarify    → Always resolve ambiguity first
2. decide     → Decision gates block other work
3. validate   → Review before building
4. consult    → Address specific problems
5. advise     → Provide strategic perspective
6. design     → Create after decisions and validation
```

**Reference**: `@memory/ltm/intents/sequencing-rules.md`

---

## Component Inventory

### Recipe

| File | Status | Description |
|------|--------|-------------|
| `recipes/consult-cto.md` | ✓ Complete | Main recipe orchestrating the flow |

### Agents

| File | Registration | Status | Handles |
|------|--------------|--------|---------|
| `agents/strategy-guardian.md` | `phoenix:strategy-guardian` | ✓ Complete | clarify, decide, validate, consult |
| `agents/architect.md` | `phoenix:architect` | ✓ Stubbed | design → tech-design |
| `agents/advisor.md` | `phoenix:advisor` | ✗ Missing | advise |
| `agents/ux-architect.md` | `phoenix:ux-architect` | ✗ Missing | design → ux-design |
| `agents/strategist.md` | `phoenix:strategist` | ✗ Missing | design → strategy-design |

### Skills - Context Domain

| Skill | File | Status |
|-------|------|--------|
| `context:initialize-stm` | `skills/context/initialize-stm/SKILL.md` | ✓ Complete |
| `context:identify-intent` | `skills/context/identify-intent/SKILL.md` | ✓ Complete |

### Skills - Consult Domain

| Skill | File | Status |
|-------|------|--------|
| `consult:analyze-request` | `skills/consult/analyze-request/SKILL.md` | ✗ Missing |
| `consult:clarify-requirements` | `skills/consult/clarify-requirements/SKILL.md` | ✗ Missing |
| `consult:synthesize-response` | `skills/consult/synthesize-response/SKILL.md` | ✗ Missing |

### Skills - Validate Domain

| Skill | File | Status |
|-------|------|--------|
| `validate:challenge-assumptions` | `skills/validate/challenge-assumptions/SKILL.md` | ✗ Missing |
| `validate:detect-antipatterns` | `skills/validate/detect-antipatterns/SKILL.md` | ✗ Missing |
| `validate:generate-report` | `skills/validate/generate-report/SKILL.md` | ✗ Missing |

### Skills - Advise Domain

| Skill | File | Status |
|-------|------|--------|
| `advise:provide-perspective` | `skills/advise/provide-perspective/SKILL.md` | ✗ Missing |
| `advise:assess-trends` | `skills/advise/assess-trends/SKILL.md` | ✗ Missing |
| `advise:strategic-counsel` | `skills/advise/strategic-counsel/SKILL.md` | ✗ Missing |

### Skills - Architect Domain

| Skill | File | Status |
|-------|------|--------|
| `architect:analyze-requirements` | `skills/architect/analyze-requirements/SKILL.md` | ✗ Missing |
| `architect:propose-options` | `skills/architect/propose-options/SKILL.md` | ✗ Missing |
| `architect:document-decision` | `skills/architect/document-decision/SKILL.md` | ✗ Missing |
| `architect:identify-risks` | `skills/architect/identify-risks/SKILL.md` | ✗ Missing |

### Skills - UX Domain

| Skill | File | Status |
|-------|------|--------|
| `ux:map-user-journey` | `skills/ux/map-user-journey/SKILL.md` | ✗ Missing |
| `ux:design-flow` | `skills/ux/design-flow/SKILL.md` | ✗ Missing |
| `ux:validate-experience` | `skills/ux/validate-experience/SKILL.md` | ✗ Missing |

### Skills - Strategy Domain

| Skill | File | Status |
|-------|------|--------|
| `strategy:analyze-market` | `skills/strategy/analyze-market/SKILL.md` | ✗ Missing |
| `strategy:design-gtm` | `skills/strategy/design-gtm/SKILL.md` | ✗ Missing |
| `strategy:competitive-positioning` | `skills/strategy/competitive-positioning/SKILL.md` | ✗ Missing |

### Memory

| File | Status | Purpose |
|------|--------|---------|
| `memory/ltm/intents/cto-intents.md` | ✓ Complete | Intent patterns for classification |
| `memory/ltm/intents/sequencing-rules.md` | ✓ Complete | Multi-intent ordering rules |

---

## Implementation Status

| Category | Complete | Total | Progress |
|----------|----------|-------|----------|
| Recipe | 1 | 1 | 100% |
| Agents | 2 | 5 | 40% |
| Context Skills | 2 | 2 | 100% |
| Consult Skills | 0 | 3 | 0% |
| Validate Skills | 0 | 3 | 0% |
| Advise Skills | 0 | 3 | 0% |
| Architect Skills | 0 | 4 | 0% |
| UX Skills | 0 | 3 | 0% |
| Strategy Skills | 0 | 3 | 0% |
| Memory (intents) | 2 | 2 | 100% |
| **Total** | **7** | **29** | **24%** |

---

## Pending Work

### Priority 1: Core Path (Strategy-Guardian)

Enables `clarify`, `decide`, `validate`, `consult` intents:

**Agents**: None (strategy-guardian exists)

**Skills**:
1. `consult:analyze-request` - Classify intent, detect vagueness, assess complexity
2. `consult:clarify-requirements` - Challenge buzzwords, ask focused questions
3. `consult:synthesize-response` - Create executive summary with next steps
4. `validate:challenge-assumptions` - Identify and stress-test implicit assumptions
5. `validate:detect-antipatterns` - Recognize common failure patterns
6. `validate:generate-report` - Produce validation report with verdict

### Priority 2: Advisory Path

Enables `advise` intent:

**Agents**:
1. `phoenix:advisor` - Strategic advisor agent

**Skills**:
1. `advise:provide-perspective` - Share informed viewpoint
2. `advise:assess-trends` - Evaluate industry/technology trends
3. `advise:strategic-counsel` - Provide strategic guidance

### Priority 3: Technical Design Path

Enables `design` → `tech-design`:

**Agents**: None (architect exists as stub)

**Skills**:
1. `architect:analyze-requirements` - Extract functional and non-functional requirements
2. `architect:propose-options` - Generate architecture alternatives with trade-offs
3. `architect:document-decision` - Create ADR for chosen approach
4. `architect:identify-risks` - Surface technical risks and mitigations

### Priority 4: UX Design Path

Enables `design` → `ux-design`:

**Agents**:
1. `phoenix:ux-architect` - UX design agent

**Skills**:
1. `ux:map-user-journey` - Map user flows and touchpoints
2. `ux:design-flow` - Design interaction flows
3. `ux:validate-experience` - Validate UX decisions

### Priority 5: Strategy Design Path

Enables `design` → `strategy-design`:

**Agents**:
1. `phoenix:strategist` - Business strategy agent

**Skills**:
1. `strategy:analyze-market` - Analyze market landscape
2. `strategy:design-gtm` - Design go-to-market strategy
3. `strategy:competitive-positioning` - Position against competitors

---

## Usage Examples

### Clarify Intent
```
User: "I need to scale"
Flow: initialize-stm → identify-intent(clarify) → strategy-guardian → [consult:clarify-requirements] → synthesize
```

### Decide Intent
```
User: "Should I use Postgres or MongoDB?"
Flow: initialize-stm → identify-intent(decide) → strategy-guardian → [consult + validate skills] → synthesize
```

### Validate Intent
```
User: "Is my microservices plan solid?"
Flow: initialize-stm → identify-intent(validate) → strategy-guardian → [validate skills] → synthesize
```

### Consult Intent
```
User: "Help me with authentication"
Flow: initialize-stm → identify-intent(consult) → strategy-guardian → [consult skills] → synthesize
```

### Advise Intent
```
User: "What's your take on AI agents?"
Flow: initialize-stm → identify-intent(advise) → advisor → [advise skills] → synthesize
```

### Design → Tech-Design
```
User: "Design my API layer"
Flow: initialize-stm → identify-intent(design) → sub-intent(tech-design) → architect → [architect skills] → synthesize
```

### Design → UX-Design
```
User: "Design the onboarding flow"
Flow: initialize-stm → identify-intent(design) → sub-intent(ux-design) → ux-architect → [ux skills] → synthesize
```

### Design → Strategy-Design
```
User: "Design our go-to-market"
Flow: initialize-stm → identify-intent(design) → sub-intent(strategy-design) → strategist → [strategy skills] → synthesize
```

---

## Communication Style

All outputs follow the CTO voice:

- **Direct**: "This will fail because..." not soft suggestions
- **Specific**: Concrete examples, numbers, failure scenarios
- **Constructive**: Problems paired with alternatives
- **Questioning**: "What happens when this fails?"

---

**Version**: 1.1.0
**Last Updated**: 2026-01-02
