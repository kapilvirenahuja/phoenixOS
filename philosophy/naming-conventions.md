# Naming Conventions

> **Scope**: Recipes, Sub-Agents, and Skills
> **Status**: Active
> **Last Updated**: 2026-01-01

## Overview

This document defines the naming conventions for Phoenix OS components. Consistent naming ensures discoverability, clarity, and alignment with the system's philosophy of explicit via abstraction.

---

## 1. Recipes

**Purpose**: Pre-defined workflows that orchestrate agents through SDLC steps (Discover → Specify → Design → Build → Run).

### Convention

| Aspect | Convention | Rationale |
|--------|------------|-----------|
| **Case** | `kebab-case` | Consistent with file naming, URL-friendly |
| **Pattern** | `{verb}-{noun}` or `{verb}-{noun}-{modifier}` | Action-oriented, describes the workflow goal |
| **Invocation** | `/{recipe-name}` | CLI signal format |
| **Location** | `docs/wiki/usage/recipes/` | Centralized recipe documentation |

### Pattern Structure

```
{verb}-{noun}[-{modifier}]

verb     = action to perform (fix, implement, define, refactor, migrate, deploy)
noun     = target artifact (bug, component, feature, module, database)
modifier = optional context (react, api, schema)
```

### Examples

| Recipe Name | Invocation | Description |
|-------------|------------|-------------|
| `fix-bug` | `/fix-bug ISSUE-123` | Bug resolution workflow |
| `implement-react-component` | `/implement-component "UserProfile"` | React component implementation |
| `product-feature-definition` | `/define-feature "Analytics"` | Feature definition workflow |
| `refactor-module` | `/refactor-module "auth-service"` | Module refactoring workflow |
| `migrate-database` | `/migrate-database "v2-schema"` | Database migration workflow |
| `deploy-release` | `/deploy-release "v1.4.0"` | Release deployment workflow |

### Rules

1. **Always start with an action verb** — Recipes orchestrate work, so names should be imperative
2. **Noun describes the target artifact** — What is being acted upon
3. **Modifier is optional** — Use when disambiguation is needed (e.g., `implement-react-component` vs `implement-api-endpoint`)
4. **Match the signal** — Recipe name should align with the CLI invocation users will type

### Anti-Patterns

| Avoid | Prefer | Reason |
|-------|--------|--------|
| `bugFix` | `fix-bug` | Use kebab-case, verb-first |
| `ComponentImplementation` | `implement-component` | No PascalCase |
| `do_feature` | `define-feature` | No underscores, use specific verb |
| `workflow1` | `deploy-release` | Avoid generic/numbered names |

---

## 2. Sub-Agents

**Purpose**: Autonomous decision-makers that own outcomes, read memory, build context, and invoke skills.

### Convention

| Aspect | Convention | Rationale |
|--------|------------|-----------|
| **Case** | `kebab-case` | Consistent across system |
| **Prefix** | `phoenix:` | Namespace for Task tool registration |
| **Location** | `core/agents/{domain}/` | Domain-organized agent definitions |

### Agent Type Patterns

Phoenix OS uses distinct naming patterns based on agent type:

#### Domain Stewards: `{domain}-keeper`

Agents that are **stewards of a domain** with continuous responsibility.

| Agent | Domain | Responsibility |
|-------|--------|----------------|
| `project-keeper` | Project Management | Issues, tracking, metadata |
| `repo-keeper` | Repository | Commits, branches, worktrees |
| `deploy-keeper` | Deployment | Releases, environments, rollbacks |
| `quality-keeper` | Quality | Standards, gates, compliance |

**Rationale**: The `-keeper` suffix emphasizes stewardship, ownership, and continuous responsibility over a domain.

#### SDLC Roles: `{role-name}`

Agents aligned to **standard SDLC positions**.

| Agent | SDLC Role | Responsibility |
|-------|-----------|----------------|
| `advisor` | Advisor | Strategic counsel, experienced perspective |
| `developer` | Developer | Code implementation, TDD |
| `tech-lead` | Tech Lead | Technical design, architecture decisions |
| `scrum-master` | Scrum Master | Progress tracking, todo management |
| `architect` | Architect | System design, patterns |
| `designer` | Designer | UX/UI design |
| `validator` | QA/Tester | Validation, testing criteria |

