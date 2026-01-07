# Data Flow: consult-cto Recipe

> Complete data flow from user query through recipe execution

---

## Core Mechanics

Phoenix OS is built on six core mechanics that work together to provide intelligent, context-aware assistance.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           PHOENIX OS ARCHITECTURE                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐               │
│  │ RECIPE  │────▶│ INTENT  │────▶│ AGENT   │────▶│ SKILL   │               │
│  │         │     │         │     │         │     │         │               │
│  │ Entry   │     │ What    │     │ Who     │     │ How     │               │
│  │ point   │     │ user    │     │ handles │     │ work    │               │
│  │ + scope │     │ wants   │     │ intent  │     │ is done │               │
│  └─────────┘     └─────────┘     └─────────┘     └─────────┘               │
│       │               │               │               │                     │
│       │               │               │               │                     │
│       ▼               ▼               ▼               ▼                     │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                          MEMORY SYSTEM                               │   │
│  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐            │   │
│  │  │    ENGINE     │  │    VAULT      │  │     STM       │            │   │
│  │  │  (System)     │  │  (Knowledge)  │  │  (Runtime)    │            │   │
│  │  │               │  │               │  │               │            │   │
│  │  │ Intent defs   │  │ Radars        │  │ state.md      │            │   │
│  │  │ Flow patterns │  │ Signals       │  │ context.md    │            │   │
│  │  │ Schemas       │  │ Mental models │  │ intents.md    │            │   │
│  │  └───────────────┘  └───────────────┘  └───────────────┘            │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 1. Recipe

**What it is**: The entry point and execution container for a user request.

**Analogy**: A recipe is like a restaurant menu item — it defines what's offered, what ingredients are needed, and how to prepare it.

```
┌─────────────────────────────────────────────────────────────────┐
│ RECIPE: consult-cto                                             │
├─────────────────────────────────────────────────────────────────┤
│ Description: CTO-level strategic and technical guidance         │
│                                                                 │
│ Inherits: recipe-orchestration-pattern.md (Level 2 flow)        │
│                                                                 │
│ Enabled Intents: [clarify, decide, validate, consult, advise]   │
│                                                                 │
│ Available Agents: [orchestrator, strategy-guardian, advisor]    │
│                                                                 │
│ Synthesizer: phoenix:strategy-guardian                          │
│                                                                 │
│ Intent Domain: @memory/engine/intents/cto-intents.md            │
└─────────────────────────────────────────────────────────────────┘
```

**Core responsibilities**:
- Define scope (what intents are enabled)
- Specify available agents
- Inherit execution pattern (Level 1, 2, or 3)
- Handle response gates (when to talk to user)

**Location**: `recipes/consult-cto.md`

---

### 2. Intent

**What it is**: The classification of what the user wants to accomplish.

**Analogy**: Intents are like verbs — they describe the action the user needs (clarify, decide, validate, etc.).

```
┌─────────────────────────────────────────────────────────────────┐
│ INTENT: clarify                                                 │
├─────────────────────────────────────────────────────────────────┤
│ Purpose: Resolve ambiguity in vague queries                     │
│                                                                 │
│ Patterns:                                                       │
│   - Vague queries ("something", "stuff", "it")                  │
│   - Buzzwords without specifics ("AI-powered", "scalable")      │
│   - Missing critical context (no WHO, WHAT, WHY)                │
│                                                                 │
│ Context Signals (boost confidence):                             │
│   - Undefined target (+0.3)                                     │
│   - Missing specifics (+0.2)                                    │
│   - Missing actor (+0.15)                                       │
│                                                                 │
│ Routes To: phoenix:strategy-guardian                            │
│                                                                 │
│ Completion Criteria:                                            │
│   - WHAT, WHO, WHY, CONSTRAINTS all documented                  │
│   - Vagueness resolved                                          │
│                                                                 │
│ Related Intents:                                                │
│   - evolves_to: [decide, design, validate]                      │
│   - conflicts_with: []                                          │
└─────────────────────────────────────────────────────────────────┘
```

**Core mechanics**:
- **Pattern matching**: Query scanned against intent patterns
- **Confidence scoring**: 0.0-1.0 based on pattern + context signals
- **Thresholds**: >0.8 select, 0.5-0.8 flag, <0.5 discard
- **Dependencies**: `requires_first`, `evolves_to`, `conflicts_with`

**Location**: `memory/engine/intents/cto-intents.md`

---

### 3. Agent

**What it is**: The specialized persona that handles a specific intent or set of intents.

**Analogy**: Agents are like specialists on a team — each has a defined role, skills, and way of working.

```
┌─────────────────────────────────────────────────────────────────┐
│ AGENT: phoenix:strategy-guardian                                │
├─────────────────────────────────────────────────────────────────┤
│ Role: CTO-level strategic advisor                               │
│                                                                 │
│ Handles Intents: [clarify, decide, validate, consult]           │
│                                                                 │
│ Tools: [Read, Grep, Glob, Skill]                                │
│                                                                 │
│ Memory Access:                                                  │
│   - Engine: READ (intent mechanics, flow patterns)              │
│   - STM context.md: READ (pre-loaded signals)                   │
│   - STM: READ/WRITE (store outputs, update state)               │
│   - Vault: NEVER (signals pre-loaded via STM)                   │
│                                                                 │
│ Skill Chains:                                                   │
│   clarify: analyze-request → generate-questions → [user] →      │
│            evaluate-understanding                               │
│                                                                 │
│ Communication Style: Direct, specific, constructive             │
└─────────────────────────────────────────────────────────────────┘
```

**Types of agents**:

| Type | Purpose | Example |
|------|---------|---------|
| **Engine** | System orchestration, routing | `phoenix:orchestrator` |
| **Domain** | Handle user intents | `phoenix:strategy-guardian` |
| **Utility** | Cross-cutting concerns | `phoenix:advisor` |

**Key constraint**: Agents read from STM, never search Vault directly.

**Location**: `agents/strategy-guardian.md`

---

### 4. Skill

**What it is**: A reusable, testable unit of work with defined inputs and outputs.

**Analogy**: Skills are like functions — they take inputs, do specific work, and return outputs.

```
┌─────────────────────────────────────────────────────────────────┐
│ SKILL: phoenix-perception-analyze-request                       │
├─────────────────────────────────────────────────────────────────┤
│ PCAM Domain: Perception (understanding inputs)                  │
│                                                                 │
│ Purpose: Classify complexity, detect vagueness, identify gaps   │
│                                                                 │
│ Inputs:                                                         │
│   - query: string (user's raw query)                            │
│   - stm_path: string (path to STM workspace)                    │
│   - intent: string (current intent)                             │
│                                                                 │
│ Outputs:                                                        │
│   - vagueness_score: float (0.0-1.0)                            │
│   - buzzwords_detected: array                                   │
│   - missing_dimensions: array (5W1H analysis)                   │
│   - complexity: enum (simple|moderate|complex|undefined)        │
│   - signal_mapping: object (buzzwords → signals)                │
│   - recommended_action: enum (clarify|proceed|...)              │
│                                                                 │
│ When to Use: Before generate-questions, as first PCAM step      │
└─────────────────────────────────────────────────────────────────┘
```

**PCAM Domains** (skill organization):

| Domain | Purpose | Skills |
|--------|---------|--------|
| **Perception** | Understand inputs | `analyze-request` |
| **Cognition** | Reason and evaluate | `evaluate-understanding` |
| **Agency** | (reserved for action) | — |
| **Manifestation** | Produce outputs | `generate-questions` |
| **Engine** | System infrastructure | `stm-initialize`, `identify-intents`, `build-plan` |

**Location**: `skills/phoenix-perception-analyze-request/SKILL.md`

---

### 5. Memory System

**What it is**: The three-layer architecture for storing and accessing knowledge.

```
┌─────────────────────────────────────────────────────────────────┐
│                      MEMORY ARCHITECTURE                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                        ENGINE                             │   │
│  │              memory/engine/ (System)                      │   │
│  │                                                           │   │
│  │  Purpose: HOW Phoenix works                               │   │
│  │  Content: Intent definitions, flow patterns, schemas      │   │
│  │  Access:  Agents READ for mechanics                       │   │
│  │                                                           │   │
│  │  intents/cto-intents.md     → Intent patterns, routes     │   │
│  │  flows/orchestration.md     → Execution patterns          │   │
│  │  schemas/routing-plan.md    → Data structures             │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              │                                   │
│                              ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                        VAULT                              │   │
│  │              {user-vault}/ (Knowledge)                    │   │
│  │                                                           │   │
│  │  Purpose: WHAT Phoenix knows (user's knowledge)           │   │
│  │  Content: Mental models, frameworks, best practices       │   │
│  │  Access:  NEVER directly — loaded to STM via radars       │   │
│  │                                                           │   │
│  │  radars/                    → Classification lenses       │   │
│  │    ai-intelligence.md       → "AI", "ML" keywords         │   │
│  │    innovation.md            → "new", "disrupt" keywords   │   │
│  │                                                           │   │
│  │  signals/                   → Actual knowledge            │   │
│  │    ai/augmentation-principle.md                           │   │
│  │    ai/pcam-framework.md                                   │   │
│  │    leadership/flywheel.md                                 │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              │                                   │
│                              ▼ (loaded at Step 0)                │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                         STM                               │   │
│  │           .phoenix-os/stm/ (Runtime)                      │   │
│  │                                                           │   │
│  │  Purpose: Session state, pre-loaded signals               │   │
│  │  Content: Execution state, matched signals, user input    │   │
│  │  Access:  Agents READ context.md, WRITE outputs/          │   │
│  │  Lifetime: Per-session (ephemeral)                        │   │
│  │                                                           │   │
│  │  state.md      → Execution state, step log                │   │
│  │  context.md    → Radar matches + loaded signal content    │   │
│  │  intents.md    → Current intent state                     │   │
│  │  outputs/      → Skill outputs (JSON)                     │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Key principle**: **Agents read from STM, never Vault directly.**

The radar-signal architecture works like this:

```
User Query: "Help me build something AI-powered"
     │
     ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 0: Radar Scanning                                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Query ──► Scan against all 7 radars                             │
│                                                                 │
│   AI/Intelligence radar:                                        │
│     Keywords: "AI", "ML", "intelligent", "AI-powered"           │
│     Match: "AI-powered" → confidence 0.9                        │
│                                                                 │
│   Innovation radar:                                             │
│     Keywords: "build", "create", "new"                          │
│     Match: "build" → confidence 0.7                             │
│                                                                 │
│ Direct matches (>= 0.6) ──► Load mapped signals to STM          │
│                                                                 │
│   AI/Intelligence signals loaded:                               │
│     - augmentation-principle.md                                 │
│     - pcam-framework.md                                         │
│     - build-vs-buy.md                                           │
│                                                                 │
│ Graph traversal ──► Follow signal→radar relationships           │
│   augmentation-principle.md also in Leadership                  │
│     ──► Load Leadership signals too                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
     │
     ▼
STM context.md now contains pre-loaded signals
Agents read from here, not Vault
```

---

### 6. Orchestration Pattern

**What it is**: The execution flow that recipes inherit based on their complexity level.

```
┌─────────────────────────────────────────────────────────────────┐
│                    ORCHESTRATION LEVELS                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ LEVEL 1: Direct Execution                                       │
│   - Single task, fixed flow                                     │
│   - No intent routing                                           │
│   - Example: utility recipes                                    │
│                                                                 │
│ LEVEL 2: Intent Orchestration (consult-cto uses this)           │
│   - Intent classification required                              │
│   - Routes to different agents based on intent                  │
│   - Re-invocation cycle for clarification                       │
│   - Response gates control user interaction                     │
│                                                                 │
│ LEVEL 3: Autonomous                                             │
│   - Self-directing with own orchestration                       │
│   - Example: fully autonomous agent recipes                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Level 2 Response Gates** (when system talks to user):

| Gate | Condition | Output |
|------|-----------|--------|
| **Clarification** | Agent returns `needs_clarification` | Present questions |
| **Blocked** | Agent returns `blocked` | Explain blocker |
| **Synthesis** | All agents complete | Strategic Brief |
| **Error** | Unrecoverable error | Explain failure |

**Key rule**: Steps 0, 1, 2 execute **silently**. No output to user until a gate is reached.

**Location**: `memory/engine/flows/recipe-orchestration-pattern.md`

---

### How They Work Together

```
                         ┌─────────────────────────────────────┐
                         │           USER QUERY                │
                         │  "Help me build something AI-powered"│
                         └─────────────────────────────────────┘
                                          │
                                          ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ RECIPE (consult-cto)                                                         │
│ "I know what intents are enabled and which agents can handle them"          │
└─────────────────────────────────────────────────────────────────────────────┘
                                          │
         ┌────────────────────────────────┼────────────────────────────────┐
         │                                │                                │
         ▼                                ▼                                ▼
┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐
│ MEMORY: Vault       │     │ MEMORY: STM         │     │ MEMORY: Engine      │
│                     │     │                     │     │                     │
│ Radars scan query   │────▶│ Signals pre-loaded  │     │ Intent patterns     │
│ Signals matched     │     │ to context.md       │     │ Flow mechanics      │
└─────────────────────┘     └─────────────────────┘     └─────────────────────┘
                                          │                        │
                                          │                        │
                                          ▼                        ▼
                            ┌─────────────────────────────────────────────────┐
                            │ INTENT DETECTION                                │
                            │ "Query is vague → clarify intent (0.80)"        │
                            │                                                 │
                            │ Pattern: "something" + "AI-powered" buzzword    │
                            │ Boost: undefined target, missing specifics      │
                            └─────────────────────────────────────────────────┘
                                          │
                                          ▼
                            ┌─────────────────────────────────────────────────┐
                            │ AGENT (strategy-guardian)                       │
                            │ "I handle clarify intent"                       │
                            │                                                 │
                            │ I read from:                                    │
                            │   - Engine: intent mechanics, question shapes   │
                            │   - STM: pre-loaded signals for grounding       │
                            │                                                 │
                            │ I invoke my skill chain:                        │
                            └─────────────────────────────────────────────────┘
                                          │
                    ┌─────────────────────┼─────────────────────┐
                    ▼                     ▼                     ▼
        ┌───────────────────┐ ┌───────────────────┐ ┌───────────────────┐
        │ SKILL: Perception │ │ SKILL: Manifest.  │ │ SKILL: Cognition  │
        │ analyze-request   │▶│ generate-questions│ │ evaluate-underst. │
        │                   │ │                   │ │                   │
        │ IN: query, stm    │ │ IN: analysis, stm │ │ IN: responses     │
        │ OUT: vagueness,   │ │ OUT: question     │ │ OUT: completeness │
        │      buzzwords,   │ │      blocks with  │ │      resolved     │
        │      dimensions   │ │      signal refs  │ │      understanding│
        └───────────────────┘ └───────────────────┘ └───────────────────┘
                                          │
                                          ▼
                            ┌─────────────────────────────────────────────────┐
                            │ RESPONSE GATE: needs_clarification              │
                            │                                                 │
                            │ Present signal-grounded questions to user       │
                            └─────────────────────────────────────────────────┘
```

