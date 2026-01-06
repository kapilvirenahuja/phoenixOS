---
name: consult-clarify-requirements
description: Generate signal-grounded clarifying questions based on request analysis. Second skill in the clarify chain - transforms analysis into actionable questions that unblock progress.
---

# Clarify Requirements

Generate focused, signal-grounded clarifying questions that resolve ambiguity and unblock progress. This is the **generation** phase - producing questions based on the analysis from `consult:analyze-request`.

## When to Use

- **After**: `consult:analyze-request` (requires analysis output as input)
- **Before**: User response → `consult:synthesize-response`
- **Trigger**: Analysis indicates `recommended_action: clarify` or `clarify_light`
- **By**: `phoenix:strategy-guardian` agent

## Position in Chain

```
┌─────────────────────────────────┐
│  consult:analyze-request        │
│  Output: analysis object        │
└─────────────────────────────────┘
      │
      ▼ analysis
┌─────────────────────────────────┐
│  consult:clarify-requirements   │  ◄── YOU ARE HERE
│  (Generation: What questions    │
│   should we ask?)               │
└─────────────────────────────────┘
      │
      ▼ questions
┌─────────────────────────────────┐
│  Present to user                │
│  User responds                  │
└─────────────────────────────────┘
      │
      ▼ user_response
┌─────────────────────────────────┐
│  consult:synthesize-response    │
│  (or re-analyze if still vague) │
└─────────────────────────────────┘
```

---

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `analysis` | Yes | Output from `consult:analyze-request` |
| `stm_path` | Yes | Path to STM workspace (for context.md signals) |
| `engine_intent_path` | Yes | Path to intent definition (for question shapes) |
| `max_questions` | No | Maximum questions to generate (default: 4) |
| `intent` | Yes | Current intent (`clarify`, `consult`, `decide`, etc.) |

---

## Instructions

### Step 1: Read Engine Question Shapes

Load the question framing structure from the intent definition:

**For `clarify` intent** (from `@memory/engine/intents/cto-intents.md`):

```markdown
Question shapes:
- "What specifically...?" — narrow scope
- "Who/what/when/where...?" — fill gaps
- "What does [term] mean to you?" — define terms
- "What would success look like?" — clarify outcomes
```

**For `consult` intent**:

```markdown
Question shapes:
- "What have you tried so far?" — understand current state
- "What specifically is blocking you?" — pinpoint the issue
- "What constraints are you working within?" — understand boundaries
- "What would 'solved' look like?" — clarify desired outcome
```

**For `decide` intent**:

```markdown
Question shapes:
- "What criteria matter most for this decision?" — establish framework
- "What are the constraints?" — understand boundaries
- "What happens if you choose wrong?" — assess reversibility
- "Who else is affected by this decision?" — identify stakeholders
```

Store the shapes for the current intent.

---

### Step 2: Read STM Signals

Load signals from `{stm_path}/context.md`:

```markdown
For each signal in STM:
  - Extract: title, path, content, implications
  - Index by: keywords, radar source
```

Build a signal lookup that can be queried by topic/keyword.

---

### Step 3: Group by Signal Lens (Signal-First Format)

Instead of generating individual questions, group questions by the **signal** that grounds them. This teaches the user the mental model while gathering information.

**3a: Identify unique signals from analysis**

```
From analysis.signal_mapping, extract unique signals:
  - augmentation-principle.md → covers: AI-powered buzzword, WHY dimension
  - pcam-framework.md → covers: WHAT dimension (type of system)
  - build-vs-buy.md → covers: HOW/CONSTRAINTS dimension
```

**3b: For each signal, build a question block**

A question block contains:
1. **Signal reference** — path to the signal
2. **Core insight** — the key message from the signal (1-2 sentences)
3. **Questions** — multiple questions derived from this signal's framework
4. **Examples** — concrete options for each question

```json
{
  "signal_path": "@{user-vault}/signals/ai/augmentation-principle.md",
  "radar": "AI / Intelligence",
  "core_insight": "AI should augment human capabilities, not replace judgment. Success = humans become more effective, not obsolete.",
  "questions": [
    {
      "id": "q1",
      "question": "What human capability should AI augment?",
      "dimension": "WHY",
      "examples": [
        "Research speed (help find answers faster)",
        "Attention/review (help catch errors)",
        "Pattern recognition (help spot trends)",
        "Creative throughput (help produce drafts)"
      ]
    },
    {
      "id": "q2",
      "question": "What does success look like?",
      "dimension": "WHY",
      "examples": [
        "Reduce task time from X to Y",
        "Enable non-experts to do Z",
        "Catch N% of issues before production"
      ]
    }
  ]
}
```

**3c: Create block for questions without signal grounding**

For dimensions with no relevant signal in STM:

```json
{
  "signal_path": null,
  "radar": null,
  "core_insight": "Context needed (no signal found — using logical reasoning)",
  "questions": [
    {
      "id": "q5",
      "question": "Who are the users or customers?",
      "dimension": "WHO",
      "examples": [
        "Internal team (developers, ops, support)",
        "Business customers (enterprise, SMB)",
        "Consumers (end users)",
        "Developers (API consumers)"
      ]
    }
  ]
}
```

