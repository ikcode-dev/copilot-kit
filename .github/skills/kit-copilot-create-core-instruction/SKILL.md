---
name: kit-copilot-create-core-instruction
description: "Creates the foundational .github/copilot-instructions.md file that provides universal project context for all GitHub Copilot interactions. Use when user asks to create, generate, set up, or scaffold copilot instructions, core instructions, project instructions, or copilot-instructions.md."
argument-hint: Provide details about the project or let me discover them automatically
user-invocable: false
disable-model-invocation: false
---

# Create Core Copilot Instruction

## What This Skill Does

Creates a production-ready `.github/copilot-instructions.md` file — the foundational layer of GitHub Copilot's custom instructions system. This file is attached to **every** Copilot conversation, providing universal project context that shapes all AI interactions.

## Key Concepts: The Layered Instruction Architecture

GitHub Copilot supports a layered approach to custom instructions:

| Layer | File | Scope | Purpose |
|-------|------|-------|---------|
| **Core** | `.github/copilot-instructions.md` | Every conversation | Universal project context |
| **Granular** | `.github/instructions/*.instructions.md` | Matching files only (via `applyTo` glob) | File-type-specific or folder-specific conventions |

This skill creates the **core layer**. The core file must contain only what Copilot needs to know universally — regardless of which file or feature is being worked on. File-type-specific or folder-specific conventions belong in granular instruction files.

### The Golden Rule

> **The core file is attached to every single Copilot conversation. Every line must earn its place.**

## Content Boundaries

Knowing what belongs in the core file — and what doesn't — is critical for quality.

| Include in Core File | Exclude (Delegate or Omit) |
|----------------------|----------------------------|
| Project type and domain context | File-type-specific conventions (→ granular `.instructions.md` files) |
| Tech stack with versions (brief) | Standard language/framework behaviors |
| Universal naming conventions | Verbose API references |
| Architectural philosophy | Folder-specific patterns (→ granular `.instructions.md` files) |
| Cross-cutting patterns (errors, logging) | Library documentation |
| Common pitfalls/anti-patterns | Detailed configuration examples |
| Key workflow commands (essential only) | Exhaustive command lists |
| Testing philosophy (not test file specifics) | Test file structure details (→ granular files) |

**Never include instructions about:**
- How to create custom Copilot prompts, agents, skills, or instruction files
- Meta-guidance about Copilot customization itself

The core instruction file must be **about the project's code and conventions**, not about Copilot tooling.

## Step-by-step Procedure

### Step 1: Check for Existing File

Before starting, check if `.github/copilot-instructions.md` already exists in the workspace.

- **If it exists**: Read the current contents. Ask the user whether they want to replace it entirely or update/improve it. If updating, identify what's missing or outdated before proceeding.
- **If it doesn't exist**: Proceed to Step 2.

Also check for related files that inform the core instructions:
- `.github/instructions/*.instructions.md` — understand what granular instructions already exist so the core file doesn't duplicate them
- `AGENTS.md`, `CLAUDE.md` — alternative instruction formats that may contain relevant conventions

### Step 2: Discovery

Perform systematic discovery to identify what belongs in the universal context layer. For each phase, search the workspace for the listed files and patterns.

#### Phase 1: Project Identity

Establish the fundamental nature of the project:

- **Project type**: Web app, API, library, CLI tool, monorepo, microservice?
- **Domain**: E-commerce, fintech, healthcare, developer tools, etc.?
- **Stage**: Greenfield, mature, legacy modernization?

**Check**: `README.md`, `CONTRIBUTING.md`, project root for clues.

#### Phase 2: Technology Stack Inventory

Scan for authoritative technology sources and extract versions:

- **Package manifests**: `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pom.xml`, `*.csproj`, `Gemfile`, `pyproject.toml`, `composer.json`
- **Config files**: `tsconfig.json`, `vite.config.*`, `next.config.*`, `astro.config.*`, `webpack.config.*`, `.babelrc`, `Dockerfile`
- **Document only major technologies with versions** (e.g., "Next.js 14, TypeScript 5.4, Prisma 5.x")

#### Phase 3: Universal Conventions Mining

Identify conventions that apply across the entire codebase:

