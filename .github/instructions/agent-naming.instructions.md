---
name: 'Agent Naming Conventions'
description: 'Naming and structure conventions for custom agents in copilot-kit'
applyTo: '.github/agents/**/*.agent.md'
---
# Agent Naming Conventions for copilot-kit

These conventions ensure agents from this repository are instantly identifiable when mixed with project-specific agents in the VS Code agent picker.

## Naming Pattern

All agents in this repository **must** follow:

```
kit-{category}-{role}(-{qualifier})*
```

| Segment | Purpose | Examples |
|---------|---------|----------|
| `kit-` | Origin prefix — identifies the agent as coming from `copilot-kit` | Always `kit-` |
| `{category}` | Domain grouping | `copilot`, `sdd`, `backend` |
| `{role}` | The persona or role the agent fulfills | `architect`, `expert`, `engineer` |
| `(-{qualifier})*` | Optional qualifier for specificity | `-senior`, `-planner` |

### Why `kit-`?

- `ck-` — too cryptic, not recognizable at a glance
- `copilot-kit-` — too long, wastes space in the agent picker
- `kit-` — 4 chars, groups all copilot-kit agents together when browsing

### Agent naming is role-oriented, not action-oriented

Skills use `kit-{category}-{verb}-{object}` because they perform actions.
Agents use `kit-{category}-{role}` because they represent personas with expertise, tools, and behavioral guidelines.

## Categories

| Category | Scope |
|----------|-------|
| `copilot` | Agents related to Copilot customization and configuration workflows |
| `sdd` | Agents related to Spec-Driven Development workflows |
| `backend` | Agents focused on backend / server-side engineering |

New categories follow the same pattern: short, lowercase, hyphenated. A category can be domain-specific (e.g., `frontend`, `devops`) or workflow-specific (e.g., `sdd`, `copilot`).

## Filename Convention

The filename **must** follow:

```
{agent-name}.agent.md
```

Where `{agent-name}` matches the naming pattern. All agents live directly in `.github/agents/` (flat structure, no subdirectories):

```
.github/agents/
  kit-copilot-engineering-team.agent.md
  kit-sdd-expert.agent.md
  kit-backend-engineer.agent.md
```

## Agent Frontmatter Requirements

```yaml
---
description: >-                          # be specific — this drives agent selection
  A brief sentence describing the agent's expertise and purpose.
  When/why to use this agent.
argument-hint: Describe what input to provide
tools: [...]                             # tools the agent has access to
---
```

- `description` should explain both **what role** the agent fills and **when** to select it, so users (and other agents via handoffs) can quickly identify the right agent.
- The frontmatter `name:` field is optional for agents. When present, it should be human-readable (e.g., "IKC SDD Expert") — the filename is what follows the `kit-` naming convention.

## Naming Examples

| Agent filename | Category | Role |
|----------------|----------|------|
| `kit-copilot-engineering-team` | copilot | Domain orchestrator for Copilot customization engineering |
| `kit-sdd-expert` | sdd | Orchestrates spec-driven development workflows |
| `kit-backend-engineer` | backend | Pragmatic backend implementation specialist |
| `kit-frontend-engineer` | frontend | *(example)* Frontend/UI implementation specialist |
| `kit-devops-engineer` | devops | *(example)* CI/CD, infrastructure, and deployment specialist |

## Anti-Patterns

| Avoid | Why | Use instead |
|-------|-----|-------------|
| `copilot-engineering-team` | No origin prefix; collides with project agents | `kit-copilot-engineering-team` |
| `kit-engineering-team` | Missing category; ambiguous grouping | `kit-copilot-engineering-team` |
| `kit-copilot-create-engineering-team` | Verb-based; agents are roles, not actions | `kit-copilot-engineering-team` |
| `Kit-Copilot-Engineering-Team` | Must be lowercase with hyphens | `kit-copilot-engineering-team` |
| `spec-planner` | No origin prefix; no category | `kit-sdd-expert` |

## Cross-References

When agents reference each other (e.g., in `handoffs`), use the filename (without `.agent.md`):

```yaml
handoffs:
  - label: "Implement Specification"
    agent: kit-backend-engineer     # filename-based reference
```

When renaming an agent, search the repository for all references to the old name and update them.

## Validation

After creating or renaming any agent in this repository, verify naming compliance by checking all `.agent.md` files under `.github/agents/`:

### Rules to verify

1. **Filename matches pattern**: `^kit-[a-z]+(-[a-z]+)+\.agent\.md$`
2. **Length limit**: filename (without `.agent.md`) must be ≤ 64 characters

### Quick validation command

Run from the repository root:

```bash
find .github/agents -name "*.agent.md" -type f | while read -r f; do
  basename=$(basename "$f" .agent.md)
  if [[ ! "$basename" =~ ^kit-[a-z]+(-[a-z]+)+$ ]]; then
    echo "FAIL  $f — filename '$basename' does not match naming pattern"
  elif (( ${#basename} > 64 )); then
    echo "FAIL  $f — name exceeds 64 chars (${#basename})"
  else
    echo "OK    $f"
  fi
done
```

If any check fails, rename the file and update all cross-references before committing.
