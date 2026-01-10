# Architecture Overview

> The big picture: two layers that make Phoenix OS work

---

## Two Layers

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         PHOENIX OS PLATFORM                              │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                      CONFIGURATION LAYER                           │ │
│  │   (Web UI / Admin Dashboard / API)                                 │ │
│  │                                                                    │ │
│  │   Define: agents, recipes, skills, signals, radars, LLM config    │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                  │                                       │
│                           configures                                     │
│                                  ▼                                       │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                        CORE RUNTIME                                │ │
│  │   (Execution Engine)                                               │ │
│  │                                                                    │ │
│  │   Signal → Recipe → Orchestrator → Agent → Skills → Output        │ │
│  │                          │                    │                    │ │
│  │                       Memory ◄────────────────┘                    │ │
│  └────────────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Configuration Layer

**What it is**: The interface where you DEFINE everything.

| What You Configure | Examples |
|--------------------|----------|
| Agents | Goals, skills, routing logic, personality |
| Recipes | Intent bindings, orchestration patterns |
| Skills | Input/output schemas |
| Memory | Radars, signals |
| LLM | Providers, models |
| Workspaces | Isolation, permissions |

**Think of it as**: IDE where you build things.

---

## Core Runtime

**What it is**: The engine that EXECUTES everything.

```
User Query → Signal → Recipe → Orchestrator → Agent → Skills → Output
                                    │                    │
                                 Memory ◄────────────────┘
```

**Think of it as**: Production where things run.

---

## The Relationship

| Configuration Layer | Core Runtime |
|---------------------|--------------|
| **Define** components | **Execute** them |
| **Store** memory | **Read** at runtime |
| **Build** via UI | **Run** via CLI/API |

---

## Core Components

→ [Agents](../components/agent-architecture.md) — Domain experts
→ [Skills](../components/skills.md) — Atomic operations
→ [Intents](../components/intents.md) — What user wants
→ [Recipes](../components/recipes.md) — Execution contracts
→ [Memory](../components/memory.md) — Engine + Vault + STM
→ [Orchestration](../components/orchestration.md) — Intent detection + execution

---

## Next

→ [Core Runtime](02-core-runtime.md) — How execution works

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
