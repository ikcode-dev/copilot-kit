---
name: 'XML-like Tags for AI Content'
description: 'Conventions for using XML-like semantic tags in Markdown files consumed by AI agents (agents, skills, prompts, instructions)'
applyTo: '.github/{agents,skills,prompts,instructions}/**/*.md'
---
# XML-like Tags for AI-Consumed Content

Markdown files in `.github/agents/`, `.github/skills/`, `.github/prompts/`, and `.github/instructions/` are consumed primarily by AI agents, not humans. Standard Markdown conveys structure (headers, lists, tables) but not **content type** — the agent can't reliably distinguish "follow this instruction" from "output this template" from "learn from this example."

XML-like tags solve this by wrapping content blocks with explicit semantic boundaries. They tell the agent **what kind of content** a block is and **how to process it**.

## When to Use Tags

Use XML-like tags when Markdown alone creates ambiguity about content type. Specifically:

- **Templates vs instructions** — Is a code block something to fill in and output, or an example to learn from? Wrap it in `<template>` or `<example>`.
- **Hard rules vs advisory guidance** — Are these bullet points non-negotiable constraints or suggestions? Wrap them in `<rules>` or `<best-practices>`.
- **Agent content vs user content** — Should the agent process this internally or display it verbatim? Wrap user-facing output in `<user-message>`.
- **Action items vs reference material** — Should the agent iterate through a checklist or read it for context? Wrap checklists in `<validation>` or `<questions>`.

Do **not** use tags when Markdown is unambiguous on its own — explanatory prose, section introductions, and standard tables don't need wrapping.

## Tag Vocabulary

Every tag below is part of the standard vocabulary. Use these exact tag names — do not invent one-off tags.

### Action Tags (agent should DO something with this content)

| Tag | Purpose | Agent action |
|-----|---------|--------------|
| `<questions>` | Interview questions to ask the user before proceeding | Iterate through and ask each question |
| `<validation>` | Checklist to verify before reporting completion | Iterate through every item and confirm |
| `<context-gathering>` | Steps for scanning workspace/environment before acting | Execute each scan step in order |

### Content-Type Tags (agent should INTERPRET this content correctly)

| Tag | Purpose | Attributes |
|-----|---------|------------|
| `<rules>` | Non-negotiable constraints the agent MUST follow | — |
| `<best-practices>` | Advisory guidance the agent SHOULD follow | — |
| `<template name="...">` | Literal content skeleton to fill in and output | `name` — identifies the template |
| `<example quality="good\|bad" name="...">` | Example content showing what good/bad output looks like | `quality` — `good` or `bad`; `name` — optional identifier |
| `<decision-guide name="...">` | Decision tree or lookup table for choosing between options | `name` — identifies what decision is being made |
| `<user-message>` | Exact message to display to the user verbatim | — |

### Scope Tags (agent should understand WHERE content belongs)

| Tag | Purpose | Notes |
|-----|---------|-------|
| `<belongs-in-{type}>` | What content belongs in this customization type | Replace `{type}` with the customization: `agent`, `prompt`, `skill`, `instruction` |
| `<does-not-belong-in-{type}>` | What content does NOT belong in this type | Same `{type}` substitution |
| `<escalation-criteria>` | When to recommend a different mechanism instead | Used when the current mechanism may not fit |
| `<anti-patterns>` | What NOT to do, with explanations | Negative examples and reasoning |

## Placement Rules

1. **Markdown headers stay** — Use `##` and `###` for top-level section navigation. Headers are part of the SKILL.md and `.agent.md` specs and are well-understood by all models. Tags live **inside** sections, not instead of them.

2. **Tags wrap content blocks** — A tag wraps one logical block of content (a list, a table, a code fence, or a short prose paragraph). Never wrap an entire section including its header.

3. **Tags can nest (max 2 levels)** — `<rules>` inside `<decision-guide>` is valid. Deeper nesting reduces clarity. If you need more than 2 levels, restructure into separate sections.

4. **Attributes are minimal** — Only use `name`, `quality`, and type-specific identifiers. No custom attributes beyond the vocabulary.

