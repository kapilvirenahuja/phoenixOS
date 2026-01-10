# Key Principles

> Rules and patterns that govern Phoenix OS

---

## 1. Skills Execute, Agents Decide

| Skills | Agents |
|--------|--------|
| Atomic operations | Orchestrate skills |
| Stateless | Manage state |
| No judgment | Apply judgment |

**Rule**: Never put decision logic in skills.

→ [Agents](../components/agent-architecture.md) | [Skills](../components/skills.md)

---

## 2. Memory Access Rules

| Layer | Who Reads | Who Writes |
|-------|-----------|------------|
| Engine | Orchestrator, Skills | Never |
| Vault | Step 0 only | Configuration |
| STM | Agents, Skills | Agents, Skills |

**Rule**: Agents read STM, not Vault directly.

→ [Memory](../components/memory.md)

---

## 3. Intent Confidence

| Confidence | Action |
|------------|--------|
| ≥ 0.8 | Execute |
| 0.5 - 0.8 | Confirm |
| < 0.5 | Clarify |

**Rule**: Never execute with low confidence.

→ [Intents](../components/intents.md)

---

## 4. Signal-Grounded Output

All output traces to signals:
```
Question: "What capability should AI augment?"
Source: @{user-vault}/signals/ai/augmentation-principle.md
```

**Rule**: No signal = say "No signal found."

→ [Memory](../components/memory.md)

---

## 5. Orchestration Steps

| Step | Before |
|------|--------|
| 0 (Init) | Everything |
| 1 (Intent) | Plan |
| 2 (Plan) | Execute |
| 3 (Execute) | Synthesize |

**Rule**: Never skip. Never reorder.

→ [Orchestration](../components/orchestration.md)

---

## 6. Separation of Concerns

| Component | Owns |
|-----------|------|
| Recipe | Intent bindings |
| Orchestrator | Routing plan |
| Agent | Skill invocation |
| Skill | Atomic operation |

**Rule**: Each layer owns one thing.

---

## 7. Radar-Signal Architecture

```
Radar (classification) → Signal (content)
      └── Keywords             └── Knowledge
      └── Questions            └── Implications
```

**Rule**: Radars classify. Signals contain.

→ [Memory](../components/memory.md)

---

## Summary

| Principle | One-Liner |
|-----------|-----------|
| Skills Execute, Agents Decide | Separate execution from judgment |
| Memory Access | Agents read STM, not Vault |
| Intent Confidence | ≥0.8 execute, <0.5 clarify |
| Signal-Grounded | All output traces to signals |
| Orchestration Steps | Never skip, never reorder |
| Separation of Concerns | Each layer owns one thing |
| Radar-Signal | Radars classify, signals contain |

---

## Back to

← [Architecture Index](../architecture.md)

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