#### Specialists: `{domain}-{action}er`

Agents with **specialized operations** within a domain.

| Agent | Domain | Specialization |
|-------|--------|----------------|
| `bug-analyzer` | Bug | Root cause analysis |
| `bug-implementer` | Bug | Fix implementation |
| `code-reviewer` | Code | Review and feedback |
| `security-auditor` | Security | Security assessment |
| `performance-analyzer` | Performance | Performance analysis |

#### High-Order Agents: `{domain}-guardian`, `{domain}-alchemist`, or `orchestrator`

Agents with **LTM write access** for governance, evolution, and system orchestration.

| Agent | Type | Responsibility |
|-------|------|----------------|
| `orchestrator` | Engine | Intent detection, routing, plan building |
| `strategy-guardian` | Guardian | Strategic decisions, validation, clarification |
| `standards-guardian` | Guardian | Enforce and evolve standards |
| `pattern-alchemist` | Alchemist | Transform and create patterns |
| `compliance-guardian` | Guardian | Regulatory compliance |

**Note**: The `orchestrator` is a special high-order engine agent that handles intent classification and agent routing. It does not perform domain work—it routes to agents that do.

### Registration Format

Agents are registered with the `phoenix:` prefix for Task tool invocation:

```yaml
# Task tool subagent_type values
phoenix:project-keeper
phoenix:repo-keeper
phoenix:bug-analyzer
phoenix:bug-implementer
phoenix:developer
phoenix:tech-lead
```

### Rules

1. **Keeper suffix** — Use for agents that are stewards of a domain (continuous responsibility)
2. **Role names** — Use standard SDLC role names for role-based agents
3. **Compound names** — Use `{domain}-{action}er` for specialized operations
4. **Guardian/Alchemist** — Reserve for high-order agents with LTM access
5. **Never use generic names** — Avoid "helper", "util", "manager" (too vague)

### Anti-Patterns

| Avoid | Prefer | Reason |
|-------|--------|--------|
| `bugFixer` | `bug-implementer` | Use kebab-case |
| `helper-agent` | `code-reviewer` | Avoid generic "helper" |
| `ProjectManager` | `project-keeper` | Use kebab-case, keeper suffix |
| `doer` | `developer` | Use specific role name |
| `agent1` | `bug-analyzer` | Avoid numbered/generic names |

---

## 3. Skills

**Purpose**: Bounded, reusable capabilities invoked by agents. Skills are stateless and deterministic.

### Convention

| Aspect | Convention | Rationale |
|--------|------------|-----------|
| **File Case** | `kebab-case.md` | Consistent file naming |
| **Invocation** | `{domain}-{action}` | Namespace + verb pattern |
| **Location** | `core/commands/phoenix/{domain}/` | Domain-based grouping |

### Pattern Structure

```
{domain}-{action}[-{target}]

domain = capability area (phoenix-context, phoenix-orchestrator, consult, impl, bug, test, deploy, review)
action = imperative verb (initialize, analyze, match, fetch, create, apply, execute)
target = optional object (stm, request, issue, pr, branch)
```

**Note**: Skills use hyphen separation throughout. The `phoenix-` prefix denotes system-level skills.

### Domain Categories

| Domain | Description | Examples |
|--------|-------------|----------|
| `phoenix-context` | Memory and context management | STM initialization, updates, intent identification |
| `phoenix-orchestrator` | Intent detection and routing | Pattern matching, confidence boosting, plan building |
| `consult` | Consultation and clarification | Request analysis, requirements clarification, synthesis |
| `impl` | Implementation and coding | Code, commits, design |
| `bug` | Bug analysis and fixing | RCA, fix application |
| `test` | Testing operations | Unit, integration, E2E |
| `deploy` | Deployment and releases | Release, rollback |
| `review` | Code review operations | PR review, feedback |

### Examples

