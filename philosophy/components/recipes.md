# Recipes

> Execution contracts that define workflows

---

## What is a Recipe?

A recipe is an execution contract:
- **Intent bindings** — Which agent handles which intent
- **Orchestration pattern** — What flow to follow
- **Constraints** — Rules and boundaries
- **Roadblock handling** — How to handle failures

---

## Recipe Structure

```markdown
---
description: {What this recipe does}
argument-hint: {How to invoke}
---

# {Recipe Name}

{Recipe description}

## Execution Flow

Inherits: @memory/engine/flows/{pattern}.md

### Configuration

| Setting | Value |
|---------|-------|
| Vault Path | @{user-vault}/ |
| Synthesizer | phoenix:{agent} |

### Intent Bindings

| Intent | Agent | Status |
|--------|-------|--------|
| clarify | strategy-guardian | Active |
| decide | strategy-guardian | Planned |

### Roadblock Handling

| Roadblock | Resolution |
|-----------|------------|
| User still vague | Re-probe, max 3 rounds |
| Conflicting requirements | Surface conflict, ask to prioritize |

### Constraints

{Communication style, scope limits}
```

---

## Current Recipes

| Recipe | Purpose | Intents |
|--------|---------|---------|
| `consult-cto` | CTO-level guidance | clarify, decide, validate, consult, advise |

---

## Recipe Levels

| Level | Description | Orchestration |
|-------|-------------|---------------|
| **1** | Simple, single-task | None (direct execution) |
| **2** | Multi-intent, human-in-loop | Uses orchestration pattern |
| **3** | Autonomous | Self-directing |

---

## Example: consult-cto

```markdown
# Consult CTO

You are a strategic CTO advisor.

## Execution Flow

Inherits: @memory/engine/flows/recipe-orchestration-pattern.md

### Intent Bindings

| Intent | Agent |
|--------|-------|
| clarify | strategy-guardian |
| decide | strategy-guardian |
| validate | strategy-guardian |
| advise | advisor |

### Constraints

- Direct: "This will fail because..."
- Specific: Concrete examples, numbers
- Constructive: Problems paired with alternatives
```

---

## Location

```
recipes/
+-- consult-cto.md
```

---

## Related

-> [Orchestration](orchestration.md) — How recipes execute
-> [Intents](intents.md) — What recipes bind
-> [Agent Architecture](agent-architecture.md) — What recipes route to
-> [Memory](memory.md) — What recipes read/write

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
