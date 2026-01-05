# Design Principles: Detailed Specification

## Overview

This document provides detailed specifications for Phoenix OS design principles. These principles define how each interacts, maintain boundaries, and ensure system integrity.

---

## 1. Separation of Concerns

**Core Principle**: Each component has a distinct role with clear boundaries. No component crosses into another's domain.

### Flow

The core flow is aligned with standards where CLIs are moving towards. Claude-code & Factory AI already follows this design. GitHub Copilot has recently adopted the Skills design, and the only part missing is Agentic, which we believe is something they will adopt sooner than later.

The part that is unique to our approach is Memory and Context building. No CLI harness adopts this approach, yet we feel this is going to be critical. The approach that each follows is via AGENT.MD or CLAUDE.MD that represent memory, we expect this towards a more structured approach.

**Philosophy-level flow**:
```
Signal → Recipe → Sub-Agent(s) → Skill(s) → Execute
                       ↓
                 Read Memory (STM + LTM)
                       ↓
                 Build Context
```

**Note**: The internal implementation of `Recipe → Sub-Agent` involves additional steps (STM initialization, orchestrator routing, skill chains). See [Recipe Orchestration](./components/recipe-orchestration.md) for implementation details.

#### AI-Native SDLC Steps

All recipes follow a 5-step AI-Native SDLC:

```
DISCOVER ──► SPECIFY ──► DESIGN ──► BUILD ──► RUN
```

| Step | Purpose |
|------|---------|
| **Discover** | Prototype ideas and validate concepts before formal specification |
| **Specify** | Define functional requirements, technical requirements (NFRs), and validation criteria |
| **Design** | Create UX and technical design (including security, performance, accessibility) |
| **Build** | TDD implementation, testing, quality gates, documentation, PR workflow until merged |
| **Run** | Deploy to production, monitor, alert, and respond to incidents |


Each step follows the core flow:
```
Recipe ──► Sub-Agent ──► Skill(s) ──► Execute
               │
               ▼
         Read Memory (LTM + STM)
               │
               ▼
         Build Context ──► Output ──► Write STM
```

### Signals

**Role**: Entry points that trigger recipe execution.

#### Characteristics

- **Stateless**: Carry information but hold no state
- **Unidirectional**: Flow into the system, never out
- **Recipe-bound**: Always enter through recipes, never directly to agents

#### Current Signal Types

| Type | Source | Example |
|------|--------|---------|
| **Manual Invocation** | User CLI command | `/consult-cto "Should I use microservices?"` |

**Note**: Additional signal types (webhooks, scheduled triggers) will be added as Phoenix OS matures.

#### Rules

- All signals enter via recipes
- Signals do not directly invoke agents
- Signals do not update memory directly
- Signals inform decisions without prescribing actions

> **See also**: [Signals Component](./components/signals.md) for detailed signal documentation.

---

### Recipes
**Pre-defined workflows executed via the Agentic system.**

Pre-configured set of instructions which drive a clear set of instructions towards a goal.

#### Characteristics
- **Entry point**: All signals enter through recipes
- **Flow orchestration**: Determine agent sequences and logic (refer to types)
- **Validation**: Ensure inputs are correct before proceeding
- **No content construction**: Do not build inputs or outputs

#### Responsibilities

1. **Signal Reception**
   - All system interactions start with a recipe

2. **Pre-flight Validation**
   - Validate inputs before execution
   - Check environment readiness
   - Verify prerequisites

3. **Flow Determination**
   - Define which agents to invoke
   - Specify execution sequence (when deterministic)
   - Allow system to decide agent selection (when non-deterministic)

4. **Intent Provision**
   - Pass explicit intent to agents (not memory context)
   - Define workflow boundaries
   - Agents are responsible for reading memory and building their own context


#### Types

| Level | Name | Description | Human Involvement |
|-------|------|-------------|-------------------|
| **Level 1** | One-Shot Flows | Simple, single-task executions (fetch-issue, commits) | Direct input → output |
| **Level 2** | Composed Workflows | Multi-task workflows combining several steps | Human-in-the-loop |
| **Level 3** | Autonomous Execution | Goal-driven, runs to completion autonomously | Approval gates only |

#### Rules

- All system interactions start with a recipe
- Recipes orchestrate flow but never build agent context
- Recipes pass explicit intent and goals and steps; agents determine execution

#### Examples

- **Implement React Component**: discover → specify → design → build → run — [Full Recipe](../usage/recipes/implement-react-component.md)

