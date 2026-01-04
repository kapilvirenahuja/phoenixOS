# PCAM Model

> Mental model for understanding agentic systems

## Overview

PCAM describes the four fundamental capabilities that define intelligent, agentic behavior. Any system claiming to be "agentic" must demonstrate competence across all four dimensions.

## The Four Dimensions

### 1. Perception

**What it is**: The ability to sense, observe, and interpret signals from the environment.

**In agentic systems**:
- Detecting events (user prompts, file changes, git events, environment state)
- Understanding context and intent
- Recognizing patterns and anomalies
- Parsing structured and unstructured inputs

**Questions to ask**:
- What signals can this agent perceive?
- How does it interpret ambiguous inputs?
- What context does it gather before acting?

---

### 2. Cognition

**What it is**: The ability to reason, analyze, and make decisions based on perceived information.

**In agentic systems**:
- Classifying and categorizing inputs
- Evaluating options and trade-offs
- Planning sequences of actions
- Learning from outcomes and adapting

**Questions to ask**:
- How does this agent reason about the problem?
- What decision frameworks does it apply?
- How does it handle uncertainty?

---

### 3. Agency

**What it is**: The ability to take autonomous action and execute decisions.

**In agentic systems**:
- Invoking skills and tools
- Orchestrating multi-step workflows
- Delegating to other agents
- Persisting through obstacles

**Questions to ask**:
- What actions can this agent take?
- How autonomous is it?
- What are its boundaries and guardrails?

---

### 4. Manifestation

**What it is**: The ability to produce tangible outputs and observable results.

**In agentic systems**:
- Generating artifacts (code, documents, reports)
- Updating state (memory, databases, systems)
- Communicating results
- Creating lasting change

**Questions to ask**:
- What does this agent produce?
- How is success measured?
- What traces does it leave behind?

---

## Applying PCAM to Agent Design

When designing or evaluating an agent, assess each dimension:

| Dimension | Weak | Strong |
|-----------|------|--------|
| **Perception** | Only accepts explicit commands | Detects context, interprets intent, gathers signals |
| **Cognition** | Follows rigid rules | Reasons about trade-offs, adapts to context |
| **Agency** | Requires human approval for each step | Autonomous within defined boundaries |
| **Manifestation** | Produces verbose, unclear output | Creates actionable, measurable artifacts |

## PCAM in Phoenix OS

| Component | PCAM Role |
|-----------|-----------|
| **Signals** | Perception - entry points for environmental awareness |
| **Sub-Agents** | Cognition + Agency - reason and decide within domains |
| **Skills** | Agency + Manifestation - execute and produce outputs |
| **Memory** | Cognition - accumulated knowledge for reasoning |
| **Recipes** | Agency - orchestrated workflows |

---

**Source**: HowToArchitect Vault
**Version**: 1.0.0
**Last Updated**: 2026-01-02
