---
name: kit-copilot-create-instruction
description: "Creates fine-grained .github/instructions/*.instructions.md files with targeted applyTo glob patterns for specific technologies, file types, or architectural layers. Use when user asks to create, scaffold, generate, or set up granular instructions, file-type-specific instructions, technology-specific instructions, or .instructions.md files."
argument-hint: Describe the scope, technology, or file patterns for which you want to create granular instruction files (e.g., "React components", "API routes", "test files")
user-invocable: true
disable-model-invocation: false
---

# Create Granular Copilot Instructions

## What This Skill Does

Creates properly structured granular instruction files (`.instructions.md`) for GitHub Copilot in VS Code following the [custom instructions documentation](https://code.visualstudio.com/docs/copilot/customization/custom-instructions). These files provide **targeted, file-type-specific guidance** that activates only when Copilot is working with matching files.

Granular instructions are the **conditional layer** of Copilot customization — they complement the universal `.github/copilot-instructions.md` by adding context-specific conventions that would be too verbose or irrelevant in the core file.

## Key Concepts: Granular Instructions vs Other Customizations

Understanding the separation of concerns is critical for effective granular instructions. Each customization mechanism has a distinct purpose:

| Component | Defines | Analogy | File Type |
|-----------|---------|---------|-----------|
| **Core Instructions** | Universal project context for ALL interactions | The company handbook | `.github/copilot-instructions.md` |
| **Granular Instructions** | Context-specific conventions for MATCHING files | Department-specific guidelines | `.github/instructions/*.instructions.md` |
| **Custom Agent** | WHO the AI is and HOW it behaves | The employee's role and personality | `.agent.md` |
| **Agent Skill** | Reusable CAPABILITY or multi-step workflow | A specialized training module | `SKILL.md` in a skill directory |
| **Prompt File** | WHAT specific task to perform | A work order or task assignment | `.prompt.md` |

### The Layered Instruction Architecture

GitHub Copilot supports a layered approach to custom instructions:

1. **Core layer** (`.github/copilot-instructions.md`) — Attached to EVERY Copilot conversation. Contains universal project context.
2. **Granular layer** (`.github/instructions/*.instructions.md`) — Applied conditionally via `applyTo` glob patterns. Contains targeted, context-specific guidance.

This skill creates the **granular layer** files that complement (not duplicate) the core instructions.

<belongs-in-instruction>
- File-type-specific naming conventions
- Framework patterns for specific file types (e.g., React component patterns for `*.tsx`)
- Testing patterns for test files
- API response formats for route handlers
- Import ordering for specific file types
- Type patterns for specific domains
- Language-specific or framework-specific conventions that apply only to matching files
</belongs-in-instruction>

<does-not-belong-in-instruction>
- General project context → belongs in `.github/copilot-instructions.md`
- Universal coding standards → belongs in `.github/copilot-instructions.md`
- Technology stack overview → belongs in `.github/copilot-instructions.md`
- Architectural decisions that apply everywhere → belongs in `.github/copilot-instructions.md`
- AI persona definitions → belongs in custom agents (`.agent.md`)
- Reusable multi-step workflows → belongs in agent skills (`SKILL.md`)
- Task-specific templates → belongs in prompt files (`.prompt.md`)
- How to create Copilot customizations (meta-guidance about Copilot tooling itself)
</does-not-belong-in-instruction>

### The Golden Rule

> **Granular instruction files must NEVER use `**` as the `applyTo` pattern.**

If an instruction applies everywhere, it belongs in `.github/copilot-instructions.md`, not in a granular file. The whole point of granular files is specificity — they activate only when working with matching files.

## Step-by-step Procedure

### Step 1: Discovery

Before creating any instruction files, perform systematic discovery to understand the project's technologies, architecture, and existing conventions.

<context-gathering>
1. **Read existing instructions** — Check `.github/copilot-instructions.md` for what's already covered at the universal level. Check `.github/instructions/` for existing granular instruction files to avoid duplication or overlap.
2. **Scan package manifests** — Read `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pom.xml`, `*.csproj` to identify technologies in use.
3. **Scan config files** — Read `tsconfig.json`, `vite.config.*`, `next.config.*`, `tailwind.config.*`, `biome.json`, `.eslintrc*` for framework and linter configurations.
4. **Map architecture layers** — Identify distinct boundaries in the project structure by responsibility (`api/`, `components/`, `services/`, `hooks/`, `stores/`), by domain (`auth/`, `payments/`), or by application (`apps/web/`, `packages/shared/`).
5. **Analyze representative files** — For each identified cluster, read 3-5 representative files to extract consistent patterns (naming, imports, exports, error handling).
6. **Perform gap analysis** — Compare discovered patterns against the core `copilot-instructions.md`. What technology-specific details are too verbose for the core file? What file-type-specific patterns aren't covered?
</context-gathering>

Use the user's request to focus discovery on the relevant scope. If the user asks for "React component instructions," focus on component files and patterns rather than scanning the entire project.

### Step 2: Interview

After discovery, gather requirements from the user. Use `#tool:vscode/askQuestions` if available to ask all clarifying questions in a single structured prompt. Otherwise, ask in chat.

<questions>
1. What specific file types or technology scope should these instructions target? (e.g., "React components", "test files", "API routes", "database migrations")
2. Are there project-specific conventions that differ from framework defaults? (e.g., custom naming patterns, specific import ordering, preferred patterns over alternatives)
3. Should we create one instruction file or multiple files for different scopes? (Based on discovery, suggest a plan)

Do NOT proceed to drafting until you have a clear understanding of the target scope and any project-specific conventions.
</questions>

### Step 3: Plan

Based on discovery and interview, create a plan for which instruction files to create:

- Define the file name for each instruction file (pattern: `<scope>.instructions.md` or `<technology>-<purpose>.instructions.md`)
- Define the `applyTo` glob pattern for each file
- Verify no overlap between patterns — each file should target a distinct set of files
- Verify no overlap with existing instruction files in `.github/instructions/`

Present the plan to the user for confirmation before generating.

### Step 4: Generate

Once confirmed, create the instruction files.

Use the bundled [instruction-template.md](./instruction-template.md) as the structural starting point for each file. Fill in the placeholders with the discovered conventions.

<rules>
1. Create files in `.github/instructions/` directory.
2. Use descriptive file names: `<scope>.instructions.md` or `<technology>-<purpose>.instructions.md`.
3. Write valid YAML frontmatter with `applyTo`, `description`, and `name` fields.
4. Keep content concise — aim for 30-80 lines per file. Split into multiple files if longer.
5. Every statement must be actionable — guide concrete decisions, not abstract principles.
6. Include short code snippets where they clarify conventions better than prose.
7. Complement, don't duplicate — reference core instructions for universal patterns.
8. Do NOT paste content as a code block in chat — create the actual file.
</rules>

### Step 5: Validate

Before reporting completion, iterate through every check below.

<validation>
- [ ] Each file has valid YAML frontmatter with `applyTo`, `description`, and `name` fields
- [ ] No file uses `**` as the sole `applyTo` pattern (that belongs in core instructions)
- [ ] `applyTo` patterns are specific and non-overlapping across instruction files
- [ ] No duplication with content in `.github/copilot-instructions.md`
- [ ] No overlap with existing `.github/instructions/*.instructions.md` files
- [ ] Content is actionable and file-type-specific (not generic AI advice)
- [ ] Content does not include meta-guidance about Copilot customization itself
- [ ] Files are saved in `.github/instructions/` directory
- [ ] File names follow the `<scope>.instructions.md` or `<technology>-<purpose>.instructions.md` pattern
</validation>

## Glob Pattern Reference

### Effective Patterns

| Pattern | Use Case |
|---------|----------|
| `**/*.test.ts` | All TypeScript test files |
| `**/*.test.{ts,tsx}` | All TypeScript/TSX test files |
| `src/components/**/*.tsx` | React components in specific folder |
| `apps/*/src/**/*.ts` | TypeScript files across all apps in monorepo |
| `**/*.stories.{ts,tsx}` | Storybook story files |
| `src/api/**` | All files in API layer |
| `packages/ui/**` | All files in UI package |
| `**/migrations/**` | Database migration files |

### Anti-Patterns to Avoid

| Avoid | Why | Instead |
|-------|-----|---------|
| `**` | Too broad, use core instructions | Specific glob for file type/folder |
| `**/*` | Same as above | Be specific about scope |
| `*.ts` | Only matches root, misses subdirs | `**/*.ts` |
| Overlapping patterns | Causes confusion | Ensure mutual exclusivity |

## Advanced Techniques

### Differential Analysis

When the user's project has similar files that follow different conventions in different locations, use differential analysis:

- Compare how similar files differ across the codebase (e.g., components in `/features/` vs `/shared/components/`)
- Create separate instruction files with folder-specific `applyTo` patterns for each convention

### Pattern Triangulation

Validate conventions by checking multiple sources before codifying them in instruction files:

1. Actual code patterns (what the code does)
2. Linter/formatter configurations (what's enforced)
3. Existing documentation (what's documented)
4. Git history (what patterns have been consistent)

### Boundary Testing

For each potential instruction file, evaluate scope by asking:

- "If I apply this pattern more broadly, does it still make sense?"
- "If I narrow this pattern, would I lose important context?"

The goal is finding the **minimum viable scope** that captures meaningful conventions.

## MCP Tools

### Context7

Use `#tool:context7/*` strategically:

- **Use when**: Verifying framework-specific best practices for targeted files (e.g., React 19 conventions for `*.tsx`), understanding library-specific patterns, confirming testing framework conventions.
- **Skip when**: General language conventions (Copilot knows these), or when existing codebase patterns are clear and consistent.

### Sequential Thinking

Use `#tool:sequentialthinking/*` for complex scenarios:

- **Use when**: Multi-technology projects where instruction file boundaries are unclear, monorepos with different conventions across packages, inconsistent patterns in the codebase.
- **Skip when**: Single-technology projects with clear patterns, user has specified exactly which files to create, small codebases with obvious conventions.
