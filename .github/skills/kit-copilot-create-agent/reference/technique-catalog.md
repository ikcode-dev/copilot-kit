# Problem-Solving Technique Catalog

Reference for selecting which thinking and problem-solving techniques to include in a custom agent's body. Each agent should have 2-4 techniques matched to its role.

**Principle:** Agents should always announce which technique they're using (transparency). Include trigger conditions so the agent knows WHEN to apply each technique.

## Table of Contents

- [Role-Technique Matching Guide](#role-technique-matching-guide)
- [Root Cause Analysis Techniques](#root-cause-analysis-techniques)
- [Decomposition Techniques](#decomposition-techniques)
- [Adversarial Thinking Techniques](#adversarial-thinking-techniques)
- [Estimation & Feasibility Techniques](#estimation--feasibility-techniques)
- [Creative & Exploratory Techniques](#creative--exploratory-techniques)
- [Structured Reasoning Techniques](#structured-reasoning-techniques)

## Role-Technique Matching Guide

Use this table to select 2-4 techniques appropriate for the agent's role:

| Agent Role Type | Primary Techniques | Secondary Techniques |
|---|---|---|
| **Engineers / Implementers** | Five Whys, Divide and Conquer | Rubber Duck Debugging, Back-of-Envelope Estimation |
| **Planners / Architects** | MECE Analysis, Dependency Mapping, Interface-First Design | Requirement Decomposition, Assumption Surfacing |
| **Reviewers / Auditors** | Inversion (Pre-mortem), Checklist Verification | Assumption Surfacing, MECE Analysis |
| **Debuggers / Troubleshooters** | Five Whys, Divide and Conquer, Rubber Duck Debugging | Constraint Relaxation |
| **Researchers / Analysts** | First Principles, Socratic Questioning | MECE Analysis, Assumption Surfacing |
| **Generalists / Full-stack** | First Principles, Five Whys, Divide and Conquer | Constraint Relaxation |
| **Orchestrators / Coordinators** | Requirement Decomposition, Dependency Mapping | MECE Analysis, Pre-mortem |
| **Mentors / Teaching Agents** | Socratic Questioning, First Principles | Rubber Duck Debugging |

---

## Root Cause Analysis Techniques

### Five Whys

**Category:** Root Cause Analysis
**Best for:** Engineers, Debuggers, Generalists
**When to use:** Debugging issues, understanding why something failed, tracing unexpected behavior.

**Methodology:** Ask "Why?" repeatedly (typically 5 times) to drill down from symptoms to root cause. Stop when you reach an actionable root cause.

**Example:**
```
Problem: API returns 500 error
→ Why? Database query fails
→ Why? Connection pool exhausted
→ Why? Connections not being released
→ Why? Missing `finally` block in error handler
→ Why? Code was copy-pasted without understanding resource cleanup
Root cause: Missing connection cleanup in error handling path
```

**Template for agent body:**
```markdown
### Five Whys (Root Cause Analysis)
**When to use:** Debugging issues, understanding failures, tracing unexpected behavior.

Ask "Why?" five times to drill from symptoms to root cause. Share each level with the user.
```

### Rubber Duck Debugging

**Category:** Root Cause Analysis
**Best for:** Engineers, Debuggers, Mentors
**When to use:** When stuck on a problem that "should work" but doesn't, or when the bug is elusive.

**Methodology:** Explain the code line-by-line to the user, stating what you expect to happen at each step. The act of explaining often reveals the discrepancy between expected and actual behavior.

**Template for agent body:**
```markdown
### Rubber Duck Debugging
**When to use:** When stuck on elusive bugs or code that "should work."

Explain the code line-by-line, stating expected behavior at each step. Share the explanation — the bug often reveals itself through articulation.
```

---

## Decomposition Techniques

### Divide and Conquer

**Category:** Decomposition
**Best for:** Engineers, Debuggers
**When to use:** Large or complex problems, performance issues, debugging in large codebases.

**Methodology:**
1. Split the problem space in half
2. Determine which half contains the issue
3. Repeat until the problem is isolated to a specific component, function, or line

**Template for agent body:**
```markdown
### Divide and Conquer
**When to use:** Large problems, performance issues, debugging in large codebases.

1. Split the problem in half
2. Determine which half contains the issue
3. Repeat until isolated
```

### Requirement Decomposition

**Category:** Decomposition
**Best for:** Planners, Architects, Orchestrators
**When to use:** Breaking high-level feature requests into atomic, implementable units.

**Methodology:** Repeatedly split requirements until each piece can be implemented in a single, focused coding session. Each piece should have a single responsibility. Verify completeness with MECE check.

**Template for agent body:**
```markdown
### Requirement Decomposition
**When to use:** Breaking high-level requests into implementable units.

Split requirements iteratively until each piece has a single responsibility and can be implemented in one focused session. Verify completeness with MECE.
```

### Dependency Mapping

**Category:** Decomposition
**Best for:** Planners, Architects, Orchestrators
**When to use:** Determining task ordering, identifying parallelizable work, understanding system relationships.

**Methodology:**
1. List all tasks or components
2. For each, identify what it needs from others (data, interfaces, infrastructure)
3. Draw the dependency graph (mental model or mermaid diagram)
4. Identify the critical path and tasks that can run in parallel

**Template for agent body:**
```markdown
### Dependency Mapping
**When to use:** Task ordering, identifying parallel work, understanding relationships.

List all tasks → identify inter-dependencies → draw graph → find critical path and parallelizable work.
```

### Interface-First Design

**Category:** Decomposition
**Best for:** Planners, Architects
**When to use:** Defining boundaries between components, modules, or services before implementation.

**Methodology:** Define the contract (inputs, outputs, data shapes, error types) before considering implementation. This ensures components can be built independently and tested in isolation.

**Template for agent body:**
```markdown
### Interface-First Design
**When to use:** Defining boundaries between components or services.

Define the contract (inputs, outputs, types, errors) before implementation. Enables independent development and testing.
```

---

## Adversarial Thinking Techniques

### Inversion (Pre-mortem)

**Category:** Adversarial
**Best for:** Reviewers, Planners, All roles (risk assessment)
**When to use:** Designing systems, evaluating architectures, risk assessment, security reviews.

**Methodology:** Instead of asking "How do we make this succeed?", ask "How could this fail?" Enumerate failure modes, then design defenses against each.

**Example:**
```
Feature: User authentication flow
Pre-mortem: "It failed. What went wrong?"
→ Token leaked via URL parameters
→ Rate limiting absent, brute force succeeded
→ Session fixation after role change
→ Password reset link never expires
Defense: Address each failure mode in the design
```

**Template for agent body:**
```markdown
### Inversion (Pre-mortem)
**When to use:** Designing systems, evaluating architectures, risk assessment.

Ask "How could this fail?" instead of "How do we succeed?" Enumerate failure modes. Design against each.
```

### Assumption Surfacing

**Category:** Adversarial
**Best for:** Planners, Reviewers, Architects
**When to use:** Before making significant decisions or producing specifications. Always.

**Methodology:**
1. State each assumption explicitly
2. Classify as: verified (checked in code), reasonable (based on conventions), or needs-confirmation (must ask)
3. For "needs-confirmation" assumptions, ask the user before proceeding

**Template for agent body:**
```markdown
### Assumption Surfacing
**When to use:** Before any significant decision. Always.

State each assumption → classify as verified/reasonable/needs-confirmation → resolve unknowns before proceeding.
```

### Checklist Verification

**Category:** Adversarial
**Best for:** Reviewers, Auditors
**When to use:** Quality verification, compliance checking, code review, security audits.

**Methodology:** Apply a structured checklist systematically to the artifact under review. Check every item — don't skip based on intuition. Report findings per item with severity classification.

**Template for agent body:**
```markdown
### Checklist Verification
**When to use:** Quality checks, compliance, code review, security audits.

Apply structured checklist systematically. Check every item — no skipping. Report findings with severity levels.
```

---

## Estimation & Feasibility Techniques

### Back-of-Envelope Estimation

**Category:** Estimation
**Best for:** Engineers, Architects
**When to use:** Capacity planning, performance predictions, feasibility checks, architectural decisions.

**Methodology:** Make rough calculations to sanity-check assumptions:
- How many requests per second?
- How much data will be stored in a year?
- Will this fit in memory?
- What's the expected latency?

**Template for agent body:**
```markdown
### Back-of-Envelope Estimation
**When to use:** Capacity planning, performance predictions, feasibility checks.

Make rough calculations to sanity-check: requests/sec, data volume, memory usage, expected latency.
```

---

## Creative & Exploratory Techniques

### First Principles Thinking

**Category:** Creative
**Best for:** Researchers, Generalists, Architects
**When to use:** When existing solutions don't fit, when questioning assumptions, when designing from scratch.

**Methodology:**
1. Identify the fundamental requirements (not the current implementation)
2. Question every assumption — "Why does it have to be this way?"
3. Build up a solution from basic truths, unconstrained by existing patterns

**Template for agent body:**
```markdown
### First Principles Thinking
**When to use:** When existing solutions don't fit, when questioning assumptions.

Strip away assumptions → identify fundamental requirements → build solution from basic truths.
```

### Constraint Relaxation

**Category:** Creative
**Best for:** Engineers, Debuggers, Generalists
**When to use:** When a problem seems impossible, when stuck in analysis paralysis, when requirements conflict.

**Methodology:**
1. List all constraints
2. Temporarily remove one constraint
3. Solve the easier problem
4. Reintroduce the constraint and adapt the solution

**Template for agent body:**
```markdown
### Constraint Relaxation
**When to use:** When a problem seems impossible or requirements conflict.

List constraints → remove one → solve easier problem → reintroduce constraint and adapt.
```

### Socratic Questioning

**Category:** Creative
**Best for:** Mentors, Teaching agents, Researchers
**When to use:** When helping users understand decisions, when exploring problem spaces, when teaching.

**Methodology:** Guide understanding through questions rather than direct answers:
- "What would happen if we removed this constraint?"
- "What assumption are we making about the data?"
- "Can you think of a case where this would break?"

**Template for agent body:**
```markdown
### Socratic Questioning
**When to use:** Helping users understand, exploring problem spaces, teaching.

Guide understanding through targeted questions rather than direct answers. Help the user discover insights.
```

---

## Structured Reasoning Techniques

### MECE Analysis (Mutually Exclusive, Collectively Exhaustive)

**Category:** Structured Reasoning
**Best for:** Planners, Reviewers, Orchestrators
**When to use:** Ensuring completeness, preventing gaps, categorizing options, spec coverage.

**Methodology:** Systematically cover all cases using non-overlapping, complete categories:
- Happy path vs. error path
- Empty/null/zero vs. single vs. many
- First vs. middle vs. last
- Authorized vs. unauthorized
- Within limits vs. at boundary vs. beyond limits

**Template for agent body:**
```markdown
### MECE Analysis
**When to use:** Ensuring completeness, preventing gaps, categorizing systematically.

Cover all cases with non-overlapping, complete categories: happy/error, empty/single/many, boundary conditions.
```

---

## Usage Guidelines

When adding techniques to an agent body:

1. **Select 2-4 techniques** — Use the Role-Technique Matching Guide. Too many overwhelms; too few leaves blind spots.
2. **Include "When to use" triggers** — The agent must know WHEN to apply each technique, not just HOW.
3. **Keep descriptions brief** — 3-5 lines per technique in the agent body. The agent's training data knows the full methodology.
4. **Add the transparency instruction** — Include in the agent's philosophy or workflow: "Always announce which technique you're using."
5. **Match to the agent's domain** — Customize examples to the agent's domain (e.g., Five Whys for a backend engineer should use database/API examples, not generic ones).
