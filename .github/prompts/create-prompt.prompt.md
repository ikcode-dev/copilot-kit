---
name: create-prompt
description: "Generate a new, well-structured Copilot prompt file following VS Code best practices."
agent: agent
argument-hint: "Describe the workflow or task this prompt should automate."
---

## Goal

Create a production-ready Copilot prompt file that automates a specific development workflow. The generated prompt should follow VS Code conventions, be tech-stack agnostic (unless explicitly required), and include clear instructions, expected outputs, and context-gathering strategies.

## Inputs & Context Gathering

- Use `${input:task:Describe the workflow or task to automate}` to understand the desired prompt's purpose.
- Ask clarifying questions if the task description is vague:
  - What problem does this prompt solve?
  - Who is the intended user (junior dev, reviewer, DevOps, etc.)?
  - What inputs should the prompt accept (selection, file, custom parameters)?
  - What output format is expected (code, report, checklist, diff)?
  - Should it reference project-specific instructions or stay universal?
- Scan existing `.github/prompts/`, `.github/agents/`, and instruction files to avoid duplication and maintain consistency with established patterns.

## Prompt Generation Protocol

### 1. YAML Frontmatter (Header)

Build the metadata block with:

```yaml
---
name: <kebab-case-name>
description: "<One-line summary of what this prompt does>"
agent: <agent|ask|edit|custom-agent-name>
argument-hint: "<Guidance text shown in chat input>"
tools: [<optional-tool-list>]
---
```

**Decision guide**:

- `name`: Derive from the task (e.g., `generate-api-tests`, `refactor-legacy`, `draft-changelog`).
- `description`: Write a user-facing summary (30–60 chars).
- `agent`: Always use `agent` (most capable) or reference a custom agent name if specialized behavior is needed. Avoid `ask` or `edit` modes—use `tools` to constrain capabilities instead.
- `argument-hint`: Provide a short prompt to guide user input (e.g., "Optional: specify test framework or coverage target").
- `tools`: Use to limit available tools when needed (e.g., restrict to read-only tools for analysis tasks). Omit to allow all tools.

### 2. Prompt Body Structure

Organize the body into logical sections:

#### **## Goal**
- State what the prompt accomplishes in 1–2 sentences.
- Mention any preferred models or reasoning techniques (e.g., "Use GPT-5.1-Codex for deep code analysis" or "Apply chain-of-thought for multi-step planning").

#### **## Inputs & Context Gathering**
- List variables the prompt uses: `${selection}`, `${file}`, `${input:variableName:placeholder}`, workspace paths.
- Describe what context to proactively gather (README, architecture docs, config files, custom instructions, test suites).
- Specify when to ask clarifying questions vs. proceeding with best-guess defaults.

#### **## [Task-Specific Protocol Name]**
- Break the task into numbered steps or a decision tree.
- Include reasoning checkpoints (e.g., "Verify assumptions before generating code," "Cross-check against existing patterns").
- Reference tools, file searches, or validations needed at each step.
- Emphasize evidence-based decisions (cite files, line numbers, existing implementations).

#### **## Expected Output Format**
- Provide a concrete template or example of the deliverable (code snippet, Markdown table, checklist, diff).
- Use fenced code blocks with placeholders to illustrate structure.
- Specify fallback behavior (e.g., "If no issues found, state 'All checks passed.'").

#### **## Guidance (optional but recommended)**
- Edge case handling (e.g., "If multiple solutions exist, present trade-offs").
- Quality standards (e.g., "Prefer idiomatic patterns; avoid over-engineering").
- Collaboration cues (e.g., "When uncertain, list assumptions and ask for confirmation").

### 3. Best Practices to Apply
- **Clarity**: Write instructions as if for a senior developer unfamiliar with the project.
- **Variables**: Use `${input:name:hint}` for dynamic parameters; prefer `${selection}` over asking users to paste code.
- **Markdown links**: Reference other prompts or instructions with relative paths when they exist.
- **Examples**: Include inline examples or sample outputs to reduce ambiguity.
- **Tool references**: Use `#tool:<tool-name>` syntax when referencing specific chat tools.
- **Modularity**: If the prompt grows complex, suggest splitting it into multiple prompts or creating a custom agent.
- **MCP Integration**: Leverage MCP (Model Context Protocol) servers when appropriate:
  - **Sequential Thinking** (`sequentialthinking`): For complex, multi-step reasoning tasks that benefit from structured problem decomposition (e.g., architecture design, debugging complex issues, planning refactors).
  - **Memory** (`memory_*`): When the prompt needs to remember context across conversations (e.g., project conventions, user preferences, library IDs from Context7).
  - **Context7** (`context7_*`): For tasks requiring up-to-date documentation of frameworks/libraries (e.g., generating code with latest API patterns, migration guides, best practices).
  - **Shadcn UI** (`shadcn-ui_*`): When building or modifying UI components using Shadcn UI (e.g., component scaffolding, pattern matching).
  - **Chrome DevTools** (`chrome-devtools_*`): For web debugging, performance analysis, or testing workflows (e.g., automated UI testing, performance audits).

### 4. Validation Checklist
Before finalizing the prompt, verify:
- [ ] YAML frontmatter is valid and complete.
- [ ] Prompt name follows `kebab-case.prompt.md` convention.
- [ ] Task scope is clear and achievable without human intervention (or explicitly requests clarification when needed).
- [ ] Output format is concrete and actionable.
- [ ] No tech-stack assumptions unless explicitly required by the task.
- [ ] References to variables, tools, and files are syntactically correct.

## Expected Output Format
Deliver the complete prompt file content following the pattern in <fileContent>:

<fileContent>
---
name: example-prompt
description: "Short description"
agent: agent
---

## Goal
...

## Inputs & Context Gathering
...

## Protocol
...

## Expected Output Format
...
</fileContent>

Then provide:
1. **Suggested file path**: `.github/prompts/<name>.prompt.md`
2. **Usage instructions**: How to invoke the prompt (e.g., `/example-prompt focus=security`)
3. **Follow-up recommendations**: Related prompts, agents, or instruction files that would complement this prompt.

## Guidance

- If the user's task description is too broad (e.g., "make code better"), ask targeted questions to narrow the scope before generating.
- For read-only analysis tasks, consider limiting tools to prevent unintended edits (e.g., `tools: []` or specific read tools).
- For code modification workflows, include explicit edit patterns and validation steps in the protocol.
- If the task requires sequential tool execution (e.g., run tests → analyze failures → suggest fixes), include a multi-step protocol with checkpoints.
- Suggest creating a custom agent instead if the prompt would need persistent state or complex tool orchestration across multiple chat turns.
