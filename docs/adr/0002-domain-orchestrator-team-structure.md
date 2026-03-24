# ADR 0002: Domain Orchestrator Team Structure

## Status

Accepted

## Date

2026-03-11

## Context

ADR 0001 established the orchestrator architecture for `copilot-kit`: one primary orchestrator as the default entry point, optional domain orchestrator custom agents as secondary public entry points, and hidden worker specialists underneath.

That ADR also introduced two naming rules:

- public domain orchestrators may use domain-facing, team-style names when that improves discoverability,
- public naming does not change the internal architecture vocabulary, which remains orchestrator / worker / artifact.

Under that policy, `kit-copilot-engineering-team` is the first domain orchestrator custom agent currently present in this repository.

What ADR 0001 did not capture is the broader pattern: that the team metaphor is not a one-off naming choice for a single agent, but an intentional, consistent public presentation model applied across domain orchestrators throughout the system.

This matters because:

- repository visitors, contributors, and users scanning agent files or documentation should immediately understand the system's structure and direction,
- the team metaphor needs to be deliberate and documented rather than appearing accidental or inconsistent,
- the relationship between the public team identity and the internal architecture vocabulary needs an explicit decision record,
- follow-up action #8 in ADR 0001 explicitly calls for capturing detailed naming policy separately.

A decision is needed so that the team-structure pattern is recorded as an accepted convention, not left implicit.

## Decision

Domain orchestrators in `copilot-kit` use a consistent **team-structure model** as their public identity.

This means:

- each domain orchestrator custom agent that earns a public entry point expresses its public team identity through a `*-team` suffix inside the full repository agent identifier,
- the team metaphor is the public presentation layer — it is how users, repository visitors, and documentation refer to domain orchestrators,
- the internal architecture vocabulary remains **orchestrator / worker / artifact** as established in ADR 0001,
- the team name does not imply that human teams exist behind these agents — it is a structural and navigational metaphor that makes the system feel collaborative and intuitive,
- all domain teams share the same relationship to the primary orchestrator: they are secondary public entry points, not replacements for the primary orchestrator.

For example, `.github/agents/kit-copilot-engineering-team.agent.md` follows the repository-wide `kit-...` naming convention while still expressing the team metaphor through the `engineering-team` suffix. The `kit-copilot-` portion anchors the agent in the repository's naming system; the `-team` ending preserves the public team identity.

### Naming pattern

| Public team identity | Repository agent identifier | Domain | Internal role |
| --- | --- | --- | --- |
| `engineering-team` | `kit-copilot-engineering-team` | Copilot customization engineering | Domain orchestrator |
| `*-team` | `kit-<category>-<domain>-team` | *(future domains)* | Domain orchestrator |

The public naming pattern is a `*-team` suffix inside the full repository agent identifier. The active example in this repository is `kit-copilot-engineering-team`; future team-oriented identifiers should follow the same repository-wide `kit-...` format while ending in `-team`. New teams will be added as individual domains mature through the earning criteria defined later in this ADR. The current PRD set covers domains including discovery, specification, implementation, QA, design, devops, and AI integration, but team boundaries will be determined as those PRDs are accepted and domain scopes are finalized — not assumed from the current PRD split.

### How this looks to a repository visitor

```text
copilot-kit/
│
├── Primary orchestrator                                      ← default entry point for all users
│
├── .github/agents/kit-copilot-engineering-team.agent.md      ← domain orchestrator (active)
├── .github/agents/kit-<category>-<domain>-team.agent.md      ← additional teams added as domains mature
│
└── Worker specialists                                      ← hidden, orchestrator-invoked only
```

A visitor scanning the repository should be able to see the teams listed, understand that each one corresponds to a product engineering domain, and know that they can either start with the primary orchestrator or go directly to a specific team.

### Criteria for when a domain earns a team identity

Building on ADR 0001's decision that domain orchestrators must be curated, a domain should earn a public `*-team` identity when:

1. the domain has a defined scope that does not substantially overlap with another team,
2. the domain has at least one worker specialist that the team orchestrator would coordinate,
3. the domain workflow is mature enough that a user selecting the team directly would get useful, grounded results — not a stub,
4. the team name is stable enough that it will not need renaming within a reasonable horizon,
5. exposing the team improves discoverability without cluttering the visible custom-agent list.

