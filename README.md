# Phoenix OS

An agentic framework for spec-driven development that transforms AI copilots from non-deterministic to deterministic behavior for enterprise-grade code generation.

## The Problem

AI coding assistants produce non-deterministic outputs—the same prompt yields different results each time. For enterprises, this means:
- Code developers don't understand
- Systems that can't be debugged
- Technical debt that compounds

## The Solution

Phoenix OS is an **operating system for AI-powered development**. Like an OS orchestrates hardware resources, Phoenix OS orchestrates AI agents through cognitive flows to deliver quality, consistent code.

### Core Flow

```
Signal → Recipe → Orchestrator → Agent → Output
                       ↓            ↓
                    Memory       Router → Skill Chain
                       ↓
                   Context
```

**Expanded:**

```
Signal (user query)
    │
    ▼
Recipe
    │
    ├── Intent Bindings: intent → agent mapping
    │
    ▼
Orchestrator
    │
    ├── Memory ──────────────────┐
    │   ├── Engine (how)         │
    │   └── Vault (what)         │
    │            │               │
    │            ▼               │
    │        Context (STM)  ◄────┘
    │
    ├── Step 0: Load signals
    ├── Step 1: Detect intent → Build plan
    └── Step 2: Dispatch
                │
                ▼
Agent
    │
    ├── Goal: What this agent does
    │
    ├── Router (analyze input)
    │       │
    │       ├── confidence < 0.8 → clarify first
    │       │
    │       └── confidence ≥ 0.8 → Skill Chain
    │
    ▼
Output
    │
    └── complete | blocked | needs_clarification
```

**Key mechanics:**
- **Recipe** maps intents to agents (WHO does the work)
- **Orchestrator** builds context from Memory, detects intent, routes to agent
- **Agent** has a goal, routes input to skill chain (HOW to do the work)
- **Confidence gate** ensures agents only execute when confident (≥ 0.8)
- **Agents** never access Vault directly — signals are pre-loaded to STM

### AI-Native SDLC

```
DISCOVER ──► SPECIFY ──► DESIGN ──► BUILD ──► RUN
```

## Three Tenets

1. **Intent-Driven, Tool-Agnostic Execution** - Focus on outcomes, adapt to available tools
2. **Context-Aware Decision Making** - Adapt behavior based on environment and context
3. **Built as an Agentic System** - Autonomous agents collaborate to deliver deterministic outcomes

## Core Components

| Component | Role |
|-----------|------|
| **Recipe** | Execution contract - defines what should happen, inherits orchestration patterns |
| **Intent** | Classification unit - what the user wants (clarify, decide, validate, etc.) |
| **Agent** | Execution unit - handles domain-specific work via skill chains |
| **Skill** | Atomic capability - bounded, deterministic, single-responsibility operations |
| **Memory** | Three-layer system: Engine (mechanics), Vault (knowledge), STM (runtime) |
| **Orchestration** | Control flow pattern - routes intents to agents, manages response gates |

### Memory Architecture

```
memory/
├── engine/         # Phoenix OS mechanics (intents, flows, schemas)
└── vault/          # User's knowledge base
    ├── radars/     # Classification lenses (keywords + mappings)
    └── signals/    # Actual content (mental models, practices)

.phoenix-os/
└── stm/            # Runtime workspace (ephemeral, per-session)
```

## Agent Types

- **System Keepers** (`{area}-keeper`) - project-keeper, repo-keeper, deploy-keeper
- **SDLC Roles** - developer, tech-lead, architect, validator
- **Specialists** (`{domain}-{action}er`) - bug-analyzer, code-reviewer
- **High-Order** (`{domain}-guardian`) - standards-guardian, pattern-alchemist

## Documentation

### Architecture
- [Data Flow: consult-cto](philosophy/data-flow-consult-cto.md) - Complete execution trace showing component interactions
- [Orchestration Engine](philosophy/components/orchestration-engine.md) - Core orchestration mechanics
- [Recipe Orchestration](philosophy/components/recipe-orchestration.md) - Level 2 recipe patterns

### Philosophy
- [Philosophy](philosophy/philosophy.md) - Fluidic SDLC and core tenets
- [Principles](philosophy/principles.md) - Design principles and component specs
- [Naming Conventions](philosophy/naming-conventions.md) - Standards for recipes, agents, skills

## Getting Started

```
Coming soon for Claude-code and Droids
```

## License

MIT License - see [LICENSE](LICENSE) for details.

## Author

Kapil Viren Ahuja
emailto:kapil@howtoarchitect.io
