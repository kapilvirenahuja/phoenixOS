# Skills

> Atomic, testable, reusable operations

---

## What is a Skill?

A skill is an atomic operation that:
- **Does one thing** — Single responsibility
- **Is stateless** — Same input -> same output
- **Doesn't decide** — Agents invoke skills
- **Has schemas** — Defined inputs/outputs

---

## Skill vs Agent

| | Skill | Agent |
|-|-------|-------|
| **Purpose** | Execute | Orchestrate |
| **State** | Stateless | Manages |
| **Judgment** | None | Applies |
| **Testing** | Unit | Integration |

**Rule**: Skills EXECUTE. Agents DECIDE.

---

## Skill Domains

| Domain | Purpose | Examples |
|--------|---------|----------|
| **Engine** | System operations | stm-initialize, identify-intents, build-plan |
| **Perception** | Understanding inputs | analyze-request |
| **Cognition** | Reasoning | evaluate-understanding |
| **Manifestation** | Producing outputs | generate-questions |

---

## Current Skills

| Skill | Domain | What It Does |
|-------|--------|--------------|
| `stm-initialize` | Engine | Create STM, load signals |
| `stm-update` | Engine | Update STM after user response |
| `identify-intents` | Engine | Pattern match -> detect intent |
| `build-plan` | Engine | Create routing plan |
| `analyze-request` | Perception | Analyze query for vagueness |
| `generate-questions` | Manifestation | Create clarifying questions |
| `evaluate-understanding` | Cognition | Check if clarity achieved |

---

## Skill Structure

```markdown
# Skill: {skill-name}

> {One-line description}

## Domain
{Engine | Perception | Cognition | Manifestation}

## Input
| Parameter | Type | Description |
|-----------|------|-------------|
| query | string | User's query |
| stm_path | string | Path to STM |

## Output
| Field | Type | Description |
|-------|------|-------------|
| result | object | The result |
| status | string | complete | error |

## Algorithm
{Step-by-step execution logic}

## Example
{Input -> Output example}
```

---

## Example: analyze-request

```
Skill: analyze-request
Domain: Perception

Input:
  query: "Help me build something AI-powered"
  stm_path: ".phoenix-os/stm/consult-cto-123/"
  intent: "clarify"

Output:
  vagueness_score: 0.85
  missing_dimensions: ["WHAT", "WHO", "WHY", "CONSTRAINTS"]
  buzzwords_detected: ["AI-powered", "something"]
  recommended_action: "clarify"
```

---

## Key Rules

1. **Stateless** — No internal state, same input -> same output
2. **Single responsibility** — One thing per skill
3. **Schema-defined** — Clear input/output types
4. **Agent-invoked** — Skills never decide when to run

---

## Location

```
skills/
+-- phoenix-engine-stm-initialize/
+-- phoenix-engine-stm-update/
+-- phoenix-engine-identify-intents/
+-- phoenix-engine-build-plan/
+-- phoenix-perception-analyze-request/
+-- phoenix-manifestation-generate-questions/
+-- phoenix-cognition-evaluate-understanding/
```

---

## Related

-> [Agent Architecture](agent-architecture.md) — What invokes skills
-> [Intents](intents.md) — What skills serve
-> [Orchestration](orchestration.md) — Where skills are chained
-> [Memory](memory.md) — What skills read/write

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
