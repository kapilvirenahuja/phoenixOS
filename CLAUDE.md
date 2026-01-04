# Phoenix OS - Claude Code Instructions

> Project-level instructions for Claude Code when working in this repository

---

## Critical Requirements

### 1. Procedural Pattern Compliance (MANDATORY)

**When executing recipes that inherit orchestration patterns, you MUST follow the procedural steps in order. Do not optimize for speed.**

This applies to all Level 2+ recipes, including `consult-cto`.

#### Non-Negotiable Rules

1. **Steps MUST execute in order** — Step 0 before Step 1, Step 1 before Step 2, etc.
2. **Step 0 (Initialize STM) is MANDATORY** — Initialize STM before any other work. No exceptions.
3. **Auto-verify completion** — Each step must complete successfully before the next begins (no human verification required).
4. **No skipping "setup" steps** — Infrastructure steps (STM, context loading) are not optional overhead.
5. **Pattern compliance over response speed** — A slow, correct execution beats a fast, broken one.

#### ⛔ Response Gates (MANDATORY)

For Level 2 recipes, you may ONLY output to user when ONE of these conditions is met:

| Gate | Condition | What to Output |
|------|-----------|----------------|
| **Clarification** | Agent returns `needs_clarification` | Present clarifying questions per format |
| **Blocked** | Agent returns `blocked` | Explain blocker, request user action |
| **Synthesis** | Step 4 complete (all agents done) | Present final synthesis |
| **Error** | Unrecoverable error | Explain failure, suggest recovery |

**Steps 0, 1, 2 execute SILENTLY.** No status updates. No confirmations. No "proceeding to Step X".

Violation of response gates = pattern breach.

#### ⛔ DO NOT (Level 2 Recipes)

- DO NOT say "Step 0 complete" or any step completion status
- DO NOT say "Proceeding to Step X" or "Now executing Step X"
- DO NOT output anything between steps until a response gate is reached
- DO NOT treat step completion as a user response point
- DO NOT ask for confirmation to proceed between steps
- DO NOT summarize what you're about to do before doing it

**Execute silently until a response gate is reached.**

#### Step 0 Completion Criteria (Auto-Verified)

Step 0 completes automatically when all of the following exist:
- STM workspace at `.phoenix-os/stm/{recipe-id}-{timestamp}/`
- `state.md`, `context.md`, `intents.md` files
- LTM patterns loaded
- Initial state captured

**No human verification required.** Proceed directly to Step 1 after initialization.

#### Why This Matters

- **STM is the execution workspace** — Without it, there's no persistent state for re-invocation cycles
- **Agents read/write to STM** — Skipping initialization means agents operate without shared context
- **Debugging requires state** — When things fail, STM provides the audit trail
- **Pattern integrity** — Partial execution breaks the orchestration contract

Reference: `@memory/engine/flows/recipe-orchestration-pattern.md`

---

## Memory Architecture

Phoenix OS separates memory into distinct layers with different purposes:

### Memory Structure

```
memory/
├── engine/              # Phoenix OS system (ships with Phoenix)
│   ├── intents/         # Intent definitions & classification rules
│   ├── flows/           # Orchestration patterns
│   └── schemas/         # Data schemas
└── vault/               # User's knowledge (accumulated LTM)
    ├── radars/          # 7 classification lenses (keywords + signal mappings)
    └── signals/         # Actual content (organized by category)
        ├── ai/
        ├── evolutionary-architecture/
        ├── innovation/
        ├── leadership/
        └── technology/

.phoenix-os/
└── stm/                 # Short-term memory (runtime, ephemeral)
```

### Engine vs Vault Distinction

| Type | Location | Purpose | Examples |
|------|----------|---------|----------|
| **Engine** | `memory/engine/` | Phoenix OS system knowledge — how to orchestrate, classify intents, route | `cto-intents.md`, `sequencing-rules.md`, `recipe-orchestration-pattern.md` |
| **Vault Radars** | `{user-vault}/radars/` | Classification lenses — keywords for matching, signal mappings | `ai-intelligence.md`, `leadership.md` |
| **Vault Signals** | `{user-vault}/signals/` | Actual knowledge — mental models, frameworks, practices | `augmentation-principle.md`, `flywheel.md` |
| **STM** | `.phoenix-os/stm/` | Runtime state — ephemeral, per-session, pre-populated with signals | `state.md`, `context.md`, `intents.md` |

---

## Project Structure

```
phoenixOS/
├── agents/              # Agent definitions
├── memory/
│   ├── engine/          # System knowledge (intents, flows, schemas)
│   └── vault/           # User knowledge (mental models, practices)
├── philosophy/          # Design principles and usage guides
├── recipes/             # Executable recipes (skills)
├── skills/              # Reusable skill definitions
└── .phoenix-os/
    └── stm/             # Runtime short-term memory
```

---

## Key Patterns

