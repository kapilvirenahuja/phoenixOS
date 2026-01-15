# Vault Sync Status

> Tracks synchronization history between external sources and Phoenix OS vault

---

## Current State

| Metric | Value |
|--------|-------|
| **Last Sync** | 2026-01-15 |
| **Total Radars** | 7 |
| **Total Signals** | 173 |
| **Primary Source** | HowToArchitect Vault |

---

## Sync History

| Date | Source | Action | Signals Added | Notes |
|------|--------|--------|---------------|-------|
| 2026-01-15 | Inception/Frames + Organizing Ideas + Readwise | Full v2 sync | +15 | AI (4): context-engineering-production, enterprise-ai-reality, sdlc-transformation-narrative, agent-architecture-comprehensive; Architecture (3): phoenixos-architecture, phoenixos-philosophy, architecture-thinking-art; Leadership (5): hands-on-cto-series, engineering-shift, architect-roles-decoded, product-thinking-synthesis; Experience (3): agentic-cx-patterns, dxp-composability-challenges, storytelling-dxp-framework; Innovation (1): strategic-bets-2026 |
| 2026-01-14 | Inception/Frames | Full audit sync | +6 | AI (3): ai-roles-maturity, ai-native-sdlc-maturity, phoenix-autonomy-ladder; Leadership (3): five-skills-to-unlearn, specifier-designer-builder, ai-leadership-maturity |
| 2026-01-13 | Readwise Articles | Radar expansion | +17 | Technology (6), Leadership (4), AI (4), Experience (3) from Readwise articles |
| 2026-01-13 | HowToArchitect Vault | Enhancement batch | +13 | Leadership (4), Architecture (3), AI (3), Experience (2), Innovation (2), enrichments (Blue Ocean, Storyscaping, Clean Architecture) |
| 2026-01-11 | HowToArchitect Vault | Bulk import | +31 | Batch 1-8: Radar keywords, AI/Architecture/Leadership/Innovation signals, Readwise books |
| 2026-01-05 | Manual | Initial setup | 31 | Base vault with core signals |

---

## Signal Inventory by Category

| Category | Count | Last Updated |
|----------|-------|--------------|
| `signals/ai/` | 55 | 2026-01-15 |
| `signals/leadership/` | 51 | 2026-01-15 |
| `signals/architecture/` | 34 | 2026-01-15 |
| `signals/innovation/` | 13 | 2026-01-15 |
| `signals/experience/` | 12 | 2026-01-15 |
| `signals/technology/` | 8 | 2026-01-15 |

---

## Pending Sync Items

| Source | Item | Priority | Notes |
|--------|------|----------|-------|
| HowToArchitect/Readwise | Remaining articles (~500) | Low | Most already covered or low strategic value |
| HowToArchitect/Readwise | Podcast highlights (16) | Low | Tactical content, already well-represented |
| HowToArchitect/Inception | Organizing Ideas (11) | Low | Mostly WIP project documents |

**Full Audit Complete (2026-01-14)**: Source vault (773 files) audited. High-value content extracted. Remaining items are either already covered, WIP, or low strategic priority.

---

## Source Folder Mappings

> Key folders to read when refreshing the vault from HowToArchitect

<!-- VAULT_ROOT: Defined in .vault-config.local (gitignored) -->

### Radars Source

| Local | External Source |
|-------|-----------------|
| `radars/` | `{VAULT_ROOT}/Serendipity/Story Scape/Radars` |

### Signals Sources

| Local | External Source | Content Type |
|-------|-----------------|--------------|
| `signals/` | `{VAULT_ROOT}/Extraction` | Extracted insights and mental models |
| `signals/` | `{VAULT_ROOT}/z - Others/Sources/Readwise` | Book highlights, articles, podcasts |
| `signals/` | `{VAULT_ROOT}/Inception` | Original ideas and frameworks |

---

## Sync Protocol

When syncing from external sources:

1. **Pre-sync**: Record current signal count
2. **Convert format**: Obsidian → Phoenix (see format below)
3. **Categorize**: Assign to appropriate `signals/` folder based on radar tags
4. **Map**: Update radar Signal Mappings tables
5. **Post-sync**: Update this file with new counts and date

### Format Conversion (Obsidian → Phoenix)

```markdown
# Obsidian format:
---
tags: extraction/signals
radar: "[[0004 - Artificial Intelligence]]"
---

# Phoenix format:
# Signal Title

> One-line summary

## Content
{content}

## Implications
{implications}

## Keywords
{keywords}

---
**Captured**: {date}
**Source**: {source}
```

---

**Version**: 1.4.0
**Last Updated**: 2026-01-15
**Changes**: v2 full sync complete. Added 15 signals from Inception/Frames (Phase 1), Organizing Ideas (Phase 2), and Readwise Articles/Podcasts (Phases 5-6). Books (Phase 3) had minimal highlights - content already covered by existing signals. Total signals: 173.
