---
name: context-initialize-stm
description: Initialize Short-Term Memory workspace for recipe execution. Use at the start of any Level 2+ recipe to boot the STM workspace, load relevant LTM patterns (intents, practices, frameworks), and capture initial state. This is always Step 1 of composed workflows.
---

# Initialize STM

Boot the Short-Term Memory workspace so agents have a prepared context to work with.

## When to Use

- **Always**: First step of every Level 2+ recipe
- **Once**: Only invoked once per recipe execution
- **By**: Recipe (not agents)

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `recipe_id` | Yes | Identifier for the recipe being executed |
| `signal` | Yes | The incoming signal (query, source, metadata) |
| `user_context` | No | Known user information (role, history, preferences) |

## Instructions

### Step 1: Create Workspace Structure

Create the STM workspace at `.phoenix-os/stm/{recipe-id}-{timestamp}/`:

```
.phoenix-os/stm/{recipe-id}-{timestamp}/
├── state.md           # Current execution state
├── context.md         # Accumulated context
├── intents.md         # Active intent(s)
├── outputs/           # Outputs from each step
└── evidence/          # Supporting artifacts
```

### Step 2: Load Relevant LTM Patterns

Based on recipe type, load from `memory/ltm/`:
- Intent patterns (`intents/`)
- Domain practices (`practices/`)
- Mental models (`mental-models/`)
- Relevant frameworks

### Step 3: Capture Initial State

Write to `state.md`:

```markdown
## Initial State

**Timestamp**: {ISO timestamp}
**Recipe**: {recipe_id}
**Signal Source**: {user_prompt | schedule | webhook}

### Raw Query
{verbatim user input}

### Environment
- Working directory: {path}
- Git branch: {branch}
- Prior session: {session_id or null}
```

### Step 4: Initialize Intent State

Write to `intents.md`:

```markdown
## Intent State

**Status**: pending_identification
**Detected**: []
**Confidence**: null
**Sequence**: null
```

## Outputs

| Output | Description |
|--------|-------------|
| `stm_path` | Path to initialized STM workspace |
| `loaded_patterns` | LTM patterns loaded for this context |
| `initial_state` | Captured initial state object |

## Success Criteria

- [ ] STM workspace directory created
- [ ] LTM patterns loaded and available
- [ ] Initial state captured in state.md
- [ ] Intent state initialized as pending

## Error Handling

| Error | Action |
|-------|--------|
| Cannot create workspace | Fail recipe, surface to user |
| LTM patterns not found | Warn, proceed with defaults |
| Prior session conflict | Prompt user: resume or start fresh |