---

### Step 4: Ensure Required Dimensions Coverage

The clarify intent requires: **WHAT, WHO, WHY, CONSTRAINTS**

Check that all required dimensions are covered across question blocks:

| Dimension | Required | Covered By |
|-----------|----------|------------|
| WHAT | ✓ | Signal block or context block |
| WHO | ✓ | Signal block or context block |
| WHY | ✓ | Signal block or context block |
| CONSTRAINTS | ✓ | Signal block or context block |
| WHEN | Optional | Include if relevant |
| WHERE | Optional | Include if relevant |

**If a required dimension is missing:**
- Add it to the "Context needed" block
- Use logical reasoning for why it matters

---

### Step 5: Order Question Blocks

Order blocks by signal relevance:

1. **Primary signal blocks** — Signals that directly address buzzwords or critical dimensions
2. **Supporting signal blocks** — Signals that provide additional frameworks
3. **Context block** — Questions without signal grounding (always last)

Within each block, questions are already ordered by the signal's logical flow.

---

### Step 6: Format Output (Signal-First Presentation)

Structure for user presentation:

```markdown
## From: AI Augmentation Principle
**Source**: @{user-vault}/signals/ai/augmentation-principle.md
**Radar**: AI / Intelligence

AI should augment human capabilities, not replace judgment.
Success = humans become more effective, not obsolete.

**Questions**:

1. **What human capability should AI augment?**
   - Research speed (help find answers faster)
   - Attention/review (help catch errors)
   - Pattern recognition (help spot trends)
   - Creative throughput (help produce drafts)

2. **What does success look like?**
   - Reduce task time from X to Y
   - Enable non-experts to do Z
   - Catch N% of issues before production

---

## From: PCAM Framework
**Source**: @{user-vault}/signals/ai/pcam-framework.md
**Radar**: AI / Intelligence

Four dimensions define AI systems: Perception, Cognition, Agency, Manifestation.
Most "AI features" are perception-only with no real agency.

**Questions**:

3. **What type of thing are you building?**
   - A product feature (e.g., AI-powered search)
   - A standalone tool (e.g., internal assistant)
   - A platform/API (e.g., AI service for other apps)
   - An agentic system (e.g., autonomous workflow)

4. **Which PCAM dimension is primary?**
   - Perception (understanding inputs, context)
   - Cognition (reasoning, deciding, planning)
   - Agency (taking actions, using tools)
   - Manifestation (producing artifacts, outputs)

---

## Context Needed
**Source**: No signal found — using logical reasoning

**Questions**:

5. **Who are the users or customers?**
   - Internal team (developers, ops, support)
   - Business customers (enterprise, SMB)
   - Consumers (end users)
   - Developers (API consumers)

6. **What constraints exist?**
   - Timeline (MVP in weeks vs production in months)
   - Budget ($0 free tier vs $50K/month inference)
   - Team (solo dev vs ML engineers)
   - Data (public only vs proprietary)
```

**Benefits of this format:**
- Shows user HOW questions derive from frameworks
- Educational — user learns the mental model
- Groups related questions logically
- Less repetitive than per-question citations

---

## Outputs

| Output | Type | Description |
|--------|------|-------------|
| `question_blocks` | array | Ordered list of signal-grouped question blocks |
| `total_questions` | int | Total number of questions across all blocks |
| `signal_coverage` | float | Percentage of questions with signal grounding |
| `dimensions_covered` | object | Which required dimensions are covered |
| `status` | enum | `needs_clarification` |

**Output Schema:**

```json
{
  "question_blocks": [
    {
      "id": "block-1",
      "signal_path": "@{user-vault}/signals/ai/augmentation-principle.md",
      "radar": "AI / Intelligence",
      "core_insight": "AI should augment human capabilities, not replace judgment. Success = humans become more effective, not obsolete.",
      "questions": [
        {
          "id": "q1",
          "question": "What human capability should AI augment?",
          "dimension": "WHY",
          "examples": ["Research speed", "Attention/review", "Pattern recognition", "Creative throughput"]
        },
        {
          "id": "q2",
          "question": "What does success look like?",
          "dimension": "WHY",
          "examples": ["Reduce time from X to Y", "Enable non-experts to Z", "Catch N% of issues"]
        }
      ]
    },
    {
      "id": "block-2",
      "signal_path": "@{user-vault}/signals/ai/pcam-framework.md",
      "radar": "AI / Intelligence",
      "core_insight": "Four dimensions: Perception, Cognition, Agency, Manifestation. Most AI features are perception-only.",
      "questions": [
        {
          "id": "q3",
          "question": "What type of thing are you building?",
          "dimension": "WHAT",
          "examples": ["Product feature", "Standalone tool", "Platform/API", "Agentic system"]
        },
        {
          "id": "q4",
          "question": "Which PCAM dimension is primary?",
          "dimension": "WHAT",
          "examples": ["Perception", "Cognition", "Agency", "Manifestation"]
        }
      ]
    },
    {
      "id": "block-context",
      "signal_path": null,
      "radar": null,
      "core_insight": "Context needed — using logical reasoning",
      "questions": [
        {
          "id": "q5",
          "question": "Who are the users or customers?",
          "dimension": "WHO",
          "examples": ["Internal team", "Business customers", "Consumers", "Developers"]
        },
        {
          "id": "q6",
          "question": "What constraints exist?",
          "dimension": "CONSTRAINTS",
          "examples": ["Timeline", "Budget", "Team", "Data"]
        }
      ]
    }
  ],
  "total_questions": 6,
  "signal_coverage": 0.67,
  "dimensions_covered": {
    "WHAT": true,
    "WHO": true,
    "WHY": true,
    "CONSTRAINTS": true
  },
  "dimensions_missing": [],
  "status": "needs_clarification"
}
```

