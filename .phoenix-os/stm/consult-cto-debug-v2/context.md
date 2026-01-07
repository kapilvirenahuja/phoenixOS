# STM Context: Loaded Signals (FIXED Algorithm)

## Query

**Raw**: "Help me build something AI-powered"
**Analysis**: Vague request for AI-powered product/feature with no specifics

---

## Radar Matches (Semantic Analysis)

### Direct Matches (Score >= 0.6)

#### AI/Intelligence (confidence: 0.8)

**Match type**: Semantic direct match
**Reasoning**: Query directly involves AI capabilities; radar questions about AI augmentation and build-vs-buy directly apply
**Source**: @{user-vault}/radars/ai-intelligence.md

**Signals loaded**:

##### AI Augmentation Principle
**Path**: @{user-vault}/signals/ai/augmentation-principle.md

AI should augment human capabilities, not replace human judgment for critical decisions.

##### PCAM Framework
**Path**: @{user-vault}/signals/ai/pcam-framework.md

Four dimensions define agentic AI systems:
1. Perception — What signals can it sense?
2. Cognition — How does it reason?
3. Agency — What actions can it take?
4. Manifestation — What outputs does it produce?

##### Build vs Buy for AI
**Path**: @{user-vault}/signals/ai/build-vs-buy.md

Framework for deciding whether to build custom AI or buy/integrate existing solutions.

---

### Peripheral Matches (Score 0.3-0.59)

#### Evolutionary Architecture (confidence: 0.4)

**Match type**: Semantic peripheral match
**Reasoning**: Building AI-powered things requires architectural decisions; radar asks about AI-native technical capabilities
**Source**: @{user-vault}/radars/evolutionary-architecture.md

**Signals loaded via graph traversal**:

##### Composability Framework
**Path**: @{user-vault}/signals/architecture/composability.md

##### Sustainable Velocity Index
**Path**: @{user-vault}/signals/architecture/sustainable-velocity.md

---

#### Digital Experience (confidence: 0.4)

**Match type**: Semantic peripheral match
**Reasoning**: AI-powered products need UX decisions; radar explicitly covers "AI-powered experiences"
**Source**: @{user-vault}/radars/digital-experience.md

**Signals loaded via graph traversal**:

##### AI UX Anti-Patterns
**Path**: @{user-vault}/signals/ai/ux-anti-patterns.md

---

#### Innovation (confidence: 0.3)

**Match type**: Semantic peripheral match
**Reasoning**: Building new AI capabilities relates to innovation and prototyping
**Source**: @{user-vault}/radars/innovation.md

---

## Match Summary

| Radar | Score | Match Type | Method |
|-------|-------|------------|--------|
| AI/Intelligence | 0.8 | direct | Semantic: AI-powered + build |
| Evol. Architecture | 0.4 | peripheral | Semantic: AI-native architecture |
| Digital Experience | 0.4 | peripheral | Semantic: AI-powered experiences |
| Innovation | 0.3 | peripheral | Semantic: build/prototype |
| Technology | 0.1 | none | Below threshold |
| Purpose | 0.0 | none | No relevance |
| Leadership | 0.0 | none | No relevance |

---

## Comparison: Before vs After Fix

| Radar | BEFORE (keyword) | AFTER (semantic) |
|-------|------------------|------------------|
| AI/Intelligence | 0.9 direct | 0.8 direct |
| Evol. Architecture | ❌ 0 none | ✅ 0.4 peripheral |
| Digital Experience | ❌ 0 none | ✅ 0.4 peripheral |
| Innovation | 0.3 peripheral | 0.3 peripheral |
