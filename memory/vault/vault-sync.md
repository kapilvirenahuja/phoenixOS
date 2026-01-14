# Vault Sync Status

> Tracks synchronization history between external sources and Phoenix OS vault

---

## Current State

| Metric | Value |
|--------|-------|
| **Last Sync** | 2026-01-13 |
| **Total Radars** | 7 |
| **Total Signals** | 92 |
| **Primary Source** | HowToArchitect Vault |

---

## Sync History

| Date | Source | Action | Signals Added | Notes |
|------|--------|--------|---------------|-------|
| 2026-01-13 | Readwise Articles | Radar expansion | +17 | Technology (6), Leadership (4), AI (4), Experience (3) from Readwise articles |
| 2026-01-13 | HowToArchitect Vault | Enhancement batch | +13 | Leadership (4), Architecture (3), AI (3), Experience (2), Innovation (2), enrichments (Blue Ocean, Storyscaping, Clean Architecture) |
| 2026-01-11 | HowToArchitect Vault | Bulk import | +31 | Batch 1-8: Radar keywords, AI/Architecture/Leadership/Innovation signals, Readwise books |
| 2026-01-05 | Manual | Initial setup | 31 | Base vault with core signals |

---

## Signal Inventory by Category

| Category | Count | Last Updated |
|----------|-------|--------------|
| `signals/ai/` | 36 | 2026-01-13 |
| `signals/architecture/` | 16 | 2026-01-13 |
| `signals/leadership/` | 20 | 2026-01-13 |
| `signals/innovation/` | 5 | 2026-01-13 |
| `signals/experience/` | 8 | 2026-01-13 |
| `signals/technology/` | 7 | 2026-01-13 |

---

## Pending Sync Items

| Source | Item | Priority | Notes |
|--------|------|----------|-------|
| HowToArchitect/Readwise | Remaining articles (~200) | Low | Filter for strategic relevance first |
| HowToArchitect/Readwise | Podcast highlights | Medium | Actionable insights |

---

## Source Folder Mappings

> Key folders to read when refreshing the vault from HowToArchitect

<!-- VAULT_ROOT: Your local path to the HowToArchitect Obsidian vault (e.g., Google Drive sync folder) -->

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

**Version**: 1.1.0
**Last Updated**: 2026-01-13
