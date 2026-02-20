---
name: kit-copilot-create-agent
description: "Scaffolds a custom agent (.agent.md) file for GitHub Copilot with persona, philosophy, tools, and workflow. Use when user asks to create, scaffold, build, or set up a custom agent, copilot agent, AI persona, chat mode, or .agent.md file."
argument-hint: Describe the role or persona for the custom agent
user-invokable: true
disable-model-invocation: false
---

# Create a Custom Agent

## What This Skill Does

Creates a properly structured custom agent file (`.agent.md`) for GitHub Copilot in VS Code following the [custom agents documentation](https://code.visualstudio.com/docs/copilot/customization/custom-agents). The generated agent defines a **persona** — a specialized AI identity with its own philosophy, problem-solving approach, tool access, and workflow.

A custom agent is NOT a task template or a project configuration. It is the **WHO** and **HOW** of the AI — the role it plays and the way it thinks.

## Key Concepts: Agent vs Prompt vs Instructions

Understanding the separation of concerns is critical for creating effective agents. Each customization mechanism has a distinct purpose:

| Component | Defines | Analogy | File Type |
|-----------|---------|---------|-----------|
| **Custom Agent** | WHO the AI is and HOW it behaves | The employee's role, personality, and work style | `.agent.md` || **Agent Skill** | Reusable CAPABILITY or multi-step workflow | A specialized training module the employee can use | `SKILL.md` in a skill directory || **Prompt File** | WHAT specific task to perform | A work order or task assignment | `.prompt.md` |
| **Custom Instructions** | WHERE — project context and standards | The company handbook | `.instructions.md` |

<belongs-in-agent>
- Role and expertise areas (identity)
- Core philosophy and guiding principles
- Problem-solving methodologies and named techniques
- Tool usage guidelines (WHEN to use tools, not HOW they work)
- Workflow and process patterns
- Quality standards and output expectations
- Response style and behavioral traits
</belongs-in-agent>

<does-not-belong-in-agent>
- Task-specific workflows → put these in prompt files (`.prompt.md`)
- Reusable specialized capabilities (testing strategies, deployment processes, code review checklists) → put these in Agent Skills (`SKILL.md`). Skills are loaded on-demand and work across VS Code, Copilot CLI, and Copilot coding agent.
- Project-specific info (tech stack, coding standards) → put these in custom instructions (`.instructions.md` or `copilot-instructions.md`)
- Volatile/frequently changing content (sprint priorities, ticket numbers) → keep in external files
- Generic AI advice ("write clean code") → the AI already knows this
- Tool API documentation → focus on WHEN to use tools, not their internal parameters
</does-not-belong-in-agent>

### Agent Skills vs Custom Agents

A common confusion is between Agent Skills and Custom Agents. Here's how they differ:

- A **Custom Agent** is a persistent persona — it defines WHO the AI is, its philosophy, and which tools it can access. It's selected by the user or delegated to as a subagent.
- An **Agent Skill** is a reusable capability — it teaches the AI HOW to do a specific task (e.g., run a test suite, scaffold a component, debug CI failures). Skills are loaded on-demand when the task matches the skill's description.

Think of it this way: an agent might **mention** that it's good at debugging, but a skill provides the **actual step-by-step debugging procedure** including scripts and examples. An agent defines the persona; skills provide the specialized knowledge that persona can draw on.

When creating an agent, consider which existing skills in the workspace might complement the agent's role. The agent's body can reference skills as part of its workflow (e.g., "For testing tasks, leverage available testing skills").

## Agent Anatomy: The Four-Tier Structure

A well-structured agent body follows four tiers of content, from essential to specialized:

<template name="agent-anatomy">
### Tier 1: Essential (every agent needs these)

1. **Agent Identity** — Opening statement establishing who the agent is. Start with "You are a..." followed by specific role, 3-5 expertise areas, and key behavioral traits. Keep to 2-3 sentences.
2. **Core Philosophy** — 3-5 non-negotiable principles that guide ALL decisions. Each principle should be actionable, include a bold key phrase, and explain "why." These are the agent's personality.
3. **Context Gathering Protocol** — A numbered checklist of what the agent should read/check before taking action. Prevents the agent from making assumptions.

### Tier 2: Behavioral (recommended)

4. **Tool Usage Guidelines** — For each tool or MCP server, list specific "trigger conditions" for WHEN to use it. Do not document how the tool works internally.
5. **Problem-Solving Techniques** — Named methodologies the agent can apply (Five Whys, Divide and Conquer, etc.). Include "When to use" triggers for each.
6. **Workflow/Process** — Step-by-step approach the agent follows for tasks (Understand → Gather → Plan → Implement → Validate).

### Tier 3: Quality Gates (recommended)

7. **Quality Standards** — Always/Never rules specific to the agent's domain. Organize by category (Security, Performance, etc.).
8. **Output Expectations** — What users should expect from this agent's responses.

### Tier 4: Specialized (optional, domain-specific)

9. **Domain-Specific Sections** — e.g., Database Guidelines, Accessibility Standards, Security Considerations.
10. **Brainstorming Mode** — For agents that need to ideate or explore options.
11. **Handoffs** — Workflow transitions to other agents.
</template>

## Step-by-step Procedure

### Step 1: Interview

Before creating anything, gather requirements from the user. Use `ask_questions` if available to ask all clarifying questions in a single structured prompt. Otherwise, ask in chat.

<questions>
1. **Role & Domain**: What specialized role should this agent embody? (e.g., backend engineer, security reviewer, planner, solution architect, DevOps specialist). Be specific — avoid "full-stack developer who can do anything."
2. **Core Philosophy**: What are the 3-5 non-negotiable principles that should guide this agent's decisions? (e.g., "pragmatism over perfection", "security-first thinking", "readability over cleverness", "test before implementing"). If the user is unsure, suggest principles appropriate for the chosen role.
3. **Problem-Solving Style**: How should the agent approach problems? (e.g., systematic breakdown, research-first, test-driven, iterative prototyping, root cause analysis)
4. **Tool Access**: What tools should the agent have access to? This is critical — it defines capabilities and restrictions.
   - **Discover available tools**: Before suggesting tools, search the current workspace for existing agent files (`.agent.md`) and MCP server configurations to understand what tools are actually available in this environment. Look for `.vscode/mcp.json`, `.mcp.json`, or `mcp` settings in `.vscode/settings.json` to find configured MCP servers.
   - Suggest tools appropriate for the role based on what's available (e.g., a planner should have read-only tools, an implementer needs full editing capabilities).
   - Use inline YAML array syntax for the `tools` field: `tools: [tool1, tool2, 'mcp-server/*']`
5. **Handoffs**: Should this agent hand off to other agents? If so, describe the workflow (e.g., planning → implementation, implementation → review).
6. **Storage Location**: Where should the agent file live? Default to workspace `.github/agents/` folder. Alternatives: user profile (for cross-workspace reuse), `.claude/agents/` (for Claude Code compatibility).

Do NOT proceed to drafting until you have clear answers to at least questions 1, 2, and 4.
</questions>

### Step 2: Draft

Using the interview answers, compose the agent:

**Frontmatter:**
- Derive the filename in kebab-case with `.agent.md` extension (e.g., `kit-backend-engineer.agent.md`)
- Write a `description` that clearly states the agent's role and specialization
- Write an `argument-hint` that guides users on how to start a conversation
- Build the `tools` using inline YAML array syntax: `tools: [tool1, tool2, 'mcp/*']` — based on tools discovered during the interview (question 4)
- Add `handoffs` if applicable (from question 5)
- Optionally set a `model` if the role benefits from a specific model

**Body — Follow the four-tier anatomy:**

Use the bundled [agent-template.md](./agent-template.md) as the structural starting point. Fill in each tier:

- **Tier 1 (Essential):** Write the identity statement, philosophy principles, and context gathering protocol. The philosophy should reflect the user's answer to question 2 — these principles define the agent's personality.
- **Tier 2 (Behavioral):** Add tool usage guidelines matching the selected tools, problem-solving techniques matching the chosen style, and a workflow process.
- **Tier 3 (Quality Gates):** Add domain-specific quality standards (always/never rules) and output expectations.
- **Tier 4 (Specialized):** Add any domain-specific sections relevant to the role.

Present the complete draft to the user for review before generating.

### Step 3: Generate

Once confirmed:

<rules>
1. Create the `.agent.md` file in the chosen location (default: `.github/agents/` in the workspace).
2. Ensure the YAML frontmatter is valid and properly delimited with `---`.
3. Ensure the Markdown body follows the four-tier anatomy structure.
</rules>

### Step 4: Validate and Verify Tools

Before reporting completion, iterate through every check below.

<validation>
- [ ] YAML frontmatter is valid (properly closed `---` delimiters)
- [ ] `description` field is present and clearly states the agent's role
- [ ] `tools` array is populated with appropriate tools for the role
- [ ] Body follows the four-tier anatomy (Identity → Philosophy → Context Gathering → Tool Usage → Workflow → Quality → Output)
- [ ] No task-specific instructions in the body (those belong in prompt files)
- [ ] No project-specific info in the body (those belong in custom instructions)
- [ ] No overly broad specialization ("full-stack developer who does everything")
- [ ] Philosophy principles are actionable, not generic
</validation>

After generating the agent, always display this message to the user:

<user-message>
⚠️ **Verify Your Agent's Tools**

The tools listed in your agent's frontmatter define what capabilities the agent has. Please review and adjust them:

1. Open the generated `.agent.md` file
2. Review the `tools:` section in the frontmatter
3. **Add** any MCP servers or tools specific to your workflow
4. **Remove** tools that shouldn't be available for this agent's role (e.g., a planner shouldn't have editing tools)
5. To include all tools from an MCP server, use the `server-name/*` format

You can verify the agent loads correctly by selecting **Configure Custom Agents** from the agents dropdown in the Chat view, or by checking **Diagnostics** (right-click in Chat view → Diagnostics).

Available tool categories:
- **Editing**: `edit/createFile`, `edit/editFiles`, `edit/createDirectory`
- **Search**: `search`, `usages`, `problems`
- **Execution**: `runCommands`, `runTests`
- **External**: `fetch`
- **MCP**: `server-name/*` (includes all tools from the named MCP server)
- **Agents**: Add `agents: ['*']` or specific agent names to allow subagent delegation
</user-message>

## Tool Discovery

<context-gathering>
Do NOT hardcode a list of tools to suggest. Tools vary across workspaces and users. Instead, follow this discovery process when helping the user choose tools:

1. **Search for existing agents** — Look for `.agent.md` files in `.github/agents/`, `.claude/agents/`, and user profile locations. Read their `tools:` fields to understand what tools are available in this environment.
2. **Search for MCP configurations** — Look for `.vscode/mcp.json`, `.mcp.json`, or `mcp` settings in `.vscode/settings.json` to find configured MCP servers. Each MCP server can be included with `'server-name/*'` syntax.
3. **Consider the role** — Based on the agent's role, suggest a subset of discovered tools:
   - **Read-only roles** (planners, reviewers, architects): Exclude editing and execution tools. Focus on search, navigation, and fetch capabilities.
   - **Active roles** (implementers, engineers, debuggers): Include editing, execution, testing, and search tools.
4. **Present findings** — Show the user what tools were discovered and recommend which ones suit the agent's role.
</context-gathering>

<rules>
Always use inline YAML array syntax for the `tools` field:

```yaml
tools: [edit/createFile, edit/editFiles, search, usages, 'mcp-server/*']
```

Quote tool identifiers that contain special YAML characters (like `*` in `'server/*'`).
</rules>