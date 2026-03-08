# Orchestrator & Subagent Patterns

Reference for designing agents that delegate work to subagents. Consult this when the agent being created is an orchestrator, coordinator, or multi-agent system.

## Table of Contents
- Coordinator + Worker Pattern
- Multi-Perspective Review Pattern
- Sequential Handoffs Pattern
- Hybrid Patterns
- Anti-Patterns

## Coordinator + Worker Pattern

**When to use:** Complex tasks requiring multiple specializations where a single agent would suffer context bloat.

**Architecture:**
- Coordinator: high-level orchestration, breaks down tasks, delegates, synthesizes results
- Workers: focused specialists, each handling one aspect

**Configuration:**

Coordinator frontmatter:

<template name="coordinator-frontmatter">
```yaml
agents: ['kit-impl-worker', 'kit-review-worker']
model: "Claude Sonnet 4"  # Powerful model for orchestration
tools: [agent, search, read/readFile, 'sequential-thinking/*']
```
</template>

Worker frontmatter:

<template name="worker-frontmatter">
```yaml
user-invocable: false      # Only accessible as subagent
model: "GPT-4.1 mini"      # Faster/cheaper for focused work
tools: [edit/createFile, edit/editFiles, search]  # Domain-specific tools only
```
</template>

**Context Management:**

<rules>
- Each subagent runs in an isolated context window — only the final summary returns to the coordinator.
- This prevents context rot: the coordinator's context stays focused on high-level workflow.
- Dead-end explorations by workers don't pollute the coordinator's context.
- Workers receive ONLY the task prompt — no conversation history from the coordinator.
</rules>

**Model Tiering:**

<decision-guide name="model-tiering">
| Role | Model Selection | Rationale |
|------|----------------|-----------|
| Coordinator | Powerful (Claude Sonnet 4, GPT-4.1) | Needs deep reasoning for decomposition and synthesis |
| Implementation Worker | Fast (GPT-4.1 mini, Claude Haiku) | Focused tasks need speed, not depth |
| Review Worker | Medium-powerful | Needs analytical depth but narrower scope |
</decision-guide>

**Worked Example: Feature Development Pipeline**

<example quality="good" name="feature-development-pipeline">
```
Feature Builder (coordinator)
├── Planner (read, search) — analyzes requirements, produces task breakdown
├── Architect (read, search, fetch) — validates design against codebase patterns
├── Implementer (edit, search, runTests) — writes code per task spec
└── Reviewer (read, search, problems) — reviews implementation quality
```
The coordinator iterates: planning ↔ architecture until plan is solid, then implementation ↔ review until quality passes.
</example>

## Multi-Perspective Review Pattern

**When to use:** Tasks requiring independent analysis from multiple viewpoints where anchoring bias is a concern.

**Architecture:** Single coordinator dispatches parallel subagents, each analyzing from a different perspective. Results are synthesized without cross-contamination.

**Key advantage:** Each subagent approaches the problem fresh — no anchoring to previous analysis.

**Configuration approaches:**

*Lightweight (no additional agent files):*
The coordinator shapes each subagent's focus via the task prompt alone. No custom worker agents needed.

*Advanced (dedicated perspective agents):*
Each perspective gets its own `.agent.md` with specialized tools:
- Security reviewer → security MCP server, SAST tools
- Performance reviewer → profiling tools, benchmark scripts
- Accessibility reviewer → a11y linting tools

**Worked Example: Code Review**

<example quality="good" name="multi-perspective-code-review">
```
Review Coordinator
├── Correctness Analyst — "Does this code do what it claims?"
├── Security Analyst — "Are there vulnerabilities or unsafe patterns?"
├── Performance Analyst — "Are there bottlenecks or wasteful patterns?"
└── Architecture Analyst — "Does this fit the project's design?"
```
Coordinator merges findings into a single prioritized report.
</example>

## Sequential Handoffs Pattern

**When to use:** Multi-phase workflows where the user should review/approve between phases.

**Architecture:** Chain of agents connected via `handoffs` frontmatter. After each agent completes, buttons appear for the user to continue to the next phase.

**Configuration:**

<template name="handoffs-frontmatter">
```yaml
handoffs:
  - label: "Implement Specification"
    agent: kit-backend-engineer
    prompt: "Implement the specification above, following each task in order."
    send: false  # User reviews before sending
  - label: "Review Implementation"
    agent: kit-code-reviewer
    prompt: "Review the implementation against the specification."
    send: false
```
</template>

**`send` field tradeoffs:**

<decision-guide name="send-field">
| Value | Behavior | When to use |
|-------|----------|-------------|
| `false` (default) | Pre-fills prompt, user reviews/edits before sending | Recommended for most workflows — user maintains control |
| `true` | Auto-submits prompt immediately | Only for well-tested, low-risk transitions |
</decision-guide>

**Worked Example: Spec-Driven Development**

<example quality="good" name="spec-driven-development">
```
Spec Planner → [user reviews spec] → Implementer → [user reviews code] → Reviewer
```
</example>

## Hybrid Patterns

### Coordinator with Handoffs

Combine subagent delegation (automated internal work) with handoffs (user-guided phase transitions):

<example quality="good" name="coordinator-with-handoffs">
```
Planning Phase (coordinator + planning workers)
  ↓ handoff
Implementation Phase (coordinator + implementation workers)
  ↓ handoff
Review Phase (coordinator + review workers)
```
The coordinator uses subagents for internal work within each phase, then hands off to the next phase via buttons.
</example>

### Worker-to-Worker Delegation

Workers can themselves be mini-coordinators if their task is complex enough. Keep nesting shallow (max 2 levels) to avoid overhead.

## Anti-Patterns

<anti-patterns>
| Anti-Pattern | Problem | Solution |
|---|---|---|
| **Over-delegation** — Creating workers for trivial tasks | Context overhead exceeds benefit. Each subagent incurs setup cost. | Only delegate when the subtask is substantial enough to justify isolated context. |
| **Starving workers** — Coordinator passes insufficient context | Worker can't complete the task, produces poor results, coordinator retries. | Include all relevant context in the delegation prompt. Workers have no conversation history. |
| **Circular delegation** — Agent A delegates to Agent B delegates to Agent A | Infinite loop or stack overflow. | Map delegation graph explicitly. Ensure it's a DAG (Directed Acyclic Graph). |
| **Universal tools** — Giving every agent access to all tools | Defeats role separation. A "planner" with edit tools will edit. | Principle of least privilege: each agent gets only the tools its role requires. |
| **No model differentiation** — All agents use the same expensive model | Wastes budget with no benefit. Workers don't need deep reasoning. | Tier models: coordinators → powerful, workers → fast/cheap. |
| **Missing synthesis** — Coordinator returns raw worker outputs without integration | User gets fragmented, potentially contradictory information. | Coordinator must always synthesize, reconcile conflicts, and present unified output. |
</anti-patterns>
