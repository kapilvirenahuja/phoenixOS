# Agents

> Domain experts that orchestrate skills and apply judgment

---

## What is an Agent?

An agent is a specialized persona that:
- **Owns a goal** — What it's trying to achieve
- **Orchestrates skills** — Decides which to invoke, when
- **Applies judgment** — When to clarify, when to proceed
- **Has personality** — Communication style, constraints

---

## Agent vs Skill

| | Agent | Skill |
|-|-------|-------|
| **Purpose** | Orchestrate | Execute |
| **State** | Manages | Stateless |
| **Judgment** | Applies | None |
| **Testing** | Integration | Unit |

**Rule**: Agents DECIDE. Skills EXECUTE.

---

## Current Agents

| Agent | Goal | Skills |
|-------|------|--------|
| `orchestrator` | Identify intent, build routing plan | identify-intents, build-plan |
| `strategy-guardian` | Challenge and sharpen strategic thinking | analyze-request, generate-questions, evaluate-understanding |
| `advisor` | Provide strategic perspective | (planned) |

---

## Agent Structure

```markdown
---
name: {agent-name}
description: {one-line description}
tools: Read, Grep, Glob, Skill
---

# {Agent Name}

{Agent persona description}

## Goal

{What this agent is trying to achieve}

## Inputs

| Input | Description |
|-------|-------------|
| query | User's query |
| stm_path | Path to STM |
| prior_outputs | Results from other agents |

## Outputs

| Output | Description |
|--------|-------------|
| findings | Key findings |
| next_steps | Actionable steps |
| status | complete | blocked | needs_clarification |

## Execution

### Router
{Decision logic for skill selection}

### Skill Chains
{Which skills to invoke for each path}

## Constraints
{Rules this agent must follow}

## Memory Access
{What this agent reads/writes}
```

---

## Example: Strategy Guardian

```
Agent: strategy-guardian

Goal: Challenge and sharpen strategic thinking

Router:
  analyze-request
        |
        +-- confidence < 0.8 --> generate-questions --> loop
        |
        +-- confidence >= 0.8 --> execute chain

Skills:
  +-- analyze-request (perception)
  +-- generate-questions (manifestation)
  +-- evaluate-understanding (cognition)

Style: Direct, specific, constructive

Constraints:
  - Do NOT make assumptions - ask if unclear
  - Do NOT validate without stress-testing
  - ALWAYS provide actionable next steps
```

---

## How Agents Work

```
Agent receives task from orchestrator
    |
    +-- Reads context from STM
    |
    +-- Runs router (which skill chain?)
    |
    +-- Invokes skills in sequence
    |
    +-- Applies judgment on results
    |
    +-- Updates STM
    |
    +-- Returns output with status
```

---

## Key Rules

1. **Agents read STM, not Vault** — Signals are pre-loaded during Step 0
2. **Agents invoke skill chains** — Never perform skill work directly
3. **Agents apply personality** — Style, constraints, framing
4. **Agents manage loops** — Clarification rounds, roadblock handling

---

## Location

```
agents/
+-- orchestrator.md
+-- strategy-guardian.md
+-- advisor.md
```

---

## Related

-> [Skills](skills.md) — What agents invoke
-> [Orchestration](orchestration.md) — How agents are invoked
-> [Intents](intents.md) — What agents serve
-> [Recipes](recipes.md) — What binds intents to agents

---

**Version**: 1.0.0
**Last Updated**: 2026-01-10
