---
name: example-prompt
description: "Short, clear summary of what this prompt does (30-60 chars)"
agent: agent
argument-hint: "Guidance text shown in chat input when user invokes this prompt"
tools: []
---

## Goal

State what this prompt accomplishes in 1-2 sentences. Be specific about the outcome.

## Inputs & Context Gathering

- `${input:variableName:Placeholder text for user input}`
- `${selection}` — currently selected code (if applicable)
- `${file}` — the active file (if applicable)
- Scan relevant workspace files for context: README, architecture docs, config files.
- Ask clarifying questions if the input is ambiguous.

## Protocol

### 1. Understand

- Parse the user's input and determine exact scope.
- Gather workspace context (search for related files, read configs).

### 2. Plan

- Break the task into concrete steps.
- Identify dependencies and prerequisites.

### 3. Execute

- Perform each step methodically.
- Cite evidence (file paths, line numbers) for decisions.

### 4. Validate

- Verify the output meets the stated goal.
- Run any applicable checks (tests, linting, type-checking).

## Expected Output Format

Describe exactly what the prompt should produce. Use a fenced code block with placeholders:

```
[Expected output structure here]
```

If no issues are found or no action is needed, state explicitly: "No changes required — [reason]."

## Guidance

- Handle edge cases explicitly (e.g., "If multiple solutions exist, present trade-offs").
- Prefer idiomatic patterns over clever shortcuts.
- When uncertain, list assumptions and ask for confirmation before proceeding.