---

## Question Quality Checklist

Before finalizing each question, verify:

- [ ] **Focused**: Asks one thing, not multiple things
- [ ] **Actionable**: User can answer it concretely
- [ ] **Unblocking**: Answering it enables progress
- [ ] **Grounded**: Either cites signal or explains logical reasoning
- [ ] **Exampled**: Provides concrete options to help user respond
- [ ] **Non-leading**: Doesn't push toward a specific answer

**Anti-patterns to avoid:**

| Anti-pattern | Example | Problem |
|--------------|---------|---------|
| Compound question | "What users and what scale?" | Two questions in one |
| Yes/no question | "Do you need AI?" | Doesn't gather information |
| Leading question | "Don't you think microservices would be better?" | Biases response |
| Vague question | "Tell me more about the project" | Too open-ended |
| Jargon-heavy | "What's your CQRS strategy?" | May confuse user |

---

## Success Criteria

- [ ] All required dimensions covered (WHAT, WHO, WHY, CONSTRAINTS)
- [ ] Questions grouped by signal lens (not individual citations)
- [ ] Each signal block has: path, core insight, derived questions
- [ ] Context block exists for questions without signal grounding
- [ ] Examples provided for each question
- [ ] Signal-first format used in presentation
- [ ] No forced/weak citations — logical reasoning is acceptable

---

## Error Handling

| Error | Action |
|-------|--------|
| No analysis provided | Fail with "Requires analyze-request output" |
| STM path not found | Generate questions without signal grounding |
| No buzzwords AND no missing dimensions | Return `status: proceed` (nothing to clarify) |
| Engine intent path not found | Use default question shapes |

---

## Integration Notes

### Receiving from analyze-request

```python
# Pseudo-code
analysis = consult_analyze_request(query, stm_path, intent)

if analysis.recommended_action in ["clarify", "clarify_light"]:
    questions = consult_clarify_requirements(
        analysis=analysis,
        stm_path=stm_path,
        engine_intent_path="@memory/engine/intents/cto-intents.md",
        max_questions=4,
        intent=intent
    )
```

### Passing to Agent for Presentation

The agent receives `question_blocks` and formats using the **signal-first** presentation:

```markdown
## From: AI Augmentation Principle
**Source**: @{user-vault}/signals/ai/augmentation-principle.md
**Radar**: AI / Intelligence

AI should augment human capabilities, not replace judgment.
Success = humans become more effective, not obsolete.

**Questions**:

1. **What human capability should AI augment?**
   - Research speed (help find answers faster)
   - Attention/review (help catch errors)
   - Pattern recognition (help spot trends)
   - Creative throughput (help produce drafts)

2. **What does success look like?**
   - Reduce task time from X to Y
   - Enable non-experts to do Z
   - Catch N% of issues before production

---

## From: PCAM Framework
**Source**: @{user-vault}/signals/ai/pcam-framework.md
**Radar**: AI / Intelligence

Four dimensions define AI systems: Perception, Cognition, Agency, Manifestation.
Most "AI features" are perception-only with no real agency.

**Questions**:

3. **What type of thing are you building?**
   - A product feature (e.g., AI-powered search)
   - A standalone tool (e.g., internal assistant)
   - A platform/API (e.g., AI service for other apps)
   - An agentic system (e.g., autonomous workflow)

---

## Context Needed
**Source**: No signal found — using logical reasoning

**Questions**:

4. **Who are the users or customers?**
   - Internal team (developers, ops, support)
   - Business customers (enterprise, SMB)
   - Consumers (end users)
   - Developers (API consumers)

5. **What constraints exist?**
   - Timeline (MVP in weeks vs production in months)
   - Budget ($0 free tier vs $50K/month inference)
   - Team (solo dev vs ML engineers)
   - Data (public only vs proprietary)
```

### After User Response

User response flows to:
1. `phoenix-context-update-stm` (update STM with response)
2. Either:
   - `consult:analyze-request` again (if response still vague)
   - `consult:synthesize-response` (if clarified enough to proceed)

---

**Version**: 2.0.0
**Last Updated**: 2026-01-05
**Changes**: Switched to signal-first format — questions grouped by signal lens, not individual citations. Ensures all required dimensions (WHAT, WHO, WHY, CONSTRAINTS) are covered.