- **Fix Bug**: discover (RCA) → specify (fix strategy) → design (approach) → build (TDD fix) → run (deploy) — [Full Recipe](../usage/recipes/fix-bug.md)

- **Product Feature Definition**: discover (intake) → specify (PRD) → design (tech feasibility) → build (backlog) → run (tracking) — [Full Recipe](../usage/recipes/product-feature-definition.md)

> **See also**: [Recipes Reference](../usage/recipes.md) for a complete list of available recipes.

---

### Sub-Agents: Authority Holders

**Role**: Autonomous decision-makers that own outcomes, not procedures.

#### Characteristics

- **Defined roles**: Like roles in an SDLC team (Tech Lead, Scrum Master, Tester, Developer, Builder, Validator, Designer, Specifier, Alchemist)
- **Context builders**: Read memory and construct their own execution context
- **Memory consumers**: Access to memory; read both short-term and long-term memory
- **Output generators**: Create results (generate outputs) and update memory
- **Boundary-aware**: Operate within defined guardrails and permissions

#### Responsibilities

1. **Accept Explicit Intent**
   - Receive clear goals from recipes or signals
   - Understand boundaries of operation

2. **Context Building**
   - Read short-term and long-term memory
   - Construct execution context
   - Coordinate with other agents as needed

3. **Decide Actions**
   - Determine what actions to take
   - Select appropriate skills and tools
   - Make decisions within role boundaries

4. **Execute Skills (1:many)**
   - Invoke **multiple skills** to accomplish their goal
   - Orchestrate skill sequence based on context
   - Pass context and patterns from memory to skills
   - Receive and process skill outputs

5. **Memory Updates**
   - **Short-term memory**: ALLOWED at any time
   - **Long-term memory**: RESTRICTED to high-order agents only
   - Output results for downstream consumption

#### Types

| Type | Purpose | Memory Access |
|------|---------|---------------|
| **AI-Native** | Capability-based agents aligned to SDLC steps | STM only |
| **Domain Stewards** | Specialized agents for specific domains (repo, project, deployment) | STM only |
| **High-Order** | Governance agents that can evolve organizational standards | STM + LTM |

> **See also**: [Agents Reference](../usage/agents.md) for complete agent listings.

#### Rules

- Standard agents may update STM freely
- Only high-order agents may update LTM
- Agents cannot receive signals directly (must go through recipes)
- Agents cannot bypass recipes
- Agents cannot access other agent contexts directly
- Agents own decisions, not procedures—they determine "how" based on context

> **See also**: [Agents Reference](../usage/agents.md) for a complete list of available agents.

---

### Skills: Memory-Backed Capabilities

**Role**: Reusable, bounded capabilities that execute work.

#### Characteristics

- **Bounded**: Single, well-defined capability with clear inputs and outputs
- **Stateless**: No memory of previous invocations
- **Deterministic**: Same inputs produce same outputs
- **Agent-invoked**: Never decide when to run; agents invoke them
- **Memory-backed**: Leverage LTM patterns for consistency

#### Responsibilities

1. **Execute Bounded Task**
   - Perform single, well-defined operation
   - Accept parameters from invoking agent
   - Return structured output

2. **Apply Patterns**
   - Use memory patterns provided by agent
   - Ensure consistency with organizational standards
   - Follow project conventions

3. **Report Results**
   - Return success/failure status
   - Provide output artifacts
   - Surface errors with context

#### Types

| Type | Purpose | Examples |
|------|---------|----------|
| **Analysis** | Investigate and understand | Code analysis, RCA, log reading, pattern investigation |
| **Specification** | Define requirements and designs | Specs, NFRs, validation criteria, technical designs |
| **Creation** | Build deliverables | Code, tests, deployments, releases, PRs |
| **Validation** | Verify quality | Testing, scanning, linting, reviewing |
| **Monitoring** | Observe and respond | Metrics tracking, alerting, incident response |

> **See also**: [Implement React Component Recipe](../usage/recipes/implement-react-component.md) for complete skill listings per step.

#### Rules

- Skills never decide when to run—agents invoke them
- Skills cannot invoke other skills
- Skills cannot update memory directly
- Skills cannot bypass agents
- Skills receive parameters and execute their bounded capability—nothing more
- Skills are trusted because they are bounded and repeatable

> **See also**: [Skills Reference](../usage/skills.md) for a complete list of available skills.

---

### Memory: The Organizational Brain

**Role**: Knowledge repository that remains pristine and component-agnostic.

#### Characteristics