---

### Summary: The Six Mechanics

| Mechanic | Question It Answers | Key Files |
|----------|---------------------|-----------|
| **Recipe** | What's the scope? What's enabled? | `recipes/consult-cto.md` |
| **Intent** | What does the user want? | `memory/engine/intents/cto-intents.md` |
| **Agent** | Who handles this intent? | `agents/strategy-guardian.md` |
| **Skill** | How is work done? | `skills/phoenix-*/*.md` |
| **Memory** | What knowledge is available? | `memory/engine/`, `{user-vault}/`, `.phoenix-os/stm/` |
| **Orchestration** | What's the execution flow? | `memory/engine/flows/recipe-orchestration-pattern.md` |

---

## Component-Centric Data Flow

This diagram shows how data flows **between Phoenix OS components** during consult-cto execution.

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                              USER QUERY                                        ║
║                    "Help me build something AI-powered"                        ║
╚═══════════════════════════════════════════════════════════════════════════════╝
                                      │
                                      │ query
                                      ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                 RECIPE                                         ┃
┃                              (consult-cto)                                     ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  DEFINES:                        READS:                     PRODUCES:         ┃
┃  ├─ enabled_intents[]            ├─ orchestration pattern   ├─ stm_path       ┃
┃  ├─ available_agents[]           └─ intent_domain           ├─ final_output   ┃
┃  ├─ synthesizer                                             └─ response_gates ┃
┃  └─ intent_domain_path                                                        ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │                              │                              │
          │ recipe_config                │ pattern                      │
          ▼                              ▼                              ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                              ORCHESTRATION                                     ┃
┃                         (recipe-orchestration-pattern)                         ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  RECEIVES:                       CONTROLS:                  ENFORCES:         ┃
┃  ├─ query                        ├─ step sequence           ├─ response gates ┃
┃  ├─ recipe_config                ├─ agent dispatch          ├─ silent steps   ┃
┃  └─ stm_path                     ├─ re-invocation cycle     └─ step order     ┃
┃                                  └─ gate conditions                           ┃
┃                                                                               ┃
┃  STEP 0 ──► STEP 1 ──► STEP 2 ──► GATE ──► STEP 3a ──► EVAL ──► STEP 4       ┃
┃  (STM)      (Plan)     (Execute)  (User)   (Update)    (Check)   (Synth)      ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │                              │                              │
          │                              │                              │
          ▼                              ▼                              ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                MEMORY                                          ┃
┣━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃       ENGINE        ┃        VAULT        ┃              STM                  ┃
┃    (memory/engine)  ┃    ({user-vault})   ┃       (.phoenix-os/stm)           ┃
┣━━━━━━━━━━━━━━━━━━━━━╋━━━━━━━━━━━━━━━━━━━━━╋━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                     ┃                     ┃                                   ┃
┃  PROVIDES:          ┃  PROVIDES:          ┃  STORES:                          ┃
┃  ├─ intent patterns ┃  ├─ radars (7)      ┃  ├─ state.md (execution)          ┃
┃  ├─ flow patterns   ┃  └─ signals         ┃  ├─ context.md (signals)          ┃
┃  ├─ sequencing      ┃      (knowledge)    ┃  ├─ intents.md (intent state)     ┃
┃  └─ schemas         ┃                     ┃  └─ outputs/ (skill results)      ┃
┃                     ┃                     ┃                                   ┃
┃  READ BY:           ┃  ACCESSED BY:       ┃  READ/WRITE BY:                   ┃
┃  ├─ Orchestrator    ┃  └─ STM Initialize  ┃  ├─ All Skills                    ┃
┃  └─ Skills          ┃     (Step 0 only)   ┃  └─ All Agents                    ┃
┃                     ┃                     ┃                                   ┃
┃  ⚠️ Agents: READ    ┃  ⛔ Agents: NEVER   ┃  ✅ Agents: READ context.md       ┃
┃                     ┃     (pre-loaded     ┃     WRITE outputs/                ┃
┃                     ┃      to STM)        ┃                                   ┃
┗━━━━━━━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │                              │                              │
          │ intent_patterns              │ signals                      │ stm_context
          │ sequencing_rules             │ (via radar scan)             │
          ▼                              ▼                              ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                INTENT                                          ┃
┃                            (cto-intents.md)                                    ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  DETECTED FROM:                  PRODUCES:                   ROUTES TO:       ┃
┃  ├─ query patterns               ├─ selected_intent          └─ agent         ┃
┃  ├─ context signals              ├─ confidence (0.0-1.0)                      ┃
┃  └─ STM radar matches            └─ context_enrichment                        ┃
┃                                                                               ┃
┃  ┌─────────────────────────────────────────────────────────────────────────┐  ┃
┃  │  Query: "Help me build something AI-powered"                            │  ┃
┃  │                                                                         │  ┃
┃  │  Pattern Match:           Context Boost:           Selection:           │  ┃
┃  │  ├─ "something" → vague   ├─ undefined_target +0.3  clarify: 0.80 ✓    │  ┃
┃  │  ├─ "AI-powered" → buzz   ├─ missing_specs +0.2     consult: 0.45 ✗    │  ┃
┃  │  └─ base: 0.45            └─ final: 0.80            (below threshold)  │  ┃
┃  └─────────────────────────────────────────────────────────────────────────┘  ┃
┃                                                                               ┃
┃  INTENT LIFECYCLE:                                                            ┃
┃  detected → routed → handled → [needs_clarification] → re-evaluated → done   ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │
          │ intent: clarify
          │ routes_to: strategy-guardian
          │ context_enrichment: { probe_for, expects, user_needs }
          ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                 AGENT                                          ┃
┃                          (phoenix:strategy-guardian)                           ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  RECEIVES:                       READS:                     INVOKES:          ┃
┃  ├─ query                        ├─ Engine (mechanics)      └─ skill_chain[]  ┃
┃  ├─ intent                       └─ STM context.md                            ┃
┃  ├─ goal                            (pre-loaded signals)                      ┃
┃  └─ context_enrichment                                                        ┃
┃                                  ⛔ NEVER: Vault directly                     ┃
┃                                                                               ┃
┃  SKILL CHAIN FOR clarify:                                                     ┃
┃  ┌─────────────────────────────────────────────────────────────────────────┐  ┃
┃  │                                                                         │  ┃
┃  │  analyze-request ──► generate-questions ──► [USER] ──► evaluate-underst │  ┃
┃  │    (Perception)        (Manifestation)                   (Cognition)    │  ┃
┃  │                                                                         │  ┃
┃  └─────────────────────────────────────────────────────────────────────────┘  ┃
┃                                                                               ┃
┃  RETURNS:                                                                     ┃
┃  ├─ findings                                                                  ┃
┃  ├─ next_steps                                                                ┃
┃  └─ status: complete | blocked | needs_clarification                         ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │
          │ Skill(name, inputs)
          ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                 SKILL                                          ┃
┃                           (PCAM Domain Skills)                                 ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  ┌─────────────────────┐ ┌─────────────────────┐ ┌─────────────────────┐      ┃
┃  │      PERCEPTION     │ │    MANIFESTATION    │ │      COGNITION      │      ┃
┃  │   analyze-request   │ │  generate-questions │ │ evaluate-understand │      ┃
┃  ├─────────────────────┤ ├─────────────────────┤ ├─────────────────────┤      ┃
┃  │ IN:                 │ │ IN:                 │ │ IN:                 │      ┃
┃  │ ├─ query            │ │ ├─ analysis         │ │ ├─ questions_asked  │      ┃
┃  │ ├─ stm_path         │ │ ├─ stm_path         │ │ ├─ user_responses   │      ┃
┃  │ └─ intent           │ │ ├─ intent           │ │ ├─ stm_path         │      ┃
┃  │                     │ │ └─ intent_defn_path │ │ └─ intent_defn_path │      ┃
┃  ├─────────────────────┤ ├─────────────────────┤ ├─────────────────────┤      ┃
┃  │ OUT:                │ │ OUT:                │ │ OUT:                │      ┃
┃  │ ├─ vagueness_score  │ │ ├─ question_blocks[]│ │ ├─ status           │      ┃
┃  │ ├─ buzzwords[]      │ │ ├─ signal_coverage  │ │ ├─ completeness     │      ┃
┃  │ ├─ missing_dims[]   │ │ └─ status           │ │ ├─ resolved_underst │      ┃
┃  │ ├─ complexity       │ │                     │ │ └─ assumptions[]    │      ┃
┃  │ └─ signal_mapping   │ │                     │ │                     │      ┃
┃  └─────────────────────┘ └─────────────────────┘ └─────────────────────┘      ┃
┃           │                        │                        │                 ┃
┃           │ analysis               │ questions              │ evaluation      ┃
┃           ▼                        ▼                        ▼                 ┃
┃  ┌─────────────────────────────────────────────────────────────────────────┐  ┃
┃  │                         STM outputs/                                    │  ┃
┃  │  ├─ analysis-{ts}.json                                                  │  ┃
┃  │  ├─ questions-{ts}.json                                                 │  ┃
┃  │  └─ evaluation-{ts}.json                                                │  ┃
┃  └─────────────────────────────────────────────────────────────────────────┘  ┃
┃                                                                               ┃
┃  ENGINE SKILLS (system):                                                      ┃
┃  ├─ stm-initialize      → Creates STM, loads signals from Vault              ┃
┃  ├─ identify-intents    → Pattern match, boost, select                       ┃
┃  ├─ build-plan          → Map to agents, construct routing plan              ┃
┃  └─ stm-update          → Capture user response, extract facts               ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │
          │ status: needs_clarification
          │ question_blocks[]
          ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                            RESPONSE GATE                                       ┃
┃                        (Orchestration Control)                                 ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  GATE CONDITIONS:                                                             ┃
┃  ├─ needs_clarification → Present questions (signal-first format)             ┃
┃  ├─ blocked             → Explain blocker, request user action                ┃
┃  ├─ synthesis           → Present Strategic Brief                             ┃
┃  └─ error               → Explain failure, suggest recovery                   ┃
┃                                                                               ┃
┃  CURRENT: needs_clarification                                                 ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │
          │ question_blocks[] (formatted)
          ▼
╔═══════════════════════════════════════════════════════════════════════════════╗
║                              USER OUTPUT                                       ║
║                                                                               ║
║  ## From: AI Augmentation Principle                                           ║
║  **Source**: @{user-vault}/signals/ai/augmentation-principle.md               ║
║                                                                               ║
║  AI should augment human capabilities, not replace judgment.                  ║
║                                                                               ║
║  **Questions**:                                                               ║
║  1. What human capability should AI augment?                                  ║
║  2. What does success look like?                                              ║
║  ...                                                                          ║
╚═══════════════════════════════════════════════════════════════════════════════╝
          │
          │ user_response
          ▼
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                           RE-INVOCATION CYCLE                                  ┃
┃                        (Orchestration → Memory → Intent → Agent → Skill)       ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                                               ┃
┃  1. SKILL: stm-update                                                         ┃
┃     └─ Extract facts from response, update STM context.md                     ┃
┃                                                                               ┃
┃  2. SKILL: evaluate-understanding                                             ┃
┃     └─ Assess completeness, check required dimensions                         ┃
┃                                                                               ┃
┃  3. ORCHESTRATION: Check status                                               ┃
┃     ├─ clarified         → Re-evaluate intent, may shift                      ┃
┃     ├─ needs_reclarify   → Generate follow-up questions                       ┃
┃     ├─ scope_changed     → Full re-orchestration from Step 1                  ┃
┃     └─ proceed_with_assum → Route forward with documented assumptions         ┃
┃                                                                               ┃
┃  4. INTENT: Re-identification (if clarified)                                  ┃
┃     └─ May shift: clarify (0.80) → consult (0.85)                             ┃
┃                                                                               ┃
┃  5. AGENT: New assignment (if intent shifted)                                 ┃
┃     └─ strategy-guardian now handles consult intent                           ┃
┃                                                                               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
          │
          │ all intents complete
          ▼
╔═══════════════════════════════════════════════════════════════════════════════╗
║                           FINAL OUTPUT                                         ║
║                         (Strategic Brief)                                      ║
║                                                                               ║
║  Synthesized by: phoenix:strategy-guardian                                    ║
║  Signals used: augmentation-principle.md, pcam-framework.md                   ║
║  Intents handled: [clarify, consult]                                          ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

---

### Component Interaction Matrix

| From / To | Recipe | Orchestration | Memory | Intent | Agent | Skill |
|-----------|--------|---------------|--------|--------|-------|-------|
| **Recipe** | — | inherits pattern | reads Engine | defines enabled | defines available | — |
| **Orchestration** | controls flow | — | triggers STM init | dispatches to | dispatches to | — |
| **Memory** | provides patterns | provides flows | — | provides patterns | provides context | provides mechanics |
| **Intent** | scoped by | detected during | patterns from Engine | — | routes to | — |
| **Agent** | assigned by | dispatched by | reads STM, Engine | handles | — | invokes chain |
| **Skill** | — | — | reads/writes STM | — | invoked by | chains to |

---

