---
name: kit-copilot-create-agent
description: "Scaffolds a custom agent (.agent.md) file for GitHub Copilot with persona, philosophy, tools, subagent composition, and workflow. Use when user asks to create, scaffold, build, or set up a custom agent, copilot agent, AI persona, chat mode, orchestrator, subagent worker, or .agent.md file."
argument-hint: Describe the role or persona for the custom agent
user-invokable: true
disable-model-invocation: false
---

# Create a Custom Agent

## What This Skill Does

Creates a properly structured custom agent file (`.agent.md`) for GitHub Copilot in VS Code following the [custom agents documentation](https://code.visualstudio.com/docs/copilot/customization/custom-agents). The generated agent defines a **persona** — a specialized AI identity with its own philosophy, problem-solving approach, tool access, skill composition, and optionally subagent orchestration capabilities.

A custom agent is NOT a task template or a project configuration. It is the **WHO** and **HOW** of the AI — the role it plays, the way it thinks, and how it composes with other agents and skills.

## Key Concepts: Agent vs Skill vs Prompt vs Instructions

Understanding the separation of concerns is critical for creating effective agents. Each customization mechanism has a distinct purpose:

| Component | Defines | Analogy | File Type |
|-----------|---------|---------|-----------|
| **Custom Agent** | WHO the AI is and HOW it behaves | The employee's role, personality, and work style | `.agent.md` |
| **Agent Skill** | Reusable CAPABILITY or multi-step workflow | A specialized training module the employee can use | `SKILL.md` in a skill directory |
| **Prompt File** | WHAT specific task to perform | A work order or task assignment | `.prompt.md` |
| **Custom Instructions** | WHERE — project context and standards | The company handbook | `.instructions.md` |

<belongs-in-agent>
- Role and expertise areas (identity)
- Core philosophy and guiding principles
- Problem-solving methodologies and named techniques (with "When to use" triggers)
- Tool usage guidelines (WHEN to use tools, not HOW they work)
- Workflow and process patterns
- Subagent orchestration strategy (if coordinator role)
- Skills composition references ("For testing tasks, leverage available testing skills")
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

- A **Custom Agent** is a persistent persona — it defines WHO the AI is, its philosophy, and which tools it can access. It's selected by the user or delegated to as a subagent.
- An **Agent Skill** is a reusable capability — it teaches the AI HOW to do a specific task (e.g., run a test suite, scaffold a component, debug CI failures). Skills are loaded on-demand when the task matches the skill's description.

Think of it this way: an agent might **mention** that it's good at debugging, but a skill provides the **actual step-by-step debugging procedure** including scripts and examples. An agent defines the persona; skills provide the specialized knowledge that persona can draw on.

**Critical:** When creating an agent, discover which existing skills in the workspace complement the agent's role. The agent's body should reference complementary skills (e.g., "For testing tasks, leverage available testing skills"), and the agent's `tools` must include any MCP servers those skills require.

<escalation-criteria>
If during the interview the user's needs don't match a custom agent, recommend the right mechanism:

- The user is describing a repeatable procedure with steps and bundled resources → recommend an **Agent Skill** instead
- The user only needs project-wide coding standards → recommend **Custom Instructions** instead
- The user wants a simple one-shot task template → recommend a **Prompt File** instead
- The user needs guaranteed deterministic automation (format, lint, block) → recommend a **Hook** instead

**Steel Manning:** Before redirecting, present the strongest case FOR keeping it as an agent: *"The strongest argument for making this an agent is [X]."* Then present the counter: *"However, [Y] suggests a [mechanism] is better because [Z]."* This produces well-justified decisions.
</escalation-criteria>

## Agent Anatomy: The Four-Tier Structure

A well-structured agent body follows four tiers of content, from essential to specialized:

<template name="agent-anatomy">
### Tier 1: Essential (every agent needs these)

1. **Agent Identity** — Opening statement establishing who the agent is. Start with "You are a..." followed by specific role, 3-5 expertise areas, and key behavioral traits. Include a transparency instruction: "Always announce which thinking technique you're applying." Keep to 2-3 sentences.
2. **Core Philosophy** — 3-5 non-negotiable principles that guide ALL decisions. Each principle should be actionable, include a bold key phrase, and explain "why." These are the agent's personality. Every principle must be falsifiable — "Pragmatism over perfection" is good; "Write good code" is not.
3. **Context Gathering Protocol** — A numbered checklist of what the agent should read/check before taking action. Prevents the agent from making assumptions.

### Tier 2: Behavioral (recommended)

4. **Tool Usage Guidelines** — For each tool or MCP server, list specific "trigger conditions" for WHEN to use it. Use `#tool:<tool-name>` syntax to reference tools in body text. Do not document how the tool works internally.
5. **Problem-Solving Techniques** — 2-4 named methodologies matched to the agent's role. Include "When to use" triggers for each. Consult the [technique catalog](./reference/technique-catalog.md) to select appropriate techniques.
6. **Workflow/Process** — Step-by-step approach the agent follows for tasks (Understand → Gather → Plan → Implement → Validate). For orchestrators, use the delegation workflow variant (Understand → Decompose → Delegate → Synthesize → Validate).

### Tier 3: Quality Gates (recommended)

7. **Quality Standards** — Always/Never rules specific to the agent's domain. Organize by category (Security, Performance, etc.).
8. **Output Expectations** — What users should expect from this agent's responses.

### Tier 4: Specialized (optional, domain-specific)

9. **Skills Integration** — List complementary workspace skills and when they're invoked.
10. **Orchestration Workflow** — For coordinators: delegation guidelines, context passing, result synthesis. See [orchestrator patterns](./patterns/orchestrator-patterns.md).
11. **Domain-Specific Sections** — e.g., Database Guidelines, Accessibility Standards, Security Considerations.
12. **Brainstorming Mode** — For agents that need to ideate or explore options.
13. **Handoffs** — Workflow transitions to other agents with `send: true/false` tradeoffs.
</template>

## Step-by-step Procedure

### Step 1: Interview

Before creating anything, gather requirements from the user. Use `#tool:vscode/askQuestions` if available to ask all clarifying questions in a single structured prompt. Otherwise, ask in chat.

**Problem Reframing (How Might We):** Before diving into questions, reframe the user's raw request as an opportunity question: *"How might we create an AI persona that excels at [domain] so that the user gets [consistent expert behavior] with [the right tool access, skill composition, and thinking approach]?"* This shifts focus from "what YAML fields to fill" to "what problem does this persona solve."

<questions>
1. **Role & Domain**: What specialized role should this agent embody? (e.g., backend engineer, security reviewer, planner, solution architect, DevOps specialist). Be specific — avoid "full-stack developer who can do anything."
2. **Core Philosophy**: What are the 3-5 non-negotiable principles that should guide this agent's decisions? (e.g., "pragmatism over perfection", "security-first thinking", "readability over cleverness", "test before implementing"). If the user is unsure, suggest principles appropriate for the chosen role.
3. **Problem-Solving Style**: How should the agent approach problems? (e.g., systematic breakdown, research-first, test-driven, iterative prototyping, root cause analysis). Use the [technique catalog](./reference/technique-catalog.md) to recommend 2-4 techniques matched to the role.
4. **Tool Access**: What tools should the agent have access to? This is critical — it defines capabilities and restrictions.
   - **Discover available tools**: Before suggesting tools, search the current workspace for existing agent files and MCP server configurations. Look for `.vscode/mcp.json`, `.mcp.json`, or `mcp` settings in `.vscode/settings.json`.
   - Suggest tools appropriate for the role based on what's available (e.g., a planner should have read-only tools, an implementer needs full editing capabilities).
   - Use inline YAML array syntax: `tools: [tool1, tool2, 'mcp-server/*']`
5. **Agent Composition**: Is this a standalone agent, an orchestrator that delegates to subagents, or a worker that only other agents invoke?
   - **Standalone**: Default. Single persona, no delegation.
   - **Orchestrator**: Delegates to specialized workers. Needs `agents` field and `agent` tool. See [orchestrator patterns](./patterns/orchestrator-patterns.md).
   - **Worker**: Only accessible as subagent. Set `user-invokable: false`. Typically uses faster/cheaper model.
6. **Skills Integration**: Which existing workspace skills should this agent leverage? (Discover during Workspace Scan in Step 2.)
7. **Handoffs**: Should this agent hand off to other agents? If so, describe the workflow (e.g., planning → implementation, implementation → review).
8. **Storage Location**: Where should the agent file live? Default to workspace `.github/agents/` folder. Alternatives: user profile (for cross-workspace reuse), `.claude/agents/` (for Claude Code compatibility).

Do NOT proceed to workspace scanning until you have clear answers to at least questions 1, 2, and 5.
</questions>

### Step 2: Workspace Scan

Before drafting, scan the workspace for context.

<context-gathering>
1. **Check existing agents** — Search `.github/agents/`, `.claude/agents/` for existing `.agent.md` files. Read their `description`, `tools`, and `agents` fields to avoid overlap and understand what agent ecosystem already exists.
2. **Check naming conventions** — Look for `.github/instructions/agent-naming.instructions.md` or similar. Apply naming conventions found (e.g., `kit-{category}-{role}` pattern).
3. **Discover existing skills** — Search `.github/skills/` for `SKILL.md` files. Read their `name` and `description` to identify skills that complement the agent's role. Note which MCP servers those skills reference — the agent may need those in its `tools` array.
4. **Check existing instructions** — Read `copilot-instructions.md` and relevant `.instructions.md` files for project context the agent will inherit.
5. **Check MCP configurations** — Scan `.vscode/mcp.json`, `.mcp.json`, or `.vscode/settings.json` for configured MCP servers. Each can be included with `'server-name/*'` syntax.
6. **Check existing prompts** — Search `.github/prompts/` for `.prompt.md` files that might reference this agent or benefit from it.

Report findings: "Found X agents, Y skills, Z MCP servers. Your new agent complements/overlaps with [specifics]. These skills are relevant: [list]."
</context-gathering>

### Step 3: Draft

Using the interview answers and workspace scan, compose the agent:

**Frontmatter:**
- Derive the filename in kebab-case with `.agent.md` extension (e.g., `kit-backend-engineer.agent.md`)
- Write a `description` that clearly states the agent's role and specialization
- Write an `argument-hint` that guides users on how to start a conversation
- Build the `tools` using inline YAML array syntax — include MCP servers required by complementary skills
- Set `agents` field based on composition type (orchestrator → list workers, standalone → omit)
- Set `user-invokable: false` for subagent-only workers
- Optionally set `model` based on role (coordinators → powerful, workers → fast/cheap)
- Add `handoffs` if applicable (from question 7)

**Body — Follow the four-tier anatomy:**

Use the bundled [agent-template.md](./agent-template.md) as the structural starting point. Fill in each tier:

- **Tier 1 (Essential):** Write the identity statement (include transparency instruction), philosophy principles, and context gathering protocol.
- **Tier 2 (Behavioral):** Add tool usage guidelines with `#tool:<tool-name>` references, 2-4 problem-solving techniques from the [technique catalog](./reference/technique-catalog.md), and a workflow process (or orchestration workflow for coordinators).
- **Tier 3 (Quality Gates):** Add domain-specific quality standards (always/never rules) and output expectations.
- **Tier 4 (Specialized):** Add skills integration, orchestration sections (if coordinator), domain-specific sections, and handoffs.

#### Agent Design Analysis

Before presenting the draft to the user, run these four named analysis techniques. Use Sequential Thinking MCP (`sequential-thinking/*`) for complex agents where boundaries, composition, or technique selection are unclear.

**1. Persona MECE Check** — Decompose the agent into five non-overlapping partitions. Verify nothing is missing and nothing overlaps:

| Partition | Question to answer |
|---|---|
| **Identity** | Is the role specific and non-overlapping with existing agents? |
| **Philosophy** | Are principles actionable, distinct, and non-generic? Is each falsifiable? |
| **Capabilities** | Do tools + skills + MCP servers match the role? Nothing missing, nothing excess? |
| **Boundaries** | What does this agent explicitly NOT do? What does it delegate? |
| **Interactions** | How does this agent relate to other agents (handoffs, subagents, skill references)? |

**2. Scope Boundary Test** — Dynamically evaluate borders against adjacent agents and mechanisms. *"Where does this agent's responsibility end? Does any philosophy principle bleed into project-specific instructions? Does any workflow step belong in a skill? Does this agent overlap with an existing agent?"*

**3. Assumption Audit** — List exactly 4 assumptions, one per dimension. For each, ask: *"What if this is wrong?"*

| Dimension | What to audit |
|---|---|
| **Workspace** | Available tools, MCP servers, existing agents and skills |
| **User intent** | Is this standalone, orchestrator, or worker? |
| **Scope stability** | Will this role expand? Should it start narrow? |
| **Skill composition** | Will the agent leverage existing skills, or operate independently? |

**4. Agent Pre-Mortem** — Imagine the agent deployed and producing poor results. Check five failure modes:

| Failure Mode | What to verify in the draft |
|---|---|
| **Identity too broad** → mediocre at everything | Role is specific with clear expertise areas |
| **Philosophy too generic** → no behavioral differentiation | Principles are opinionated, not motherhood statements |
| **Wrong tools** → can't do its job, or can do damage | Tools match role (planners read-only, engineers have edit) |
| **Missing context gathering** → agent makes assumptions | Protocol covers project instructions, patterns, tech stack |
| **No composition** → agent reinvents existing skills | Agent references complementary skills, uses subagents where appropriate |

If any row fails, revise the draft before presenting to the user.

Present the complete draft to the user for review before generating.

### Step 4: Generate

Once confirmed:

<rules>
1. Create the `.agent.md` file in the chosen location (default: `.github/agents/` in the workspace).
2. Ensure the YAML frontmatter is valid and properly delimited with `---`.
3. Ensure the Markdown body follows the four-tier anatomy structure.
4. If creating an orchestrator, also create or verify worker agent files exist.
</rules>

### Step 5: Validate

Before reporting completion, iterate through every check below.

<validation>
- [ ] YAML frontmatter is valid (properly closed `---` delimiters)
- [ ] `description` field is present and clearly states the agent's role
- [ ] `tools` array is populated with appropriate tools for the role
- [ ] `tools` includes MCP servers required by complementary skills
- [ ] Body follows the four-tier anatomy (Identity → Philosophy → Context Gathering → Tool Usage → Techniques → Workflow → Quality → Output)
- [ ] No task-specific instructions in the body (those belong in prompt files or skills)
- [ ] No project-specific info in the body (those belong in custom instructions)
- [ ] No overly broad specialization ("full-stack developer who does everything")
- [ ] Philosophy principles are actionable and falsifiable, not generic
- [ ] Problem-solving techniques have "When to use" trigger conditions
- [ ] If `agents` field is set, referenced agents exist in the workspace
- [ ] If `user-invokable: false`, at least one orchestrator agent references this agent
- [ ] If `handoffs` defined, target agents exist and transitions are logical
- [ ] Model selection matches role (coordinators → powerful, workers → fast)
</validation>

**Adversarial Walkthrough** — After structural checks pass, stress-test the agent:
- [ ] Would a user selecting this agent from the picker understand its purpose from the description alone?
- [ ] Does the agent overlap significantly with any existing agent in the workspace?
- [ ] If given a task outside its domain, does the agent have guidance to decline or redirect?
- [ ] Are the tool restrictions appropriate? (Planner with edit tools? Engineer without test tools?)
- [ ] Could any philosophy principle be interpreted as contradicting project instructions?

After generating the agent, always display this message to the user:

<user-message>
**Verify Your Agent's Configuration**

1. Open the generated `.agent.md` file
2. Review the `tools:` section — add MCP servers for your workflow, remove tools that don't match the role
3. Review the `agents:` section (if orchestrator) — verify worker agents exist
4. Test by selecting **Configure Custom Agents** from the agents dropdown, or check **Diagnostics** (right-click in Chat view → Diagnostics)

**Tool reference:**
- Editing: `edit/createFile`, `edit/editFiles`, `edit/createDirectory`
- Search: `search`, `usages`, `problems`
- Execution: `runCommands`, `runTests`
- External: `fetch`
- MCP: `server-name/*` (all tools from named server)
- Subagents: `agents: ['*']` or specific names; requires `agent` in tools
- Tool refs in body: `#tool:<tool-name>` syntax
</user-message>

## Reference

### Frontmatter Fields

| Field | Required | Notes |
|-------|----------|-------|
| `description` | Yes | Drives agent selection. Be specific about role and when to use. |
| `tools` | Yes | Inline YAML array. Use `'server/*'` for MCP. Include `agent` for subagent delegation. |
| `argument-hint` | Recommended | Guides user on how to start a conversation. |
| `agents` | No | Subagent access: `['*']` = all, `['name']` = specific, `[]` = none. Overrides `disable-model-invocation` on referenced agents. |
| `handoffs` | No | Workflow transitions. Fields: `label`, `agent`, `prompt`, `send` (default `false`). |
| `model` | No | Lock to specific model or prioritized array. Coordinators → powerful, workers → fast. |
| `user-invokable` | No | Default `true`. Set `false` for subagent-only workers (hidden from dropdown). |
| `disable-model-invocation` | No | Default `false`. Set `true` to prevent auto-invocation as subagent. `agents` array overrides this. |

### Tool Discovery

<context-gathering>
Do NOT hardcode a list of tools to suggest. Tools vary across workspaces and users. Follow this discovery process:

1. **Search for existing agents** — Read `tools:` fields from `.agent.md` files in `.github/agents/`, `.claude/agents/`, and user profile locations.
2. **Search for MCP configurations** — Scan `.vscode/mcp.json`, `.mcp.json`, or `.vscode/settings.json`. Each MCP server can be included with `'server-name/*'` syntax.
3. **Search for skill tool requirements** — Read `SKILL.md` files that complement this agent. If a skill references MCP servers (e.g., `sequential-thinking/*`, `context7/*`), the agent needs those in its `tools` array.
4. **Consider the role** — Match tools to composition type:
   - **Read-only roles** (planners, reviewers, architects): `search`, `read/readFile`, `problems`, `usages`, `web/fetch`
   - **Active roles** (implementers, engineers): Add `edit/createFile`, `edit/editFiles`, `runCommands`, `runTests`
   - **Orchestrators**: Add `agent` tool + `agents` field in frontmatter
   - **Workers**: Minimal tools scoped to their specialty
5. **Present findings** — Show the user what tools were discovered and recommend which ones suit the role.
</context-gathering>

<rules>
Always use inline YAML array syntax for the `tools` field:

```yaml
tools: [edit/createFile, edit/editFiles, search, usages, 'mcp-server/*']
```

Quote tool identifiers that contain special YAML characters (like `*` in `'server/*'`).
Reference tools in agent body text with `#tool:<tool-name>` syntax.
</rules>

### Orchestrator & Subagent Patterns

For agents that delegate to subagents, consult the [orchestrator patterns reference](./patterns/orchestrator-patterns.md). Key patterns:

| Pattern | When to use | Key configuration |
|---|---|---|
| **Coordinator + Workers** | Complex tasks needing multiple specializations | `agents: ['worker-a', 'worker-b']`, workers set `user-invokable: false` |
| **Multi-Perspective Review** | Parallel independent analysis (security, quality, perf) | Coordinator dispatches parallel subagents via `runSubagent` |
| **Sequential Handoffs** | User-guided phase transitions (Plan → Implement → Review) | `handoffs` with `send: false` for user review |

**Context isolation principle:** Each subagent runs in its own context window. Only the final summary returns to the coordinator. This prevents context rot and keeps the coordinator focused on high-level orchestration.

**Model tiering:** Coordinators use powerful models (deep reasoning for decomposition and synthesis). Workers use faster/cheaper models (focused execution on narrow tasks).

### Problem-Solving Technique Selection

For the complete catalog with role matching, examples, and agent body templates, consult the [technique catalog](./reference/technique-catalog.md). Summary:

<decision-guide name="technique-selection">
| Agent Role Type | Primary Techniques | Secondary Techniques |
|---|---|---|
| **Engineers / Implementers** | Five Whys, Divide and Conquer | Rubber Duck Debugging, Back-of-Envelope Estimation |
| **Planners / Architects** | MECE Analysis, Dependency Mapping, Interface-First Design | Requirement Decomposition, Assumption Surfacing |
| **Reviewers / Auditors** | Inversion (Pre-mortem), Checklist Verification | Assumption Surfacing, MECE Analysis |
| **Debuggers / Troubleshooters** | Five Whys, Divide and Conquer, Rubber Duck Debugging | Constraint Relaxation |
| **Orchestrators / Coordinators** | Requirement Decomposition, Dependency Mapping | MECE Analysis, Pre-mortem |
| **Researchers / Analysts** | First Principles, Socratic Questioning | MECE Analysis, Assumption Surfacing |
</decision-guide>

Each agent should have 2-4 techniques. Include "When to use" trigger conditions. Instruct the agent to announce which technique it's using (transparency principle).

### MCP Tool Integration

When selecting MCP servers for the agent being created:

| MCP Server | When to recommend | Best for roles |
|---|---|---|
| **Sequential Thinking** (`sequential-thinking/*`) | Complex reasoning, multi-step analysis, architecture decisions | Engineers, planners, architects, reviewers |
| **Context7** (`context7/*`) | Agents that generate code with framework/library APIs | Engineers, frontend/backend specialists |
| **Fetch** (`web/fetch`) | External documentation or API reference lookup | Researchers, architects |
| **Memory** (`memory/*`) | Cross-conversation context persistence | Long-running project agents |

### Examples

For complete examples demonstrating different agent patterns:

- [Standalone agent example](./examples/standalone-agent.md) — single persona, four-tier anatomy, no orchestration
- [Orchestrator agent example](./examples/orchestrator-agent.md) — coordinator + workers, model tiering, context isolation

<best-practices>
- **Conciseness justifies its tokens**: Challenge each paragraph in the agent body — "Does this justify its token cost?"
- **Opinionated > Generic**: "Pragmatism over perfection" beats "Write good code." Every principle should be falsifiable.
- **Tell what TO DO**: "Use Five Whys for debugging" instead of "Don't ignore root causes."
- **Tools define capability boundaries**: A planner with edit tools WILL edit. A reviewer with terminal access WILL run commands. Tool selection is the strongest behavioral constraint.
- **Skills complement, agents don't duplicate**: If a skill exists for testing, the agent mentions "leverage testing skills" — it doesn't embed a testing procedure.
- **Transparency principle**: Always instruct the agent to announce its reasoning technique. Users trust transparent AI.
- **Model choice matters**: Don't default to the most powerful model. Match model capability to role complexity.
- **Context isolation for quality**: Prefer subagent delegation for substantial subtasks. Each subagent gets a clean context window, preventing context rot in the main conversation.
</best-practices>