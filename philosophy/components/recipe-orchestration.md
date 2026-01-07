# Recipe Orchestration Pattern

> Internal implementation of the Recipe → Sub-Agent flow for Level 2 recipes

## Overview

When a Level 2 recipe is invoked, it follows a structured orchestration pattern. This pattern is the **internal implementation** of the philosophy's `Recipe → Sub-Agent(s) → Skill(s)` flow.

## The Pattern

```
Signal: /recipe-name "query"
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│  Step 0: Initialize STM                                 │
│  ├─ Create workspace: .phoenix-os/stm/{id}-{timestamp}/ │
│  ├─ Scan query against radars                           │
│  ├─ Load matched signals into context.md                │
│  └─ Initialize state.md, intents.md                     │
└─────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│  Step 1: Build Routing Plan                             │
│  ├─ Invoke orchestrator agent                           │
│  ├─ Identify intents from query + context               │
│  ├─ Map intents to agents                               │
│  └─ Construct sequenced routing plan                    │
└─────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│  Step 2: Execute Routing Plan                           │
│  ├─ For each step in plan:                              │
│  │   ├─ Invoke domain agent with goal                   │
│  │   ├─ Agent reads STM (signals + context)             │
│  │   ├─ Agent invokes skill chain                       │
│  │   └─ Agent updates STM with output                   │
│  └─ Handle roadblocks (clarification, blocked, error)   │
└─────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│  Step 3: Synthesize Response                            │
│  ├─ Designated synthesizer consolidates outputs         │
│  └─ Generate final response to user                     │
└─────────────────────────────────────────────────────────┘
```

## Component Mapping

| Philosophy Concept | Internal Implementation |
|-------------------|------------------------|
| Signal | User invokes `/recipe-name` |
| Recipe | Step 0-3 orchestration flow |
| Sub-Agent | Orchestrator + Domain Agents |
| Skills | Skill chains invoked by agents |
| Read Memory | Agent reads from STM context.md |
| Build Context | Radar scanning in Step 0 |
| Write STM | Agent updates after execution |

## Response Gates

Recipes only output to the user when one of these gates is reached:

| Gate | Condition | Action |
|------|-----------|--------|
| **Clarification** | Agent returns `needs_clarification` | Present questions to user |
| **Blocked** | Agent returns `blocked` | Explain blocker, request action |
| **Synthesis** | All steps complete | Present final synthesis |
| **Error** | Unrecoverable failure | Explain failure, suggest recovery |

**Steps 0, 1, 2 execute silently.** No status updates between steps.

## STM Workspace Structure

```
.phoenix-os/stm/{recipe-id}-{timestamp}/
├── state.md       # Execution state, interaction log
├── context.md     # Pre-loaded signals from radar matching
├── intents.md     # Active intents with confidence scores
├── outputs/       # Results from each step
└── evidence/      # Supporting artifacts
```

## Why This Pattern?

1. **Determinism** — STM provides consistent context across agent invocations
2. **Traceability** — Every step is auditable via STM state
3. **Resumability** — If interrupted, state enables continuation
4. **Signal-grounding** — Agents work with pre-filtered, relevant signals

## Skill Chains

Domain agents invoke skill chains, not individual skills:

```
Strategy-Guardian (clarify intent):
  phoenix-perception-analyze-request → phoenix-manifestation-generate-questions → phoenix-cognition-evaluate-understanding

Orchestrator (routing):
  phoenix-engine-identify-intents → phoenix-engine-build-plan
```

Skills are mandatory. Agents MUST invoke their defined skill chains.

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
