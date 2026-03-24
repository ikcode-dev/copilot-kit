# 🚀 Copilot Kit

> A curated collection of GitHub Copilot prompts, instructions, and chat modes for modern web development

[![GitHub stars](https://img.shields.io/github/stars/ikcode-dev/copilot-kit)](https://github.com/ikcode-dev/copilot-kit/stargazers)
[![YouTube Channel](https://img.shields.io/badge/YouTube-IKcodeIgorWnek-red)](https://youtube.com/@IKcodeIgorWnek)
[![BlueSky 🦋](https://img.shields.io/badge/BlueSky%20🦋-%40ikcode.dev-blue)](https://bsky.app/profile/ikcode.dev)

## 🎯 What is this?

This repository contains battle-tested GitHub Copilot configurations that I use daily and showcase on my [YouTube channel](https://youtube.com/@IKcodeIgorWnek). Each prompt and configuration is designed to boost your productivity and help you write better code faster.

**Star ⭐ this repo** to keep up with new additions and updates!

## 📚 Contents

### 🎨 Custom Instructions

Fine-tuned instructions for specific frameworks and patterns

| Instruction | Description | Tech Stack |
|------------|-------------|------------|
| *Coming soon* | Custom instructions for specific frameworks | *Planned* |

### 💬 Prompts

Ready-to-use prompts for common development tasks

📖 **[Read the Custom Prompts Guide](docs/custom-prompts-guide.md)** for detailed documentation on how to use and create prompts.

#### 💡 How to Work with Prompts

Prompts are starting points, not one-shot solutions—this is **AI-assisted programming**. Run the prompt, optionally provide additional context, then iterate on the results until you're satisfied.

```mermaid
flowchart LR
    A[Run Prompt] --> B[Review Results]
    B --> C{Satisfied?}
    C -->|No| D[Iterate: Ask/Answer/Clarify]
    D --> B
    C -->|Yes| E[Done ✓]
```

#### 🏁 Getting Started — Customise Copilot for Your Project

New to this repo? Start with the public prompt entrypoint:

**Recommended default: [`/kit-customize-copilot`](.github/prompts/kit-customize-copilot.prompt.md)**

Run the prompt in Copilot Chat and describe the customization outcome you want. For example:

> *"/kit-customize-copilot Create project-wide Copilot guidance for this repository based on its tech stack and conventions"*

This routes to the [`kit-copilot-engineering-team`](.github/agents/kit-copilot-engineering-team.agent.md) orchestrator, which inspects the repo, classifies the request, and chooses the right internal workflow for you.

If you prefer to go deeper after the first pass, read the dedicated guide: **[Customize Copilot with `/kit-customize-copilot`](docs/customize-copilot-prompt-guide.md)**.

**Optional direct path:** if you like working from the agent picker, you can select [`kit-copilot-engineering-team`](.github/agents/kit-copilot-engineering-team.agent.md) directly and describe the same outcome there.

The repository-owned `kit-copilot-create-*` skills still exist as internal building blocks, but they are no longer the default onboarding path or the recommended public alternative to `/kit-customize-copilot`.

> **Why does this matter?** Without custom instructions, Copilot relies on generic knowledge. With them, it understands *your* project's conventions, patterns, and tech stack — producing more consistent, relevant, and accurate suggestions across your entire codebase.

#### 🔧 Task Prompts

Use these prompts for day-to-day development tasks.

| Prompt | Description | Category |
|--------|-------------|----------|
| [architecture-review](.github/prompts/architecture-review.prompt.md) | Focused architectural review using Sequential Thinking for multi-dimensional analysis | Architecture |
| [code-review](.github/prompts/code-review.prompt.md) | High-rigor, tech-agnostic code review that surfaces critical issues first | Code Quality |
| [pragmatic-code-review](.github/prompts/pragmatic-code-review.prompt.md) | Production-focused code review balancing engineering rigor with real-world constraints | Code Quality |
| [refactor](.github/prompts/refactor.prompt.md) | Intelligent refactoring following user instructions or applying best practices automatically | Code Quality |
| [optimize-performance](.github/prompts/optimize-performance.prompt.md) | Identify and eliminate performance anti-patterns using static analysis and language-idiomatic optimizations | Performance |
| [document](.github/prompts/document.prompt.md) | Generate exceptional documentation with Mermaid diagrams and practical examples | Documentation |

### 🤖 Custom Agents

Specialized Copilot agent personalities for different development scenarios

| Agent | Description | MCP Tools |
|-------|-------------|-----------|
| [kit-copilot-engineering-team](.github/agents/kit-copilot-engineering-team.agent.md) | Copilot engineering orchestrator — handles the full range of customization requests from single-mechanism tasks to complex multi-artifact projects. Delegates to specialized workers for research, creation, validation, and review. | Sequential Thinking |
| [kit-backend-engineer](.github/agents/kit-backend-engineer.agent.md) | Pragmatic backend engineer specializing in APIs, databases, and server-side architecture | Sequential Thinking, Context7 |

#### How to Use Custom Agents

1. **Copy the agent file** to your project's `.github/agents/` directory (VS Code automatically detects `.agent.md` files in this folder)
2. **Ensure required MCP servers are configured** in your `.vscode/mcp.json` (see [MCP Servers](#mcp-servers) section)
3. **Select the agent** from the agents dropdown in Copilot Chat (click on the current agent name at the top of the chat panel)
4. **Start chatting** — describe the feature, API endpoint, database operation, or issue you want to solve. You can also ask the agent to brainstorm ideas for backend architecture, API design, or database schema — it will generate multiple alternatives with trade-offs for you to choose from.

> **Note:** Custom agents require VS Code 1.106 or later.

#### 🏗️ Copilot Engineering Team — Getting Started

The **kit-copilot-engineering-team** agent is the optional direct-agent path for Copilot customization in this repository. If you prefer using the agent picker instead of the public prompt, select it directly and describe what you want to achieve.

For most users, the recommended public starting point remains [`/kit-customize-copilot`](.github/prompts/kit-customize-copilot.prompt.md).

**Example prompts:**

- *"I want the AI to always follow our team's coding conventions"* → research identifies the right mechanism, then creates the appropriate instruction files
- *"I need a reusable workflow for scaffolding API endpoints"* → creates an agent skill with full review
- *"I want a specialized AI persona for security reviews"* → creates a custom agent with quality validation
- *"Set up a complete Copilot customization suite for my project"* → full audit, gap analysis, and multi-artifact creation

The orchestrator delegates to specialized workers: the researcher discovers what exists and classifies what's needed, the creator builds the artifacts, the validator runs checks, and the reviewer ensures quality. For simple unambiguous requests, it uses a lightweight create-and-review path.

### 🏛️ Architecture Decision Records (ADRs)

Want to understand not just **what** is in this repository, but **why** it is structured this way? Browse the ADRs for the architectural and workflow decisions behind `copilot-kit`.

More ADRs will be added over time as the project evolves.

- 📘 [ADR index](docs/adr/README.md) — what ADRs are, when to create them, and how this repo uses them
- 0001 [Orchestrator architecture](docs/adr/0001-orchestrator-architecture.md) — why `copilot-kit` uses one primary orchestrator with curated domain orchestrators
- 0002 [Domain orchestrator team structure](docs/adr/0002-domain-orchestrator-team-structure.md) — why domain orchestrators use a public team metaphor while keeping orchestrator / worker / artifact as the internal architecture vocabulary

### 🧩 Agent Skills

Reusable skill artifacts that extend Copilot's capabilities. Skills are defined as `SKILL.md` files following the [Agent Skills specification](https://agentskills.io/), [VS Code Skills docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills), and can be adapted for your own repositories.

For the Copilot customization workflow in this repository, the preferred public slash-command entry is [`/kit-customize-copilot`](.github/prompts/kit-customize-copilot.prompt.md). The repository-owned `kit-copilot-create-*` skills below are documented as reusable building blocks and reference implementations — not as the default public onboarding path.

| Skill | Description |
|-------|-------------|
| [kit-copilot-create-agent](.github/skills/kit-copilot-create-agent/SKILL.md) | Scaffolds a custom agent (`.agent.md`) file for GitHub Copilot with persona, philosophy, tools, and workflow. Useful as a reusable building block when creating a new custom agent, AI persona, or chat mode. |
| [kit-copilot-create-commit-instructions](.github/skills/kit-copilot-create-commit-instructions/SKILL.md) | Generates a commit message instruction file for Copilot's commit message generation in VS Code. Useful for standardizing commit-message behavior in a repo. |
| [kit-copilot-create-core-instruction](.github/skills/kit-copilot-create-core-instruction/SKILL.md) | Creates the foundational `.github/copilot-instructions.md` file that provides universal project context for Copilot interactions. |
| [kit-copilot-create-instruction](.github/skills/kit-copilot-create-instruction/SKILL.md) | Creates fine-grained `.github/instructions/*.instructions.md` files with targeted `applyTo` glob patterns for specific technologies, file types, or architectural layers. |
| [kit-copilot-create-prompt](.github/skills/kit-copilot-create-prompt/SKILL.md) | Creates a simple Copilot prompt file (`.prompt.md`) for lightweight, repeatable workflows. |
| [kit-copilot-create-skill](.github/skills/kit-copilot-create-skill/SKILL.md) | Scaffolds an Agent Skill directory with `SKILL.md` and optional bundled resources for richer reusable workflows. |

#### How to Use Agent Skills

If you want to study, copy, or adapt Agent Skills for your own repository, there are two common ways to make them available. For this repository's Copilot customization workflow, though, start with [`/kit-customize-copilot`](.github/prompts/kit-customize-copilot.prompt.md) instead of manually choosing among the repository-owned `kit-copilot-create-*` skills.

##### Option A: Copy to your project (per-repo)

Copy the skill directory into your project's `.github/skills/` folder. This lets you adjust the skill to fit your project's specific needs.

##### Option B: Configure a global skills location (all repos, 🌟 recommended)

Point VS Code to this repo's skills directory so reusable skills are available across projects — no copying needed.

1. Open VS Code Settings (`Cmd + ,` on macOS, `Ctrl + ,` on Windows/Linux)
1. Search for **"Agent Skills Locations"** (setting ID: `chat.agentSkillsLocations`)
1. Add the absolute path to the skills directory, e.g.:

  ```text
  ~/dev/ikcode-dev/copilot-kit/.github/skills
  ```

1. Any user-invocable skills from that location will now appear in Copilot Chat across your workspaces

> **Tip:** If you clone this repo to a different path, update the setting accordingly. Use `~` for your home directory to keep it portable across machines.

### MCP Servers

What is MCP?

- Model Context Protocol (MCP) lets Copilot (and other MCP-capable assistants) connect to external "tool servers" for memory, project context, tools and frameworks docs, helps to structure it's thinking process, and domain-specific knowledge.
- This repo ships a ready-to-use `.vscode/mcp.json` plus a memory template (it's empty, MCP will fill it out) at `.mcp/memory.json.dist`.

#### What's In This Kit?

- `.vscode/mcp.json` - a sample configuration wiring up the servers below
- `.mcp/memory.json.dist` - template for local, per-project memory storage

#### Quick Setup

- Prerequisites
  - GitHub Copilot Chat in VSCode with MCP enabled in settings
  - Sometimes additional things like Node.js installed, etc. as required by each server (see their repos)

- Copy the memory storage template (if you want to use `memory` MCP) `.mcp/memory.json.dist` and save it as `.mcp/memory.json`
  - Initially `memory.json` is an empty file, so you can just create empty file at `.mcp/memory.json`
  - I recommend to add `.mcp/memory.json.dist` to the Git repo if you work with a team
  - ⚠️ Never commit the `.mcp/memory.json` file, it'll contain data relevant to your work
  - ⚠️ Memory file might be cleared on extensions update and in other cases, check MCP's repo
  - It's recommended to add similar instruction to your project's README so collaborators set it up too

- Copy configurations of the servers you want to use from the `.vscode/mcp.json` to your project
  - Sometimes MCP server has `${input:input-id}`in the `env` or other section, this means it's going to store some env variables in VSCode and the specific `input` is defined in the `inputs` field of the JSON configuration object at `.vscode/mcp.json`.
  - If `input` is required for a MCP server (like for `shadcn-ui`) you will be prompted for the value on the server's first startup, then it's going to be saved securely by VSCode.
  - 👍 To use `inputs` is my preferred way of storing env variables for MCP servers. I want to commit `.vscode/mcp.json`, so my team members can use them too anytime they want, without any danger of sharing the real env variables.

#### MCP Servers List

| Name | What it gives you | Notes |
|--------|-------------|----------|
| [Sequential Thinking](https://github.com/modelcontextprotocol/servers/tree/main/src/sequentialthinking) | This MCP gives the agent ability use more structured thinking process, it's really powerful when combined with the right prompts | 🌟 I use it everyday, basically all the time |
| [Memory](https://github.com/modelcontextprotocol/servers/blob/main/src/memory/README.md)| It gives your agent ability to memorise different things between conversations, it's really powerful when connected with other MCP servers or remembering some things about the project | ♥️ I combine it with Context7 to remember libraries IDs |
| [Context7](https://github.com/upstash/context7) | It allows your agent to have the best context when it comes to the newest versions of tools, libraries, frameworks, etc. | 🆓 Free for all developers! I use this whenever I need up-to-date library context. Highly recommended for every project. |
| [Shadcn UI](https://github.com/Jpisnice/shadcn-ui-mcp-server) | It gives the agent access to the Shadcn UI components and helps understand how they should be used | To me it's absolute banger. 🚀 I use it whenever I work with Shadcn UI in my projects. |
| [Chrome DevTools](https://github.com/ChromeDevTools/chrome-devtools-mcp) | Brings Chrome DevTools debugging capabilities to your agent - inspect live pages, debug network/console errors, simulate user behavior, analyze performance, and verify code changes in real-time | 🔍 Perfect for debugging web apps and performance audits |

## 🚀 Quick Start

1. **Browse** the categories above
2. **Click** on any link / prompt that interests you
3. **Copy** the content
4. **Paste** into GitHub Copilot Chat or just copy entire instruction / prompt / chat mode / MCP configuration and 💾 save in your repo
5. **Customize** for your specific needs

## 📹 Featured in Videos

- [I Can't Code Without These 3 MCP Servers (Game Changers!)](https://youtu.be/pTLrQ2c5A7M)

## 🛠️ My Tech Stack

All things in this repo are optimized for:

- **Backend**: Go, Node.js, Express, NestJS, TypeScript, PHP
- **Frontend**: React, Astro, Next.js, TailwindCSS, TanStack
- **Database**: PostgreSQL, MySQL, TypeORM, GORM, Atlas
- **DevOps**: Docker, Colima (Docker Desktop alternative),  GitHub Actions
- **Testing**: Jest, Vitest, native testing libs (Node.js, Go), Playwright

## 🤝 Contributing

Found a prompt that supercharged your workflow? Feel free to contribute!

1. Fork this repository
2. Add your prompt/instruction/chat mode
3. Follow the existing format
4. Submit a PR with a clear description

If you have a question or want to talk about some idea related to this repo - feel free to open an issue.

## 📬 Stay Connected

- **YouTube**: [IKcode Igor Wnek](https://youtube.com/@IKcodeIgorWnek) - New videos weekly
- **GitHub**: [@IgorWnek](https://github.com/IgorWnek)
- **BlueSky 🦋**: [@ikcode.dev](https://bsky.app/profile/ikcode.dev)

## 📄 License

[MIT License](LICENSE) - Use these prompts freely in your projects!

---

⭐ **If you find this useful, please star the repository!** It helps others discover these resources and motivates me to add more content.
