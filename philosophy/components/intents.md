# Intent Taxonomy

> Foundational framework for understanding and working with intents

## What is an Intent?

An **intent** is a learned pattern that captures what a user wants to achieve when they express something in a particular context. Intents are:

- **User-owned**: They represent what the USER wants, not what the system does
- **Deterministic**: Same expression in same context → same intent
- **Evolving**: Patterns refine over time through STM-LTM exchange
- **Context-dependent**: The same words can map to different intents based on context

## SDLC Phases vs Intent Model

**SDLC phases are the high-level philosophy. Intents are the architecture that operates WITHIN each phase.**

### SDLC: The What

```
DISCOVER → SPECIFY → DESIGN → BUILD → RUN
```

These phases define what Phoenix OS does—the software development lifecycle.

### Intent Model: The How

Intents (clarify, decide, validate, consult, advise, design) are the **mechanism** for executing work within any phase.

```
┌────────────────────────────────────────────────────────────────┐
│  DISCOVER Phase                                                │
│  ├─ clarify: "What problem are we solving?"                    │
│  ├─ consult: "How do similar products handle this?"            │
│  └─ advise: "Is this worth pursuing?"                          │
├────────────────────────────────────────────────────────────────┤
│  DESIGN Phase                                                  │
│  ├─ clarify: "What should this component do?"                  │
│  ├─ decide: "Should we use React or Vue?"                      │
│  └─ validate: "Will this architecture scale?"                  │
├────────────────────────────────────────────────────────────────┤
│  BUILD Phase                                                   │
│  ├─ clarify: "What edge cases should we handle?"               │
│  ├─ consult: "How do I implement this pattern?"                │
│  └─ validate: "Is this implementation correct?"                │
└────────────────────────────────────────────────────────────────┘
```

**Key insight**: The same intent (e.g., `clarify`) can exist in any SDLC phase. The phase provides context; the intent provides the interaction pattern.

## Intents vs Radars/Signals

**Intents and radar/signal matching are independent processes.**

| Process | Purpose | Timing |
|---------|---------|--------|
| **Radar Scanning** | Load relevant knowledge (signals) into STM | Step 0: initialize-stm |
| **Intent Detection** | Identify what user wants (clarify, decide, etc.) | Step 1: orchestrator |

Intents provide the **shape** of agent output (question framing, output structure).
Signals provide the **content** (strategic knowledge, mental models).

Agents combine both: they serve an intent using signals from STM.

## Intent vs Goal

| Concept | Owner | Nature | Stability |
|---------|-------|--------|-----------|
| **Intent** | User | Specific, deterministic | Identified per interaction |
| **Goal** | Agent | Purpose-driven, broader | Defined in agent specification |

**Relationship**: Agents use their GOALS to determine HOW to serve user INTENTS.

```
User Intent (WHAT they want)
        │
        ▼
Agent Goal (WHY agent exists)
        │
        ▼
Agent Action (HOW to serve intent via goal)
```

## Intent Structure

Each intent in LTM should define:

```markdown
# Intent: {name}

## Patterns
Expressions that signal this intent:
- "Pattern 1..."
- "Pattern 2..."

## Context Signals
Contextual factors that strengthen this classification:
- Prior conversation state
- User role or domain
- Time/urgency indicators

## Routes To
Agent: phoenix:{agent-name}
Skills: {domain}:{skill}, {domain}:{skill}

## Context Enrichment
What the agent should understand about this intent:
- user_needs: What the user is trying to accomplish
- expects: What output format/content they anticipate
- probe_for: Information to gather if missing

## Related Intents
- evolves_to: Intents this commonly transitions to
- requires_first: Intents that should be resolved before this one
- conflicts_with: Mutually exclusive intents
```

## STM-LTM Architecture

### LTM (Long-Term Memory)

**Engine** (`memory/engine/`):
- Intent patterns and routing rules
- Sequencing rules
- Flow patterns

**Vault** (`{user-vault}/`):
- Radars: Classification lenses with keywords + signal mappings
- Signals: Actual knowledge content (mental models, practices)

### STM (Short-Term Memory)

Session-specific state at `.phoenix-os/stm/{recipe-id}-{timestamp}/`:
- `context.md`: Pre-loaded signals from radar scanning
- `intents.md`: Currently active intent(s) with confidence
- `state.md`: Execution state and interaction log

### How They Connect

```
Recipe Start
    │
    ├── Step 0: Radar scanning loads signals into STM context.md
    │
    ├── Step 1: Intent detection writes to STM intents.md
    │
    └── Step 2+: Agents read from both, execute, update STM
```

**Key principle**: Agents read from STM, not LTM directly. STM is pre-populated with relevant content.

## Multi-Intent Handling

When multiple intents are detected:

1. **Identify**: Classify all intents present in query
2. **Sequence**: Determine order based on dependencies
3. **Execute**: Process intents in sequence, passing outputs forward
4. **Synthesize**: Combine outputs into coherent response

## Intent Lifecycle

```
┌─────────────────────────────────────────────────┐
│  1. DETECT                                      │
│     Query + Context → Pattern Match → Intent(s) │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  2. ENRICH                                      │
│     Load context enrichment from LTM            │
│     Probe for missing information if needed     │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  3. ROUTE                                       │
│     Intent → Agent (via routing rules)          │
│     Multi-intent → Sequencer → Ordered agents   │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  4. EXECUTE                                     │
│     Agent serves intent using its goal          │
│     Skills invoked, outputs generated           │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  5. EVOLVE                                      │
│     Update STM with results                     │
│     Check for intent transition                 │
│     Promote patterns to LTM if stable           │
└─────────────────────────────────────────────────┘
```

---

**Version**: 2.0.0
**Last Updated**: 2026-01-05
