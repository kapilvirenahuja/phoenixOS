---
name: phoenix-context-initialize-stm
description: Initialize Short-Term Memory workspace for recipe execution. Use at the start of any Level 2+ recipe to boot the STM workspace, scan query against radars, and populate context with matched signals. This is always Step 0 of composed workflows.
---

# Initialize STM

Boot the Short-Term Memory workspace and populate it with relevant Vault content via radar scanning.

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

## Vault Structure

```
{user-vault}/
├── radars/                       # Classification lenses (keywords + mappings)
│   ├── ai-intelligence.md
│   ├── purpose.md
│   ├── innovation.md
│   ├── digital-experience.md
│   ├── evolutionary-architecture.md
│   ├── leadership.md
│   └── technology.md
│
└── signals/                      # Actual content (organized by radar)
    ├── ai/                       # AI/Intelligence signals
    │   ├── augmentation-principle.md
    │   ├── pcam-framework.md
    │   ├── risk-categories.md
    │   ├── build-vs-buy.md
    │   ├── llm-integration-patterns.md
    │   └── ux-anti-patterns.md
    ├── evolutionary-architecture/ # Architecture signals
    ├── innovation/               # Innovation signals
    ├── leadership/               # Leadership + mental models
    │   ├── pcam.md
    │   ├── flywheel.md
    │   └── strategic-radars.md
    └── technology/               # Technology signals
```

## Instructions

### Step 1: Create Workspace Structure

Create the STM workspace at `.phoenix-os/stm/{recipe-id}-{timestamp}/`:

```
.phoenix-os/stm/{recipe-id}-{timestamp}/
├── state.md           # Current execution state
├── context.md         # Matched signals from Vault
├── intents.md         # Active intent(s)
├── outputs/           # Outputs from each step
└── evidence/          # Supporting artifacts
```

---

### Step 2: Scan Query Against Radars

#### 2a: Load Radar Files

Read all radar files from `{user-vault}/radars/`. Each radar contains:
- **Keywords** (Primary and Secondary) — for matching
- **Signal Mappings** — paths to actual signal files

#### 2b: Direct Matching

For each radar file:

1. Extract `Keywords.Primary` and `Keywords.Secondary`
2. Tokenize the query
3. Check for keyword matches:

```
If query contains ANY keyword from radar.keywords.primary:
  → DIRECT MATCH (confidence: 0.9)
Else if query contains ANY keyword from radar.keywords.secondary:
  → DIRECT MATCH (confidence: 0.7)
Else:
  → No direct match for this radar
```

Record matches with the triggering keyword.

#### 2c: Load Mapped Signals

For each radar with a direct match:

1. Read the `Signal Mappings` table
2. Load each mapped signal file from `{user-vault}/signals/`
3. Extract signal content for STM

#### 2d: Peripheral Matching

For each radar that did NOT have a direct match:

1. Check if any of its mapped signals are also mapped to a direct-matched radar
2. If yes, include as peripheral match

#### 2e: No Match Handling

```
If no radar has direct OR peripheral match:
  → Set flag: no_radar_match = true
  → This will trigger clarify intent
```

---

### Step 3: Populate context.md

Write matched content to `context.md`:

```markdown
# STM Context

## Query

**Raw**: {verbatim query}
**Tokenized**: {list of significant terms}

---

## Radar Matches

### Direct Matches

#### {Radar Name} (confidence: {score})

**Matched keyword**: {the keyword that triggered the match}
**Source**: @{user-vault}/radars/{radar-file}.md

**Radar questions**:
- {question 1}
- {question 2}
- {question 3}

**Signals loaded**:

##### {Signal Title}
**Path**: @{user-vault}/signals/{path}

{Full signal content}

---

### Peripheral Matches

#### {Radar Name} (confidence: {score})

**Match reason**: {shared signal with direct-matched radar}

**Signals loaded**:
- {signal title} (shared with {radar})

---

### No Matches

{List radars with no match}

---

## Match Summary

| Radar | Match Type | Confidence | Trigger |
|-------|------------|------------|---------|
| AI/Intelligence | direct | 0.9 | "AI-powered" |
| Digital Experience | peripheral | 0.4 | shared signal |
| Innovation | none | - | - |
```

---

### Step 4: Capture Initial State

Write to `state.md`:

```markdown
# STM State

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

---

## Radar Scan Results

**Direct matches**: {count}
**Peripheral matches**: {count}
**Signals loaded**: {count}
**No match flag**: {true|false}

---

## Execution State

**Current Step**: 1 (Build Routing Plan)
**Status**: pending

### Step Log

| Timestamp | Step | Status | Notes |
|-----------|------|--------|-------|
| {ts} | 0: Initialize STM | complete | {n} radars matched, {m} signals loaded |
```

---

### Step 5: Initialize Intent State

Write to `intents.md`:

```markdown
# STM Intent State

## Status

**Status**: pending_identification
**Detected**: []
**Confidence**: null
**Sequence**: null

---

## Radar Context for Intent Detection

**Direct matched radars**: {list}
**Peripheral matched radars**: {list}
**No radar match**: {true|false} → triggers clarify if true

---

## Intent History

(Empty - pending initial identification)
```

---

## Outputs

| Output | Description |
|--------|-------------|
| `stm_path` | Path to initialized STM workspace |
| `radar_matches` | Object with direct and peripheral matches |
| `signals_loaded` | List of signal paths loaded into context |
| `no_radar_match` | Boolean - true if no radars matched |

## Success Criteria

- [ ] STM workspace directory created (with outputs/ and evidence/ subdirectories)
- [ ] All radar files scanned for keyword matches
- [ ] Mapped signals loaded for matched radars
- [ ] Signal content extracted to context.md
- [ ] Initial state captured in state.md
- [ ] Intent state initialized in intents.md

## Error Handling

| Error | Action |
|-------|--------|
| Cannot create workspace | Fail recipe, surface to user |
| Radar file missing | Warn, skip that radar, continue |
| Signal file missing | Warn, note in context, continue |
| No radar matches at all | Set flag, proceed (clarify will handle) |
| Prior session conflict | Prompt user: resume or start fresh |

---

**Version**: 3.0.0
**Last Updated**: 2026-01-05
