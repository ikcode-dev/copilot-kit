---
name: {skill-name}
description: "{Does what}. Use when {trigger scenarios}."
argument-hint: Describe what input to provide
user-invocable: true
disable-model-invocation: false
---

# {Skill Title}

## What This Skill Does

{1-2 sentences explaining what the skill accomplishes and why it exists. Be specific about the outcome.}

## Key Concepts

{Optional — include only if the skill's domain has common misconceptions or if users might confuse it with other mechanisms. Use a comparison table or brief clarification.}

## Step-by-step Procedure

### Step 1: {First Phase Name}

{Instructions for gathering requirements, understanding context, or preparing. Most skills start with an Interview or Discovery phase.}

<questions>
1. {First question to ask the user}
2. {Second question}
3. {Third question — include enough to proceed}

Do NOT proceed until you have clear answers to at least questions {list critical ones}.
</questions>

### Step 2: {Context Gathering Phase}

{Instructions for scanning the workspace, checking for conflicts, and gathering project context.}

<context-gathering>
1. {What to check first — e.g., existing implementations, naming conventions}
2. {What to check second — e.g., related customizations, project standards}
3. {What to check third — e.g., dependencies, configurations}

Report any findings to the user before proceeding.
</context-gathering>

### Step 3: {Planning/Drafting Phase}

{Instructions for composing the output based on gathered information. Present the draft to the user for confirmation before generating.}

### Step 4: {Generation Phase}

Once confirmed:

<rules>
1. {First generation rule — file creation}
2. {Second generation rule — content structure}
3. {Third generation rule — resources or dependencies}
</rules>

### Step 5: {Validation Phase}

Before reporting completion, iterate through every check below.

<validation>
- [ ] {First validation check}
- [ ] {Second validation check}
- [ ] {Third validation check}
- [ ] {Add checks for structural correctness, naming, content quality}
</validation>

After validation, display this message to the user:

<user-message>
**How to use your new {artifact}:**
1. {First usage step}
2. {Second usage step}
3. {Verification step}
</user-message>

## Reference

{Reference material that supports the procedure above — templates, decision guides, field descriptions, examples. This content is consulted during execution, not followed sequentially.}

### {Reference Section 1 — e.g., Field Descriptions}

| Field | Required | Notes |
|-------|----------|-------|
| {field} | {Yes/No} | {Description} |

### {Reference Section 2 — e.g., Writing Guidelines}

<rules>
- {Guideline 1}
- {Guideline 2}
</rules>

<example quality="good">
{A good example showing the expected output or pattern}
</example>

<example quality="bad">
{A bad example showing what to avoid, with explanation}
</example>

### {Reference Section 3 — e.g., Bundled Resources}

{Describe when and how to use bundled resources. Reference them via relative paths.}

```
{skill-name}/
  SKILL.md           # Required — skill instructions
  {resource}.md      # Optional — referenced resource
  examples/          # Optional — example files
```
