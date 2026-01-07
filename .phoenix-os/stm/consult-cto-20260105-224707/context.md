# STM Context

## Query

**Raw**: what all should i consider when designing mint/burn mechanics for FIFA collect?
**Analysis**: User is designing tokenomics/supply mechanics for a digital collectibles platform (FIFA Collect). Seeking comprehensive design considerations.

---

## User Response [2026-01-05T22:55:00Z]

**In response to**: Key questions to sharpen design recommendations
**Content**:
1. Scale: 10M collectors
2. Integration: FIFA Collect has to be integrated (not greenfield)
3. Non-negotiable: FIFA Collect method
4. Behavior goal: More trading, increase ROI for FIFA

**Impact**: scope_change + decision
**Extracted facts**:
- Scale is massive (10M users) - infrastructure-critical
- Must integrate with existing FIFA Collect platform
- Existing FIFA Collect methods/patterns are constraints
- Business objective: maximize trading volume and FIFA revenue
- Not optimizing for holding or crafting - optimizing for TRADING

---

## Radar Matches

### Direct Matches (Semantic Relevance >= 0.6)

#### Evolutionary Architecture (confidence: 0.75)

**Match type**: Semantic direct match
**Reasoning**: "Designing mechanics" implies system design decisions. Mint/burn operations require architectural decisions around state management, scalability, transaction handling, and composability.
**Matched aspects**: ["system design", "architectural patterns", "composability", "scalability"]
**Source**: @{user-vault}/radars/evolutionary-architecture.md

**Radar questions**:
- How does this affect our architectural decisions?
- What technical capabilities do we need for AI-native development?
- How do we evolve without disruption?
- Are we building composable or monolithic?
- What is our sustainable velocity index?

**Signals loaded**:

##### Composability Framework
**Path**: @{user-vault}/signals/architecture/composability.md

True composability requires two dimensions:

**1. Technology Composability:**
- Pluggable components
- No vendor lock-in
- API-first architecture
- Mix and match best-of-breed

**2. Experience Composability:**
- Business user speed
- Non-technical content assembly
- Rapid experience iteration
- Decoupled from developer cycles

**Common confusion:**
- "Headless" alone is not composability
- "MACH" is a direction, not a destination
- Technology composability without experience composability leaves business users dependent on developers

**Implications:**
- Evaluate both dimensions independently
- Technology flexibility alone is insufficient
- Empower business users, not just developers
- Composable architecture enables faster iteration

---

##### Sustainable Velocity Index
**Path**: @{user-vault}/signals/architecture/sustainable-velocity.md

A formula for measuring velocity that accounts for the hidden cost of technical debt:

```
SVI = Velocity(Sprint N) / (Velocity(Sprint N+4) + Cost of Tech Debt Incurred)
```

**Why it matters:**
- Raw velocity metrics are misleading
- Technical debt shows up in future sprints
- Unsustainable velocity borrows from the future
- Teams can appear productive while accruing debt

**What a healthy SVI looks like:**
- SVI > 1.0: Paying down debt faster than accruing
- SVI = 1.0: Stable (neither accruing nor paying down)
- SVI < 1.0: Accruing debt, velocity will decline

---

##### DevOps 3-4-5 Method
**Path**: @{user-vault}/signals/architecture/devops-345.md

**The 3 Ways:**
1. Flow — Optimize for fast, smooth delivery
2. Feedback — Create rapid feedback loops
3. Continuous Experimentation — Enable learning and improvement

**The 4 Metrics (DORA):**
1. Deployment Frequency
2. Lead Time for Changes
3. Mean Time to Recovery (MTTR)
4. Change Failure Rate

**The 5 Ideals:**
1. Locality and Simplicity
2. Focus, Flow, and Joy
3. Improvement of Daily Work
4. Psychological Safety
5. Customer Focus

---

#### Technology (confidence: 0.65)

**Match type**: Semantic direct match
**Reasoning**: Implementing mint/burn mechanics requires technology platform decisions—blockchain vs. traditional database, vendor selection for tokenomics infrastructure, build vs buy decisions.
**Matched aspects**: ["platform selection", "build vs buy", "technology stack", "vendor decisions"]
**Source**: @{user-vault}/radars/technology.md

**Radar questions**:
- Should we adopt this technology?
- How does this fit our tech stack?
- What build vs. buy decisions does this inform?
- Is this commoditized enough to buy or strategic enough to build?
- What's the vendor lock-in risk?

**Signals loaded**:

##### Build vs Buy for AI
**Path**: @{user-vault}/signals/ai/build-vs-buy.md

Decision framework for AI components:

- **Buy/API**: Commoditized capabilities (transcription, translation, general chat)
- **Fine-tune**: Domain-specific behavior on commodity models
- **Build**: Proprietary advantage or unique data moat

Most teams over-index on "build" when "buy + fine-tune" is faster and cheaper.

