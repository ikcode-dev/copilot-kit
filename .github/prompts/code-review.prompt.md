---
name: code-review
description: "Run a high-rigor, tech-agnostic code review that surfaces the most critical issues first."
agent: agent
argument-hint: "Optional: describe focus areas, risks, or files to prioritize."
---

## Goal
Perform an uncompromising code review that emulates a senior reviewer: reconstruct the intent of the change, cross-check it against project expectations, and report actionable findings ordered by severity.

## Inputs & Context Gathering
- Use the latest user request, `${selection}`, `${file}`, and `${input:focus:Preferred focus or risk (optional)}` to determine the review scope.
- Proactively gather context from workspace guidance before reviewing code. Scan: `README.md`, `CONTRIBUTING.md`, `.github/**/*`, `docs/**/*`, architecture notes, config/manifests, and any referenced instruction files. Summarize the applicable rules and honor them throughout the review. If critical context is missing, ask concise follow-up questions.
- Identify the tech stack, runtime constraints, and domain rules from the repo rather than assuming defaults.

## Review Protocol
1. **Build a mental model**: State the suspected intent of the change, incoming/outgoing data flows, and user impact. If reasoning requires, diagram checkpoints or list invariants.
2. **Instruction & contract alignment**: Verify the change obeys repository conventions, security/privacy policies, API contracts, and dependency constraints described in the gathered instructions.
3. **Deep reasoning checklist** (apply even when not explicitly asked):
   - Logic & correctness: edge cases, null/async behavior, concurrency, state resets, error propagation, resource cleanup.
   - Cross-file and architectural impact: compatibility, migrations, feature flags, persistence format changes, dependency versions.
   - Quality attributes: security, privacy, performance, accessibility, localization, resilience, observability.
   - Testing: existing coverage impact, regressions, missing unit/integration/e2e tests, data fixtures, and negative paths.
   - Maintainability: naming, complexity, dead code, duplication, documentation drift.
4. **Evidence gathering**: Cite concrete file paths and line numbers/ranges when possible. Prefer reading entire functions/modules over snippets to avoid tunnel vision. Use repo tools (search, grep, tests) whenever they reduce guesswork.
5. **Prioritization**: Flag blockers (must-fix), major issues, and nits separately. Merge-ready code still receives a quick risk assessment and suggested tests.

## Expected Output Format

<output>
## Findings
| Severity | File/Location | Details |
| --- | --- | --- |
| critical/major/minor/nit | path:line | Problem description, impact, and fix suggestion |
(Write `No findings after full review.` if genuinely none.)

## Questions / Assumptions
- Clarifying questions or explicit assumptions that could change the review.

## Follow-ups & Tests
- Missing or recommended tests, tooling checks, docs, or metrics to run before merging.
</output>

Guidance:
- Always start with the highest-severity issues. Merge actionable duplicates when they share the same root cause.
- When uncertain, describe the risk and what evidence would confirm it instead of staying silent.
- If the review scope is unclear, first respond with the clarification questions needed to proceed.
- When the code appears solid, state residual risks (e.g., untested failure modes) so the user knows what was considered.
- Suggest targeted improvements rather than broad rewrites; cite rationale grounded in observed code and instructions.
