---
name: 'Prompt Naming Conventions'
description: 'Naming conventions for Copilot prompt files in copilot-kit'
applyTo: '.github/prompts/**/*.prompt.md'
---
# Prompt Naming Conventions for copilot-kit

Apply these rules when creating a new prompt file or when a prompt rename is explicitly in scope.

## Naming Pattern

New or renamed prompt files in this repository must use this filename pattern:

`kit-{task}(-{qualifier})*.prompt.md`

<rules>
- Start every new or renamed prompt filename with the `kit-` origin prefix.
- Keep the rest of the filename stem lowercase and hyphenated.
- Name the prompt after the task or workflow it performs, not after an implementation detail.
- Save prompt files in `.github/prompts/` with the `.prompt.md` suffix.
</rules>

## Frontmatter Alignment

<rules>
- If the prompt includes an explicit frontmatter `name`, set it to the filename stem without `.prompt.md`.
- For new or renamed prompts, that `name` value should therefore also begin with `kit-`.
- Keep the prompt `name` concise and task-oriented so the picker label matches the file name cleanly.
</rules>

## Rollout Boundary

<rules>
- Existing legacy prompt files that do not start with `kit-` may remain as-is until a dedicated migration is requested.
- Do not opportunistically rename legacy prompt files during unrelated content edits.
- Apply the `kit-` prefix when creating a new prompt or when a prompt rename is intentionally part of the current change.
</rules>

## Anti-Patterns

<anti-patterns>
| Avoid | Why | Use instead |
|-------|-----|-------------|
| `code-review.prompt.md` for a new prompt | Legacy pattern; new prompts must carry the repository origin prefix | `kit-code-review.prompt.md` |
| `Kit-Code-Review.prompt.md` | Prompt filenames must be lowercase kebab-case | `kit-code-review.prompt.md` |
| `kit-code-review.md` | Missing the required prompt file suffix | `kit-code-review.prompt.md` |
| `review-kit.prompt.md` | Prefix must be at the start of the filename stem | `kit-review.prompt.md` |
</anti-patterns>

## Validation

<validation>
- [ ] File path matches `.github/prompts/<name>.prompt.md`
- [ ] New or renamed prompt filename begins with `kit-`
- [ ] Filename stem is lowercase kebab-case
- [ ] Explicit frontmatter `name`, if present, matches the filename stem
- [ ] No legacy prompt was renamed unless that rename was explicitly requested
</validation>