**Implications:**
- Default to API-first unless you have unique data or capability requirements
- Fine-tuning is the middle ground most teams underutilize
- Building from scratch requires ML engineering expertise most teams lack

---

### Peripheral Matches (Semantic Relevance 0.3-0.59)

#### Digital Experience (confidence: 0.55)

**Match type**: Semantic peripheral match
**Reasoning**: Mint/burn mechanics directly affect collector experience—how users perceive scarcity, value, and engagement. UX friction in minting/burning impacts satisfaction.
**Matched aspects**: ["user experience", "friction points", "engagement", "value perception"]
**Source**: @{user-vault}/radars/digital-experience.md

**Radar questions**:
- How does this improve customer experience?
- What friction does this remove?
- How do customers expect to interact with AI?
- Are we building experience-based or price-based differentiation?
- What story are we enabling customers to tell?

**Signals loaded**:

##### Storyscaping Framework
**Path**: @{user-vault}/signals/experience/storyscaping.md

Four approaches to marketing differentiation:

1. **Price-based differentiation** — Race to the bottom
2. **Story-based differentiation** — Compelling narrative, builds connection
3. **Experience-based differentiation** — Memorable interactions, reduces friction
4. **Storyscaping differentiation** (highest level) — Create a world people want to be part of

**Key insight:** When its an experience, you are the Maker. When someone tells you a story, you are listening to a Teller. Making is more powerful than telling.

**Process:**
1. Immerse (research, audits, workshops)
2. Assess (analysis, distillation)
3. Originate (purpose workshop, strategy creation)
4. Articulate (blueprints, presentations, comms)

---

#### Innovation (confidence: 0.50)

**Match type**: Semantic peripheral match
**Reasoning**: Designing new tokenomics mechanics involves experimental/innovative approaches. Digital collectibles space is rapidly evolving with new patterns.
**Matched aspects**: ["new mechanics", "experimental approaches", "emerging patterns"]
**Source**: @{user-vault}/radars/innovation.md

**Radar questions**:
- What new capability does this enable?
- How might this disrupt our current approach?
- What experiments should we run?
- Is this innovation theater or real value creation?
- What's the IWHT factor (I Wouldn't Have Thought)?

**Signals loaded**:

##### ADAPT System
**Path**: @{user-vault}/signals/innovation/adapt-system.md

Five-component strategy for the AI era:

- **A - Agents**: Build custom agents that build agents
- **D - Deprecate**: Unlearn outdated skills systematically
- **A - Actionable Pipelines**: Systematize agentic workflows
- **P - Power Interfaces**: High-bandwidth agent orchestration
- **T - Throttle**: Maximize compute utilization, optimize costs

---

#### Purpose / Why (confidence: 0.45)

**Match type**: Semantic peripheral match
**Reasoning**: Mint/burn mechanics should serve strategic purpose—what value do they create for collectors and the platform? How do they align with platform mission?
**Matched aspects**: ["strategic intent", "value creation", "mission alignment"]
**Source**: @{user-vault}/radars/purpose.md

**Radar questions**:
- Why does this matter to our mission?
- How does this affect our strategic positioning?
- What does this mean for our long-term vision?
- Is the flywheel spinning in service of our purpose?
- Are we building a world people want to be part of?

**Signals loaded**:

##### Flywheel Protocol
**Path**: @{user-vault}/signals/leadership/flywheel.md

Mental model for building sustainable momentum through Build-Share-Learn cycles.

**Core Principles:**
1. **Customer Zero**: Be your own first customer
2. **Daily Accountability**: Small, consistent actions compound
3. **Momentum Building**: Each action makes the next easier

**Flywheel Stages:**
- BUILD → SHARE → LEARN → (repeat, each cycle easier)

**Anti-Patterns:**
- Big Bang Launches (all-or-nothing bets)
- Perfection Paralysis (waiting until "ready")
- Vanity Metrics (measuring what doesn't matter)
- Isolated Building (no feedback loops)
- Burnout Sprints (unsustainable intensity)

---

### No Matches (Semantic Relevance < 0.3)

- AI / Intelligence (relevance: 0.25) — No AI/ML component in query
- Leadership (relevance: 0.20) — No team/organizational context

---

## Match Summary

| Radar | Match Type | Confidence | Method |
|-------|------------|------------|--------|
| Evolutionary Architecture | direct | 0.75 | Semantic: "designing mechanics" |
| Technology | direct | 0.65 | Semantic: "platform decisions" |
| Digital Experience | peripheral | 0.55 | Semantic: "collector experience" |
| Innovation | peripheral | 0.50 | Semantic: "new mechanics" |
| Purpose / Why | peripheral | 0.45 | Semantic: "strategic purpose" |
| AI / Intelligence | none | 0.25 | Below threshold |
| Leadership | none | 0.20 | Below threshold |

---

## Traversal Statistics

**Depth reached**: 1
**Radars loaded**: 5
**Signals loaded**: 7
**Graph traversal stops**: No additional radars discovered via signal cross-references