5. **Keep opening and closing tags on their own lines** — This ensures clean parsing:

```markdown
### Step 4: Generate

<rules>
1. Create the file in the correct location.
2. Ensure YAML frontmatter is valid.
3. Do NOT paste content as a code block in chat.
</rules>
```

6. **Multiple tags of the same type are fine** — A section can contain multiple `<rules>` blocks if they apply to different sub-topics. Use context (surrounding headers or prose) to clarify scope.

## Usage Examples

### Wrapping interview questions

```markdown
### Step 1: Interview

Before creating anything, gather requirements from the user.

<questions>
1. What task should this prompt automate?
2. What inputs should it accept?
3. What output format is expected?

Do NOT proceed until you have answers to at least questions 1 and 3.
</questions>
```

### Distinguishing rules from best practices

```markdown
<rules>
- One prompt per file — each `.prompt.md` defines exactly one prompt.
- Do NOT paste inline — always create the actual file.
</rules>

<best-practices>
- Use `${selection}` over asking users to paste code.
- Include inline examples to reduce ambiguity.
</best-practices>
```

### Marking templates vs examples

```markdown
<template name="frontmatter">
```yaml
---
name: <kebab-case-name>
description: "<30-60 char summary>"
---
```
</template>

<example quality="good">
"Analyze selected code for security vulnerabilities and generate a prioritized findings report."
</example>

<example quality="bad">
"Help with security." — too vague, no clear action or output.
</example>
```

### Decision guides with nested rules

```markdown
<decision-guide name="agent-mode">
| Value | When to use |
|-------|-------------|
| `agent` | General-purpose tasks |
| `<custom-agent>` | Tasks needing specialized persona |

<rules>
- Avoid `ask` and `edit` modes — use `tools` restrictions instead.
- Verify custom agents exist in the workspace before referencing.
</rules>
</decision-guide>
```

### User-facing messages

```markdown
After validation, display this message to the user:

<user-message>
**How to use your new prompt:**
1. Open Copilot Chat and type `/`
2. Select the prompt from the list
3. Provide any requested input
</user-message>
```

### Validation checklists

```markdown
Before reporting completion, iterate through every check below.

<validation>
- [ ] YAML frontmatter is valid
- [ ] `name` field follows kebab-case convention
- [ ] File is saved in the correct location
- [ ] No tech-stack assumptions unless explicitly required
</validation>
```

### Scope boundaries

```markdown
<belongs-in-prompt>
- A specific, repeatable task with clear inputs and outputs
- Variable placeholders for dynamic input
- Optional tool restrictions
</belongs-in-prompt>

<does-not-belong-in-prompt>
- Persona definitions → use custom agents
- Project coding standards → use custom instructions
- Complex workflows with bundled resources → use Agent Skills
</does-not-belong-in-prompt>

<escalation-criteria>
If the workflow needs bundled resource files or cross-platform portability,
recommend converting to an Agent Skill instead.
</escalation-criteria>
```

## Anti-Patterns

| Avoid | Why | Do instead |
|-------|-----|------------|
| Wrapping every paragraph in tags | Over-tagging adds noise without clarity | Only tag content where Markdown is ambiguous about content type |
| Using tags to replace section headers | Headers are part of the file specs and aid navigation | Keep `##` headers; use tags inside sections |
| Inventing one-off tags (e.g., `<my-custom-tag>`) | Inconsistent vocabulary confuses agents across files | Use only tags from the vocabulary above |
| Putting tags in YAML `description` fields | Breaks YAML parsing and skill auto-invocation | Keep `description` as plain quoted strings |
| Using Markdown blockquotes (`>`) for user messages | Agent may treat blockquotes as emphasis rather than verbatim output | Use `<user-message>` for content to display to users |
| Nesting tags more than 2 levels deep | Deep nesting reduces parsing reliability | Restructure into separate sections or flatten |
| Using self-closing tags (`<rules />`) | Content tags always wrap content; self-closing implies empty | Always use opening + closing pairs |
| Wrapping an entire section including its header | Tags scope content blocks, not structural sections | Place the tag after the header, wrapping only the content |