### Data Flow by Component

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        COMPONENT DATA FLOW                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  RECIPE                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ IN:  user_query, $ARGUMENTS                                         │   │
│  │ OUT: stm_path, final_output, response_gates                         │   │
│  │ READS: orchestration_pattern, intent_domain                         │   │
│  │ DEFINES: enabled_intents, available_agents, synthesizer             │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                              │                                              │
│                              ▼                                              │
│  MEMORY (Engine)                                                            │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ PROVIDES: intent_patterns, flow_patterns, sequencing_rules, schemas │   │
│  │ READ BY: Orchestrator agent, all Skills                             │   │
│  │ WRITE: never (static)                                               │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                              │                                              │
│  MEMORY (Vault)              │                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │ PROVIDES: radars (classification), signals (knowledge)              │  │
│  │ READ BY: stm-initialize skill ONLY (Step 0)                         │  │
│  │ WRITE: never (static)                                               │  │
│  │ ACCESS: agents NEVER read directly (pre-loaded to STM)              │  │
│  └──────────────────────────────────────────────────────────────────────┘  │
│                              │                                              │
│                              ▼                                              │
│  MEMORY (STM)                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ STORES: state.md, context.md, intents.md, outputs/                  │   │
│  │ CREATED BY: stm-initialize skill                                    │   │
│  │ READ BY: all Agents (context.md), all Skills                        │   │
│  │ WRITE BY: stm-update skill, all Skills (outputs/)                   │   │
│  │ LIFETIME: per-session (ephemeral)                                   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                              │                                              │
│                              ▼                                              │
│  INTENT                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ IN:  query, stm_context, enabled_intents                            │   │
│  │ OUT: selected_intent, confidence, context_enrichment, routes_to     │   │
│  │ DETECTED BY: identify-intents skill (via Orchestrator agent)        │   │
│  │ LIFECYCLE: detected → routed → handled → re-evaluated → complete    │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                              │                                              │
│                              ▼                                              │
│  AGENT                                                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ IN:  query, intent, goal, context_enrichment, stm_path              │   │
│  │ OUT: findings, next_steps, status                                   │   │
│  │ READS: Engine (mechanics), STM context.md (signals)                 │   │
│  │ INVOKES: skill_chain[] specific to intent                           │   │
│  │ WRITES: STM outputs/ (via skills)                                   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                              │                                              │
│                              ▼                                              │
│  SKILL                                                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ IN:  defined per skill (query, stm_path, analysis, etc.)            │   │
│  │ OUT: defined per skill (structured JSON)                            │   │
│  │ READS: STM context.md, Engine patterns                              │   │
│  │ WRITES: STM outputs/{skill}-{timestamp}.json                        │   │
│  │ CHAINS: output of one → input of next                               │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### Component Ownership

| Data | Owner | Readers | Writers |
|------|-------|---------|---------|
| `enabled_intents` | Recipe | Orchestrator | — |
| `intent_patterns` | Engine | Orchestrator, Skills | — |
| `signals` | Vault | — (loaded to STM) | — |
| `context.md` | STM | All Agents | stm-initialize, stm-update |
| `routing_plan` | Orchestrator | Recipe | build-plan skill |
| `question_blocks` | Skill | Agent, Recipe | generate-questions |
| `resolved_understanding` | Skill | Orchestrator | evaluate-understanding |

---

## Step-by-Step Execution Trace

This section traces **every component interaction** through the consult-cto execution.

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║  LEGEND                                                                        ║
╠═══════════════════════════════════════════════════════════════════════════════╣
║  [RECIPE]        Recipe-level action                                          ║
║  [ORCHESTRATION] Flow control action                                          ║
║  [MEMORY:X]      Memory access (Engine/Vault/STM)                             ║
║  [INTENT]        Intent detection/routing                                     ║
║  [AGENT:X]       Agent invocation                                             ║
║  [SKILL:X]       Skill invocation                                             ║
║  ──READ──►       Read operation                                               ║
║  ──WRITE──►      Write operation                                              ║
║  ──INVOKE──►     Invocation                                                   ║
║  ──RETURN──►     Return data                                                  ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

---

