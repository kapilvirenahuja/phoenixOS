# Vault Architecture

> How knowledge is organized, indexed, and retrieved in Phoenix OS

---

## Overview

The Vault is Phoenix OS's long-term memory (LTM). It stores accumulated knowledge organized for efficient retrieval during recipe execution.

## Core Principle: Separation of Classification and Content

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              VAULT                                       │
│                                                                         │
│  ┌─────────────────────────┐      ┌─────────────────────────────────┐  │
│  │        RADARS           │      │           SIGNALS               │  │
│  │   (Classification)      │      │         (Content)               │  │
│  │                         │      │                                 │  │
│  │  • Keywords             │──────│  • Actual knowledge             │  │
│  │  • Questions            │      │  • Implications                 │  │
│  │  • Signal Mappings      │      │  • Additional keywords          │  │
│  │                         │      │                                 │  │
│  └─────────────────────────┘      └─────────────────────────────────┘  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

**Radars** = Classification lenses (how to find knowledge)
**Signals** = Actual content (the knowledge itself)

---

## Directory Structure

```
{user-vault}/
├── radars/                         # 7 classification lenses
│   ├── ai-intelligence.md
│   ├── digital-experience.md
│   ├── evolutionary-architecture.md
│   ├── innovation.md
│   ├── leadership.md
│   ├── purpose.md
│   └── technology.md
│
└── signals/                        # Actual content (organized by radar)
    ├── ai/
    ├── evolutionary-architecture/
    ├── innovation/
    ├── leadership/
    └── technology/
```

---

## The 7 Strategic Radars

| Radar | Focus | Keywords Example |
|-------|-------|------------------|
| **Purpose / Why** | Mission, vision, strategic intent | mission, vision, strategy, north star |
| **Innovation** | New ideas, technologies, approaches | disrupt, emerging, breakthrough, experimental |
| **Digital Experience** | Customer and user experience | UX, customer experience, usability, journey |
| **AI / Intelligence** | AI, ML, intelligent automation | AI, machine learning, LLM, AI-powered |
| **Evolutionary Architecture** | System design, technical architecture | architecture, scalability, microservices, API |
| **Leadership** | Team, culture, organizational effectiveness | leadership, team, culture, hiring, talent |
| **Technology** | Tools, platforms, technical ecosystem | technology, tools, platform, stack, framework |

---

## Radar File Structure

Each radar file contains:

```markdown
# {Radar Name}

> {One-line focus}

---

## Focus
{What this radar monitors}

## Questions
- {Strategic question 1}
- {Strategic question 2}
- {Strategic question 3}

---

## Keywords

### Primary
{Core terms - high confidence match}

### Secondary
{Related terms - medium confidence match}

---

## Signal Mappings

| Signal | Path |
|--------|------|
| {Signal Name} | @{user-vault}/signals/{category}/{file}.md |

---

**Version**: X.X.X
**Last Updated**: YYYY-MM-DD
```

---

## Signal File Structure

Each signal file contains:

```markdown
# {Signal Title}

> {One-line summary}

---

## Content
{The actual knowledge/insight}

## Implications
{What this means, why it matters}

## Keywords
{Additional searchable terms}

---

**Captured**: {date}
**Source**: {reference or "internal"}
```

---

## Query Matching Flow

When a query arrives during STM initialization:

```
Query: "Help me build something AI-powered"
              │
              ▼
┌─────────────────────────────────────────────────────────────┐
│  STEP 1: SCAN ALL RADARS                                    │
│                                                             │
│  For each radar in {user-vault}/radars/:                    │
│    1. Load Keywords.Primary and Keywords.Secondary          │
│    2. Tokenize query                                        │
│    3. Check for keyword matches                             │
│                                                             │
│  Match rules:                                               │
│    Primary keyword match   → DIRECT (confidence: 0.9)       │
│    Secondary keyword match → DIRECT (confidence: 0.7)       │
│    No keyword match        → Check for peripheral           │
└─────────────────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────┐
│  STEP 2: LOAD MAPPED SIGNALS                                │
│                                                             │
│  For each radar with direct match:                          │
│    1. Read Signal Mappings table                            │
│    2. Load each signal file from {user-vault}/signals/      │
│    3. Extract content for STM                               │
└─────────────────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────┐
│  STEP 3: PERIPHERAL MATCHING                                │
│                                                             │
│  For each radar WITHOUT direct match:                       │
│    Check if any mapped signal is ALSO mapped to a           │
│    direct-matched radar → PERIPHERAL (confidence: 0.4)      │
└─────────────────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────┐
│  STEP 4: NO MATCH HANDLING                                  │
│                                                             │
│  If no radar has direct OR peripheral match:                │
│    → Set no_radar_match = true                              │
│    → Triggers clarify intent                                │
└─────────────────────────────────────────────────────────────┘
              │
              ▼
        STM context.md populated with matched signals
```

---

## Adding New Signals

### Step 1: Create Signal File

Create a new file in the appropriate signals folder:

```bash
{user-vault}/signals/{category}/{signal-name}.md
```

### Step 2: Add Radar Mapping

Update the relevant radar file(s) in `{user-vault}/radars/`:

```markdown
## Signal Mappings

| Signal | Path |
|--------|------|
| Existing Signal | @{user-vault}/signals/... |
| **New Signal** | @{user-vault}/signals/{category}/{signal-name}.md |  ← Add this
```

### Step 3: Done

The signal is now discoverable via the radar's keywords.

---

## Multi-Radar Signals

A signal can be mapped to multiple radars when it spans domains:

**Example**: "AI UX Anti-Patterns" is mapped to:
- AI/Intelligence radar (primary domain)
- Digital Experience radar (UX focus)

This enables peripheral matching: if a query matches AI radar, the UX radar gets a peripheral match via the shared signal.

---

## Key Design Decisions

### Why Separate Radars and Signals?

1. **Radars are stable** — The 7 strategic dimensions rarely change
2. **Signals grow** — New knowledge is added frequently
3. **Decoupling** — Adding signals doesn't require changing radar structure
4. **Multi-classification** — One signal can belong to multiple radars

### Why Keywords in Radars (not Signals)?

1. **Efficient scanning** — Only 7 radar files to scan, not hundreds of signals
2. **Controlled vocabulary** — Keywords are curated, not scattered
3. **Predictable matching** — Same keywords always route to same radar

### Why Signal Mappings (not Folder Structure)?

1. **Explicit relationships** — Clear which signals belong to which radar
2. **Multi-radar support** — One signal can be in multiple radar mappings
3. **Auditable** — Easy to see all signals for a radar in one place

---

## Integration with STM

During recipe execution, STM is populated via radar scanning:

```
LTM (Vault)                    STM (Session)
───────────                    ────────────
radars/                        .phoenix-os/stm/{session}/
  ├── ai-intelligence.md  ──┐
  └── ...                   │    context.md
                            │      ├── Direct Matches
signals/                    │      │   └── AI/Intelligence
  ├── ai/                   │      │       └── [signal content]
  │   └── *.md ─────────────┘      └── Peripheral Matches
  └── ...                              └── ...
```

Agents read from STM, not directly from Vault. This ensures:
- Relevant content is pre-filtered
- Context is session-specific
- Vault structure is abstracted from agents

---

**Version**: 1.0.0
**Last Updated**: 2026-01-05
**Part of**: Phoenix OS Engine
