# Phoenix OS Architecture

> Navigate the architecture documentation

---

## Start Here

| If you want to... | Read |
|-------------------|------|
| Understand the big picture | [Overview](architecture/01-overview.md) |
| See how execution works | [Core Runtime](architecture/02-core-runtime.md) |
| Learn the key rules | [Principles](architecture/03-principles.md) |

---

## Core Components

| Component | What It Is | Document |
|-----------|------------|----------|
| **Agents** | Domain experts that orchestrate skills | [Agent Architecture](components/agent-architecture.md) |
| **Skills** | Atomic, testable operations | [Skills](components/skills.md) |
| **Intents** | What the user wants | [Intents](components/intents.md) |
| **Recipes** | Execution contracts | [Recipes](components/recipes.md) |
| **Memory** | Knowledge system (Engine + Vault + STM) | [Memory](components/memory.md) |
| **Orchestration** | Intent detection + routing + execution | [Orchestration](components/orchestration.md) |

---

## Document Map

```
                    ┌─────────────────┐
                    │    Overview     │
                    │  (big picture)  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
      ┌───────────┐  ┌───────────┐  ┌───────────┐
      │   Core    │  │  Princi-  │  │   Compo-  │
      │  Runtime  │  │   ples    │  │   nents   │
      └───────────┘  └───────────┘  └─────┬─────┘
                                          │
         ┌────────────────────────────────┼────────────────────┐
         │         │         │         │         │             │
         ▼         ▼         ▼         ▼         ▼             ▼
    ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐
    │ Agents  │ │ Skills  │ │ Intents │ │ Recipes │ │ Memory  │ │  Orch.  │
    └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘
```

---

## Quick Reference

| Question | Answer |
|----------|--------|
| What's the web/config layer? | [Overview](architecture/01-overview.md) — Configuration Layer |
| How do agents differ from skills? | [Agent Architecture](components/agent-architecture.md) orchestrate, [Skills](components/skills.md) execute |
| What are radars and signals? | [Memory](components/memory.md) — Vault section |
| What is STM? | [Memory](components/memory.md) — STM section |
| How does intent detection work? | [Intents](components/intents.md) + [Orchestration](components/orchestration.md) |
| How do recipes execute? | [Orchestration](components/orchestration.md) — Step 0-3 flow |

---

**Version**: 1.1.0
**Last Updated**: 2026-01-10
