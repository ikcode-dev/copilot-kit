---
name: kit-copilot-create-skill
description: "Scaffolds a new Agent Skill directory and SKILL.md file following the Agent Skills open standard. Use when a user asks to create, scaffold, or set up a new skill, slash command, or reusable capability for GitHub Copilot."
argument-hint: Skill name, purpose, and any specific capabilities or resources it should include
user-invocable: true
disable-model-invocation: false
---

# Create a New Agent Skill

## What This Skill Does

Creates a properly structured Agent Skill directory containing a `SKILL.md` file and optional resources (scripts, templates, examples). Follows the [Agent Skills specification](https://agentskills.io/) and [VS Code docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills) for Copilot's skills development.

An Agent Skill is a **reusable capability** — a multi-step procedure with optional bundled resources that the AI agent follows to complete a specific type of task. Skills are portable across VS Code, Copilot CLI, and Copilot coding agent.

## Key Concepts: Skill vs Agent vs Prompt vs Instructions

Understanding the separation of concerns is critical for creating effective skills. Each customization mechanism has a distinct purpose:

| Component | Defines | Analogy | File Type |
|-----------|---------|---------|-----------|
| **Agent Skill** | Reusable CAPABILITY or multi-step workflow | A specialized training module the employee can use | `SKILL.md` in a skill directory |
| **Custom Agent** | WHO the AI is and HOW it behaves | The employee's role, personality, and work style | `.agent.md` |
| **Prompt File** | WHAT specific task to perform | A work order or task assignment | `.prompt.md` |
| **Custom Instructions** | WHERE — project context and standards | The company handbook | `.instructions.md` |

<belongs-in-skill>
- Multi-step procedures the agent follows methodically (Interview → Scan → Draft → Generate → Validate)
- Reusable workflows with bundled scripts, templates, or examples
- Domain-specific knowledge that loads on-demand via progressive disclosure
- Convention detection and adaptation to project patterns
- Tasks requiring workspace scanning before execution
- Capabilities that should work across VS Code, CLI, and coding agent (portability)
</belongs-in-skill>

<does-not-belong-in-skill>
- AI persona definitions (philosophy, behavioral traits) → custom agents (`.agent.md`)
- Simple one-shot task templates with no bundled resources → prompt files (`.prompt.md`)
- Project coding standards and conventions → custom instructions (`.instructions.md`)
- Generic AI advice the model already knows ("write clean code", "handle errors")
- Tool API documentation — focus on WHEN to use tools, not their internal parameters
</does-not-belong-in-skill>

<escalation-criteria>
If during the interview the user's needs don't match a skill, recommend the right mechanism:

- The workflow is a simple one-shot task with no bundled resources → recommend a **prompt file** instead
- The user is defining a persona or role rather than a procedure → recommend a **custom agent** instead
- The user only needs project-wide coding standards → recommend **custom instructions** instead
- The user needs guaranteed deterministic automation (format on save, block commands) → recommend a **hook** instead
</escalation-criteria>

## Step-by-step Procedure

### Step 1: Interview

Before creating anything, gather requirements from the user. Use `#tool:vscode/askQuestions` if available to ask all clarifying questions in a single structured prompt. Otherwise, ask in chat.

**Problem Reframing (How Might We):** Before diving into questions, reframe the user's raw request as an opportunity question: *"How might we help the agent automate [specific task] so that the user gets [consistent, quality outcome] with [minimal input]?"* This shifts focus from "what files to create" to "what problem to solve" — leading to better skill scope and design.

<questions>
1. **Task**: What repetitive task do you want to automate? (e.g., writing tests, planning sprints, scaffolding components)
2. **Complexity**: How many steps does the workflow have? Does it need scripts, templates, or examples? (If it's a simple one-shot task with no resources, a prompt file may be more appropriate — see escalation criteria above.)
3. **Name**: What should the skill be called? (suggest a kebab-case name if the user doesn't have one)
4. **Invocation**: Should it appear as a `/` slash command, or only trigger automatically? (determines `user-invocable` and `disable-model-invocation`)
5. **Triggers**: What phrases would a user say that should trigger this skill? (feeds into the description's "Use when" clause)
6. **Resources**: Does the skill need bundled resources — scripts, templates, examples?

Do NOT proceed to workspace scanning until you have clear answers to at least questions 1, 3, and 5. Use question 2 to validate that a skill (not a prompt) is the right mechanism.

**Steel Manning for Escalation:** If question 2 suggests a different mechanism (e.g., prompt file), don't immediately redirect. First, present the strongest case FOR keeping it as a skill: *"The strongest argument for making this a skill is [X]."* Then present the counter: *"However, [Y] suggests a prompt is better because [Z]."* This produces well-justified decisions and catches cases where the first instinct was wrong.
</questions>

### Step 2: Workspace Scan

Before drafting, scan the workspace for context.

<context-gathering>
1. **Check existing skills** — Search `.github/skills/` for existing `SKILL.md` files to avoid naming conflicts and maintain consistency with established patterns.
2. **Check naming conventions** — Look for `.github/instructions/` files that target skills (e.g., `skill-naming.instructions.md`). Apply any naming conventions found (like `kit-{category}-{verb}-{object}` patterns).
3. **Check existing prompts** — Search `.github/prompts/` for `.prompt.md` files that might already cover the same task.
4. **Check existing agents** — Search `.github/agents/` for `.agent.md` files that might already embed this capability.
5. **Check project instructions** — Read `copilot-instructions.md` and relevant `.instructions.md` files for project context the new skill should respect.

Report any findings to the user: "I found X existing skills. Your new skill complements/overlaps with [specific skill]."
</context-gathering>

### Step 3: Draft

Using the interview answers and workspace scan:

**Frontmatter:**
- Derive the `name` in strict kebab-case (lowercase, hyphens only, max 64 chars). If the workspace uses a naming convention (e.g., `kit-{category}-{verb}-{object}`), follow it.
- Draft a `description` that follows the required `[What it does]. Use when [trigger scenarios].` pattern, explicitly listing the trigger phrases from question 5
- Determine the directory location (`.github/skills/{skill-name}/` or `.github/skills/{category}/{skill-name}/`)

**Body:**
- Plan the body structure using the Skill Body Structure Guide below
- Determine which XML tags are appropriate for the skill's content
- Identify any bundled resources to include (templates, examples, scripts)

#### Skill Design Analysis

Before presenting the draft to the user, run these four named analysis techniques. Use Sequential Thinking MCP (`sequential-thinking/*`) for complex skills where boundaries or requirements are unclear.

**1. Requirements MECE Check** — Decompose the skill's scope into five non-overlapping partitions. Verify nothing is missing and nothing overlaps:

| Partition | Question to answer |
|---|---|
| **Input** | What triggers this skill? What arguments does it accept? |
| **Procedure** | What steps does the agent follow? What decisions does it make? |
| **Output** | What files, messages, or artifacts does it produce? |
| **Boundaries** | What does this skill explicitly NOT do? What does it delegate? |
| **Error handling** | What failure paths exist? How does the skill recover? |

**2. Scope Boundary Test** — For the specific skill being designed, dynamically evaluate its borders against adjacent mechanisms. Ask: *"Where does this skill's responsibility end? Does any step bleed into agent persona territory? Instruction territory? Prompt territory?"* This is the dynamic version of the static `<belongs-in-skill>` / `<does-not-belong-in-skill>` analysis — applied to this particular skill's content.

**3. Assumption Audit** — List exactly 4 assumptions the skill design makes, one per dimension. For each, ask: *"What if this is wrong? Does the skill still work, or does it fail silently?"*

| Dimension | What to audit |
|---|---|
| **Workspace state** | Project structure, existing files, directory layout |
| **Agent capabilities** | Tool access, MCP servers, file creation permissions |
| **User expertise** | Beginner vs. expert, familiarity with Copilot customization |
| **Triggering context** | How users will invoke the skill (auto vs. manual), typical phrasing |

**4. Skill Pre-Mortem** — Imagine the skill has been deployed and an agent produced poor results using it. Check each of these five failure modes against the draft:

| Failure Mode | What to verify in the draft |
|---|---|
| **Description too vague** → skill never auto-triggers | Description has specific verb + multiple trigger phrases |
| **Steps too vague** → agent improvises badly | Each step specifies what to do, check, and produce |
| **Missing validation** → poor output goes uncaught | Validation checklist covers structural + content quality |
| **Scope too broad** → skill does everything, does nothing well | Boundaries are explicit, escalation criteria defined |
| **Missing edge cases** → common scenarios fail | Empty/minimal/maximal/conflicting inputs handled |

If any row fails, revise the draft before presenting to the user.

Present the draft frontmatter and body outline to the user for confirmation before generating.

### Step 4: Generate

Once confirmed:

<rules>
1. Create the directory named exactly as the skill name.
2. Create `SKILL.md` inside it using the bundled [skill-template.md](./skill-template.md) as the structural starting point:
   - Write valid YAML frontmatter (use quoted strings for `description`, never `>-`)
   - Write the body instructions using clear numbered steps (Step 1, Step 2, Step 3...) so the agent can follow them methodically
   - Use XML-like semantic tags per the repository's tag conventions: `<rules>`, `<questions>`, `<validation>`, `<template>`, `<example>`, `<context-gathering>`, `<decision-guide>`, `<belongs-in-X>`, `<does-not-belong-in-X>`, `<escalation-criteria>`, `<user-message>`, `<best-practices>`
3. Add any bundled resources — scripts, templates, examples — referenced from the SKILL.md body via relative paths.
4. Keep the SKILL.md body under 500 lines. If approaching this limit, extract domain-specific content, advanced patterns, or extensive examples to separate resource files.
</rules>

### Step 5: Validate

Before reporting completion, iterate through every check below.

<validation>
- [ ] `name` field matches the parent directory name exactly
- [ ] `name` follows the workspace naming convention (if one exists)
- [ ] `description` uses quoted string syntax (`"..."`) — no YAML multiline (`>-`, `|`)
- [ ] `description` contains no XML tags
- [ ] `description` follows the `[What]. Use when [triggers].` pattern
- [ ] `description` starts with a verb and includes the "Use when" trigger clause
- [ ] YAML frontmatter is valid (properly closed `---` delimiters)
- [ ] `argument-hint` is present and helpful (if `user-invocable: true`)
- [ ] Body has a clear step-by-step procedure with numbered steps
- [ ] Body uses appropriate XML semantic tags (`<rules>`, `<questions>`, `<validation>`, etc.)
- [ ] Resources referenced in the body actually exist in the skill directory
- [ ] No content that belongs in an agent, prompt, or instruction file (see Key Concepts above)
- [ ] No `README.md` file in the skill directory (only `SKILL.md` is recognized)
- [ ] Skill directory is in the correct location (`.github/skills/` or `.github/skills/{category}/`)

**Adversarial Walkthrough** — After structural checks pass, stress-test the skill:
- [ ] Would an agent with no prior context about this workspace follow the steps successfully?
- [ ] Does every step handle gracefully when expected workspace state isn't found (no existing skills, empty project)?
- [ ] Could this skill's description be confused with another existing skill during auto-invocation?
- [ ] Is any step doing two unrelated things at once? (Single Responsibility violation)
</validation>

After validation, display this message to the user:

<user-message>
**How to use your new skill:**
1. The skill auto-triggers when Copilot detects a matching request based on the description
2. You can also invoke it manually via `/skill-name` in Copilot Chat
3. Verify it loads correctly by checking **Diagnostics** (right-click in Chat view → Diagnostics)

To test: ask Copilot a question using one of the trigger phrases from the skill's description and confirm the skill activates.
</user-message>

## Reference

### SKILL.md Template

Use the bundled [skill-template.md](./skill-template.md) as the structural starting point. For complete examples, see:
- [Minimal skill example](./examples/minimal-skill.md) — simple skill, no bundled resources
- [Intermediate skill example](./examples/intermediate-skill.md) — multi-step skill with resources, XML tags, and context-gathering

<template name="frontmatter">
```yaml
---
name: {skill-name}
description: "{Does what}. Use when {trigger scenarios}."
argument-hint: Describe what input to provide
user-invocable: true
disable-model-invocation: false
---
```
</template>

### Frontmatter Fields

| Field | Required | Notes |
|-------|----------|-------|
| `name` | Yes | Must match parent directory name. Strictly lowercase-with-hyphens. Max 64 chars. |
| `description` | Yes | **Critical for auto-invocation.** See Writing Effective Descriptions below. Max 1024 chars. |
| `argument-hint` | No | Shown in chat input when skill is invoked as `/` command. |
| `user-invocable` | No | Default `true`. Set `false` to hide from `/` menu. |
| `disable-model-invocation` | No | Default `false`. Set `true` to require manual `/` invocation only. |

### Writing Effective Descriptions

The `description` is the **single most important field** for auto-invocation. Copilot reads it during skill discovery (Level 1) to decide whether to load the skill. A vague description means the skill never gets triggered.

**Required pattern:** `[What it does] + [When to use it]`

Every description must have two parts:
1. **What it does** — a concrete, specific statement of the skill's action (third person)
2. **When to use it** — explicit trigger scenarios with keywords the user is likely to say

<example quality="bad">
- "Helps with testing." — Too vague, Copilot can't distinguish this from any other testing tool
- "A skill for specs." — No action verb, no trigger scenario
- "I can help you create components." — First person, no trigger clause
</example>

<example quality="good">
- "Scaffolds a new specification document. Use when user asks to create, draft, or start a new spec." — Clear action + explicit trigger words
- "Verifies implementation matches its specification. Use when user asks to check conformance, validate against spec, or review spec compliance." — Concrete action + multiple trigger phrases
- "Creates tickets in Linear. Use when user mentions sprint planning, backlog grooming, or ticket creation." — Specific tool + situational triggers
</example>

<rules>
- Start with a verb ("Scaffolds", "Creates", "Verifies", "Generates")
- Write in third person — not "I can..." or "You can..."
- Include the words "Use when" to clearly separate the trigger clause
- List multiple trigger phrases/synonyms the user might say
- Never include XML tags in the description — strip them if present
- Always use quoted strings (`"..."`) for the description value — never use YAML multiline syntax (`>-`, `|`, `>`, `|-`)
- Be specific enough that Copilot can distinguish this skill from others
</rules>

### Skill Body Structure Guide

A well-structured skill body follows a consistent pattern. Adapt these sections to the skill's domain:

**1. What This Skill Does** — Opening section (1-2 sentences). State the specific outcome and why it exists.

**2. Key Concepts** (optional) — Include when the skill's domain has common misconceptions. Use a comparison table and `<belongs-in-X>` / `<does-not-belong-in-X>` / `<escalation-criteria>` tags to define boundaries.

**3. Step-by-step Procedure** — The core of the skill. Break the workflow into clear, sequential steps:

| Step | Purpose | Common Tags |
|------|---------|-------------|
| Interview / Gather Requirements | Get user input before proceeding | `<questions>` |
| Workspace Scan / Discovery | Check existing state, detect conventions | `<context-gathering>` |
| Draft / Plan | Compose output, present for confirmation | `<template>`, `<decision-guide>` |
| Generate / Execute | Create files, run operations | `<rules>` |
| Validate | Verify correctness before reporting done | `<validation>`, `<user-message>` |

Not every skill needs all five steps. A simple skill might only need Gather → Execute → Validate.

**4. Reference Material** — Supporting content consulted during execution: field descriptions, decision guides, writing guidelines, templates. This section is navigated on-demand, not followed sequentially.

### Progressive Disclosure: Bundled Resources

Skills leverage a three-level loading architecture:

1. **Discovery** — Only `name` + `description` from frontmatter are pre-loaded. Token cost: minimal.
2. **Activation** — When a task matches, the full `SKILL.md` body loads into context.
3. **Execution** — Bundled resource files load only when the agent navigates to them. Token cost: zero until accessed.

**Key insight:** Resource files in the skill directory are **free until accessed**. You can bundle comprehensive resources without impacting context window usage.

<decision-guide name="inline-vs-resource-file">
| Content type | Keep inline in SKILL.md | Extract to resource file |
|---|---|---|
| Core workflow steps | Yes | — |
| Essential rules (must always be visible) | Yes | — |
| Brief examples (1-3 lines) | Yes | — |
| Complete templates / skeletons | — | Yes (e.g., `template.md`) |
| Extensive examples / worked walkthroughs | — | Yes (e.g., `examples/`) |
| Domain-specific reference material | — | Yes (e.g., `reference/`) |
| Large decision tables | — | Yes |
| Content that grows over time | — | Yes |
</decision-guide>

<rules>
- The directory name MUST exactly match the `name` field in SKILL.md frontmatter
- One `SKILL.md` per directory — each skill gets its own directory
- Resources in the skill directory are only loaded when the agent references them (progressive disclosure)
- Keep references **one level deep** from `SKILL.md` — avoid `SKILL.md → doc.md → details.md` chains
- For resource files longer than 100 lines, include a table of contents at the top
- Use descriptive file names: `form_validation_rules.md`, not `doc2.md`
- Skills work across VS Code, Copilot CLI, and Copilot coding agent
</rules>

<template name="directory-structure">
```
.github/skills/
  my-skill/
    SKILL.md           # Required — skill instructions
    template.md        # Optional — fillable skeleton
    examples/          # Optional — worked examples
      minimal.md
      advanced.md
    scripts/           # Optional — automation scripts
      validate.py
```
</template>

### MCP Tool Integration

When the task benefits from MCP (Model Context Protocol) servers, reference them in the skill body:

| MCP Server | When to use | Example tasks |
|------------|------------|---------------|
| **Sequential Thinking** (`sequential-thinking/*`) | Complex multi-step reasoning, architecture decisions | Skill Design Analysis, scope boundary evaluation, pre-mortem reasoning |
| **Context7** (`context7_*`) | Up-to-date framework/library documentation | Code generation with latest API patterns |
| **Fetch** (`fetch` or `web/fetch`) | Tasks requiring external web content | Checking documentation, API references |

**When to use Sequential Thinking for skill design:**
- The skill has >5 procedural steps or involves multiple resource files
- Scope boundaries with existing skills/prompts/agents are unclear
- The user's requirements are ambiguous or could map to multiple mechanisms
- The Skill Design Analysis (Step 3) reveals conflicting constraints or trade-offs

**Skip Sequential Thinking when:**
- Simple 3-step skill with clear requirements and no overlap concerns
- The skill is a straightforward adaptation of an existing example
- All four analysis techniques in Step 3 produce clean results with no tensions

<best-practices>
- **Conciseness justifies its tokens**: Challenge each paragraph — "Does this justify its token cost?" Assume the agent's baseline knowledge; only add context it doesn't already have.
- **Tell what TO DO, not what NOT to do**: "Use formatted tables for comparison" instead of "Do not use unformatted lists."
- **Consistent terminology**: Pick one term and use it throughout (e.g., always "skill" not mixing "capability", "workflow", "command").
- **Calibrate freedom**: Fragile operations need exact commands with low freedom. Flexible tasks need general guidance with high freedom.
- **Feedback loops for quality-critical tasks**: Include validate → fix → re-validate cycles for operations where correctness matters.
- **Examples over prose**: 3-5 input/output examples communicate expectations better than paragraphs of description.
</best-practices>