# Intent Taxonomy

> Foundational framework for understanding and working with intents

## What is an Intent?

An **intent** is a learned pattern that captures what a user wants to achieve when they express something in a particular context. Intents are:

- **User-owned**: They represent what the USER wants, not what the system does
- **Deterministic**: Same expression in same context → same intent
- **Evolving**: Patterns refine over time through STM-LTM exchange
- **Context-dependent**: The same words can map to different intents based on context

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

## STM-LTM Intent Exchange

### LTM (Long-Term Memory)
Stable intent patterns learned over time:
- Pattern definitions
- Routing rules
- Sequencing rules
- Context enrichment templates

### STM (Short-Term Memory)
Session-specific intent state:
- Currently active intent(s)
- Intent evolution during conversation
- Context accumulated for this session
- Confidence scores for classification

### Exchange Patterns

**LTM → STM (Load)**:
- At recipe start, load relevant intent patterns
- Match user context to known patterns
- Initialize session intent state

**STM → LTM (Promote)**:
- When a new pattern proves stable across sessions
- When existing patterns need refinement
- Requires high-order agent validation

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

**Version**: 1.0.0
**Last Updated**: 2026-01-02
