# Clarify Intent Test Cases

> Regression tests for `clarify` intent via `strategy-guardian` agent

## Test Configuration

| Config | Value |
|--------|-------|
| Recipe | `consult-cto` |
| Intent | `clarify` |
| Agent | `strategy-guardian` |
| Status | ✅ Active |

---

## Test Case 1: AI Buzzword

### Input

```
Query: "Help me build something AI-powered"
Intent: clarify
```

### Expected Behavior

- Identify vague terms: "something", "AI-powered", "build"
- Ask max 3 focused questions
- Return `status: needs_clarification`

### Actual Output

**Vague Terms Identified:**

| Term | Problem |
|------|---------|
| "something" | No defined product, feature, or outcome |
| "AI-powered" | Buzzword without a specific problem to solve |
| "build" | Unclear scope - prototype? production system? MVP? |

**Questions Asked:**

1. What specific problem are you trying to solve?
   - Who experiences this problem today?
   - What do they currently do instead?
   - What makes this problem worth solving now?

2. Why do you believe AI is the right solution?
   - What task requires intelligence that can't be rule-based?
   - Have you validated that humans can't do this cheaper/better?
   - What happens when the AI is wrong?

3. What does success look like?
   - How will you measure if this works?
   - What's the minimum viable outcome you'd accept?
   - Who is the first user and what will they do with it?

**Output:**

```json
{
  "status": "needs_clarification",
  "findings": {
    "problem_definition": "Missing",
    "target_user": "Unknown",
    "success_criteria": "Undefined",
    "ai_justification": "Not established"
  },
  "next_steps": "Answer the 3 questions above"
}
```

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] At least 1 vague term identified
- [ ] Max 3 questions asked
- [ ] Questions are specific, not generic

---

## Test Case 2: Modernization Buzzword

### Input

```
Query: "I want to modernize our system"
Intent: clarify
```

### Expected Behavior

- Identify vague terms: "modernize", "our system"
- Ask about pain points, success metrics, scope
- Return `status: needs_clarification`

### Actual Output

**Vague Terms Identified:**

| Term | Problem |
|------|---------|
| "modernize" | Buzzword - could mean rewrite, refactor, re-platform, add features, improve performance, update UI, migrate cloud, adopt microservices |
| "our system" | Which system? What components? Current state? |
| "I want" | Who is "I"? Authority/budget? Stakeholders? |

**Questions Asked:**

1. What pain is "modern" solving?
   - Is it slow deployments?
   - Frequent outages?
   - Can't hire developers who know the stack?
   - Features take months instead of days?
   - Scaling costs are unsustainable?

2. What does success look like in 6 months?
   - Deploy frequency: X per day/week?
   - Response time: under X ms?
   - Developer onboarding: X days instead of X weeks?
   - Infrastructure cost: reduced by X%?

3. What's the scope and constraint?
   - Which system or component?
   - Budget range?
   - What can we NOT change?

**Output:**

```json
{
  "status": "needs_clarification",
  "findings": "Query is too vague to act on. 'Modernize' without pain points and success metrics leads to expensive rewrites that solve nothing.",
  "next_steps": "Answer the 3 questions above. Then validate whether modernization is the right approach."
}
```

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] "modernize" identified as vague
- [ ] Questions ask for measurable outcomes
- [ ] Warning about expensive failures included

---

## Test Case 3: Scalability Without Numbers

### Input

```
Query: "I need a scalable architecture"
Intent: clarify
```

### Expected Behavior

- Identify "scalable" as vague
- Ask for target numbers (users, requests, data volume)
- Return `status: needs_clarification`

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] "scalable" identified as vague
- [ ] Questions ask for specific numbers
- [ ] Probes for: current load, expected growth, budget constraints

---

## Test Case 4: Performance Without Metrics

### Input

```
Query: "Make my app faster"
Intent: clarify
```

### Expected Behavior

- Identify "faster" as vague
- Ask for current latency, acceptable latency, which operations
- Return `status: needs_clarification`

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] "faster" identified as vague
- [ ] Questions ask for latency numbers
- [ ] Probes for: current performance, target performance, bottleneck location

---

## Test Case 5: Zero Context

### Input

```
Query: "Help me with my project"
Intent: clarify
```

### Expected Behavior

- Identify entire query as lacking specifics
- Ask fundamental questions: what, who, why, when
- Return `status: needs_clarification`

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] Multiple vague terms identified
- [ ] Questions cover: problem, user, goal, constraints
- [ ] Does not make assumptions

---

## Test Case 6: Better Without Criteria

### Input

```
Query: "We need better infrastructure"
Intent: clarify
```

### Expected Behavior

- Identify "better" as subjective/vague
- Ask for measurable criteria
- Return `status: needs_clarification`

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] "better" identified as vague
- [ ] Questions ask for success metrics
- [ ] Probes for: current state, pain points, budget

---

## Test Case 7: MVP Without Problem

### Input

```
Query: "Build me an MVP"
Intent: clarify
```

### Expected Behavior

- Identify missing problem statement
- Ask what problem MVP solves, for whom
- Return `status: needs_clarification`

### Assertions

- [ ] `status` equals `needs_clarification`
- [ ] Missing problem identified
- [ ] Questions ask for: problem, target user, success criteria
- [ ] Does not jump to solution

---

## Non-Clarify Test Cases (Should NOT Trigger)

These queries should NOT trigger `clarify` intent:

| Query | Expected Intent |
|-------|-----------------|
| "Should I use Postgres or MongoDB for a 10K user B2B SaaS?" | `decide` |
| "Is my plan to use microservices for a 3-person team solid?" | `validate` |
| "Help me debug this authentication error in my Node.js app" | `consult` |
| "What's your perspective on the future of serverless?" | `advise` |

---

## Running Tests

### Manual Testing

```bash
# Invoke strategy-guardian with clarify intent
claude -p "Handle clarify intent for: '<query>'" --agent strategy-guardian
```

### Automated Testing (Future)

```python
# Pseudocode for automated test runner
def test_clarify_intent(query, expected_status):
    result = invoke_agent(
        agent="strategy-guardian",
        intent="clarify",
        query=query
    )
    assert result.status == expected_status
    assert len(result.vague_terms) > 0
    assert len(result.questions) <= 3
```

---

**Version**: 1.0.0
**Last Updated**: 2026-01-04
**Coverage**: 7 positive cases, 4 negative cases