- **Component-agnostic**: No awareness of agents, recipes, or signals
- **Pure knowledge**: Rules, compliance, patterns, architecture, technologies
- **Hierarchical**: Two types with different lifecycles and permissions
- **Version-controlled**: Stored in repository, tracked over time
- **Shared**: Available to all agents across sessions

#### Responsibilities

1. **Store Organizational Knowledge**
   - Maintain patterns, standards, and best practices
   - Preserve architectural decisions and rationale
   - Track compliance and regulatory requirements

2. **Provide Context to Agents**
   - Supply relevant patterns for decision-making
   - Enable consistent behavior across agents
   - Support deterministic outcomes

3. **Maintain Task State**
   - Track in-progress work (STM)
   - Store intermediate artifacts
   - Enable workflow continuity

#### Types

| Type | Lifecycle | Scope | Write Permission | Storage |
|------|-----------|-------|------------------|---------|
| **STM** | Branch/worktree | Single task | All agents | `.phoenix-os/project/` |
| **LTM** | Permanent | Organization-wide | High-order agents only | `core/memory/` |

**Short-Term Memory (STM)**

- **Lifecycle**: Created per recipe execution
- **Content**: Pre-loaded signals, active intents, execution state, outputs
- **Structure**:
  ```
  .phoenix-os/stm/{recipe-id}-{timestamp}/
  ├── state.md       # Execution state, interaction log
  ├── context.md     # Pre-loaded signals from radar matching
  ├── intents.md     # Active intents with confidence scores
  └── outputs/       # Results from each step
  ```

**Long-Term Memory (LTM)**

- **Lifecycle**: Persists across sessions and recipes
- **Two-part structure**:

**Engine** (`memory/engine/`):
| Content | Purpose |
|---------|---------|
| `intents/` | Intent patterns and routing rules |
| `flows/` | Orchestration patterns |
| `schemas/` | Data structure definitions |

**Vault** (`{user-vault}/`):
| Layer | Purpose |
|-------|---------|
| `radars/` | Classification lenses with keywords |
| `signals/` | Actual knowledge content |

**7 Strategic Radars** (classification dimensions):
1. Purpose — Mission, vision, strategy
2. Innovation — Disruption, experimentation
3. Digital Experience — UX, customer experience
4. AI/Intelligence — AI, ML, automation
5. Evolutionary Architecture — Scalability, patterns
6. Leadership — Team, culture, talent
7. Technology — Tools, platforms, stack

- **Storage**: Repository (version controlled)

#### Rules

- Memory contains **knowledge, not process**
- Memory has no awareness of agents, recipes, or signals
- Agents may update **STM freely**
- **LTM updates require high-order agent validation**
- STM is ALWAYS created when working in a branch or Git worktree
- STM is NEVER created outside of a branch/worktree
- STM MAY be promoted to LTM
- Memory enables deterministic adaptation

> **See also**: [Memory Reference](../usage/memory.md) for complete memory structure.

---

### How Components Connect

