# Memory

> The three-layer knowledge system: Engine, Vault, STM

---

## Overview

Memory in Phoenix OS has three layers with distinct purposes:

| Layer | Location | Purpose | Lifecycle |
|-------|----------|---------|-----------|
| **Engine** | `memory/engine/` | System knowledge | Static |
| **Vault** | `{user-vault}/` | User knowledge | Persistent, growing |
| **STM** | `.phoenix-os/stm/` | Session state | Ephemeral |

```
┌─────────────────────────────────────────────────────────────┐
│                        MEMORY                                │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │    Engine    │  │    Vault     │  │     STM      │       │
│  │   (system)   │  │    (user)    │  │  (session)   │       │
│  │              │  │              │  │              │       │
│  │  - intents   │  │  - radars    │  │  - context   │       │
│  │  - flows     │  │  - signals   │  │  - state     │       │
│  │  - schemas   │  │              │  │  - outputs   │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│        │                  │                  ▲               │
│        │                  │                  │               │
│        └──────────────────┼──────────────────┘               │
│                           │                                  │
│              Step 0: Load into STM                           │
└─────────────────────────────────────────────────────────────┘
```

---

## Engine (System Knowledge)

**What it is**: How Phoenix OS works — intent patterns, orchestration flows, schemas.

**Location**: `memory/engine/`

```
memory/engine/
├── intents/
│   ├── cto-intents.md           # Intent patterns for CTO domain
│   └── sequencing-rules.md      # How to sequence multiple intents
├── flows/
│   └── recipe-orchestration-pattern.md
├── schemas/
│   └── routing-plan-schema.md
└── vault-architecture.md
```

**Who reads it**: Orchestrator reads intent patterns. Skills read schemas.

**Key rule**: Engine is static — never modified at runtime.

---

## Vault (User Knowledge)

**What it is**: Your accumulated knowledge — radars (classification lenses) and signals (content).

**Location**: `{user-vault}/`

```
{user-vault}/
├── radars/                # Classification lenses
│   ├── ai-intelligence.md
│   ├── digital-experience.md
│   ├── evolutionary-architecture.md
│   ├── innovation.md
│   ├── leadership.md
│   ├── purpose.md
│   └── technology.md
└── signals/               # Actual knowledge
    ├── ai/
    ├── evolutionary-architecture/
    ├── innovation/
    ├── leadership/
    └── technology/
```

### The 7 Strategic Radars

| Radar | Focus |
|-------|-------|
| **Purpose / Why** | Mission, vision, strategy |
| **Innovation** | New ideas, disruption |
| **Digital Experience** | UX, customer journey |
| **AI / Intelligence** | AI, ML, automation |
| **Evolutionary Architecture** | System design, scalability |
| **Leadership** | Team, culture, talent |
| **Technology** | Tools, platforms, frameworks |

### Radar Structure

```markdown
# {Radar Name}

## Keywords
### Primary (high confidence)
### Secondary (medium confidence)

## Signal Mappings
| Signal | Path |
```

### Signal Structure

```markdown
# {Signal Title}
> {One-line summary}

## Content
## Implications
## Keywords
```

**Key rule**: Radars classify. Signals contain. Agents never read Vault directly — they read from STM.

---

## STM (Session State)

**What it is**: Runtime workspace for a single session — pre-loaded signals, execution state, outputs.

**Location**: `.phoenix-os/stm/{recipe-id}-{timestamp}/`

```
.phoenix-os/stm/{recipe-id}-{timestamp}/
├── state.md       # Execution state, interaction log
├── context.md     # Pre-loaded signals from radar matching
├── intents.md     # Active intents with confidence scores
├── outputs/       # Results from each step
└── evidence/      # Debug artifacts
```

### context.md

Contains pre-loaded signals from radar matching:

```markdown
## Query Analysis
Original: "Help me build something AI-powered"

## Direct Radar Matches

### AI / Intelligence (confidence: 0.9)
**Matched on**: "AI-powered" (primary keyword)

#### Loaded Signals:
- @{user-vault}/signals/ai/augmentation-principle.md
- @{user-vault}/signals/ai/pcam-framework.md
```

### state.md

Tracks execution:

```markdown
## Initial State
- Query: "..."
- Timestamp: ...
- Recipe: consult-cto

## Current Step
Step 2: Executing routing plan

## Interaction Log
| Timestamp | Type | Summary |
```

**Key rule**: STM is ephemeral — created in Step 0, deleted after session.

---

## How Memory Flows

```
Query arrives
    │
    ▼
Step 0: Initialize STM
    │
    ├── Read Engine for orchestration patterns
    │
    ├── Scan Vault radars for keyword matches
    │
    ├── Load matched signals into STM context.md
    │
    └── STM now contains filtered, relevant knowledge
         │
         ▼
Steps 1+: Agents read from STM
    │
    ├── context.md → pre-loaded signals
    ├── state.md → execution state
    └── intents.md → current intent
```

---

## Access Rules

| Layer | Who Reads | Who Writes |
|-------|-----------|------------|
| Engine | Orchestrator, Skills | Never (static) |
| Vault | Step 0 only (radar scan) | Configuration (external) |
| STM | Agents, Skills | Agents, Skills |

**Critical rule**: Agents read STM, not Vault directly. STM is pre-populated during Step 0.

---

## Related

-> [Orchestration](orchestration.md) — How memory is used in execution
-> [Intents](intents.md) — What Engine defines
-> [Signals](signals.md) — Deep-dive on signal structure

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
