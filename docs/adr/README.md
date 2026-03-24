# Architecture Decision Records

This directory stores Architecture Decision Records (ADRs) for `copilot-kit`.

## Purpose

ADR files capture important architectural decisions after they are made, including:

- the context behind the decision,
- the options that were considered,
- the final decision,
- the consequences and trade-offs.

The goal is to preserve decision history in a way that is useful for:

- future maintainers,
- outside contributors,
- users trying to understand the repository,
- Igor in six months when today’s reasoning is no longer fresh.

## When to create an ADR

Create an ADR when a decision is:

- structurally important,
- likely to affect repository layout or workflow,
- expensive to reverse,
- useful to explain publicly.

Examples:

- orchestrator architecture,
- repository naming,
- versioning rules,
- artifact contracts,
- design handoff model,
- QA pass/fail policy.

## Suggested file naming

Use a numbered format so records stay stable over time.

Examples:

- `0001-orchestrator-architecture.md`
- `0002-repository-naming.md`
- `0003-versioning-strategy.md`

## Recommended ADR structure

Each ADR should contain:

- Status
- Date
- Context
- Decision
- Alternatives considered
- Consequences
- Follow-up actions

## Repository-level guidance

These ADRs are valuable not only internally, but also as part of the public repository.

For an open-source project like this, ADRs help explain *why* the system looks the way it does, which is often more useful than only showing the final structure.