```
Signal: /implement-component "UserProfile"
    │
    ▼
Recipe: Implement React Component
    │
    ├──► DISCOVER ──► SPECIFY ──► DESIGN ──► BUILD ──► RUN
    │
    ▼
┌────────────────────────────────────────────────────────────────────┐
│  For each step:                                                    │
│                                                                    │
│  Recipe ──► Sub-Agent ──► Skill(s) ──► Execute                     │
│                 │                                                  │
│                 ▼                                                  │
│           Read Memory                                              │
│           (LTM + STM)                                              │
│                 │                                                  │
│                 ▼                                                  │
│           Build Context ──► Output ──► Write STM                   │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

| Step | Agent(s) | Skills (1:many) | Memory (Read) | Output |
|------|----------|-----------------|---------------|--------|
| **Discover** | Researcher | Prototyper, Designer | LTM: domain, patterns | STM: prototype.md |
| **Specify** | Specifier, Technical Analyst, Validator | See recipe for skill details | LTM: spec-templates, nfr, validation, project-mgmt; STM: prototype | STM: spec.md, nfr.md, validation.md; Project: stories |
| **Design** | Designer, Architect | See recipe for skills | LTM: design-system, react-patterns, security, perf, a11y; STM: spec, nfr, validation | STM: ux-design.md, tech-design.md |
| **Build** | Builder, Tester, DevOps, Reviewer | TDD→Testing→CI→PR→Merge (see recipe) | LTM: testing, quality, devops, pr-guidelines; STM: all designs | Project: code, tests, PR merged |
| **Run** | Deployer, Operator | Deploy→Monitor→Alert (see recipe) | LTM: deployment, observability, runbooks; STM: all | Project: release, deploy, monitoring |

> **See also**: [Implement React Component Recipe](../usage/recipes/implement-react-component.md) for detailed breakdown.

---

## 2. Explicit via Abstraction

**Core Principle**: Explicit rules + abstracted implementation = deterministic outputs. Agents know *what* to do explicitly; *how* they do it is abstracted through memory.

### Tenants

**What is Explicit**:
- The task to be performed
- The expected outcome
- The decision logic

**What is Abstracted**:
- Implementation details
- Tool selection
- Storage mechanisms
- Execution methods

**How We Achieve Determinism**:
- Rules stored in memory (LTM), not embedded in prompts
- Same rules applied consistently across all invocations
- Agent handles WHAT (decisions), system handles HOW (execution)
- Platform differences hidden — GitHub/Jira/Linear produce same logical output
- Version-controlled rules ensure reproducibility and auditability

**Key Insight**: Agents make decisions based on explicit rules from memory, but the implementation of those decisions is abstracted through memory layers.

---

### Why This Enables Determinism

The separation of explicit rules from abstracted implementation ensures:

- **Consistent decisions**: Same input always triggers same classification logic
- **Tool-agnostic results**: Output is identical whether using GitHub, Jira, or Linear
- **Reproducible workflows**: Any agent following the same rules produces the same outcome
- **Predictable behavior**: Business logic is explicit and auditable, not hidden in prompts

This is how Phoenix OS transforms non-deterministic AI behavior into enterprise-grade, predictable results. The agent's decision (WHAT) is deterministic because it follows explicit rules. The execution (HOW) is abstracted but consistent because it follows configured patterns.

```
Input: "Login crashes with long username"
    │
    ▼
Explicit Rules (Deterministic)
    ├─► Contains "crashes" → type: bug
    ├─► Contains "crashes" → priority: high
    └─► Title format applied → "Fix: Login crash with 50+ char username"
    │
    ▼
Abstracted Execution (Consistent)
    └─► Platform: GitHub/Jira/Linear → Same issue created
```

---

## 3. Error Context Standard

**Core Principle**: Every error provides complete resolution context.

### Required Elements

1. **What** failed
2. **Why** it might fail
3. **How** to fix
4. **Alternative** path
5. **Impact** (what this blocks)

### Example

```markdown
**Error Context**:
- What: GitHub CLI authentication failed
- Why: Token expired or not configured
- Fix: Run 'gh auth login' to re-authenticate
- Alternative: Use MCP server or provide PAT token
- Impact: Cannot create branches or update issues
```

---

## Component Interaction Matrix

| Component | Reads From | Writes To | Invokes | Invoked By |
|-----------|-----------|-----------|---------|------------|
| Signals | - | - | Recipes | External sources |
| Recipes | - | - | Sub-Agents | Signals |
| Sub-Agents | Memory (STM + LTM) | STM (docs), Decisions | Skills | Recipes |
| Skills | Parameters (from agent) | Files, Data, Status | - | Sub-Agents |
| Memory | - | - | - | Sub-Agents (read/write) |

**What Each Outputs**:

| Component | Output Type | Examples |
|-----------|-------------|----------|
| **Sub-Agents** | Memory documents, meta-docs | `analysis.md`, `spec.md`, decisions, recommendations |
| **Skills** | Actual/final artifacts | Code files, test files, PR URLs, execution status |

---

## Boundaries and Constraints

### What Memory CANNOT Do
- ❌ Invoke agents or skills
- ❌ Receive signals
- ❌ Contain recipe logic
- ❌ Know about system components

### What Recipes CANNOT Do
- ❌ Build agent inputs
- ❌ Construct outputs
- ❌ Update memory directly
- ❌ Execute agent or skill functions

### What Sub-Agents CANNOT Do (Standard)
- ❌ Update long-term memory
- ❌ Receive signals directly
- ❌ Bypass recipes
- ❌ Access other agent contexts directly

### What Sub-Agents CAN Do (High-Order Only)
- ✅ Update long-term memory (also can convert STM to LTM)
- ✅ Modify architecture patterns
- ✅ Update compliance rules

### What Skills CANNOT Do
- ❌ Decide when to run
- ❌ Invoke other skills
- ❌ Update memory directly
- ❌ Bypass agents

---

**Author**: Kapil Viren Ahuja
**Version**: 3.0.0
**Last Updated**: 2025-12-24
**Status**: Active
