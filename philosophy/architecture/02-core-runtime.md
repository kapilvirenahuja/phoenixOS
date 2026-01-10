# Core Runtime

> How Phoenix OS executes queries

---

## Execution Flow

```
User Query
    │
    ▼
┌─────────┐     ┌─────────────┐     ┌─────────┐     ┌────────┐
│ Signal  │────►│   Recipe    │────►│  Agent  │────►│ Output │
└─────────┘     └─────────────┘     └─────────┘     └────────┘
                      │                   │
                      ▼                   ▼
                ┌───────────┐      ┌───────────┐
                │Orchestrator│      │  Skills   │
                └───────────┘      └───────────┘
                      │
                      ▼
                ┌───────────┐
                │  Memory   │
                └───────────┘
```

---

## The 5 Steps

| Step | What Happens | Component |
|------|--------------|-----------|
| **0** | Initialize STM — load knowledge | [Memory](../components/memory.md) |
| **1** | Identify intent — pattern match | [Intents](../components/intents.md) |
| **2** | Build routing plan — sequence agents | [Orchestration](../components/orchestration.md) |
| **3** | Execute plan — invoke agents | [Agents](../components/agent-architecture.md) |
| **4** | Synthesize — combine outputs | [Recipes](../components/recipes.md) |

---

## Component Roles

| Component | Role in Flow |
|-----------|--------------|
| **Signal** | Entry point (CLI command, webhook) |
| **Recipe** | Defines what happens (intent → agent bindings) |
| **Orchestrator** | Detects intent, builds plan, routes |
| **Agent** | Executes domain work via skills |
| **Skill** | Atomic operation (analyze, generate, evaluate) |
| **Memory** | Provides context (Engine + Vault + STM) |

---

## Detailed Flow

```
1. Signal arrives
   │
   ▼
2. Recipe activates
   ├── Reads intent bindings
   └── Triggers orchestrator
   │
   ▼
3. Orchestrator runs Step 0
   ├── Scans Vault radars
   ├── Loads matched signals
   └── Creates STM workspace
   │
   ▼
4. Orchestrator runs Step 1-2
   ├── Pattern matches query → intent
   ├── Builds routing plan
   └── Sequences agents
   │
   ▼
5. Orchestrator runs Step 3
   ├── Invokes agent per plan
   ├── Agent invokes skills
   ├── Handles roadblocks
   └── Updates STM
   │
   ▼
6. Synthesizer runs Step 4
   └── Combines outputs → final response
```

---

## Deep Dives

→ [Agents](../components/agent-architecture.md)
→ [Skills](../components/skills.md)
→ [Orchestration](../components/orchestration.md)
→ [Memory](../components/memory.md)

---

## Next

→ [Principles](03-principles.md) — Key rules

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