| Pattern | Location | Purpose |
|---------|----------|---------|
| **Vault Architecture** | `memory/engine/vault-architecture.md` | Radar-signal separation, query matching |
| Recipe Orchestration | `memory/engine/flows/recipe-orchestration-pattern.md` | Level 2 recipe execution flow |
| CTO Intents | `memory/engine/intents/cto-intents.md` | Intent classification for CTO domain |
| Strategic Radars | `{user-vault}/signals/leadership/strategic-radars.md` | 7 strategic dimensions overview |

---

## When Executing Recipes

1. **Read the recipe file** to understand specializations
2. **Follow the inherited pattern** step by step
3. **Initialize STM first** — always
4. **Use the orchestrator** for intent routing
5. **Update STM after critical outputs** — routing plans, agent outputs, user responses
6. **Never skip steps** to save time

### STM Update Triggers

Update STM immediately after:
- Routing plan is built (save to `outputs/`, update `state.md`)
- Agent returns output (update `state.md`, `intents.md` if applicable)
- User responds (invoke `phoenix-context-update-stm` skill)
- Status changes (blocked, needs_clarification, complete)

### Clarification Handling

When an agent returns `status: needs_clarification`:
1. **Present questions directly in text** — do NOT use the `AskUserQuestion` tool
2. For each question, include:
   - The question itself
   - **LTM source** — which file/line the probe came from (with `@memory/engine/` or `@{user-vault}/` path)
   - **Why it matters** — what decision or direction this unblocks
   - **Examples** — concrete options or scenarios to help the user respond
3. Update STM with the questions asked
4. When user responds, invoke `phoenix-context-update-stm` before re-routing

---

## Signal-Grounded Protocol (CRITICAL)

**Reference**: `@memory/engine/vault-architecture.md`

**Agents read from STM context.md, NOT Vault directly. STM is pre-populated during Step 0.**

### The Architecture

```
Step 0: Initialize STM
    │
    ├── Scan query against radars ({user-vault}/radars/)
    ├── Load mapped signals into STM context.md
    └── Agents receive pre-filtered, session-relevant content
         │
         ▼
Step 1+: Agents read from STM
    │
    ├── Read STM context.md for signals
    ├── Read Engine for intent mechanics
    └── Generate signal-grounded output
```

### Two Parallel Processes

| Process | Handles | Timing |
|---------|---------|--------|
| **Radar Scanning** | WHAT knowledge is relevant (signals) | Step 0: initialize-stm |
| **Intent Detection** | WHAT user wants (clarify, decide, etc.) | Step 1: orchestrator |

These are **independent**. Intents don't reference radars/signals. Radars don't reference intents.

### The Protocol: STM-GROUNDED

```
1. Recipe invokes initialize-stm skill (Step 0)
   - Scans query against radar keywords
   - Loads matched signals into STM context.md
   - Agents never search Vault directly

2. Agent receives task + intent

3. Agent READS STM context.md
   - Contains pre-loaded signals from radar matching
   - Each signal includes source path for citation

4. Agent READS Engine for mechanics
   - @memory/engine/intents/ → Question framing structure
   - @memory/engine/flows/ → Orchestration patterns

5. Agent FRAMES output through STM signals
   - Engine tells you the SHAPE of questions
   - STM signals provide the CONTENT and strategic context

6. Output cites signal source paths from STM
```

### Example: Clarify Intent

**Wrong (searching Vault directly):**
```
Agent action: Search @{user-vault}/mental-models/ ← VIOLATION
```

**Right (STM-grounded):**
```
1. Read STM context.md (pre-populated by initialize-stm):
   Found: AI/Intelligence radar matched (confidence: 0.9)
   Signals loaded:
     - @{user-vault}/signals/ai/augmentation-principle.md
     - @{user-vault}/signals/ai/pcam-framework.md

2. Read Engine for question framing:
   - @memory/engine/intents/cto-intents.md
   - Framing: "What specifically...?", "Who/what/when?"

3. Generate signal-grounded question:
   - Question: "What specific capability should AI augment?"
   - Signal source: @{user-vault}/signals/ai/augmentation-principle.md
   - Why it matters: AI should augment capabilities, not be added for its own sake
```

### Enforcement Checklist

Before generating any output:

- [ ] Read STM context.md for pre-loaded signals?
- [ ] Read Engine for intent mechanics?
- [ ] Output grounded in STM signal content?
- [ ] Signal source paths cited?

**⛔ If agent searches Vault directly, the output is invalid.**

### Anti-Patterns (DO NOT DO)

| Anti-Pattern | Problem |
|--------------|---------|
| Searching Vault directly | Bypasses radar filtering, breaks architecture |
| Engine-only output | No strategic grounding from signals |
| Generic from training | Not grounded in user's knowledge |
| Missing citations | Unverifiable, not traceable |

---

**Version**: 2.0.0
**Last Updated**: 2026-01-05
