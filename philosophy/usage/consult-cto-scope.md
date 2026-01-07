# Consult CTO - Recipe Scope

> Complete implementation scope for the `consult-cto` recipe

## Overview

The `consult-cto` recipe provides CTO-level strategic and technical guidance. It routes queries through intent classification to specialized agents that leverage domain-specific skills.

---

## ⛔ Critical Execution Requirement

**Step 1 (Initialize Context) is MANDATORY and must complete before any other step.**

This is not optional setup—it creates the STM workspace that all subsequent steps depend on. Do not skip this step to optimize for response speed.

See: `@memory/engine/flows/recipe-orchestration-pattern.md` → "Procedural Execution Requirements"

---

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
│ Step 0: Initialize STM                                       │
│                                                              │
│   Skill: phoenix-engine-stm-initialize                       │
│   - Create STM workspace structure                           │
│   - Scan query against radars ({user-vault}/radars/)         │
│   - Load matched signals into STM context.md                 │
│   - Capture initial state                                    │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ Step 1: Build Routing Plan                                   │
│                                                              │
│   Agent: phoenix:orchestrator                                │
│   Memory: @memory/engine/intents/cto-intents.md              │
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

**Reference**: `@memory/engine/intents/sequencing-rules.md`

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

### Skills - Engine Domain

| Skill | File | Status |
|-------|------|--------|
| `phoenix-engine-stm-initialize` | `skills/phoenix-engine-stm-initialize/SKILL.md` | ✓ Complete |
| `phoenix-engine-stm-update` | `skills/phoenix-engine-stm-update/SKILL.md` | ✓ Complete |
| `phoenix-engine-identify-intents` | `skills/phoenix-engine-identify-intents/SKILL.md` | ✓ Complete |
| `phoenix-engine-build-plan` | `skills/phoenix-engine-build-plan/SKILL.md` | ✓ Complete |

### Skills - PCAM Domain (Clarify Intent)

| Skill | File | Status |
|-------|------|--------|
| `phoenix-perception-analyze-request` | `skills/phoenix-perception-analyze-request/SKILL.md` | ✓ Complete |
| `phoenix-manifestation-generate-questions` | `skills/phoenix-manifestation-generate-questions/SKILL.md` | ✓ Complete |
| `phoenix-cognition-evaluate-understanding` | `skills/phoenix-cognition-evaluate-understanding/SKILL.md` | ✓ Complete |

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
| `memory/engine/intents/cto-intents.md` | ✓ Complete | Intent patterns for classification |
| `memory/engine/intents/sequencing-rules.md` | ✓ Complete | Multi-intent ordering rules |

---

## Implementation Status

| Category | Complete | Total | Progress |
|----------|----------|-------|----------|
| Recipe | 1 | 1 | 100% |
| Agents | 2 | 5 | 40% |
| Engine Skills | 4 | 4 | 100% |
| PCAM Skills (Clarify) | 3 | 3 | 100% |
| Validate Skills | 0 | 3 | 0% |
| Advise Skills | 0 | 3 | 0% |
| Architect Skills | 0 | 4 | 0% |
| UX Skills | 0 | 3 | 0% |
| Strategy Skills | 0 | 3 | 0% |
| Memory (intents) | 2 | 2 | 100% |
| **Total** | **12** | **30** | **40%** |

---

## Pending Work

### Priority 1: Core Path (Strategy-Guardian)

Enables `clarify`, `decide`, `validate`, `consult` intents:

**Agents**: None (strategy-guardian exists)

**Skills** (✓ Complete for clarify):
1. `phoenix-perception-analyze-request` - ✓ Classify intent, detect vagueness, assess complexity
2. `phoenix-manifestation-generate-questions` - ✓ Challenge buzzwords, ask focused questions
3. `phoenix-cognition-evaluate-understanding` - ✓ Evaluate response completeness

**Validate Skills** (Planned):
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
Flow: stm-initialize → identify-intents(clarify) → strategy-guardian → [PCAM skill chain] → synthesize
```

### Decide Intent
```
User: "Should I use Postgres or MongoDB?"
Flow: stm-initialize → identify-intents(decide) → strategy-guardian → [PCAM + validate skills] → synthesize
```

### Validate Intent
```
User: "Is my microservices plan solid?"
Flow: stm-initialize → identify-intents(validate) → strategy-guardian → [validate skills] → synthesize
```

### Consult Intent
```
User: "Help me with authentication"
Flow: stm-initialize → identify-intents(consult) → strategy-guardian → [PCAM skills] → synthesize
```

### Advise Intent
```
User: "What's your take on AI agents?"
Flow: stm-initialize → identify-intents(advise) → advisor → [advise skills] → synthesize
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

**Version**: 1.2.0
**Last Updated**: 2026-01-04