These criteria are intentionally conservative. It is better to have fewer real teams than many placeholder teams.

### Relationship between the team metaphor and internal vocabulary

The team metaphor is a **public presentation layer**. It does not redefine the architecture.

| Layer | Vocabulary | Example |
| --- | --- | --- |
| Public interface | team | `kit-copilot-engineering-team` |
| Internal architecture | orchestrator | copilot engineering domain orchestrator |
| Internal execution | worker / specialist | PRD drafter, ADR reviewer, boundary checker |
| Coordination model | artifact | PRD, ADR, spec, validation report |

Documentation, agent descriptions, and user-facing text use the team name. Agent internals, delegation contracts, and architecture discussions use the orchestrator/worker/artifact vocabulary.

## Alternatives considered

### 1. Flat naming without a metaphor

Use descriptive names like `product-definition-orchestrator` or `discovery-orchestrator` as the public names.

Why not:

- technically accurate but less approachable,
- exposes internal architecture vocabulary to users who do not need it,
- less intuitive for developers scanning a repository for the first time,
- weaker discoverability — "team" signals a collaborative capability, "orchestrator" signals a technical routing concern.

### 2. Role-based naming

Use names like `product-manager`, `architect`, `qa-lead`, `designer` to name domain orchestrators after the human role they emulate.

Why not:

- implies a single persona rather than a coordinated capability,
- makes it harder to expand the domain scope without the name becoming misleading,
- risks confusion about whether these are actual human roles or AI agents,
- ties naming to current role assumptions rather than stable domain boundaries.

### 3. No consistent naming pattern

Let each domain orchestrator choose its own naming style independently.

Why not:

- inconsistency makes the system harder to scan and understand,
- repository visitors cannot infer the pattern from one example,
- weakens the structural signal that these agents share an architectural relationship,
- increases documentation burden because each name must be explained individually.

### 4. Encode the `*-team` identity only in documentation, not in agent identifiers

Keep the `*-team` identity as a documentation label but name the actual agent identifiers differently.

Why not:

- creates a gap between what users read and what they select,
- forces users to remember two names for the same thing,
- undermines the discoverability benefit that motivated the metaphor in the first place.

## Consequences

### Positive

- repository visitors can scan the team list and immediately understand the system's scope and direction,
- the naming pattern is predictable — once a visitor knows `kit-copilot-engineering-team`, they can recognize future `kit-...-team` identifiers as the same class of public domain orchestrator,
- the team metaphor makes the system feel collaborative and approachable rather than mechanical,
- developers familiar with how real product teams are organized will find the structure intuitive,
- the pattern is easy to explain publicly — in documentation, on YouTube, on LinkedIn, and in README files,
- the separation between public team names and internal architecture vocabulary keeps each layer clean.

### Negative / trade-offs

- the team metaphor must not mislead users into thinking human teams exist behind these agents — documentation and agent descriptions must be clear about this,
- team names must stay curated so the visible list does not become noisy — this requires discipline as the system grows,
- the internal architecture vocabulary must be maintained consistently in agent internals, delegation contracts, and architecture documents, even when the public name uses "team,"
- renaming a team is more disruptive than renaming a hidden worker because the team name is part of the public interface.

## Follow-up actions

1. Update agent file naming and agent descriptions to use team-oriented `...-team` identifiers as each domain orchestrator is implemented.
2. Ensure the primary orchestrator's documentation references the team structure so new users understand the available entry points.
3. Define each team's scope boundary and initial worker roster as the corresponding PRD matures.
4. Add a visible team directory or overview to the repository's main documentation so visitors can see the planned structure at a glance.
5. Revisit team roster and naming as real usage reveals whether the planned domains map well to actual workflow needs.

## Review trigger

Re-evaluate this ADR if any of the following become true:

- the team metaphor causes measurable user confusion about whether human teams are involved,
- the number of public teams grows past the point where the visible list is easy to scan,
- a domain boundary shifts enough that the team name no longer fits its actual scope,
- the internal architecture vocabulary drifts far enough from the team metaphor that maintaining both becomes costly,
- real usage shows that a different public naming model would be more discoverable or more stable.
