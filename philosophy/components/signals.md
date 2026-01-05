# Signals

> Entry points that trigger recipe execution

## Definition

A **signal** is an event that initiates a recipe. Signals are the perception layer—they capture user or system intent and route it to the appropriate workflow.

## Current Signal Types

| Type | Source | Example |
|------|--------|---------|
| **Manual Invocation** | User CLI command | `/consult-cto "Should I use microservices?"` |

**Note**: Additional signal types (webhooks, scheduled triggers, file watchers) will be added as Phoenix OS matures.

## Signal Characteristics

- **Stateless**: Signals carry information but hold no state
- **Unidirectional**: Flow into the system, never out
- **Recipe-bound**: Always enter through recipes, never directly to agents

## How Signals Work

```
User opens Claude Code
        │
        ▼
Invokes recipe (e.g., /consult-cto)
        │
        ▼
Signal captured → Recipe triggered
        │
        ▼
Recipe orchestration begins
```

## Rules

1. **All signals enter via recipes** — Signals never invoke agents directly
2. **Signals do not update memory** — They trigger workflows that update memory
3. **Signals inform, not prescribe** — They communicate intent without dictating execution

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
