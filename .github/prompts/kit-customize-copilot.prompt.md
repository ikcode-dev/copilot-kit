---
name: kit-customize-copilot
description: "Preferred entrypoint for Copilot customization work"
agent: kit-copilot-engineering-team
model: GPT-5.4
argument-hint: "Describe the Copilot customization outcome you want"
---

## Goal

Use this prompt as the default starting point for Copilot customization work in this repository. Help the user describe the customization outcome they want — such as a new artifact, an update to existing customization files, a workflow change, or a Copilot behavior improvement — and route the request through `kit-copilot-engineering-team`, which owns the internal implementation path.

## Inputs & Context Gathering

- Start with the user’s latest request, `${selection}`, `${file}`, and any directly relevant workspace context.
- Encourage the user to describe the desired outcome, artifact type, or Copilot behavior change they want. If they do not know which artifact type is needed, accept a plain-language description of the problem or desired effect.
- Gather only the repository context needed to classify and execute the request well.
- Ask concise follow-up questions only when they are necessary to remove ambiguity or unblock execution.
- Do not ask the user to choose among internal `kit-copilot-create-*` skills or internal workflow paths.

## Protocol

1. Interpret the user’s request in terms of the desired Copilot customization outcome.
2. If the user already knows the artifact type, use that as a strong signal; if not, infer the most appropriate path from the requested outcome and repository context.
3. Route the task through `kit-copilot-engineering-team`, which chooses the internal mechanism, worker flow, and repository-owned skill or procedure when needed.
4. Keep the conversation thin, user-facing, and iterative: clarify only what matters, then proceed with the most appropriate customization workflow.
5. Preserve room for normal follow-up conversation after the initial request so the user can refine scope, confirm trade-offs, or request adjustments.

## Expected Output Format

Respond with:

- a short restatement of the requested customization outcome,
- the proposed next step, likely artifact or workflow path, or audit direction,
- any essential follow-up questions if more detail is required,
- and, once work is underway or complete, a concise summary of created or updated artifacts with file paths.

If the request is already clear enough to proceed, move directly into the appropriate workflow instead of asking unnecessary questions.

## Guidance

<rules>
- Treat this prompt as the sole public slash-command entrypoint for repository-owned Copilot customization workflows.
- Keep internal mechanism selection inside `kit-copilot-engineering-team`.
- Do not duplicate deep orchestrator internals, routing tables, or worker topology here.
- Keep the prompt durable: focus on the user’s desired outcome and let the orchestrator decide how to achieve it.
</rules>