| Skill Name | Directory | Description |
|------------|-----------|-------------|
| `phoenix-context-initialize-stm` | `phoenix-context-initialize-stm/` | Initialize STM workspace, load signals |
| `phoenix-context-update-stm` | `phoenix-context-update-stm/` | Update STM after user response |
| `phoenix-context-identify-intent` | `phoenix-context-identify-intent/` | Re-evaluate intent mid-execution |
| `phoenix-orchestrator-pattern-match` | `phoenix-orchestrator-pattern-match/` | Match query against intent patterns |
| `phoenix-orchestrator-boost-confidence` | `phoenix-orchestrator-boost-confidence/` | Apply context signals to refine scores |
| `phoenix-orchestrator-select-intents` | `phoenix-orchestrator-select-intents/` | Apply thresholds, resolve dependencies |
| `phoenix-orchestrator-match-agents` | `phoenix-orchestrator-match-agents/` | Map intents to available agents |
| `phoenix-orchestrator-build-plan` | `phoenix-orchestrator-build-plan/` | Construct routing plan |
| `consult-analyze-request` | `consult-analyze-request/` | Analyze request for complexity/vagueness |
| `consult-clarify-requirements` | `consult-clarify-requirements/` | Generate signal-grounded questions |
| `consult-synthesize-response` | `consult-synthesize-response/` | Consolidate information into synthesis |

### Rules

1. **Domain prefix is mandatory** — All skills must be namespaced
2. **Action should be imperative** — Use verbs like fetch, create, analyze, apply, execute
3. **Target is optional** — Add when disambiguation is needed
4. **Skills are stateless** — Name should not imply state or memory
5. **Single responsibility** — One skill, one bounded capability

### Anti-Patterns

| Avoid | Prefer | Reason |
|-------|--------|--------|
| `runTests` | `test-execute` | Use domain prefix with hyphen |
| `fetchIssue` | `plan-fetch-issue` | Use kebab-case with domain |
| `doCommit` | `impl-commit` | Remove unnecessary "do" |
| `helper:util` | `impl-format-code` | Be specific, avoid generic |
| `create_pr` | `impl-create-pr` | No underscores, use hyphens |
| `plan:fetch` | `plan-fetch-issue` | Use hyphen, not colon |

---

## Summary Reference

| Component | Pattern | Example | Registration/Invocation |
|-----------|---------|---------|-------------------------|
| **Recipe** | `{verb}-{noun}` | `consult-cto` | `/consult-cto` |
| **Agent (Steward)** | `{domain}-keeper` | `repo-keeper` | `phoenix:repo-keeper` |
| **Agent (Role)** | `{sdlc-role}` | `advisor` | `phoenix:advisor` |
| **Agent (Specialist)** | `{domain}-{action}er` | `bug-analyzer` | `phoenix:bug-analyzer` |
| **Agent (High-Order)** | `{domain}-guardian` | `strategy-guardian` | `phoenix:strategy-guardian` |
| **Agent (Engine)** | `orchestrator` | `orchestrator` | `phoenix:orchestrator` |
| **Skill** | `{domain}-{action}` | `consult-analyze-request` | `consult-analyze-request` |

---

## Naming Decision Tree

```
Is it a workflow that orchestrates multiple agents?
├─► YES → Recipe: {verb}-{noun}
│         Example: consult-cto, fix-bug
│
└─► NO → Is it an autonomous decision-maker?
         ├─► YES → Agent
         │         │
         │         ├─► Engine/routing agent? → orchestrator
         │         │   Example: orchestrator (intent detection & routing)
         │         │
         │         ├─► Steward of a domain? → {domain}-keeper
         │         │   Example: project-keeper, repo-keeper
         │         │
         │         ├─► SDLC role? → {role-name}
         │         │   Example: advisor, developer, tech-lead
         │         │
         │         ├─► Specialized operation? → {domain}-{action}er
         │         │   Example: bug-analyzer, code-reviewer
         │         │
         │         └─► High-order governance? → {domain}-guardian
         │             Example: strategy-guardian, standards-guardian
         │
         └─► NO → Skill: {domain}-{action}
                  Example: consult-analyze-request, phoenix-context-initialize-stm
```

---

## Related Documentation

- [Design Principles](./principles.md) — Separation of concerns, component roles
- [Recipes Reference](../usage/recipes/) — Available recipe implementations
- [CLAUDE.md](../../../CLAUDE.md) — Agent-first principle and configuration

---

**Author**: Phoenix OS Team
**Version**: 1.0.0
**Last Updated**: 2026-01-01
**Status**: Active