- **Linter/formatter configs**: `.eslintrc.*`, `eslint.config.*`, `biome.json`, `.prettierrc.*`, `ruff.toml`, `.golangci.yml`, `.editorconfig`
- **Existing documentation**: `README.md`, `CONTRIBUTING.md`, `ARCHITECTURE.md`, `docs/` directory, ADRs (Architecture Decision Records)
- **Cross-cutting patterns**: Error handling, logging, authentication approaches, API response formats

#### Phase 4: Architecture Overview

Map the high-level structure without going into file-level detail:

- **Folder structure philosophy**: Feature-based, layer-based, domain-driven?
- **Key architectural patterns**: Clean architecture, hexagonal, MVC, CQRS, etc.?
- **Monorepo structure** (if applicable): What packages exist and their relationships?

**Check**: Top-level directories, `turbo.json`, `nx.json`, `lerna.json`, `pnpm-workspace.yaml`.

**If critical information is missing after scanning**: Ask the user targeted questions to clarify uncertainties rather than making assumptions. For example, if the project has no README and the domain isn't obvious from code, ask.

#### Phase 5: Delegation Analysis

Determine what should NOT be in the core file — this is as important as what goes in.

Apply **The "Every File" Test** to each potential instruction:

> "Does this apply when working on ANY file in the project?"
> - **Yes** → Include in core file
> - **No** → Delegate to a granular `.instructions.md` file or omit

When you find conventions that are file-type-specific or folder-specific, note them as candidates for granular instruction files. Inform the user about these candidates after creating the core file.

### Step 3: Draft

Using the discovery results, compose the core instruction file:

1. **Use the bundled template** — Start from the [instruction-template.md](./instruction-template.md) as a structural foundation.
2. **Apply Convention Triangulation** — Validate conventions by cross-checking multiple sources:
   - Actual code patterns across different areas
   - Linter/formatter configurations
   - Existing documentation
   - Git history (what patterns are consistently followed?)
3. **Detect Greenfield vs Established**:
   - **Established project**: Mine conventions from existing code; document what IS, not what should be
   - **Greenfield project**: Suggest best practices for the stack; explicitly note these are recommendations
4. **Be concise** — Aim for **50-150 lines maximum**. Every line must earn its place.
5. **Structure for scanning** — Use clear headings, bullet points, and concise statements.
6. **Prioritize information** — Put the most important context (tech stack, architecture) first.

Present the complete draft to the user for review before generating.

### Step 4: Generate

Once the user confirms:

1. **Create the directory** `.github/` if it doesn't exist.
2. **Write the file** to `.github/copilot-instructions.md`.
3. Do NOT output the file content as a code block in chat — create the actual file.

### Step 5: Validate

Before reporting completion, verify:

- [ ] File is located at `.github/copilot-instructions.md`
- [ ] Content is within the 50-150 line target range
- [ ] Every instruction passes The "Every File" Test (universal applicability)
- [ ] No file-type-specific conventions are included (those belong in granular files)
- [ ] No Copilot meta-guidance (how to use prompts, agents, skills, etc.)
- [ ] No generic AI advice ("write clean code", "follow best practices")
- [ ] No library API documentation (Copilot already knows these)
- [ ] Tech stack versions are documented
- [ ] Architecture overview is present but concise

**After validation, inform the user about:**

1. **Granular instruction candidates** — Any file-type-specific or folder-specific conventions discovered during Phase 5 that should become `.github/instructions/*.instructions.md` files.
2. **Next steps** — Suggest creating granular instruction files for the identified candidates to complete the instruction architecture.

## Key Rules

- **Conciseness is non-negotiable** — The core file is attached to every conversation. Bloated instructions waste context window and degrade Copilot's performance.
- **Universal applicability** — Every line must pass The "Every File" Test. If it doesn't apply universally, it doesn't belong.
- **Discovery before drafting** — Never skip the discovery phases. Assumptions lead to incorrect or incomplete instructions.
- **Always create the file** — Write to `.github/copilot-instructions.md` directly. Never output instructions inline in chat as the final deliverable.
- **Respect existing granular files** — If `.github/instructions/` already has granular files, don't duplicate their content in the core file.
- **Document what IS, not what should be** — For established projects, capture actual conventions from the codebase, not aspirational ideals.
