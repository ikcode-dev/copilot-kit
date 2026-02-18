---
name: kit-copilot-create-skill
description: "Scaffolds a new Agent Skill directory and SKILL.md file following the Agent Skills open standard. Use when a user asks to create, scaffold, or set up a new skill, slash command, or reusable capability for GitHub Copilot."
argument-hint: Skill name, purpose, and any specific capabilities or resources it should include
user-invokable: true
disable-model-invocation: false
---

# Create a New Agent Skill

## What This Skill Does

Creates a properly structured Agent Skill directory containing a `SKILL.md` file and optional resources (scripts, templates, examples). Follows the [Agent Skills specification](https://agentskills.io/) and [VS Code docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills) for Copilot's skills development.

## Step-by-step Procedure

### Step 1: Interview

Before creating anything, gather requirements from the user. Use `#tool:vscode/askQuestions` if available to ask all clarifying questions in a single structured prompt. Otherwise, ask in chat.

**Questions to ask:**
1. What repetitive task do you want to automate? (e.g., writing tests, planning sprints, scaffolding components)
2. What should the skill be called? (suggest a kebab-case name if the user doesn't have one)
3. Should it appear as a `/` slash command, or only trigger automatically? (determines `user-invokable` and `disable-model-invocation`)
4. What phrases would a user say that should trigger this skill? (feeds into the description's "Use when" clause)
5. Does the skill need bundled resources — scripts, templates, examples?

Do **not** proceed to drafting until you have clear answers to at least questions 1, 2, and 4.

### Step 2: Draft

Using the interview answers:
- Derive the `name` in strict kebab-case (lowercase, hyphens only, max 64 chars)
- Draft a `description` that follows the required `[What it does]. Use when [trigger scenarios].` pattern, explicitly listing the trigger phrases from question 4
- Determine the directory location (`.github/skills/{skill-name}/` or `.github/skills/{category}/{skill-name}/`)

Present the draft frontmatter to the user for confirmation before generating.

### Step 3: Generate

Once confirmed:
1. **Create the directory** named exactly as the skill name.
2. **Create `SKILL.md`** inside it:
   - Write valid YAML frontmatter (use quoted strings for `description`, never `>-`)
   - Write the body instructions using clear numbered steps (Step 1, Step 2, Step 3...) so the agent can follow them methodically
3. **Add any bundled resources** — scripts, templates, examples referenced from the SKILL.md body via relative paths.

### Step 4: Validate

Before reporting completion, check:
- [ ] `name` field matches the parent directory name exactly
- [ ] `description` uses quoted string syntax (`"..."`) — no YAML multiline (`>-`, `|`)
- [ ] `description` contains no XML tags
- [ ] `description` follows the `[What]. Use when [triggers].` pattern
- [ ] No `README.md` file in the skill directory (only `SKILL.md` is recognized)
- [ ] YAML frontmatter is valid (properly closed `---` delimiters)

## SKILL.md Template

```yaml
---
name: {skill-name}
description: "{Does what}. Use when {trigger scenarios}."
argument-hint: Describe what input to provide
user-invokable: true
disable-model-invocation: false
---
```

### Frontmatter Fields

| Field | Required | Notes |
|-------|----------|-------|
| `name` | Yes | Must match parent directory name. Strictly lowercase-with-hyphens. Max 64 chars. |
| `description` | Yes | **Critical for auto-invocation.** See rules below. Max 1024 chars. |
| `argument-hint` | No | Shown in chat input when skill is invoked as `/` command. |
| `user-invokable` | No | Default `true`. Set `false` to hide from `/` menu. |
| `disable-model-invocation` | No | Default `false`. Set `true` to require manual `/` invocation only. |

### Writing Effective Descriptions

The `description` is the **single most important field** for auto-invocation. Copilot reads it during skill discovery (Level 1) to decide whether to load the skill. A vague description means the skill never gets triggered.

**Required pattern:** `[What it does] + [When to use it]`

Every description must have two parts:
1. **What it does** — a concrete, specific statement of the skill's action
2. **When to use it** — explicit trigger scenarios with keywords the user is likely to say

**Examples:**

| Quality | Description | Why |
|---------|-------------|-----|
| Bad | "Helps with testing." | Too vague — Copilot can't distinguish this from any other testing tool |
| Bad | "A skill for specs." | No action verb, no trigger scenario |
| Good | "Scaffolds a new specification document. Use when user asks to create, draft, or start a new spec." | Clear action + explicit trigger words |
| Good | "Verifies implementation matches its specification. Use when user asks to check conformance, validate against spec, or review spec compliance." | Concrete action + multiple trigger phrases |
| Good | "Creates tickets in Linear. Use when user mentions sprint planning, backlog grooming, or ticket creation." | Specific tool + situational triggers |

**Rules:**
- Start with a verb ("Scaffolds", "Creates", "Verifies", "Generates")
- Include the word "Use when" to clearly separate the trigger clause
- List multiple trigger phrases/synonyms the user might say
- Never include XML tags in the description — strip them if present
- Always use quoted strings (`"..."`) for the description value — never use YAML multiline syntax (`>-`, `|`, `>`, `|-`)
- Be specific enough that Copilot can distinguish this skill from others

### Body Content

The body should include:
- What the skill helps accomplish
- Step-by-step procedures for the agent to follow
- References to included resources via relative paths (e.g., `[template](./template.md)`)
- Examples of expected input and output

## Directory Structure Example

```
.github/skills/
  my-skill/
    SKILL.md           # Required — skill instructions
    template.js        # Optional — referenced resource
    examples/          # Optional — example files
      basic.md
      advanced.md
```

## Key Rules

- The directory name **must exactly match** the `name` field in SKILL.md frontmatter
- One `SKILL.md` per directory — each skill gets its own directory
- Resources in the skill directory are only loaded when the agent references them (progressive disclosure)
- Skills work across VS Code, Copilot CLI, and Copilot coding agent