### STEP 0: Initialize STM

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 0: INITIALIZE STM                                                          │
│ Purpose: Create session workspace, load relevant signals from Vault             │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  USER ─────────────────────────────────────────────────────────────────────────│
│    │                                                                            │
│    │ "Help me build something AI-powered"                                       │
│    ▼                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [RECIPE] consult-cto                                                    │   │
│  │                                                                         │   │
│  │  1. Receive query                                                       │   │
│  │  2. Load recipe configuration                                           │   │
│  │     ──READ──► [MEMORY:Engine] recipes/consult-cto.md                    │   │
│  │               Returns: {                                                │   │
│  │                 enabled_intents: [clarify, decide, validate,            │   │
│  │                                   consult, advise],                     │   │
│  │                 available_agents: [orchestrator, strategy-guardian,     │   │
│  │                                    advisor],                            │   │
│  │                 synthesizer: "phoenix:strategy-guardian",               │   │
│  │                 intent_domain: "cto-intents.md"                         │   │
│  │               }                                                         │   │
│  │                                                                         │   │
│  │  3. Load orchestration pattern                                          │   │
│  │     ──READ──► [MEMORY:Engine] flows/recipe-orchestration-pattern.md     │   │
│  │               Returns: Level 2 pattern (intent routing)                 │   │
│  │                                                                         │   │
│  │  4. Begin Step 0                                                        │   │
│  │     ──INVOKE──► [SKILL:phoenix-engine-stm-initialize]                   │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-engine-stm-initialize]                                   │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    recipe_id: "consult-cto",                                            │   │
│  │    signal: "Help me build something AI-powered",                        │   │
│  │    user_context: { working_dir, git_branch }                            │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 1: Create STM Workspace                                      │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] .phoenix-os/stm/consult-cto-{ts}/         │  │   │
│  │  │            Creates: state.md, context.md, intents.md, outputs/    │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 2: Semantic Radar Scanning                                   │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/ai-intelligence.md                │  │   │
│  │  │           Keywords: ["AI", "ML", "intelligent", "neural", ...]    │  │   │
│  │  │           Match: "AI-powered" → confidence: 0.9                   │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/innovation.md                     │  │   │
│  │  │           Keywords: ["build", "create", "new", "disrupt", ...]    │  │   │
│  │  │           Match: "build" → confidence: 0.7                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/leadership.md                     │  │   │
│  │  │           Keywords: ["team", "strategy", "vision", ...]           │  │   │
│  │  │           Match: none → confidence: 0.2                           │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/technology.md                     │  │   │
│  │  │           Keywords: ["system", "platform", "architecture", ...]   │  │   │
│  │  │           Match: peripheral → confidence: 0.4                     │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/purpose.md                        │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/evolutionary-architecture.md      │  │   │
│  │  │ ──READ──► [MEMORY:Vault] radars/product.md                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ Results:                                                          │  │   │
│  │  │   direct_matches: [AI/Intelligence (0.9), Innovation (0.7)]       │  │   │
│  │  │   peripheral: [Technology (0.4)]                                  │  │   │
│  │  │   below_threshold: [Leadership, Purpose, ...]                     │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 3: Load Mapped Signals                                       │  │   │
│  │  │                                                                   │  │   │
│  │  │ For radar: AI/Intelligence (0.9)                                  │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/ai/augmentation-principle.md   │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/ai/pcam-framework.md           │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/ai/build-vs-buy.md             │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/ai/human-in-loop.md            │  │   │
│  │  │                                                                   │  │   │
│  │  │ For radar: Innovation (0.7)                                       │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/innovation/flywheel.md         │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/innovation/mvp-thinking.md     │  │   │
│  │  │                                                                   │  │   │
│  │  │ Graph traversal: augmentation-principle → Leadership              │  │   │
│  │  │   ──READ──► [MEMORY:Vault] signals/leadership/strategic-radars.md │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 4: Populate STM                                              │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] context.md                                │  │   │
│  │  │            Content: radar_matches + signal_content                │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] state.md                                  │  │   │
│  │  │            Content: { step: 0, status: "initializing" }           │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] intents.md                                │  │   │
│  │  │            Content: { state: "pending_identification" }           │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-1704531600000/",              │   │
│  │    radar_matches: { direct: [...], peripheral: [...] },                 │   │
│  │    signals_loaded: [7 signals],                                         │   │
│  │    no_radar_match: false                                                │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Step 0 Complete                                         │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] state.md                                       │   │
│  │             Update: { step: 0, status: "complete" }                     │   │
│  │                                                                         │   │
│  │  Auto-verify: ✓ STM workspace exists                                    │   │
│  │               ✓ state.md, context.md, intents.md present                │   │
│  │               ✓ Signals loaded                                          │   │
│  │                                                                         │   │
│  │  Proceed to Step 1 (no user interaction)                                │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### STEP 1: Build Routing Plan

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 1: BUILD ROUTING PLAN                                                      │
│ Purpose: Identify intent, map to agents, construct execution plan               │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Dispatch to Orchestrator Agent                          │   │
│  │                                                                         │   │
│  │  ──INVOKE──► [AGENT:phoenix:orchestrator]                               │   │
│  │              Task: "Build routing plan for query"                       │   │
│  │              Inputs: {                                                  │   │
│  │                query: "Help me build something AI-powered",             │   │
│  │                stm_path: ".phoenix-os/stm/consult-cto-{ts}/",           │   │
│  │                enabled_intents: [clarify, decide, validate,             │   │
│  │                                  consult, advise],                      │   │
│  │                available_agents: [orchestrator, strategy-guardian,      │   │
│  │                                   advisor],                             │   │
│  │                intent_domain_path: "cto-intents.md"                     │   │
│  │              }                                                          │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:orchestrator]                                            │   │
│  │                                                                         │   │
│  │  1. Read agent definition                                               │   │
│  │     ──READ──► [MEMORY:Engine] agents/orchestrator.md                    │   │
│  │               Returns: skill_chain, tools, constraints                  │   │
│  │                                                                         │   │
│  │  2. Read STM context for radar matches                                  │   │
│  │     ──READ──► [MEMORY:STM] context.md                                   │   │
│  │               Returns: radar_matches, signals_loaded                    │   │
│  │                                                                         │   │
│  │  3. MANDATORY: Invoke skill chain                                       │   │
│  │     ──INVOKE──► [SKILL:phoenix-engine-identify-intents]                 │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-engine-identify-intents]                                 │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    query: "Help me build something AI-powered",                         │   │
│  │    intent_domain_path: "@memory/engine/intents/cto-intents.md",         │   │
│  │    enabled_intents: [clarify, decide, validate, consult, advise],       │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-{ts}/",                       │   │
│  │    mode: "initial"                                                      │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 1: Load Intent Patterns                                     │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │           Returns: {                                              │  │   │
│  │  │             clarify: {                                            │  │   │
│  │  │               patterns: ["vague", "something", "stuff", ...],     │  │   │
│  │  │               context_signals: [undefined_target, buzzwords],     │  │   │
│  │  │               routes_to: "strategy-guardian"                      │  │   │
│  │  │             },                                                    │  │   │
│  │  │             decide: { patterns: [...], ... },                     │  │   │
│  │  │             validate: { patterns: [...], ... },                   │  │   │
│  │  │             consult: { patterns: [...], ... },                    │  │   │
│  │  │             advise: { patterns: [...], ... }                      │  │   │
│  │  │           }                                                       │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 2: Pattern Matching                                         │  │   │
│  │  │                                                                   │  │   │
│  │  │ Query: "Help me build something AI-powered"                       │  │   │
│  │  │                                                                   │  │   │
│  │  │ Scan against clarify patterns:                                    │  │   │
│  │  │   - "something" matches vague_target → +0.35                      │  │   │
│  │  │   - "AI-powered" matches buzzword → +0.10                         │  │   │
│  │  │   Base score: 0.45                                                │  │   │
│  │  │                                                                   │  │   │
│  │  │ Scan against consult patterns:                                    │  │   │
│  │  │   - "build" matches implementation_help → +0.25                   │  │   │
│  │  │   - "help" matches guidance_request → +0.15                       │  │   │
│  │  │   Base score: 0.40                                                │  │   │
│  │  │                                                                   │  │   │
│  │  │ Scan against decide patterns: 0.15                                │  │   │
│  │  │ Scan against validate patterns: 0.10                              │  │   │
│  │  │ Scan against advise patterns: 0.20                                │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 3: Confidence Boosting                                      │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Get: radar_matches, signals                             │  │   │
│  │  │                                                                   │  │   │
│  │  │ Evaluate context signals for clarify:                             │  │   │
│  │  │   - undefined_target ("something"): +0.30                         │  │   │
│  │  │   - missing_specifics (no constraints): +0.20                     │  │   │
│  │  │   - buzzword_without_detail ("AI-powered"): +0.05                 │  │   │
│  │  │   Context boost: +0.35                                            │  │   │
│  │  │                                                                   │  │   │
│  │  │ Final scores:                                                     │  │   │
│  │  │   clarify: 0.45 + 0.35 = 0.80                                     │  │   │
│  │  │   consult: 0.40 + 0.05 = 0.45                                     │  │   │
│  │  │   decide:  0.15 + 0.00 = 0.15                                     │  │   │
│  │  │   validate: 0.10 + 0.00 = 0.10                                    │  │   │
│  │  │   advise:  0.20 + 0.00 = 0.20                                     │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 4: Intent Selection                                         │  │   │
│  │  │                                                                   │  │   │
│  │  │ Thresholds:                                                       │  │   │
│  │  │   >= 0.8: SELECT                                                  │  │   │
│  │  │   0.5-0.8: FLAG (may need confirmation)                           │  │   │
│  │  │   < 0.5: DISCARD                                                  │  │   │
│  │  │                                                                   │  │   │
│  │  │ Results:                                                          │  │   │
│  │  │   clarify (0.80): ✓ SELECTED                                      │  │   │
│  │  │   consult (0.45): ✗ DISCARDED                                     │  │   │
│  │  │   others: ✗ DISCARDED                                             │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 5: Check Dependencies                                       │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │           Check clarify.requires_first: []                        │  │   │
│  │  │           Check clarify.conflicts_with: []                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ No additional intents required                                    │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/identify-intents-{ts}.json             │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    selected_intents: [{                                                 │   │
│  │      intent: "clarify",                                                 │   │
│  │      confidence: 0.80,                                                  │   │
│  │      matched_patterns: ["vague_target", "buzzword"],                    │   │
│  │      context_boosts: ["undefined_target", "missing_specifics"],         │   │
│  │      rationale: "Query contains vague 'something' and buzzword..."      │   │
│  │    }],                                                                  │   │
│  │    intent_shifted: false,                                               │   │
│  │    discarded: [consult, decide, validate, advise]                       │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:orchestrator] (continued)                                │   │
│  │                                                                         │   │
│  │  4. MANDATORY: Invoke build-plan skill                                  │   │
│  │     ──INVOKE──► [SKILL:phoenix-engine-build-plan]                       │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-engine-build-plan]                                       │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    selected_intents: [{ intent: "clarify", confidence: 0.80, ... }],    │   │
│  │    query: "Help me build something AI-powered",                         │   │
│  │    intent_domain_path: "@memory/engine/intents/cto-intents.md",         │   │
│  │    available_agents: [orchestrator, strategy-guardian, advisor],        │   │
│  │    sequencing_rules_path: "@memory/engine/intents/sequencing-rules.md", │   │
│  │    synthesizer: "phoenix:strategy-guardian"                             │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 1: Agent Matching                                           │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │           Get clarify.routes_to: "strategy-guardian"              │  │   │
│  │  │           Get clarify.context_enrichment: {                       │  │   │
│  │  │             user_needs: "Clear understanding of what user wants", │  │   │
│  │  │             expects: "Resolved understanding of WHAT, WHO, WHY",  │  │   │
│  │  │             probe_for: ["Type of thing", "Users", "Problem"]      │  │   │
│  │  │           }                                                       │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] agents/strategy-guardian.md             │  │   │
│  │  │           Verify agent available: ✓                               │  │   │
│  │  │           Verify handles intent: ✓                                │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 2: Load Sequencing Rules                                    │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/sequencing-rules.md             │  │   │
│  │  │           Returns: {                                              │  │   │
│  │  │             clarify: { can_parallel: false, depends_on: [] },     │  │   │
│  │  │             decide: { can_parallel: false, depends_on: [] },      │  │   │
│  │  │             ...                                                   │  │   │
│  │  │           }                                                       │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Phase 3: Plan Construction                                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ Build dependency graph: clarify (no deps)                         │  │   │
│  │  │ Topological sort: [clarify]                                       │  │   │
│  │  │ Execution mode: sequential                                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Get: matched_radars, signals_loaded (for enrichment)    │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/routing-plan-{ts}.json                 │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    routing_plan: {                                                      │   │
│  │      id: "plan-2026-01-06-abc123",                                      │   │
│  │      steps: [{                                                          │   │
│  │        order: 1,                                                        │   │
│  │        mode: "sequential",                                              │   │
│  │        agents: [{                                                       │   │
│  │          agent: "phoenix:strategy-guardian",                            │   │
│  │          intent: "clarify",                                             │   │
│  │          goal: "Clarify requirements for AI-powered build",             │   │
│  │          inputs: { user_needs, expects, probe_for, stm_context }        │   │
│  │        }]                                                               │   │
│  │      }],                                                                │   │
│  │      synthesizer: { agent: "phoenix:strategy-guardian" }                │   │
│  │    }                                                                    │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:orchestrator] (returns)                                  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] intents.md                                     │   │
│  │             Update: { clarify: { status: "routed", confidence: 0.80 } } │   │
│  │                                                                         │   │
│  │  ──RETURN──► routing_plan                                               │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Step 1 Complete                                         │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] state.md                                       │   │
│  │             Update: { step: 1, status: "complete", routing_plan: {...} }│   │
│  │                                                                         │   │
│  │  Proceed to Step 2 (no user interaction)                                │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### STEP 2: Execute Routing Plan

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 2: EXECUTE ROUTING PLAN                                                    │
│ Purpose: Dispatch to assigned agent, invoke skill chain for intent              │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Execute Plan Step 1                                     │   │
│  │                                                                         │   │
│  │  Plan: { agent: "strategy-guardian", intent: "clarify" }                │   │
│  │                                                                         │   │
│  │  ──INVOKE──► [AGENT:phoenix:strategy-guardian]                          │   │
│  │              Task: "Handle clarify intent"                              │   │
│  │              Inputs: {                                                  │   │
│  │                query: "Help me build something AI-powered",             │   │
│  │                intent: "clarify",                                       │   │
│  │                goal: "Clarify requirements for AI-powered build",       │   │
│  │                stm_path: ".phoenix-os/stm/consult-cto-{ts}/",           │   │
│  │                context_enrichment: { user_needs, expects, probe_for }   │   │
│  │              }                                                          │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:strategy-guardian]                                       │   │
│  │                                                                         │   │
│  │  1. Read agent definition                                               │   │
│  │     ──READ──► [MEMORY:Engine] agents/strategy-guardian.md               │   │
│  │               Returns: {                                                │   │
│  │                 handles: [clarify, decide, validate, consult],          │   │
│  │                 skill_chains: {                                         │   │
│  │                   clarify: [analyze-request, generate-questions,        │   │
│  │                             evaluate-understanding]                     │   │
│  │                 },                                                      │   │
│  │                 memory_access: {                                        │   │
│  │                   engine: "READ",                                       │   │
│  │                   stm: "READ/WRITE",                                    │   │
│  │                   vault: "NEVER"                                        │   │
│  │                 }                                                       │   │
│  │               }                                                         │   │
│  │                                                                         │   │
│  │  2. Read STM context (pre-loaded signals)                               │   │
│  │     ──READ──► [MEMORY:STM] context.md                                   │   │
│  │               Returns: radar_matches, signal_content                    │   │
│  │               ⛔ NOT reading from Vault directly                        │   │
│  │                                                                         │   │
│  │  3. MANDATORY: Invoke skill chain for clarify                           │   │
│  │     ──INVOKE──► [SKILL:phoenix-perception-analyze-request]              │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-perception-analyze-request]                              │   │
│  │ PCAM Domain: Perception                                                 │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    query: "Help me build something AI-powered",                         │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-{ts}/",                       │   │
│  │    intent: "clarify"                                                    │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 1: Tokenize Query                                            │  │   │
│  │  │                                                                   │  │   │
│  │  │ Parse: "Help me build something AI-powered"                       │  │   │
│  │  │   verbs: ["help", "build"]                                        │  │   │
│  │  │   nouns: ["something"]                                            │  │   │
│  │  │   modifiers: ["AI-powered"]                                       │  │   │
│  │  │   structure: request + undefined_target + buzzword_modifier       │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 2: Detect Vagueness                                          │  │   │
│  │  │                                                                   │  │   │
│  │  │ Indicators:                                                       │  │   │
│  │  │   - "something" → undefined_target: +0.30                         │  │   │
│  │  │   - No specifics → missing_specifics: +0.20                       │  │   │
│  │  │   - No users mentioned → missing_actor: +0.15                     │  │   │
│  │  │                                                                   │  │   │
│  │  │ vagueness_score: 0.65 (classification: "Vague")                   │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 3: Detect Buzzwords                                          │  │   │
│  │  │                                                                   │  │   │
│  │  │ Found: "AI-powered"                                               │  │   │
│  │  │   Category: AI/Tech                                               │  │   │
│  │  │   Challenge: "What specific capability should AI provide?"        │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Map buzzword to signal:                                 │  │   │
│  │  │           "AI-powered" → augmentation-principle.md                │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 4: 5W1H Analysis                                             │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │           Get clarify.completion_criteria:                        │  │   │
│  │  │           Required: [WHAT, WHO, WHY, CONSTRAINTS]                 │  │   │
│  │  │                                                                   │  │   │
│  │  │ Analysis:                                                         │  │   │
│  │  │   WHAT: ❌ Missing ("something" undefined)                        │  │   │
│  │  │   WHO:  ❌ Missing (no users specified)                           │  │   │
│  │  │   WHY:  ❌ Missing (no problem statement)                         │  │   │
│  │  │   HOW:  ❌ Missing (no constraints)                               │  │   │
│  │  │                                                                   │  │   │
│  │  │ missing_dimensions: [WHAT, WHO, WHY, CONSTRAINTS]                 │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 5: Map to STM Signals                                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Get loaded signals                                      │  │   │
│  │  │                                                                   │  │   │
│  │  │ signal_mapping: {                                                 │  │   │
│  │  │   "AI-powered": {                                                 │  │   │
│  │  │     signal: "augmentation-principle.md",                          │  │   │
│  │  │     radar: "AI/Intelligence"                                      │  │   │
│  │  │   },                                                              │  │   │
│  │  │   "WHAT": {                                                       │  │   │
│  │  │     signal: "pcam-framework.md",                                  │  │   │
│  │  │     reason: "Helps frame AI capabilities"                         │  │   │
│  │  │   },                                                              │  │   │
│  │  │   "WHO": { signal: null },                                        │  │   │
│  │  │   "WHY": { signal: "augmentation-principle.md" }                  │  │   │
│  │  │ }                                                                 │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/analysis-{ts}.json                     │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    vagueness_score: 0.65,                                               │   │
│  │    vagueness_indicators: [...],                                         │   │
│  │    buzzwords_detected: [{ term: "AI-powered", ... }],                   │   │
│  │    missing_dimensions: [WHAT, WHO, WHY, CONSTRAINTS],                   │   │
│  │    complexity: "complex",                                               │   │
│  │    signal_mapping: {...},                                               │   │
│  │    recommended_action: "clarify"                                        │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:strategy-guardian] (continued)                           │   │
│  │                                                                         │   │
│  │  4. Continue skill chain                                                │   │
│  │     ──INVOKE──► [SKILL:phoenix-manifestation-generate-questions]        │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-manifestation-generate-questions]                        │   │
│  │ PCAM Domain: Manifestation                                              │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    analysis: { (output from analyze-request) },                         │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-{ts}/",                       │   │
│  │    intent: "clarify",                                                   │   │
│  │    intent_definition_path: "@memory/engine/intents/cto-intents.md",     │   │
│  │    max_questions: 4                                                     │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 1: Load Question Shapes from Intent Definition               │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │           Get clarify.question_shapes:                            │  │   │
│  │  │           - "What specifically...?" (narrow scope)                │  │   │
│  │  │           - "Who/what/when/where...?" (fill gaps)                 │  │   │
│  │  │           - "What does [term] mean to you?" (define terms)        │  │   │
│  │  │           - "What would success look like?" (clarify outcomes)    │  │   │
│  │  │                                                                   │  │   │
│  │  │           Get clarify.required_dimensions: [WHAT, WHO, WHY, CONST]│  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 2: Read STM Signals                                          │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Get all loaded signals:                                 │  │   │
│  │  │           - augmentation-principle.md (AI/Intelligence)           │  │   │
│  │  │           - pcam-framework.md (AI/Intelligence)                   │  │   │
│  │  │           - build-vs-buy.md (AI/Intelligence)                     │  │   │
│  │  │           - flywheel.md (Innovation)                              │  │   │
│  │  │           - mvp-thinking.md (Innovation)                          │  │   │
│  │  │           - strategic-radars.md (Leadership)                      │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 3: Generate Signal-Grounded Questions                        │  │   │
│  │  │                                                                   │  │   │
│  │  │ Block 1: augmentation-principle.md                                │  │   │
│  │  │   Core insight: "AI should augment human capabilities"            │  │   │
│  │  │   Dimensions: WHY                                                 │  │   │
│  │  │   Questions:                                                      │  │   │
│  │  │     - "What human capability should AI augment?"                  │  │   │
│  │  │     - "What does success look like?"                              │  │   │
│  │  │                                                                   │  │   │
│  │  │ Block 2: pcam-framework.md                                        │  │   │
│  │  │   Core insight: "Four dimensions: Perception, Cognition..."       │  │   │
│  │  │   Dimensions: WHAT                                                │  │   │
│  │  │   Questions:                                                      │  │   │
│  │  │     - "What type of thing are you building?"                      │  │   │
│  │  │                                                                   │  │   │
│  │  │ Block 3: Context (no signal)                                      │  │   │
│  │  │   Dimensions: WHO, CONSTRAINTS                                    │  │   │
│  │  │   Questions:                                                      │  │   │
│  │  │     - "Who are the users?"                                        │  │   │
│  │  │     - "What constraints exist?"                                   │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 4: Ensure Dimension Coverage                                 │  │   │
│  │  │                                                                   │  │   │
│  │  │ Coverage check:                                                   │  │   │
│  │  │   WHAT: ✓ (pcam-framework)                                        │  │   │
│  │  │   WHO: ✓ (context block)                                          │  │   │
│  │  │   WHY: ✓ (augmentation-principle)                                 │  │   │
│  │  │   CONSTRAINTS: ✓ (context block)                                  │  │   │
│  │  │                                                                   │  │   │
│  │  │ signal_coverage: 0.6 (3 of 5 questions signal-grounded)           │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/questions-{ts}.json                    │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    question_blocks: [                                                   │   │
│  │      {                                                                  │   │
│  │        id: "block-1",                                                   │   │
│  │        signal_path: "@{user-vault}/signals/ai/augmentation-...",        │   │
│  │        radar: "AI / Intelligence",                                      │   │
│  │        core_insight: "AI should augment human capabilities...",         │   │
│  │        questions: [{ id: "q1", question: "...", dimension: "WHY" }]     │   │
│  │      },                                                                 │   │
│  │      { id: "block-2", signal_path: "pcam-framework.md", ... },          │   │
│  │      { id: "block-context", signal_path: null, ... }                    │   │
│  │    ],                                                                   │   │
│  │    total_questions: 5,                                                  │   │
│  │    signal_coverage: 0.6,                                                │   │
│  │    dimensions_covered: { WHAT: true, WHO: true, WHY: true, ... },       │   │
│  │    status: "needs_clarification"                                        │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:strategy-guardian] (returns)                             │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] state.md                                       │   │
│  │             Update: { clarify_round: 1, questions_asked: [...] }        │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    status: "needs_clarification",                                       │   │
│  │    question_blocks: [...],                                              │   │
│  │    signals_used: [augmentation-principle, pcam-framework]               │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Step 2 Complete - Response Gate Reached                 │   │
│  │                                                                         │   │
│  │  Gate condition: status == "needs_clarification"                        │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] state.md                                       │   │
│  │             Update: { step: 2, status: "awaiting_user", gate: "clarify" │   │
│  │                                                                         │   │
│  │  ⚠️ RESPONSE GATE: Output to user                                       │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### RESPONSE GATE: Present Questions

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ RESPONSE GATE: needs_clarification                                              │
│ Purpose: Present signal-grounded questions to user                              │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Format Output                                           │   │
│  │                                                                         │   │
│  │  ──READ──► [MEMORY:STM] outputs/questions-{ts}.json                     │   │
│  │            Get question_blocks                                          │   │
│  │                                                                         │   │
│  │  Format per signal-first template:                                      │   │
│  │  ┌─────────────────────────────────────────────────────────────────┐    │   │
│  │  │ ## From: AI Augmentation Principle                              │    │   │
│  │  │ **Source**: @{user-vault}/signals/ai/augmentation-principle.md  │    │   │
│  │  │ **Radar**: AI / Intelligence                                    │    │   │
│  │  │                                                                 │    │   │
│  │  │ AI should augment human capabilities, not replace judgment.     │    │   │
│  │  │                                                                 │    │   │
│  │  │ **Questions**:                                                  │    │   │
│  │  │ 1. **What human capability should AI augment?**                 │    │   │
│  │  │    - Research speed                                             │    │   │
│  │  │    - Attention/review                                           │    │   │
│  │  │    - Pattern recognition                                        │    │   │
│  │  │                                                                 │    │   │
│  │  │ 2. **What does success look like?**                             │    │   │
│  │  │    - Reduce time from X to Y                                    │    │   │
│  │  │    - Enable non-experts to do Z                                 │    │   │
│  │  │                                                                 │    │   │
│  │  │ ---                                                             │    │   │
│  │  │                                                                 │    │   │
│  │  │ ## From: PCAM Framework                                         │    │   │
│  │  │ **Source**: @{user-vault}/signals/ai/pcam-framework.md          │    │   │
│  │  │ ...                                                             │    │   │
│  │  │                                                                 │    │   │
│  │  │ ---                                                             │    │   │
│  │  │                                                                 │    │   │
│  │  │ ## Context Needed                                               │    │   │
│  │  │ These questions help me understand your specific situation:     │    │   │
│  │  │ ...                                                             │    │   │
│  │  └─────────────────────────────────────────────────────────────────┘    │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ╔═════════════════════════════════════════════════════════════════════════╗   │
│  ║ USER OUTPUT                                                             ║   │
│  ║ (Questions presented to user)                                           ║   │
│  ╚═════════════════════════════════════════════════════════════════════════╝   │
│                              │                                                  │
│                              ▼                                                  │
│  ╔═════════════════════════════════════════════════════════════════════════╗   │
│  ║ USER INPUT                                                              ║   │
│  ║                                                                         ║   │
│  ║ "I want to build an AI assistant for our support team. It should help   ║   │
│  ║ agents find answers faster - reduce response time by 50%. We use        ║   │
│  ║ Zendesk. Timeline is 3 months."                                         ║   │
│  ╚═════════════════════════════════════════════════════════════════════════╝   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### STEP 3a: Update STM

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 3a: UPDATE STM                                                             │
│ Purpose: Capture user response, extract facts, update context                   │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Process User Response                                   │   │
│  │                                                                         │   │
│  │  ──INVOKE──► [SKILL:phoenix-engine-stm-update]                          │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-engine-stm-update]                                       │   │
│  │ PCAM Domain: Engine                                                     │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-{ts}/",                       │   │
│  │    user_response: "I want to build an AI assistant for...",             │   │
│  │    current_state: { step: 2, clarify_round: 1 },                        │   │
│  │    pending_questions: [Q1, Q2, Q3, Q4, Q5]                              │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 1: Classify Response Impact                                  │  │   │
│  │  │                                                                   │  │   │
│  │  │ Impact type: "clarification" (answers pending questions)          │  │   │
│  │  │ Secondary: "new_info" (provides facts not known before)           │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 2: Extract Facts                                             │  │   │
│  │  │                                                                   │  │   │
│  │  │ Parse response: "I want to build an AI assistant for our          │  │   │
│  │  │ support team. It should help agents find answers faster -         │  │   │
│  │  │ reduce response time by 50%. We use Zendesk. Timeline is 3 mo."   │  │   │
│  │  │                                                                   │  │   │
│  │  │ Extracted:                                                        │  │   │
│  │  │   [WHAT]: AI assistant for customer support                       │  │   │
│  │  │   [WHO]: Support team agents (internal)                           │  │   │
│  │  │   [WHY]: Reduce response time by 50%                              │  │   │
│  │  │   [CONSTRAINT]: Must integrate with Zendesk                       │  │   │
│  │  │   [CONSTRAINT]: 3 month timeline                                  │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 3: Update context.md                                         │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] context.md                                │  │   │
│  │  │            Append: {                                              │  │   │
│  │  │              ### User Response [2026-01-06T10:05:00Z]             │  │   │
│  │  │              **In response to**: Clarification questions          │  │   │
│  │  │              **Raw content**: "I want to build..."                │  │   │
│  │  │              **Impact**: clarification                            │  │   │
│  │  │              **Extracted facts**:                                 │  │   │
│  │  │                - [WHAT]: AI assistant for customer support        │  │   │
│  │  │                - [WHO]: Support team agents                       │  │   │
│  │  │                - [WHY]: Reduce response time by 50%               │  │   │
│  │  │                - [CONSTRAINT]: Zendesk integration                │  │   │
│  │  │                - [CONSTRAINT]: 3 month timeline                   │  │   │
│  │  │            }                                                      │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 4: Update intents.md                                         │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] intents.md                                │  │   │
│  │  │            Update: {                                              │  │   │
│  │  │              clarify: {                                           │  │   │
│  │  │                status: "in_progress",                             │  │   │
│  │  │                confidence: 0.80,                                  │  │   │
│  │  │                clarification_round: 1,                            │  │   │
│  │  │                facts_gathered: [WHAT, WHO, WHY, CONSTRAINTS]      │  │   │
│  │  │              }                                                    │  │   │
│  │  │            }                                                      │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 5: Update state.md                                           │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──WRITE──► [MEMORY:STM] state.md                                  │  │   │
│  │  │            Append to interaction log: {                           │  │   │
│  │  │              | Timestamp | Type | Impact | Summary |              │  │   │
│  │  │              | {ts} | user_response | clarification | Provided    │  │   │
│  │  │                WHAT, WHO, WHY, CONSTRAINTS |                      │  │   │
│  │  │            }                                                      │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    updated: true,                                                       │   │
│  │    impact_type: "clarification",                                        │   │
│  │    extracted_facts: [                                                   │   │
│  │      { domain: "WHAT", value: "AI assistant for support" },             │   │
│  │      { domain: "WHO", value: "Support team agents" },                   │   │
│  │      { domain: "WHY", value: "Reduce response time by 50%" },           │   │
│  │      { constraint: "Must integrate with Zendesk" },                     │   │
│  │      { constraint: "3 month timeline" }                                 │   │
│  │    ],                                                                   │   │
│  │    changes: ["WHAT resolved", "WHO resolved", "WHY resolved", ...],     │   │
│  │    needs_resolution: false,                                             │   │
│  │    scope_changed: false                                                 │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### STEP 3b: Evaluate Understanding

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 3b: EVALUATE UNDERSTANDING                                                 │
│ Purpose: Assess completeness, determine if clarification is sufficient          │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:strategy-guardian] (continued)                           │   │
│  │                                                                         │   │
│  │  Continue skill chain after user response                               │   │
│  │     ──INVOKE──► [SKILL:phoenix-cognition-evaluate-understanding]        │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-cognition-evaluate-understanding]                        │   │
│  │ PCAM Domain: Cognition                                                  │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    original_query: "Help me build something AI-powered",                │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-{ts}/",                       │   │
│  │    questions_asked: [Q1, Q2, Q3, Q4, Q5],                               │   │
│  │    user_responses: (from STM context.md),                               │   │
│  │    intent: "clarify",                                                   │   │
│  │    intent_definition_path: "@memory/engine/intents/cto-intents.md",     │   │
│  │    clarification_round: 1                                               │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 1: Load Completion Criteria                                  │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │           Get clarify.completion_criteria:                        │  │   │
│  │  │           - All 4 dimensions documented                           │  │   │
│  │  │           - At least 1 key decision identified                    │  │   │
│  │  │           - Vagueness resolved                                    │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 2: Load All Context from STM                                 │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Get: original query, radar matches, user responses      │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] state.md                                   │  │   │
│  │  │           Get: execution state, interaction log                   │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] intents.md                                 │  │   │
│  │  │           Get: current intent state                               │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 3: Evaluate Response Completeness                            │  │   │
│  │  │                                                                   │  │   │
│  │  │ Q1 (capability): "find answers faster" → Complete (1.0)           │  │   │
│  │  │ Q2 (success): "reduce by 50%" → Complete (1.0)                    │  │   │
│  │  │ Q3 (type): "AI assistant" → Complete (1.0)                        │  │   │
│  │  │ Q4 (users): "support team" → Complete (1.0)                       │  │   │
│  │  │ Q5 (constraints): "Zendesk, 3 months" → Complete (1.0)            │  │   │
│  │  │                                                                   │  │   │
│  │  │ completeness_score = 5.0 / 5 = 1.0                                │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 4: Check Required Dimensions                                 │  │   │
│  │  │                                                                   │  │   │
│  │  │ WHAT: ✅ Complete ("AI assistant for customer support")           │  │   │
│  │  │ WHO: ✅ Complete ("Support team agents")                          │  │   │
│  │  │ WHY: ✅ Complete ("Reduce response time by 50%")                  │  │   │
│  │  │ CONSTRAINTS: ✅ Complete ("Zendesk, 3 months")                    │  │   │
│  │  │                                                                   │  │   │
│  │  │ All required dimensions present: ✓                                │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 5: Extract Resolved Understanding                            │  │   │
│  │  │                                                                   │  │   │
│  │  │ resolved_understanding: {                                         │  │   │
│  │  │   summary: "User wants to build an AI-powered customer support    │  │   │
│  │  │            assistant that helps agents resolve tickets faster",   │  │   │
│  │  │   dimensions: {                                                   │  │   │
│  │  │     WHAT: "AI assistant for customer support",                    │  │   │
│  │  │     WHO: "Internal support agents (not customers)",               │  │   │
│  │  │     WHY: "Reduce ticket resolution time by 50%",                  │  │   │
│  │  │     CONSTRAINTS: "Zendesk integration, 3-month timeline"          │  │   │
│  │  │   }                                                               │  │   │
│  │  │ }                                                                 │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 6: Determine Completion Status                               │  │   │
│  │  │                                                                   │  │   │
│  │  │ completeness_score >= 0.8? ✓ (1.0)                                │  │   │
│  │  │ All required dimensions present? ✓                                │  │   │
│  │  │                                                                   │  │   │
│  │  │ → status: "clarified"                                             │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Step 7: Document Signals Used                                     │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:STM] context.md                                 │  │   │
│  │  │           Get signals that informed evaluation                    │  │   │
│  │  │                                                                   │  │   │
│  │  │ signals_used: [                                                   │  │   │
│  │  │   {                                                               │  │   │
│  │  │     path: "@{user-vault}/signals/ai/augmentation-principle.md",   │  │   │
│  │  │     usage: "Framed AI as augmenting agent capabilities"           │  │   │
│  │  │   }                                                               │  │   │
│  │  │ ]                                                                 │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/evaluation-{ts}.json                   │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    status: "clarified",                                                 │   │
│  │    completeness_score: 0.95,                                            │   │
│  │    resolved_understanding: { summary: "...", dimensions: {...} },       │   │
│  │    dimensions_status: { WHAT: "complete", WHO: "complete", ... },       │   │
│  │    assumptions: [],                                                     │   │
│  │    unknowns: ["Budget constraints", "Team size"],                       │   │
│  │    signals_used: [...],                                                 │   │
│  │    follow_up_questions: []                                              │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### STEP 3c: Re-Evaluate Intent (Orchestrator Inline)

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 3c: RE-EVALUATE INTENT                                                     │
│ Purpose: Determine if intent should shift now that clarification is complete    │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Orchestrator Determines Next Action                     │   │
│  │                                                                         │   │
│  │  Evaluation status: "clarified"                                         │   │
│  │                                                                         │   │
│  │  Decision: Re-invoke identify-intents with resolved understanding       │   │
│  │                                                                         │   │
│  │  ──INVOKE──► [SKILL:phoenix-engine-identify-intents]                    │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [SKILL:phoenix-engine-identify-intents] (mode: re-evaluate)             │   │
│  │                                                                         │   │
│  │  Inputs: {                                                              │   │
│  │    query: "User wants to build an AI-powered customer support           │   │
│  │            assistant that helps agents resolve tickets faster",         │   │
│  │    intent_domain_path: "@memory/engine/intents/cto-intents.md",         │   │
│  │    enabled_intents: [clarify, decide, validate, consult, advise],       │   │
│  │    stm_path: ".phoenix-os/stm/consult-cto-{ts}/",                       │   │
│  │    mode: "re-evaluate",                                                 │   │
│  │    prior_intents: [{ intent: "clarify", status: "complete" }]           │   │
│  │  }                                                                      │   │
│  │                                                                         │   │
│  │  ┌───────────────────────────────────────────────────────────────────┐  │   │
│  │  │ Re-evaluation with resolved understanding                         │  │   │
│  │  │                                                                   │  │   │
│  │  │ ──READ──► [MEMORY:Engine] intents/cto-intents.md                  │  │   │
│  │  │                                                                   │  │   │
│  │  │ New query: Clear, specific, actionable                            │  │   │
│  │  │                                                                   │  │   │
│  │  │ Pattern match:                                                    │  │   │
│  │  │   clarify: 0.20 (no longer vague)                                 │  │   │
│  │  │   consult: 0.75 ("build", "help", clear goal)                     │  │   │
│  │  │   decide: 0.30                                                    │  │   │
│  │  │   validate: 0.25                                                  │  │   │
│  │  │                                                                   │  │   │
│  │  │ Context boost for consult:                                        │  │   │
│  │  │   - Clear requirements (+0.10)                                    │  │   │
│  │  │   Final: 0.85                                                     │  │   │
│  │  │                                                                   │  │   │
│  │  │ Selection: consult (0.85) ✓                                       │  │   │
│  │  └───────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/re-identify-{ts}.json                  │   │
│  │                                                                         │   │
│  │  ──RETURN──► {                                                          │   │
│  │    selected_intents: [{                                                 │   │
│  │      intent: "consult",                                                 │   │
│  │      confidence: 0.85,                                                  │   │
│  │      rationale: "Requirements clear, user needs implementation help"   │   │
│  │    }],                                                                  │   │
│  │    intent_shifted: true,                                                │   │
│  │    shift_reason: "Clarification complete, user needs guidance"          │   │
│  │  }                                                                      │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Intent Shifted - Update Plan                            │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] intents.md                                     │   │
│  │             Update: {                                                   │   │
│  │               clarify: { status: "complete" },                          │   │
│  │               consult: { status: "routed", confidence: 0.85 }           │   │
│  │             }                                                           │   │
│  │                                                                         │   │
│  │  ──INVOKE──► [SKILL:phoenix-engine-build-plan] (update plan)            │   │
│  │             Add consult step to routing_plan                            │   │
│  │                                                                         │   │
│  │  Continue execution with new intent...                                  │   │
│  │  (Agent handles consult intent → produces recommendations)              │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### STEP 4: Synthesis

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│ STEP 4: SYNTHESIS                                                               │
│ Purpose: Synthesize all agent outputs into final response                       │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] All Intents Complete - Invoke Synthesizer               │   │
│  │                                                                         │   │
│  │  ──READ──► [MEMORY:STM] state.md                                        │   │
│  │            Verify: all plan steps complete                              │   │
│  │                                                                         │   │
│  │  ──READ──► [MEMORY:STM] outputs/                                        │   │
│  │            Gather: all skill outputs                                    │   │
│  │                                                                         │   │
│  │  ──INVOKE──► [AGENT:phoenix:strategy-guardian] (as synthesizer)         │   │
│  │              Task: "Synthesize Strategic Brief"                         │   │
│  │              Inputs: {                                                  │   │
│  │                routing_plan: {...},                                     │   │
│  │                all_outputs: {                                           │   │
│  │                  clarify: { resolved_understanding, signals_used },     │   │
│  │                  consult: { recommendations, next_steps }               │   │
│  │                }                                                        │   │
│  │              }                                                          │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [AGENT:phoenix:strategy-guardian] (synthesizer)                         │   │
│  │                                                                         │   │
│  │  1. Read all outputs                                                    │   │
│  │     ──READ──► [MEMORY:STM] outputs/evaluation-{ts}.json                 │   │
│  │     ──READ──► [MEMORY:STM] outputs/consult-{ts}.json (if exists)        │   │
│  │                                                                         │   │
│  │  2. Read signals used                                                   │   │
│  │     ──READ──► [MEMORY:STM] context.md                                   │   │
│  │               Get: signals that informed the response                   │   │
│  │                                                                         │   │
│  │  3. Construct Strategic Brief                                           │   │
│  │     ┌─────────────────────────────────────────────────────────────┐     │   │
│  │     │ ## Strategic Brief                                          │     │   │
│  │     │                                                             │     │   │
│  │     │ ### What You're Building                                    │     │   │
│  │     │ An AI-powered support assistant for your team to find       │     │   │
│  │     │ answers faster and reduce response time by 50%.             │     │   │
│  │     │                                                             │     │   │
│  │     │ ### Key Insights                                            │     │   │
│  │     │ - **Signal**: AI should augment agent capabilities, not     │     │   │
│  │     │   replace judgment                                          │     │   │
│  │     │   *Source: @{user-vault}/signals/ai/augmentation-...*       │     │   │
│  │     │ - Focus on reducing friction, not replacing agents          │     │   │
│  │     │ - Zendesk integration is critical path                      │     │   │
│  │     │                                                             │     │   │
│  │     │ ### Recommendations                                         │     │   │
│  │     │ 1. Start with retrieval-augmented generation (RAG)          │     │   │
│  │     │ 2. Build on Zendesk's existing knowledge base               │     │   │
│  │     │ 3. Measure baseline response time first                     │     │   │
│  │     │                                                             │     │   │
│  │     │ ### Next Steps                                              │     │   │
│  │     │ - [ ] Audit existing knowledge base quality                 │     │   │
│  │     │ - [ ] Define success metrics (baseline → target)            │     │   │
│  │     │ - [ ] Prototype with 3-5 common ticket types                │     │   │
│  │     │                                                             │     │   │
│  │     │ ### Signals Used                                            │     │   │
│  │     │ - @{user-vault}/signals/ai/augmentation-principle.md        │     │   │
│  │     │ - @{user-vault}/signals/ai/pcam-framework.md                │     │   │
│  │     └─────────────────────────────────────────────────────────────┘     │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] outputs/synthesis-{ts}.json                    │   │
│  │                                                                         │   │
│  │  ──RETURN──► { strategic_brief: "..." }                                 │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │ [ORCHESTRATION] Step 4 Complete - Synthesis Gate Reached                │   │
│  │                                                                         │   │
│  │  Gate condition: status == "synthesis"                                  │   │
│  │                                                                         │   │
│  │  ──WRITE──► [MEMORY:STM] state.md                                       │   │
│  │             Update: { step: 4, status: "complete" }                     │   │
│  │                                                                         │   │
│  │  ⚠️ RESPONSE GATE: Output to user                                       │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
│                              │                                                  │
│                              ▼                                                  │
│  ╔═════════════════════════════════════════════════════════════════════════╗   │
│  ║ FINAL OUTPUT                                                            ║   │
│  ║                                                                         ║   │
│  ║ (Strategic Brief presented to user)                                     ║   │
│  ╚═════════════════════════════════════════════════════════════════════════╝   │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

