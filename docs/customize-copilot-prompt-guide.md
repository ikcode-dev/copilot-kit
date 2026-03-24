# Customize Copilot with `/kit-customize-copilot`

> A user-facing guide to the preferred public entrypoint for Copilot customization in this repository

## Table of Contents

- [What `/kit-customize-copilot` is for](#what-kit-customize-copilot-is-for)
- [How it routes to `kit-copilot-engineering-team`](#how-it-routes-to-kit-copilot-engineering-team)
- [How to use it in Copilot Chat](#how-to-use-it-in-copilot-chat)
- [How to describe the outcome you want](#how-to-describe-the-outcome-you-want)
- [Copy-paste-ready examples by user intent](#copy-paste-ready-examples-by-user-intent)
  - [Project-wide Copilot guidance](#project-wide-copilot-guidance)
  - [File-type or tech-specific instructions](#file-type-or-tech-specific-instructions)
  - [Custom agent creation](#custom-agent-creation)
  - [Reusable prompt or skill workflow creation](#reusable-prompt-or-skill-workflow-creation)
  - [Commit message customization](#commit-message-customization)
  - [Workspace audit or gap analysis](#workspace-audit-or-gap-analysis)
- [Internal building blocks vs public starting point](#internal-building-blocks-vs-public-starting-point)
- [Quick recap](#quick-recap)

---

## What `/kit-customize-copilot` is for

`/kit-customize-copilot` is the **preferred public entrypoint** for Copilot customization work in this repository.

Use it when you want Copilot to help you:

- create or improve project-wide Copilot guidance,
- add instructions for a specific language, framework, or file type,
- create a custom agent,
- create a reusable prompt or skill workflow,
- customize commit message behavior,
- or audit your current Copilot setup and recommend gaps.

In plain English: if your goal is **"make Copilot work better for this project or workflow"**, this is usually the best place to start.

You do **not** need to decide up front whether the answer should be an instruction file, a prompt, a skill, an agent, or a combination of artifacts. Describe the outcome you want, and let the orchestration layer figure out the likely implementation path.

## How it routes to `kit-copilot-engineering-team`

When you run `/kit-customize-copilot`, the prompt routes your request to `kit-copilot-engineering-team`.

That team acts as the repository's Copilot customization orchestrator. Its job is to:

- understand the outcome you want,
- inspect the relevant repository context,
- classify the request,
- and choose the most appropriate internal workflow for the job.

That means you do **not** need to pick an internal `kit-copilot-create-*` asset yourself.

Instead of asking yourself questions like:

- "Do I need a prompt or a skill?"
- "Should this be project-wide instructions or tech-specific instructions?"
- "Do I need an agent, a prompt, or both?"

...you can simply describe the result you want.

Direct agent usage is still possible if you prefer selecting `kit-copilot-engineering-team` from the agent picker, but it is **not** the primary documented path here. The recommended public starting point is still `/kit-customize-copilot`.

## How to use it in Copilot Chat

1. Open **Copilot Chat** in VS Code.
2. Type `/kit-customize-copilot`.
3. Add a plain-language request describing the customization outcome you want.
4. Send the message and continue the conversation if Copilot asks for small clarifications.

Basic shape:

```text
/kit-customize-copilot <describe the Copilot customization outcome you want>
```

Example:

```text
/kit-customize-copilot Create project-wide Copilot guidance for this repository so suggestions follow our TypeScript, testing, and architecture conventions.
```

You can keep it short, but better requests usually include:

- the outcome you want,
- the scope (`whole repo`, `React files`, `commit messages`, `custom agent`, and so on),
- relevant technologies,
- and any important constraints or preferences.

## How to describe the outcome you want

The easiest way to get a good result is to describe **what you want Copilot to do better**, not which internal skill you think should be used.

Good request ingredients:

- **Desired outcome** — What should improve?
- **Scope** — Whole repository, one tech stack, one file family, one workflow, one persona.
- **Context** — Languages, frameworks, architecture, team conventions.
- **Constraints** — Required style, naming, review depth, output format, or guardrails.

A simple pattern that works well:

```text
/kit-customize-copilot I want Copilot to <desired behavior/outcome> for <scope>. This project uses <tech/context>. Please create or update the customization artifacts that fit best.
```

### Good examples of outcome-based requests

- "I want Copilot to follow our repo-wide conventions when editing code."
- "I want better instructions for React components and test files."
- "I need a custom agent for backend API design reviews."
- "I want a reusable workflow for generating migration plans."
- "I want commit messages to follow our release process."
- "Please audit the current Copilot setup and tell me what's missing."

### What you usually do **not** need to do

You typically do **not** need to say things like:

- "Use the create-agent skill"
- "Invoke the create-instruction skill"
- "Route this through a specific internal worker"

If you already know the artifact type, you can mention it. But if you do not, plain-language intent is enough. The orchestrator is expected to classify the request and choose the likely internal path.

## Copy-paste-ready examples by user intent

> The examples below are written to be pasted directly into Copilot Chat. The listed results are **likely** or **typical** outcomes, not hard guarantees about the exact internal routing.

### Project-wide Copilot guidance

Use this kind of request when you want broad, repository-level Copilot behavior.

Example requests:

```text
/kit-customize-copilot Create project-wide Copilot guidance for this repository so suggestions follow our TypeScript, testing, and architectural conventions.
```

```text
/kit-customize-copilot Review this repo and add or improve the top-level Copilot instructions so the AI stays aligned with our stack, folder structure, and team rules.
```

Likely outcome:

A typical outcome is that the orchestrator reviews the repository and creates or updates broad Copilot guidance that applies across the project.

Likely artifacts:

- `.github/copilot-instructions.md`
- possibly supporting `.github/instructions/*.instructions.md` files if the repo needs more targeted guidance

### File-type or tech-specific instructions

Use this when the customization should apply only to a certain language, framework, layer, or file pattern.

Example requests:

```text
/kit-customize-copilot Add Copilot instructions for React and TypeScript component files so generated code follows our props, styling, and testing patterns.
```

```text
/kit-customize-copilot Create targeted guidance for API route files and database migration files. I want Copilot to be stricter about validation, error handling, and rollback safety.
```

Likely outcome:

The orchestrator will typically classify this as a targeted-instructions request and produce technology-specific or file-pattern-specific guidance rather than only broad repo-wide guidance.

Likely artifacts:

- one or more `.github/instructions/*.instructions.md` files
- sometimes a refinement to `.github/copilot-instructions.md` if shared context also needs tightening

### Custom agent creation

Use this when you want a specialized Copilot persona with a distinct role, workflow, or tool posture.

Example requests:

```text
/kit-customize-copilot Create a custom agent for security-focused code reviews in Node.js services. I want it to be strict about auth, secrets, validation, and dependency risk.
```

```text
/kit-customize-copilot I need a backend architecture agent that helps design APIs, data models, and service boundaries for Go and TypeScript projects.
```

Likely outcome:

A common outcome is that the orchestrator defines a new specialized agent with a clear persona, workflow, and usage guidance.

Likely artifacts:

- `.github/agents/<agent-name>.agent.md`
- sometimes related instructions or documentation if the agent benefits from shared project context

### Reusable prompt or skill workflow creation

Use this when you want a repeatable workflow for a recurring task.

Example requests:

```text
/kit-customize-copilot Create a reusable Copilot prompt for generating API implementation plans from selected route definitions and project conventions.
```

```text
/kit-customize-copilot Build a reusable workflow for scaffolding database migration review checklists. Choose a prompt or skill format based on what fits best.
```

```text
/kit-customize-copilot I need a repeatable workflow for turning a selected specification into implementation tasks. Please create the best customization mechanism for that.
```

Likely outcome:

The orchestrator will often choose a reusable task workflow artifact. Depending on the complexity, that may be a prompt, a skill, or a combination of lightweight supporting assets.

Likely artifacts:

- `.github/prompts/<name>.prompt.md`
- or `.github/skills/<skill-name>/SKILL.md`
- possibly supporting resource files if the workflow is richer than a single prompt

### Commit message customization

Use this when you want Copilot-generated commit messages to follow a team or repository standard.

Example requests:

```text
/kit-customize-copilot Set up commit message instructions so Copilot writes Conventional Commits with a scope based on the changed area of the repository.
```

```text
/kit-customize-copilot Customize our commit message guidance so releases, fixes, and refactors follow a consistent format with short summaries and useful bodies.
```

Likely outcome:

A typical outcome is commit-message guidance tailored to your team's preferred structure and terminology.

Likely artifacts:

- `.github/commit-message-instructions.md`

### Workspace audit or gap analysis

Use this when you are not sure what should be created yet and want the repository assessed first.

Example requests:

```text
/kit-customize-copilot Audit this workspace and tell me which Copilot customization files are missing, weak, duplicated, or outdated.
```

```text
/kit-customize-copilot Review the current Copilot setup in this repository and recommend the highest-value improvements before creating anything.
```

```text
/kit-customize-copilot Do a gap analysis of our Copilot customizations for this codebase, especially around instructions, prompts, agents, and commit message guidance.
```

Likely outcome:

The orchestrator will typically start with discovery and classification, then produce recommendations and may suggest or create a prioritized set of follow-up artifacts.

Likely artifacts:

- an audit summary in chat
- possibly recommended updates to existing `.github/` customization files
- sometimes new artifacts if the request explicitly asks the agent to implement the recommended changes

## Internal building blocks vs public starting point

You may still see repository-owned `kit-copilot-create-*` assets in this repository. That is expected.

Those assets still matter, but they are now best understood as **internal building blocks** rather than the preferred public starting point.

In practice, that means:

- they still exist as reusable procedural assets,
- they still help power customization workflows behind the scenes,
- but users are generally **not** expected to choose among them manually before starting.

The recommended public UX is:

1. start with `/kit-customize-copilot`,
2. describe the outcome you want,
3. let `kit-copilot-engineering-team` classify the request and choose the likely implementation path.

If you are an advanced user and want to work directly with an agent or a lower-level artifact path, that can still be possible in some situations. It is just **not the primary path this repository documents and recommends**.

## Quick recap

- `/kit-customize-copilot` is the preferred public starting point for Copilot customization in this repository.
- It routes to `kit-copilot-engineering-team`.
- You do **not** need to choose an internal skill first.
- Describe the outcome you want in plain language.
- The orchestrator will typically decide the likely artifact or workflow path.
- The `kit-copilot-create-*` assets remain useful internal building blocks, but they are not the preferred public entry surface.
