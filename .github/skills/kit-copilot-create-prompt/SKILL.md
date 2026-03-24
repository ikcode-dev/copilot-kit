---
name: kit-copilot-create-prompt
description: "Creates a new Copilot prompt file (.prompt.md) with proper frontmatter, structured body, and context-gathering strategies. Use when user asks to create, scaffold, build, generate, or set up a prompt file, task template, or .prompt.md file."
argument-hint: Describe the workflow or task this prompt should automate
user-invocable: false
disable-model-invocation: false
---

# Create a Copilot Prompt File

## What This Skill Does

Creates a properly structured Copilot prompt file (`.prompt.md`) for VS Code following the [prompt files documentation](https://code.visualstudio.com/docs/copilot/customization/prompt-files). The generated prompt defines a **specific task** — a reusable workflow template for Copilot chat and related prompt surfaces.

Prompt files are the simplest customization mechanism: a Markdown file with optional YAML frontmatter that acts as a task shortcut. They're ideal for one-shot workflows that don't require bundled resources, scripts, or cross-platform portability.

## Key Concepts: Prompt vs Agent vs Skill vs Instructions

Understanding the separation of concerns is critical for creating effective prompts. Each customization mechanism has a distinct purpose:

| Component | Defines | Analogy | File Type |
|-----------|---------|---------|-----------|
| **Prompt File** | WHAT specific task to perform | A work order or task assignment | `.prompt.md` |
| **Custom Agent** | WHO the AI is and HOW it behaves | The employee's role, personality, and work style | `.agent.md` |
| **Agent Skill** | Reusable CAPABILITY or multi-step workflow | A specialized training module the employee can use | `SKILL.md` in a skill directory |
| **Custom Instructions** | WHERE — project context and standards | The company handbook | `.instructions.md` |

<belongs-in-prompt>
- A specific, repeatable task with clear inputs and outputs
- Task instructions that any agent can follow
- Variable placeholders for dynamic input (`${selection}`, `${file}`, `${input:...}`)
- Optional agent override to route the task to a specialized agent
- Optional tool restrictions to limit capabilities for the task
</belongs-in-prompt>

<does-not-belong-in-prompt>
- Persona definitions (philosophy, behavioral traits) → put these in custom agents (`.agent.md`)
- Complex multi-step workflows with bundled scripts/templates → put these in Agent Skills (`SKILL.md`)
- Project coding standards → put these in custom instructions (`.instructions.md` or `copilot-instructions.md`)
- Tool API documentation → focus on task intent, not tool mechanics
</does-not-belong-in-prompt>

<escalation-criteria>
If during the interview the user's needs exceed what a prompt file handles well, recommend converting to an Agent Skill instead. Signals that a skill is more appropriate:

- The workflow needs bundled resource files (scripts, templates, examples)
- The workflow should work across VS Code, CLI, and coding agent (portability)
- The workflow should auto-trigger based on description matching (not just manual prompt selection)
- The workflow is complex enough to warrant its own directory with supporting files
</escalation-criteria>

## Step-by-step Procedure

### Step 1: Interview

Before creating anything, gather requirements from the user. Use `#tool:vscode/askQuestions` if available to batch all clarifying questions in a single structured prompt. Otherwise, ask in chat.

<questions>
1. **Purpose**: What specific task or workflow should this prompt automate? What problem does it solve?
2. **Audience**: Who will use this prompt? (e.g., junior dev, reviewer, DevOps engineer, any team member)
3. **Inputs**: What data should the prompt accept? (selection, active file, custom parameters via `${input:...}`)
4. **Output**: What format should the result take? (code, report, checklist, diff, documentation)
5. **Agent mode**: Should it use the default agent, or route to a specific custom agent? (check existing agents in the workspace)
6. **Tool access**: Should the prompt have full tool access, read-only access, or restricted access?

Do NOT proceed to drafting until you have clear answers to at least questions 1, 3, and 4. If the user provides a clear enough description, infer reasonable defaults for the rest and confirm.
</questions>

### Step 2: Workspace Scan

Before generating the prompt, scan the workspace for context.

<context-gathering>
1. **Check existing prompts** — Search `.github/prompts/` for existing `.prompt.md` files to avoid naming conflicts and maintain consistency with established patterns.
2. **Check existing agents** — Search `.github/agents/` for `.agent.md` files. If the prompt should route to a specific agent, verify the agent exists.
3. **Check existing instructions** — Search for `.instructions.md` and `copilot-instructions.md` files. Ensure the prompt doesn't duplicate guidance that's already in project instructions.
4. **Check existing skills** — Search for `SKILL.md` files in `.github/skills/`. Ensure the prompt doesn't duplicate a capability that's already a skill.

Report any findings to the user: "I found X existing prompts. Your new prompt complements/overlaps with [specific prompt]."
</context-gathering>

### Step 3: Draft

Using the interview answers, compose the prompt.

**Frontmatter:**

Build the YAML metadata block using the field-by-field decision guide in the reference section below.

<template name="frontmatter">
```yaml
---
name: <kebab-case-name>
description: "<One-line summary of what this prompt does (30-60 chars)>"
agent: <agent-mode>
argument-hint: "<Guidance text shown in chat input>"
tools: [<tool-list>]
---
```
</template>

**Body:**

Use the bundled [prompt-template.md](./prompt-template.md) as the structural starting point. Fill in the following sections:

<template name="body-sections">
1. `## Goal` — State what the prompt accomplishes in 1-2 sentences.
2. `## Inputs & Context Gathering` — List variables, workspace files to scan, and clarification strategies.
3. `## Protocol` (or a task-specific name like "## Review Protocol", "## Generation Protocol") — Break the task into numbered steps with reasoning checkpoints.
4. `## Expected Output Format` — Provide a concrete template or example of the deliverable.
5. `## Guidance` (optional) — Edge cases, quality standards, collaboration cues.
</template>

Present the complete draft to the user for review before generating.

### Step 4: Generate

Once confirmed:

<rules>
1. Create the `.prompt.md` file in `.github/prompts/` (default location).
2. Ensure the YAML frontmatter is valid and properly delimited with `---`.
3. Ensure the Markdown body follows the section structure.
4. Do NOT paste the content as a code block in chat — create the actual file.
</rules>

### Step 5: Validate

Before reporting completion, iterate through every check below.

<validation>
- [ ] YAML frontmatter is valid (properly closed `---` delimiters)
- [ ] `name` field follows `kebab-case` convention
- [ ] `description` is present, 30-60 characters, clearly states what the prompt does
- [ ] `agent` field is set correctly (see Agent Mode Decision Guide)
- [ ] `tools` field is appropriate for the task (omitted for full access, restricted for limited tasks)
- [ ] File is saved as `.github/prompts/<name>.prompt.md`
- [ ] Task scope is clear and achievable
- [ ] Output format section is concrete and actionable
- [ ] No tech-stack assumptions unless explicitly required
- [ ] Variable syntax is correct (`${input:name:hint}`, `${selection}`, `${file}`)
- [ ] References to tools use `#tool:<tool-name>` syntax where applicable
</validation>

After validation, display this message to the user:

<user-message>
**How to use your new prompt:**
1. Open Copilot Chat and choose `<prompt-name>` from your available prompts
2. Provide any requested input
3. Run it with the agent specified in the prompt frontmatter, or your current agent if none is set

The prompt works with any agent mode. To verify it loads correctly, check **Diagnostics** (right-click in Chat view → Diagnostics).
</user-message>

Suggest related customizations that would complement the new prompt (agents, instructions, skills).

## YAML Frontmatter Reference

| Field | Required | Notes |
|-------|----------|-------|
| `name` | No (inferred from filename) | Explicit `name` overrides the filename. Use kebab-case. |
| `description` | Recommended | Shown in prompt-selection surfaces. Write 30-60 chars that help users identify this prompt quickly. |
| `agent` | No | Controls which agent mode processes the prompt. See Agent Mode Decision Guide below. |
| `argument-hint` | No | Placeholder text shown in the chat input after the user selects this prompt. Guide them on what input to provide. |
| `tools` | No | Restrict available tools. Omit for full access. See Tool Restriction Patterns below. |

### Agent Mode Decision Guide

<decision-guide name="agent-mode">
The `agent` field determines which agent processes the prompt. Choose based on the task:

| Value | When to use | Example |
|-------|-------------|---------|
| `agent` (default) | General-purpose tasks that benefit from full Copilot capabilities | Code generation, refactoring, analysis |
| `<custom-agent-name>` | Tasks that should be handled by a specific persona with specialized expertise | `agent: kit-backend-engineer` for API tasks |
| *(omit)* | Let the user choose the agent mode when invoking | Prompts that work differently per agent |

<rules>
- Avoid `ask` and `edit` modes — they limit capabilities. Use `tools` field restrictions instead to constrain the agent while keeping the full agent mode.
- When referencing a custom agent, verify it exists in the workspace (`.github/agents/`).
- If the prompt creates or modifies files, use `agent` (not a read-only agent).
</rules>
</decision-guide>

### Tool Restriction Patterns

<decision-guide name="tool-restrictions">
Use the `tools` field to limit what the agent can do when processing this prompt. Common patterns:

| Task type | Tools | Rationale |
|-----------|-------|-----------|
| **Read-only analysis** | `tools: [read/readFile, search, read/problems]` | Prevents accidental edits during review/audit tasks |
| **Code generation** | `tools: [edit/createFile, edit/editFiles, edit/createDirectory, search, read/readFile]` | Full file CRUD without terminal access |
| **Full capability** | *(omit `tools`)* | All tools available — for tasks that need everything |
| **Terminal-heavy** | `tools: [execute/runInTerminal, execute/awaitTerminal, read/terminalLastCommand, search]` | Build, test, deploy workflows |
| **Research only** | `tools: [search, web/fetch, read/readFile]` | Gather information without modifying anything |
| **MCP-specific** | `tools: ['mcp-server-name/*', search, read/readFile]` | Tasks that primarily use an MCP server |

<rules>
- Omit `tools` entirely when you want full capability — don't list every tool.
- When restricting, think about what the user does NOT want the agent to do.
- Include `search` and `read/readFile` in most restricted sets — the agent needs information to work.
- Use `'server-name/*'` syntax (quoted, with wildcard) for MCP server tools.
</rules>
</decision-guide>

## Prompt Body Structure Guide

### Writing the Goal section

State what the prompt accomplishes in 1-2 sentences. Be specific about the outcome.

<example quality="good">
"Analyze the selected code for security vulnerabilities and generate a prioritized findings report."
</example>

<example quality="bad">
"Help with security." — too vague, no clear action or output.
</example>

### Writing the Inputs & Context Gathering section

List the variables the prompt uses and the context it should gather.

<decision-guide name="available-variables">
| Variable | When to use | Example |
|----------|------------|---------|
| `${selection}` | Prompt operates on user-selected code | Code review, refactoring, explanation |
| `${file}` | Prompt operates on the active file | File-level analysis, documentation |
| `${input:name:hint}` | Prompt needs custom user input | `${input:framework:Target framework (e.g., React, Vue)}` |
| `#codebase` | Prompt needs broad codebase context | Architecture analysis, dependency mapping |
| `#changes` | Prompt operates on recent changes | Changelog generation, review of modifications |
</decision-guide>

<example quality="good" name="context-gathering-strategies">
- "Scan `README.md` and `CONTRIBUTING.md` for project conventions"
- "Search for related test files to understand testing patterns"
- "Read the project's custom instructions for coding standards"
</example>

### Writing the Protocol section

Break the task into numbered steps. Each step should include:
- **What to do** — concrete action
- **What to check** — reasoning checkpoint or validation
- **What to reference** — files, patterns, or context to consult

Name this section after the task when possible: "## Review Protocol", "## Generation Protocol", "## Refactoring Protocol".

### Writing the Expected Output Format section

Provide a concrete template or example of what the prompt produces. Use fenced code blocks with placeholders to illustrate structure.

<example quality="good" name="fallback-behaviors">
- "If no issues found, state 'All checks passed — no issues detected.'"
- "If multiple solutions exist, present a comparison table with trade-offs."
</example>

### Writing the Guidance section (optional)

Use this section for:
- Edge case handling
- Quality standards specific to the task
- Collaboration cues (when to ask vs. proceed)
- References to external documentation or related prompts

## MCP Tool Integration

<decision-guide name="mcp-tools">
When the task benefits from MCP (Model Context Protocol) servers, reference them in the prompt body or `tools` field:

| MCP Server | When to use | Example tasks |
|------------|------------|---------------|
| **Sequential Thinking** (`sequential-thinking/*`) | Complex multi-step reasoning, architecture design, debugging | Planning refactors, root cause analysis |
| **Memory** (`memory_*`) | Tasks needing cross-conversation context | Remembering project conventions, user preferences |
| **Context7** (`context7_*`) | Up-to-date framework/library documentation | Code generation with latest API patterns, migration guides |
| **Fetch** (`fetch` or `web/fetch`) | Tasks requiring external web content | Checking documentation, API references |

Include MCP tools in the `tools` field: `tools: ['sequential-thinking/*', search, read/readFile]`
</decision-guide>

<best-practices>
- **Clarity over cleverness**: Write instructions as if for a senior developer unfamiliar with the project.
- **Variables over paste**: Use `${selection}` over asking users to paste code. Use `${input:...}` over requiring freeform descriptions.
- **Scope discipline**: One prompt = one task. If the prompt grows beyond ~80 lines or needs multiple sub-workflows, consider splitting into multiple prompts or converting to an Agent Skill.
- **Markdown links**: Reference other prompts or instructions with relative paths: `See [code review](./code-review.prompt.md) for related analysis.`
- **Examples reduce ambiguity**: Include inline examples or sample outputs in the Expected Output Format section.
- **Tool references**: Use `#tool:<tool-name>` syntax when referencing specific chat tools in the prompt body.
- **Tech-stack agnostic by default**: Don't assume a specific language or framework unless the user explicitly requests it.
- **Evidence-based decisions**: Instruct the prompt to cite file paths, line numbers, and existing implementations when making recommendations.
</best-practices>

<rules>
- **One prompt per file** — each `.prompt.md` file defines exactly one prompt.
- **Location matters** — always place prompts in `.github/prompts/` for workspace-level access.
- **Frontmatter is optional but recommended** — without it, the filename becomes the prompt name and all defaults apply.
- **Do NOT paste inline** — always create the actual file. Never output the prompt content as a chat code block.
- **Keep it focused** — a prompt is a task template, not a persona definition or a project configuration. If it grows beyond task scope, recommend the appropriate customization type instead.
</rules>