### Complete Execution Summary

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        EXECUTION SUMMARY                                         │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  RECIPE INVOCATIONS: 1                                                          │
│    └─ consult-cto                                                               │
│                                                                                 │
│  AGENT INVOCATIONS: 3                                                           │
│    ├─ phoenix:orchestrator (Step 1)                                             │
│    ├─ phoenix:strategy-guardian (Step 2 - clarify)                              │
│    └─ phoenix:strategy-guardian (Step 4 - synthesizer)                          │
│                                                                                 │
│  SKILL INVOCATIONS: 8                                                           │
│    ├─ phoenix-engine-stm-initialize (Step 0)                                    │
│    ├─ phoenix-engine-identify-intents (Step 1)                                  │
│    ├─ phoenix-engine-build-plan (Step 1)                                        │
│    ├─ phoenix-perception-analyze-request (Step 2)                               │
│    ├─ phoenix-manifestation-generate-questions (Step 2)                         │
│    ├─ phoenix-engine-stm-update (Step 3a)                                       │
│    ├─ phoenix-cognition-evaluate-understanding (Step 3b)                        │
│    └─ phoenix-engine-identify-intents [re-evaluate] (Step 3c)                   │
│                                                                                 │
│  MEMORY ACCESS:                                                                 │
│                                                                                 │
│  [MEMORY:Engine] READS: 12                                                      │
│    ├─ recipes/consult-cto.md (1)                                                │
│    ├─ flows/recipe-orchestration-pattern.md (1)                                 │
│    ├─ agents/orchestrator.md (1)                                                │
│    ├─ agents/strategy-guardian.md (2)                                           │
│    ├─ intents/cto-intents.md (5)                                                │
│    └─ intents/sequencing-rules.md (2)                                           │
│                                                                                 │
│  [MEMORY:Vault] READS: 13                                                       │
│    ├─ radars/*.md (7 - one per radar)                                           │
│    └─ signals/**/*.md (6 - loaded signals)                                      │
│    ⚠️ All Vault reads in Step 0 ONLY (stm-initialize)                           │
│                                                                                 │
│  [MEMORY:STM] READS: 14                                                         │
│    ├─ context.md (8)                                                            │
│    ├─ state.md (3)                                                              │
│    ├─ intents.md (2)                                                            │
│    └─ outputs/*.json (1)                                                        │
│                                                                                 │
│  [MEMORY:STM] WRITES: 15                                                        │
│    ├─ state.md (5)                                                              │
│    ├─ context.md (2)                                                            │
│    ├─ intents.md (3)                                                            │
│    └─ outputs/*.json (5)                                                        │
│                                                                                 │
│  INTENT LIFECYCLE:                                                              │
│    clarify: detected (0.80) → routed → handled → complete                       │
│    consult: detected (0.85) → routed → handled → complete (after re-eval)       │
│                                                                                 │
│  RESPONSE GATES: 2                                                              │
│    ├─ needs_clarification (after Step 2)                                        │
│    └─ synthesis (after Step 4)                                                  │
│                                                                                 │
│  USER INTERACTIONS: 1                                                           │
│    └─ Clarification response                                                    │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## High-Level Flow

```
User Query
    │
    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         consult-cto (Recipe)                             │
│  Inherits: @memory/engine/flows/recipe-orchestration-pattern.md         │
└─────────────────────────────────────────────────────────────────────────┘
    │
    ├─[Step 0]──► phoenix-engine-stm-initialize
    │                    │
    │                    ▼
    │             STM Workspace Created
    │             (.phoenix-os/stm/{recipe-id}-{timestamp}/)
    │
    ├─[Step 1]──► phoenix:orchestrator (Agent)
    │                    │
    │                    ├──► Skill: phoenix-engine-identify-intents
    │                    │           ▼
    │                    │    selected_intents[]
    │                    │
    │                    └──► Skill: phoenix-engine-build-plan
    │                                ▼
    │                         routing_plan
    │
    ├─[Step 2]──► Execute routing_plan
    │                    │
    │                    └──► phoenix:strategy-guardian (Agent)
    │                                │
    │                                ├──► Skill: phoenix-perception-analyze-request
    │                                │           ▼
    │                                │    analysis (vagueness, buzzwords, dimensions)
    │                                │
    │                                └──► Skill: phoenix-manifestation-generate-questions
    │                                            ▼
    │                                     question_blocks[]
    │
    ├─[Gate]────► Response Gate: needs_clarification
    │                    │
    │                    ▼
    │             Present questions to user
    │
    ├─[User]────► User responds
    │
    ├─[Step 3a]─► phoenix-engine-stm-update
    │                    │
    │                    ▼
    │             STM updated with response
    │
    ├─[Eval]────► phoenix-cognition-evaluate-understanding
    │                    │
    │                    ▼
    │             completeness assessment
    │                    │
    │     ┌──────────────┼──────────────┐
    │     │              │              │
    │     ▼              ▼              ▼
    │  clarified    needs_reclarify  scope_changed
    │     │              │              │
    │     │              └──► [Loop back to Step 1 or generate follow-up]
    │     │                              │
    │     │              ┌───────────────┘
    │     ▼              ▼
    ├─[Step 4]──► Synthesis (via synthesizer agent)
    │                    │
    │                    ▼
    │             Final Response
    │
    └─────────► User receives Strategic Brief
```

---

## Detailed Data Flow by Component

### 1. Recipe Entry Point

**Source**: User invokes `/consult-cto <query>`

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUT                                                           │
├─────────────────────────────────────────────────────────────────┤
│ $ARGUMENTS: "Help me build something AI-powered"                │
│ Recipe: consult-cto                                             │
│ Intent Domain: @memory/engine/intents/cto-intents.md            │
│ Enabled Intents: [clarify, decide, validate, consult, advise]   │
│ Available Agents: [phoenix:orchestrator, phoenix:strategy-      │
│                    guardian, phoenix:advisor]                   │
│ Synthesizer: phoenix:strategy-guardian                          │
└─────────────────────────────────────────────────────────────────┘
```

---

### 2. Step 0: Initialize STM

**Skill**: `phoenix-engine-stm-initialize`

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ recipe_id: "consult-cto"                                        │
│ signal: "Help me build something AI-powered"                    │
│ user_context: { working_dir, git_branch, prior_session }        │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ 1. Create workspace: .phoenix-os/stm/consult-cto-{timestamp}/   │
│ 2. Semantic radar scanning:                                     │
│    - Load all radars from {user-vault}/radars/                  │
│    - Score relevance (0.0-1.0) for each radar                   │
│    - Direct matches (>= 0.6): Load signals                      │
│    - Graph traversal: Follow signal→radar relationships         │
│ 3. Populate context.md with matched signals                     │
│ 4. Initialize state.md, intents.md                              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ stm_path: ".phoenix-os/stm/consult-cto-1704531600000/"          │
│ radar_matches: {                                                │
│   direct: [                                                     │
│     { radar: "AI/Intelligence", confidence: 0.9 },              │
│     { radar: "Innovation", confidence: 0.7 }                    │
│   ],                                                            │
│   graph_traversal: [                                            │
│     { radar: "Leadership", confidence: 0.3, via: "augmentation" │
│   ],                                                            │
│   peripheral: [                                                 │
│     { radar: "Purpose", confidence: 0.5 }                       │
│   ]                                                             │
│ }                                                               │
│ signals_loaded: [                                               │
│   "@{user-vault}/signals/ai/augmentation-principle.md",         │
│   "@{user-vault}/signals/ai/pcam-framework.md",                 │
│   "@{user-vault}/signals/ai/build-vs-buy.md",                   │
│   "@{user-vault}/signals/leadership/flywheel.md",               │
│   ...                                                           │
│ ]                                                               │
│ no_radar_match: false                                           │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STM WORKSPACE CREATED                                           │
├─────────────────────────────────────────────────────────────────┤
│ .phoenix-os/stm/consult-cto-{timestamp}/                        │
│ ├── state.md      # Execution state, interaction log            │
│ ├── context.md    # Radar matches + loaded signal content       │
│ ├── intents.md    # Intent state (pending_identification)       │
│ ├── outputs/      # Outputs from each step                      │
│ └── evidence/     # Supporting artifacts                        │
└─────────────────────────────────────────────────────────────────┘
```

---

### 3. Step 1: Build Routing Plan

**Agent**: `phoenix:orchestrator`

#### 3a. Skill: phoenix-engine-identify-intents

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ query: "Help me build something AI-powered"                     │
│ intent_domain_path: "@memory/engine/intents/cto-intents.md"     │
│ enabled_intents: ["clarify", "decide", "validate", "consult",   │
│                   "advise"]                                     │
│ stm_path: ".phoenix-os/stm/consult-cto-{timestamp}/"            │
│ mode: "initial"                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ Phase 1: Pattern Matching                                       │
│   - Load patterns from cto-intents.md for each enabled intent   │
│   - Scan query: "Help me build something AI-powered"            │
│   - Score: clarify (0.65), consult (0.45)                       │
│                                                                 │
│ Phase 2: Confidence Boosting                                    │
│   - Evaluate context signals against query                      │
│   - clarify: +0.15 (vague target "something", buzzword "AI")    │
│   - Final: clarify (0.80), consult (0.45)                       │
│                                                                 │
│ Phase 3: Intent Selection                                       │
│   - Apply thresholds: >0.8 select, 0.5-0.8 flag, <0.5 discard   │
│   - Selected: clarify (0.80)                                    │
│   - Discarded: consult (below threshold)                        │
│                                                                 │
│ Phase 4: Check Dependencies                                     │
│   - clarify.requires_first: [] (none)                           │
│   - No additional intents needed                                │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ selected_intents: [                                             │
│   {                                                             │
│     intent: "clarify",                                          │
│     confidence: 0.80,                                           │
│     needs_confirmation: false,                                  │
│     added_by: "detection",                                      │
│     matched_patterns: ["Vague queries", "buzzwords"],           │
│     signals_matched: ["Undefined target", "AI/Tech buzzword"],  │
│     rationale: "Query contains vague 'something' and buzzword   │
│                 'AI-powered' without specifics"                 │
│   }                                                             │
│ ]                                                               │
│ intent_shifted: false                                           │
│ debug: { pattern_matches, confidence_boosts, discarded_intents }│
└─────────────────────────────────────────────────────────────────┘
```

#### 3b. Skill: phoenix-engine-build-plan

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ selected_intents: [{ intent: "clarify", confidence: 0.80, ... }]│
│ query: "Help me build something AI-powered"                     │
│ intent_domain_path: "@memory/engine/intents/cto-intents.md"     │
│ available_agents: ["phoenix:orchestrator",                      │
│                    "phoenix:strategy-guardian",                 │
│                    "phoenix:advisor"]                           │
│ sequencing_rules_path: "@memory/engine/intents/sequencing-      │
│                        rules.md"                                │
│ synthesizer: "phoenix:strategy-guardian"                        │
│ replan_count: 0                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ Phase 1: Agent Matching                                         │
│   - Load routes_to from intent: clarify → strategy-guardian     │
│   - Verify agent available: ✓                                   │
│   - Extract context_enrichment:                                 │
│       user_needs: "Clear understanding of what user wants"      │
│       expects: "Resolved understanding of WHAT, WHO, WHY"       │
│       probe_for: ["Type of thing", "Users", "Problem"]          │
│                                                                 │
│ Phase 2: Plan Construction                                      │
│   - Build dependency graph: clarify (no dependencies)           │
│   - Topological sort: [clarify]                                 │
│   - Execution mode: sequential                                  │
│   - Set depends_on: []                                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ routing_plan: {                                                 │
│   id: "plan-2026-01-06-abc123",                                 │
│   created_at: "2026-01-06T10:00:00Z",                           │
│   query: "Help me build something AI-powered",                  │
│   confidence: 0.80,                                             │
│   steps: [                                                      │
│     {                                                           │
│       order: 1,                                                 │
│       mode: "sequential",                                       │
│       depends_on: [],                                           │
│       agents: [                                                 │
│         {                                                       │
│           agent: "phoenix:strategy-guardian",                   │
│           intent: "clarify",                                    │
│           goal: "Clarify requirements for AI-powered build",    │
│           inputs: {                                             │
│             user_needs: "Clear understanding of what...",       │
│             expects: "Resolved understanding of WHAT...",       │
│             probe_for: ["Type of thing", "Users", "Problem"],   │
│             stm_context: {                                      │
│               matched_radars: ["AI/Intelligence", "Innovation"],│
│               signals_loaded: [...]                             │
│             }                                                   │
│           }                                                     │
│         }                                                       │
│       ]                                                         │
│     }                                                           │
│   ],                                                            │
│   synthesizer: {                                                │
│     agent: "phoenix:strategy-guardian",                         │
│     inputs: {}                                                  │
│   },                                                            │
│   metadata: {                                                   │
│     intent_domain: "@memory/engine/intents/cto-intents.md",     │
│     sequencing_rules: "@memory/engine/intents/sequencing-...",  │
│     replan_count: 0                                             │
│   }                                                             │
│ }                                                               │
│ agent_assignments: [...]                                        │
│ blocked_intents: []                                             │
└─────────────────────────────────────────────────────────────────┘
```

---

### 4. Step 2: Execute Routing Plan

**Agent**: `phoenix:strategy-guardian` (for clarify intent)

#### 4a. Skill: phoenix-perception-analyze-request

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ query: "Help me build something AI-powered"                     │
│ stm_path: ".phoenix-os/stm/consult-cto-{timestamp}/"            │
│ intent: "clarify"                                               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ Step 1: Tokenize Query                                          │
│   verbs: ["help", "build"]                                      │
│   nouns: ["something"]                                          │
│   modifiers: ["AI-powered"]                                     │
│   structure: request + undefined_target + buzzword_modifier     │
│                                                                 │
│ Step 2: Detect Vagueness                                        │
│   - undefined_target: "something" → +0.3                        │
│   - missing_specifics: no constraints → +0.2                    │
│   - missing_actor: no users defined → +0.15                     │
│   Total: 0.65 → Classification: "Vague"                         │
│                                                                 │
│ Step 3: Detect Buzzwords                                        │
│   - "AI-powered" → Category: AI/Tech                            │
│     Challenge: "What specific capability should AI provide?"    │
│                                                                 │
│ Step 4: 5W1H Analysis                                           │
│   - WHAT: ❌ Missing ("something" is undefined)                 │
│   - WHO: ❌ Missing (no users specified)                        │
│   - WHY: ❌ Missing (no problem statement)                      │
│   - HOW: ❌ Missing (no constraints)                            │
│   - WHEN: ⚠️ Not mentioned                                      │
│   - WHERE: ⚠️ Not mentioned                                     │
│                                                                 │
│ Step 5: Classify Complexity                                     │
│   - 4 missing dimensions, 1 buzzword, vagueness 0.65            │
│   → Classification: "complex"                                   │
│                                                                 │
│ Step 6: Map to STM Signals                                      │
│   - "AI-powered" → @{user-vault}/signals/ai/augmentation-...    │
│   - WHAT → @{user-vault}/signals/ai/pcam-framework.md           │
│   - WHO → No signal found                                       │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ query_parsed: {                                                 │
│   raw: "Help me build something AI-powered",                    │
│   verbs: ["help", "build"],                                     │
│   nouns: ["something"],                                         │
│   modifiers: ["AI-powered"],                                    │
│   structure: "request + undefined_target + buzzword_modifier"   │
│ }                                                               │
│ vagueness_score: 0.65                                           │
│ vagueness_indicators: [                                         │
│   { indicator: "undefined_target", weight: 0.3, trigger: ... }, │
│   { indicator: "missing_specifics", weight: 0.2, ... },         │
│   { indicator: "missing_actor", weight: 0.15, ... }             │
│ ]                                                               │
│ buzzwords_detected: [                                           │
│   {                                                             │
│     term: "AI-powered",                                         │
│     category: "AI/Tech",                                        │
│     challenge_question: "What specific capability should AI...",│
│     related_signal: "@{user-vault}/signals/ai/augmentation-..." │
│   }                                                             │
│ ]                                                               │
│ missing_dimensions: [                                           │
│   { dimension: "WHAT", reason: "Target is 'something'" },       │
│   { dimension: "WHO", reason: "No users specified" },           │
│   { dimension: "WHY", reason: "No problem statement" },         │
│   { dimension: "HOW", reason: "No constraints mentioned" }      │
│ ]                                                               │
│ complexity: "complex"                                           │
│ signal_mapping: {                                               │
│   "AI-powered": { signal: "...augmentation-principle.md", ... },│
│   "WHAT": { signal: "...pcam-framework.md", ... },              │
│   "WHO": { signal: null, ... }                                  │
│ }                                                               │
│ recommended_action: "clarify"                                   │
└─────────────────────────────────────────────────────────────────┘
```

#### 4b. Skill: phoenix-manifestation-generate-questions

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ analysis: { (output from analyze-request) }                     │
│ stm_path: ".phoenix-os/stm/consult-cto-{timestamp}/"            │
│ intent: "clarify"                                               │
│ intent_definition_path: "@memory/engine/intents/cto-intents.md" │
│ max_questions: 4                                                │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ Step 1: Read Question Shapes from Intent Definition             │
│   - "What specifically...?" — narrow scope                      │
│   - "Who/what/when/where...?" — fill gaps                       │
│   - "What does [term] mean to you?" — define terms              │
│   - "What would success look like?" — clarify outcomes          │
│                                                                 │
│ Step 2: Read Required Dimensions                                │
│   - Required: [WHAT, WHO, WHY, CONSTRAINTS]                     │
│                                                                 │
│ Step 3: Read STM Signals                                        │
│   - Load signals from context.md                                │
│   - Index by keywords/radar source                              │
│                                                                 │
│ Step 4: Group by Signal Lens                                    │
│   - augmentation-principle.md → WHY questions                   │
│   - pcam-framework.md → WHAT questions                          │
│   - Context block → WHO, CONSTRAINTS (no signal)                │
│                                                                 │
│ Step 5: Ensure Coverage                                         │
│   - WHAT: ✓ covered by pcam signal                              │
│   - WHO: ✓ covered by context block                             │
│   - WHY: ✓ covered by augmentation signal                       │
│   - CONSTRAINTS: ✓ covered by context block                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ question_blocks: [                                              │
│   {                                                             │
│     id: "block-1",                                              │
│     signal_path: "@{user-vault}/signals/ai/augmentation-...",   │
│     radar: "AI / Intelligence",                                 │
│     core_insight: "AI should augment human capabilities...",    │
│     questions: [                                                │
│       {                                                         │
│         id: "q1",                                               │
│         question: "What human capability should AI augment?",   │
│         dimension: "WHY",                                       │
│         examples: ["Research speed", "Attention/review", ...]   │
│       },                                                        │
│       {                                                         │
│         id: "q2",                                               │
│         question: "What does success look like?",               │
│         dimension: "WHY",                                       │
│         examples: ["Reduce time from X to Y", ...]              │
│       }                                                         │
│     ]                                                           │
│   },                                                            │
│   {                                                             │
│     id: "block-2",                                              │
│     signal_path: "@{user-vault}/signals/ai/pcam-framework.md",  │
│     radar: "AI / Intelligence",                                 │
│     core_insight: "Four dimensions: Perception, Cognition...",  │
│     questions: [                                                │
│       {                                                         │
│         id: "q3",                                               │
│         question: "What type of thing are you building?",       │
│         dimension: "WHAT",                                      │
│         examples: ["Product feature", "Standalone tool", ...]   │
│       }                                                         │
│     ]                                                           │
│   },                                                            │
│   {                                                             │
│     id: "block-context",                                        │
│     signal_path: null,                                          │
│     radar: null,                                                │
│     core_insight: "Context needed — using logical reasoning",   │
│     questions: [                                                │
│       { id: "q4", question: "Who are the users?", ... },        │
│       { id: "q5", question: "What constraints exist?", ... }    │
│     ]                                                           │
│   }                                                             │
│ ]                                                               │
│ total_questions: 5                                              │
│ signal_coverage: 0.6                                            │
│ dimensions_covered: { WHAT: true, WHO: true, WHY: true, ... }   │
│ status: "needs_clarification"                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

### 5. Response Gate: Clarification

```
┌─────────────────────────────────────────────────────────────────┐
│ GATE CONDITION MET                                              │
├─────────────────────────────────────────────────────────────────┤
│ Status: needs_clarification                                     │
│ Action: Present questions to user                               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ USER OUTPUT (Signal-First Format)                               │
├─────────────────────────────────────────────────────────────────┤
│ ## From: AI Augmentation Principle                              │
│ **Source**: @{user-vault}/signals/ai/augmentation-principle.md  │
│ **Radar**: AI / Intelligence                                    │
│                                                                 │
│ AI should augment human capabilities, not replace judgment.     │
│                                                                 │
│ **Questions**:                                                  │
│ 1. **What human capability should AI augment?**                 │
│    - Research speed                                             │
│    - Attention/review                                           │
│    - Pattern recognition                                        │
│    - Creative throughput                                        │
│                                                                 │
│ 2. **What does success look like?**                             │
│    - Reduce time from X to Y                                    │
│    - Enable non-experts to Z                                    │
│                                                                 │
│ ---                                                             │
│                                                                 │
│ ## From: PCAM Framework                                         │
│ **Source**: @{user-vault}/signals/ai/pcam-framework.md          │
│ ...                                                             │
│                                                                 │
│ ---                                                             │
│                                                                 │
│ ## Context Needed                                               │
│ ...                                                             │
└─────────────────────────────────────────────────────────────────┘
```

---

### 6. User Responds

```
┌─────────────────────────────────────────────────────────────────┐
│ USER INPUT                                                      │
├─────────────────────────────────────────────────────────────────┤
│ "I want to build an AI assistant for our support team.          │
│ It should help agents find answers faster - reduce response     │
│ time by 50%. We use Zendesk. Timeline is 3 months."             │
└─────────────────────────────────────────────────────────────────┘
```

---

### 7. Step 3a: Update STM

**Skill**: `phoenix-engine-stm-update`

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ stm_path: ".phoenix-os/stm/consult-cto-{timestamp}/"            │
│ user_response: "I want to build an AI assistant for..."         │
│ current_state: { step: "clarify", round: 1 }                    │
│ pending_question: [Q1-Q5 from generate-questions]               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ Step 1: Classify Response Impact                                │
│   → clarification (answers pending questions)                   │
│   → new_info (provides facts not previously known)              │
│                                                                 │
│ Step 2: Extract Facts                                           │
│   - [WHAT]: AI assistant for customer support                   │
│   - [WHO]: Support team agents (internal)                       │
│   - [WHY]: Reduce response time by 50%                          │
│   - [CONSTRAINT]: Must integrate with Zendesk                   │
│   - [CONSTRAINT]: 3 month timeline                              │
│                                                                 │
│ Step 3: Update context.md                                       │
│   → Append user response block with extracted facts             │
│                                                                 │
│ Step 4: Update intents.md                                       │
│   → clarify confidence updated                                  │
│                                                                 │
│ Step 5: Update state.md                                         │
│   → Interaction log entry added                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ updated: true                                                   │
│ impact_type: "clarification"                                    │
│ extracted_facts: [                                              │
│   { domain: "WHAT", value: "AI assistant for support" },        │
│   { domain: "WHO", value: "Support team agents" },              │
│   { domain: "WHY", value: "Reduce response time by 50%" },      │
│   { constraint: "Must integrate with Zendesk" },                │
│   { constraint: "3 month timeline" }                            │
│ ]                                                               │
│ changes: ["WHAT resolved", "WHO resolved", "WHY resolved", ...] │
│ needs_resolution: false                                         │
│ scope_changed: false                                            │
└─────────────────────────────────────────────────────────────────┘
```

---

### 8. Evaluation

**Skill**: `phoenix-cognition-evaluate-understanding`

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ original_query: "Help me build something AI-powered"            │
│ stm_path: ".phoenix-os/stm/consult-cto-{timestamp}/"            │
│ questions_asked: [Q1-Q5]                                        │
│ user_responses: (from STM context.md)                           │
│ intent: "clarify"                                               │
│ intent_definition_path: "@memory/engine/intents/cto-intents.md" │
│ clarification_round: 1                                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ PROCESSING                                                      │
├─────────────────────────────────────────────────────────────────┤
│ Step 1: Load Completion Criteria                                │
│   - Required: WHAT, WHO, WHY, CONSTRAINTS                       │
│                                                                 │
│ Step 2: Load Context from STM                                   │
│   - Original query, radar matches, signals                      │
│   - User responses with extracted facts                         │
│                                                                 │
│ Step 3: Evaluate Response Completeness                          │
│   - Q1 (capability): Complete (1.0) - "find answers faster"     │
│   - Q2 (success): Complete (1.0) - "reduce by 50%"              │
│   - Q3 (type): Complete (1.0) - "AI assistant"                  │
│   - Q4 (users): Complete (1.0) - "support team"                 │
│   - Q5 (constraints): Complete (1.0) - "Zendesk, 3 months"      │
│   → completeness_score: 1.0                                     │
│                                                                 │
│ Step 4: Check Required Dimensions                               │
│   - WHAT: ✅ Complete (AI assistant for support)                │
│   - WHO: ✅ Complete (Support team agents)                      │
│   - WHY: ✅ Complete (Reduce response time 50%)                 │
│   - CONSTRAINTS: ✅ Complete (Zendesk, 3 months)                │
│                                                                 │
│ Step 5: Extract Resolved Understanding                          │
│   → Consolidated view of what we now know                       │
│                                                                 │
│ Step 6: Determine Completion Status                             │
│   → completeness >= 0.8 AND all dimensions present              │
│   → status: clarified                                           │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS                                                         │
├─────────────────────────────────────────────────────────────────┤
│ status: "clarified"                                             │
│ completeness_score: 0.95                                        │
│ resolved_understanding: {                                       │
│   summary: "User wants to build an AI-powered customer          │
│             support assistant that helps agents resolve         │
│             tickets faster",                                    │
│   dimensions: {                                                 │
│     WHAT: "AI assistant for customer support",                  │
│     WHO: "Internal support agents (not customers)",             │
│     WHY: "Reduce ticket resolution time by 50%",                │
│     CONSTRAINTS: "Zendesk integration, 3-month timeline"        │
│   }                                                             │
│ }                                                               │
│ dimensions_status: {                                            │
│   WHAT: { status: "complete", value: "AI assistant..." },       │
│   WHO: { status: "complete", value: "Support agents" },         │
│   WHY: { status: "complete", value: "Reduce time 50%" },        │
│   CONSTRAINTS: { status: "complete", value: "Zendesk, 3mo" }    │
│ }                                                               │
│ assumptions: []                                                 │
│ unknowns: ["Budget constraints", "Team size"]                   │
│ signals_used: [                                                 │
│   {                                                             │
│     path: "@{user-vault}/signals/ai/augmentation-principle.md", │
│     usage: "Framed AI as augmenting agent capabilities"         │
│   }                                                             │
│ ]                                                               │
│ follow_up_questions: []                                         │
└─────────────────────────────────────────────────────────────────┘
```

---

### 9. Orchestrator Determines Next Action

**Agent**: `phoenix:orchestrator` (inline decision)

```
┌─────────────────────────────────────────────────────────────────┐
│ INLINE ROUTING DECISION                                         │
├─────────────────────────────────────────────────────────────────┤
│ Evaluation status: "clarified"                                  │
│ Resolved understanding: Complete                                │
│                                                                 │
│ Decision Options:                                               │
│   If status == "clarified":                                     │
│     → Re-evaluate intent with resolved_understanding            │
│     → Route to appropriate next intent                          │
│                                                                 │
│   If status == "needs_reclarification":                         │
│     → Generate follow-up questions                              │
│     → Present to user                                           │
│                                                                 │
│ This case: status == "clarified"                                │
│   → Re-invoke phoenix-engine-identify-intents with:             │
│     mode: "re-evaluate"                                         │
│     query: resolved_understanding.summary                       │
│     prior_intents: ["clarify"]                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

### 10. Re-Evaluation (if intent shifts)

**Skill**: `phoenix-engine-identify-intents` (mode: re-evaluate)

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS (Re-evaluation)                                          │
├─────────────────────────────────────────────────────────────────┤
│ query: "User wants to build an AI-powered customer support      │
│         assistant that helps agents resolve tickets faster"     │
│ intent_domain_path: "@memory/engine/intents/cto-intents.md"     │
│ enabled_intents: ["clarify", "decide", "validate", "consult",   │
│                   "advise"]                                     │
│ stm_path: ".phoenix-os/stm/consult-cto-{timestamp}/"            │
│ mode: "re-evaluate"                                             │
│ prior_intents: [{ intent: "clarify", ... }]                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS (Example: shifts to consult)                            │
├─────────────────────────────────────────────────────────────────┤
│ selected_intents: [                                             │
│   {                                                             │
│     intent: "consult",                                          │
│     confidence: 0.85,                                           │
│     rationale: "Now that requirements are clear, user needs     │
│                 guidance on how to build this"                  │
│   }                                                             │
│ ]                                                               │
│ intent_shifted: true                                            │
│ shift_reason: "Clarification complete, user now needs consult   │
│                on implementation approach"                      │
└─────────────────────────────────────────────────────────────────┘
```

---

### 11. Step 4: Synthesis

**Agent**: `phoenix:strategy-guardian` (synthesizer)

```
┌─────────────────────────────────────────────────────────────────┐
│ INPUTS                                                          │
├─────────────────────────────────────────────────────────────────┤
│ routing_plan: { (executed plan) }                               │
│ all_outputs: {                                                  │
│   clarify: { resolved_understanding, signals_used },            │
│   consult: { findings, recommendations }  (if executed)         │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ OUTPUTS: Strategic Brief                                        │
├─────────────────────────────────────────────────────────────────┤
│ ## Strategic Brief                                              │
│                                                                 │
│ ### What You're Building                                        │
│ An AI-powered support assistant for your team to find           │
│ answers faster and reduce response time by 50%.                 │
│                                                                 │
│ ### Key Insights                                                │
│ - Signal: AI should augment agent capabilities, not replace     │
│ - Focus on reducing friction, not replacing judgment            │
│ - Zendesk integration is critical path                          │
│                                                                 │
│ ### Recommendations                                             │
│ 1. Start with retrieval-augmented generation (RAG)              │
│ 2. Build on Zendesk's existing knowledge base                   │
│ 3. Measure baseline response time first                         │
│                                                                 │
│ ### Next Steps                                                  │
│ - [ ] Audit existing knowledge base quality                     │
│ - [ ] Define success metrics (baseline → target)                │
│ - [ ] Prototype with 3-5 common ticket types                    │
│                                                                 │
│ ### Signals Used                                                │
│ - @{user-vault}/signals/ai/augmentation-principle.md            │
│ - @{user-vault}/signals/ai/pcam-framework.md                    │
└─────────────────────────────────────────────────────────────────┘
```

---

## Complete Data Flow Diagram (ASCII)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              USER QUERY                                       │
│                    "Help me build something AI-powered"                       │
└──────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│                           CONSULT-CTO RECIPE                                  │
│  Enabled: [clarify, decide, validate, consult, advise]                       │
│  Synthesizer: phoenix:strategy-guardian                                       │
└──────────────────────────────────────────────────────────────────────────────┘
                                      │
══════════════════════════════════════╪═══════════════════════════════════════
                               STEP 0 │ INITIALIZE STM
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │  phoenix-engine-stm-initialize  │
                    │                                 │
                    │  IN:  recipe_id, signal,        │
                    │       user_context              │
                    │                                 │
                    │  OUT: stm_path, radar_matches,  │
                    │       signals_loaded,           │
                    │       no_radar_match            │
                    └─────────────────────────────────┘
                                      │
                         ┌────────────┴────────────┐
                         ▼                         ▼
              ┌──────────────────┐      ┌──────────────────┐
              │  Vault Radars    │      │  STM Workspace   │
              │  ({user-vault}/  │      │  (.phoenix-os/   │
              │   radars/)       │──────│   stm/)          │
              │                  │      │                  │
              │  AI/Intelligence │      │  state.md        │
              │  Innovation      │      │  context.md ◄────┼── signals loaded
              │  Leadership      │      │  intents.md      │
              │  ...             │      │  outputs/        │
              └──────────────────┘      └──────────────────┘
                                      │
══════════════════════════════════════╪═══════════════════════════════════════
                               STEP 1 │ BUILD ROUTING PLAN
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │     phoenix:orchestrator        │
                    │            (Agent)              │
                    └─────────────────────────────────┘
                                      │
                    ┌─────────────────┴─────────────────┐
                    ▼                                   ▼
    ┌───────────────────────────────┐   ┌───────────────────────────────┐
    │ phoenix-engine-identify-      │   │ phoenix-engine-build-plan     │
    │ intents                       │   │                               │
    │                               │   │ IN:  selected_intents,        │
    │ IN:  query, intent_domain,    │   │      query, agents,           │
    │      enabled_intents,         │   │      sequencing_rules         │
    │      stm_path, mode           │   │                               │
    │                               │──▶│ OUT: routing_plan,            │
    │ OUT: selected_intents[],      │   │      agent_assignments,       │
    │      pattern_matches,         │   │      blocked_intents          │
    │      confidence_boosts        │   │                               │
    └───────────────────────────────┘   └───────────────────────────────┘
                                                        │
══════════════════════════════════════════════════════════════════════════════
                               STEP 2 │ EXECUTE ROUTING PLAN
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │   phoenix:strategy-guardian     │
                    │      (Agent - clarify)          │
                    └─────────────────────────────────┘
                                      │
                    ┌─────────────────┴─────────────────┐
                    ▼                                   ▼
    ┌───────────────────────────────┐   ┌───────────────────────────────┐
    │ phoenix-perception-           │   │ phoenix-manifestation-        │
    │ analyze-request               │   │ generate-questions            │
    │                               │   │                               │
    │ IN:  query, stm_path, intent  │   │ IN:  analysis, stm_path,      │
    │                               │   │      intent, intent_defn      │
    │ OUT: vagueness_score,         │──▶│                               │
    │      buzzwords_detected,      │   │ OUT: question_blocks[],       │
    │      missing_dimensions,      │   │      signal_coverage,         │
    │      complexity,              │   │      dimensions_covered,      │
    │      signal_mapping,          │   │      status                   │
    │      recommended_action       │   │                               │
    └───────────────────────────────┘   └───────────────────────────────┘
                                                        │
══════════════════════════════════════════════════════════════════════════════
                          RESPONSE GATE │ needs_clarification
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │      PRESENT TO USER            │
                    │  (Signal-First Question Format) │
                    └─────────────────────────────────┘
                                      │
                                      ▼
                    ┌─────────────────────────────────┐
                    │         USER RESPONDS           │
                    └─────────────────────────────────┘
                                      │
══════════════════════════════════════╪═══════════════════════════════════════
                              STEP 3a │ UPDATE STM
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │   phoenix-engine-stm-update     │
                    │                                 │
                    │  IN:  stm_path, user_response,  │
                    │       current_state             │
                    │                                 │
                    │  OUT: updated, impact_type,     │
                    │       extracted_facts,          │
                    │       scope_changed             │
                    └─────────────────────────────────┘
                                      │
══════════════════════════════════════╪═══════════════════════════════════════
                         STEP 3 EVAL │ EVALUATE UNDERSTANDING
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │ phoenix-cognition-evaluate-     │
                    │ understanding                   │
                    │                                 │
                    │  IN:  original_query, stm_path, │
                    │       questions_asked,          │
                    │       user_responses, intent    │
                    │                                 │
                    │  OUT: status, completeness,     │
                    │       resolved_understanding,   │
                    │       dimensions_status,        │
                    │       assumptions               │
                    └─────────────────────────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                 │
                    ▼                 ▼                 ▼
             ┌──────────┐      ┌──────────┐      ┌──────────┐
             │clarified │      │needs_    │      │scope_    │
             │          │      │reclarify │      │changed   │
             └──────────┘      └──────────┘      └──────────┘
                    │                 │                 │
                    │                 └────────┬────────┘
                    │                          │
                    │                          ▼
                    │          ┌─────────────────────────────────┐
                    │          │  LOOP: Back to Step 1 or        │
                    │          │  generate follow-up questions   │
                    │          └─────────────────────────────────┘
                    │
══════════════════════════════════════╪═══════════════════════════════════════
                               STEP 4 │ SYNTHESIS
══════════════════════════════════════╪═══════════════════════════════════════
                                      ▼
                    ┌─────────────────────────────────┐
                    │   phoenix:strategy-guardian     │
                    │       (Synthesizer)             │
                    │                                 │
                    │  IN:  routing_plan, all_outputs │
                    │                                 │
                    │  OUT: Strategic Brief           │
                    └─────────────────────────────────┘
                                      │
                                      ▼
                    ┌─────────────────────────────────┐
                    │         FINAL OUTPUT            │
                    │     (User receives brief)       │
                    └─────────────────────────────────┘
```

---

## Data Types Reference

### STM Workspace Structure

```
.phoenix-os/stm/{recipe-id}-{timestamp}/
├── state.md           # Execution state, step log, interaction log
├── context.md         # Radar matches + loaded signal content + user responses
├── intents.md         # Intent state, confidence history
├── outputs/           # JSON outputs from each skill invocation
│   ├── routing-plan-{ts}.json
│   ├── analysis-{ts}.json
│   ├── questions-{ts}.json
│   └── evaluation-{ts}.json
└── evidence/          # Supporting artifacts
```

### Key Data Objects

| Object | Produced By | Consumed By |
|--------|-------------|-------------|
| `stm_path` | stm-initialize | All subsequent skills |
| `radar_matches` | stm-initialize | orchestrator (context) |
| `signals_loaded` | stm-initialize | All agents (via context.md) |
| `selected_intents` | identify-intents | build-plan |
| `routing_plan` | build-plan | Recipe (Step 2 executor) |
| `analysis` | analyze-request | generate-questions |
| `question_blocks` | generate-questions | Recipe (user presentation) |
| `extracted_facts` | stm-update | evaluate-understanding |
| `resolved_understanding` | evaluate-understanding | orchestrator (re-route) |

---

**Version**: 1.0.0
**Last Updated**: 2026-01-06
