# Vault Import Status

> Tracking signal imports from HowToArchitect Readwise vault to Phoenix OS LTM

---

## Source Vault

<!-- VAULT_ROOT: Defined in .vault-config.local (gitignored) -->

**Location**: `{VAULT_ROOT}/`

**Subfolders**:
- `{VAULT_ROOT}/z - Others/Sources/Readwise/Full Document Contents/Articles/` — 358 articles
- `{VAULT_ROOT}/Extraction/Signals/` — 120 extraction signals (already processed in earlier sessions)

---

## Current Status

| Metric | Value |
|--------|-------|
| **Total Articles in Source** | 358 |
| **Total Signals in Phoenix Vault** | 152 |
| **Radars** | 7 (all updated) |
| **Last Updated** | 2026-01-13 |

---

## Signals Created This Session

| Signal | Path | Source Article |
|--------|------|----------------|
| Legacy Displacement Patterns | `signals/architecture/legacy-displacement-patterns.md` | Patterns of Legacy Displacement (martinfowler.com) |
| Mental Models Compilation | `signals/leadership/mental-models-compilation.md` | 12 Mental models I frequently draw on |
| Delegation Framework Details | `signals/leadership/delegation-framework-details.md` | 3 Delegation Frameworks |
| CTO vs VP Engineering | `signals/leadership/cto-vs-vpe.md` | CTO vs. VP of Engineering |
| Assertions Framework | `signals/leadership/assertions-framework.md` | Why High Performers Make Assertions |
| Modular Monolith Architecture | `signals/architecture/modular-monolith.md` | When and why Modular Monolith is better |
| Technology Vision P6 Process | `signals/leadership/technology-vision-p6.md` | Technology Vision: How to Create an Excellent Vision & Strategy |
| Enterprise Roadmapping | `signals/architecture/enterprise-roadmapping.md` | Mapping the Future: Using Capability, Product and Technology Roadmaps |
| Agile Architect Role | `signals/leadership/agile-architect-role.md` | The Changing Role of Architect in an Agile World |
| AI-First Design Principles | `signals/ai/ai-first-design-principles.md` | Decoding the AI Experience: Design Principles for an AI-First World |
| Startup Scaleup Phases | `signals/innovation/startup-scaleup-phases.md` | Four Phases of a Startup |
| Hexagonal Architecture | `signals/architecture/hexagonal-architecture.md` | Hexagonal Architecture, There Are Always Two Sides |

---

## Articles Reviewed (Already Have Signals)

These articles were reviewed but signals already exist:

| Article | Existing Signal |
|---------|-----------------|
| Six Domains of Leadership | `signals/leadership/six-domains-leadership.md` |
| Storytelling That Drives Bold Change | `signals/leadership/storytelling-for-change.md` |
| Team Topologies | `signals/leadership/team-topologies.md` |
| Feature Toggles | `signals/architecture/feature-toggles.md` |
| High Agency | `signals/leadership/high-agency.md` |
| First Principles Thinking | `signals/innovation/first-principles-thinking.md` |
| Strategic Thinking Skills | `signals/leadership/strategic-thinking-skills.md` |
| 17 DevOps Metrics | `signals/architecture/devops-metrics.md` |
| Span of Control | `signals/leadership/span-of-control.md` |
| How Will You Measure Your Life | `signals/leadership/measure-your-life.md` |
| Say-Do Gap | `signals/leadership/say-do-gap.md` |
| Systems Thinking | `signals/leadership/systems-thinking.md` |
| Anatomy of Agentic AI | `signals/ai/agentic-systems-*.md` |
| Pyramid of Coding Principles | `signals/architecture/coding-principles-pyramid.md` |
| Tech Debt Spiral | `signals/architecture/tech-debt-spiral.md` |
| Vibe Coding | `signals/ai/vibe-coding-risks.md` |
| Microservices vs Monolith | `signals/architecture/microservices-evolution.md` |
| 6 Principles of Engineering Leadership | `signals/leadership/engineering-leadership-principles.md` |
| Creating Integrated Business Tech Strategy | `signals/architecture/integrated-tech-strategy.md` |
| How Top Engineering Leaders Build Teams | `signals/leadership/high-performance-teams.md` |
| Why Leadership Teams Fail | `signals/leadership/leadership-team-dysfunctions.md` |
| Why People Really Quit | `signals/leadership/thought-partnership.md` |
| What Is an Innovation Lab | `signals/innovation/innovation-labs.md` |

---

## Radar Versions

| Radar | Current Version | Signals Count |
|-------|-----------------|---------------|
| Leadership | 4.3.0 | 40+ |
| Evolutionary Architecture | 4.2.0 | 37+ |
| AI / Intelligence | 3.9.0 | 40+ |
| Innovation | 3.8.0 | 20+ |
| Digital Experience | 3.6.0 | 12+ |
| Technology | 3.5.0 | 15+ |
| Purpose | 2.0.0 | 5+ |

---

## How to Add New Articles

When new articles are added to the Readwise vault:

1. Read the article from the source path
2. Check if signal already exists (search by topic/keywords)
3. If new, create signal in appropriate `signals/{category}/` folder
4. Update relevant radar(s) in `radars/` with new signal mapping
5. Add entry to this log

### Signal Format

```markdown
# Signal Title

> One-line summary quote

---

## Content

{Main content with tables, frameworks, key points}

---

## Implications

- Implication 1
- Implication 2

## Keywords

- keyword1
- keyword2

---

**Captured**: {date}
**Source**: {author} / {publication} / Readwise

**Radars**: {Radar1}, {Radar2}
```

---

## Notes

- Many Readwise articles are shorter versions or summaries — the signal should capture the actionable frameworks
- Articles often map to multiple radars (e.g., AI + Leadership for AI governance topics)
- Some articles are too short (<500 chars) to warrant standalone signals
- Book summaries and podcast notes are valuable but require distillation into key frameworks

---

**Last Import Session**: 2026-01-13
**Next Action**: Continue processing remaining articles (many already have signals)

## Processing Notes

- Many Readwise articles are summaries of concepts that already exist as signals
- Priority should be given to articles with unique frameworks/models
- Articles that are methodological overviews (like Platform Design Toolkit) may not need standalone signals if the key concepts are covered elsewhere

