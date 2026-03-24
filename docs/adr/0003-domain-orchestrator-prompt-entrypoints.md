# ADR 0002: Domain Orchestrator Prompt Entry Points

## Status

Accepted

## Date

2026-03-14

## Context

`./docs/adr/0001-orchestrator-architecture.md` establishes orchestrators as the primary public workflow owners in `copilot-kit`. That decision remains intact.

What ADR 0001 did not fully specify is the preferred public slash-command story for domain-oriented workflows. In practice, users do not all enter the repository the same way. Some prefer selecting an agent directly. Others prefer starting from a slash command because it is faster, easier to remember, or more natural in their editor flow.

Without a clear repository rule, a domain can drift toward many user-facing skills or task-specific commands that compete with each other as entry points. That creates unnecessary command clutter, weakens discoverability of the intended starting point, and spreads workflow control across surfaces that are not meant to own orchestration.

A narrower architectural decision is therefore needed: how a domain orchestrator should be exposed through the repository’s preferred slash-command UX.

This ADR refines the public entry-point story from ADR 0001. It does not replace the orchestrator architecture or reduce the role of orchestrators as the domain brains.

## Decision

For a given domain, the preferred public slash-command UX is **one prompt** that routes to the domain orchestrator.

That prompt is a thin convenience wrapper. It is not a replacement for the orchestrator, and it should not duplicate orchestration logic.

Each domain should keep one preferred default prompt entry. Additional prompts may exist, but they should be optional, task-specific, or user-authored rather than equal competing default starting points for the same domain.

Also decide that:

- the domain orchestrator remains the canonical domain brain,
- orchestration and model selection should stay centralized at the agent layer rather than scattered across many user-facing skills,
- internal skills and worker agents remain reusable building blocks and do not need to be part of the public slash-command UX,
- internal skills, setup skills, scaffolding skills, and worker-facing capabilities do not need to remain public slash-command UX merely because they are useful building blocks,
- when a domain has one preferred public default, the default should still be the single prompt tied to the orchestrator,
- this ADR does **not** prohibit multiple prompt files in a repository,
- this ADR establishes the preferred **default** entry point for domain-oriented orchestration workflows,
- user-authored advanced prompts are encouraged when they improve personal or team workflows, provided they do not displace the preferred default for the domain.

The rationale is straightforward:

- better discoverability through one obvious default command,
- lower slash-command clutter,
- a clearer path for less experienced users,
- stronger control over how users enter domain orchestration,
- flexibility for advanced users to create additional prompts later without changing the repository’s default public path.

## Alternatives considered

### 1. Rely only on direct agent selection

Why not as the preferred slash-command model:

- it works for users who already understand the agent structure,
- it does not help users who prefer slash commands as their starting surface,
- it leaves the repository without a simple command-based default path.

### 2. Expose many user-facing skills or slash-command-like entry points per domain

Why not as the preferred default:

- increases visible command clutter,
- pushes users to self-route through narrower surfaces,
- makes it harder to keep orchestration and model control centralized,
- creates a noisier public UX than the repository needs.

### 3. Create multiple equal public prompts for the same domain

Why not as the preferred default:

- improves optionality at the cost of a weaker default path,
- forces users to choose among several “starting” commands,
- undermines the goal of making the entry experience simpler for less experienced users.

Additional prompts can still exist, but they should be clearly optional rather than presented as co-equal default entry points.

### 4. Expose workers or internal building blocks directly as public slash-command UX

Why not:

- workers and internal skills are implementation building blocks, not the canonical domain entry surface,
- setup and scaffolding skills may be valuable internally without needing to remain public slash-command entry points,
- exposing them publicly blurs the distinction between entry points and internal execution units,
- it weakens the orchestrator’s role as the owner of domain flow.

## Consequences

### Positive

- each domain can offer one easy-to-discover default slash-command entry,
- the orchestrator remains the public domain brain,
- internal skills and workers can stay focused on reuse rather than public discoverability,
- the repository can keep domain entry flows more consistent across different domains,
- advanced users still retain freedom to build additional prompts for their own workflows without weakening the default entry story.

### Negative / trade-offs

- the repository must maintain alignment between the thin prompt and the target orchestrator,
- some capabilities may be less immediately visible if they are no longer exposed as public commands,
- contributors need discipline to avoid recreating prompt clutter after this decision,
- documentation still matters; one prompt is simpler, but not self-explanatory without basic repository guidance.

## Follow-up actions

1. Apply this pattern to the Copilot customization domain by introducing one preferred prompt that routes to `kit-copilot-engineering-team`.
2. Review domain-facing skills and related entry artifacts to determine which should be internal-facing rather than public.
3. Update repository documentation so the default prompt entry is easy to discover.
4. Document naming guidance for future domain entry prompts so new domains follow a consistent pattern.
5. Revisit whether explicit `model:` declarations should be used on target orchestrators when tighter control of the user-facing path is desirable.

## Review trigger

Re-evaluate this ADR if any of the following become true:

- Copilot platform behavior changes enough that prompts are no longer the best command-based wrapper for domain entry,
- user testing shows that one default prompt per domain is still too confusing or not discoverable enough,
- a domain develops materially different entry flows that cannot be served well by one preferred default prompt,
- repository usage demonstrates that public user-facing skills provide a better default UX than thin prompt wrappers,
- the orchestrator architecture changes in a way that shifts ownership of entry routing away from domain orchestrators.
