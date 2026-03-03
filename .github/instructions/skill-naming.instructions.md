---
name: 'Skill Naming Conventions'
description: 'Naming and structure conventions for Agent Skills in copilot-kit'
applyTo: '.github/skills/**/SKILL.md'
---
# Skill Naming Conventions for copilot-kit

These conventions ensure skills from this repository are instantly identifiable when mixed with project-specific skills in the VS Code `/` command palette.

## Naming Pattern

All skills in this repository **must** follow:

```
kit-{category}-{verb}-{object}
```

| Segment | Purpose | Examples |
|---------|---------|----------|
| `kit-` | Origin prefix — identifies the skill as coming from `copilot-kit` | Always `kit-` |
| `{category}` | Domain grouping | `copilot`, `sdd` |
| `{verb}` | Action the skill performs | `create`, `check`, `implement`, `review` |
| `{object}` | Target of the action | `agent`, `skill`, `hook`, `spec`, `conformance` |

### Why `kit-`?

- `ck-` — too cryptic, not recognizable at a glance
- `copilot-kit-` — too long, wastes the 64-char `name` limit
- `kit-` — 4 chars, filters all copilot-kit skills together when typing `/kit-`

## Categories

| Category | Scope |
|----------|-------|
| `copilot` | Skills related to Copilot customization (agents, skills, hooks, instructions, prompts) |
| `sdd` | Skills related to Spec-Driven Development workflows (specs, conformance checks, implementation) |

New categories follow the same pattern: short, lowercase, hyphenated.

## Directory Structure

The `name` field in SKILL.md frontmatter **must match** the parent directory name. Intermediate grouping directories (`copilot/`, `sdd/`) are for filesystem organization only:

```
.github/skills/
  copilot/
    kit-copilot-create-agent/SKILL.md
    kit-copilot-create-skill/SKILL.md
  sdd/
    kit-sdd-create-spec/SKILL.md
    kit-sdd-check-conformance/SKILL.md
```

## SKILL.md Frontmatter Requirements

```yaml
---
name: kit-{category}-{verb}-{object}    # must match directory name
description: "A skill that [verb]s [object] for [context]. Use when [trigger scenario]."
argument-hint: Describe what input to provide
user-invokable: true
disable-model-invocation: false
---
```

- `description` should explain both **what** the skill does and **when** to use it so Copilot's progressive disclosure loads it automatically at the right time.
- Keep `name` under 64 characters (the spec maximum).

## Naming Examples

| Skill name | Category | Action |
|------------|----------|--------|
| `kit-copilot-create-agent` | copilot | Scaffold a custom `.agent.md` file |
| `kit-copilot-create-skill` | copilot | Scaffold a new skill directory + SKILL.md |
| `kit-copilot-create-hook` | copilot | Create a Copilot hook configuration |
| `kit-copilot-create-commit-instructions` | copilot | Generate commit message instruction files |
| `kit-sdd-create-spec` | sdd | Scaffold a new specification document |
| `kit-sdd-check-conformance` | sdd | Verify implementation matches its spec |
| `kit-sdd-implement-spec` | sdd | Implement code from a specification |

## Anti-Patterns

| Avoid | Why | Use instead |
|-------|-----|-------------|
| `create-copilot-agent` | No origin prefix; collides with project skills | `kit-copilot-create-agent` |
| `kit-create-agent` | Missing category; ambiguous grouping | `kit-copilot-create-agent` |
| `kit-copilot-copilot-agent-creator` | Redundant, noun-based | `kit-copilot-create-agent` |
| `Kit-Copilot-Create-Agent` | Must be lowercase with hyphens | `kit-copilot-create-agent` |

## Validation

After creating or renaming any skill in this repository, verify naming compliance by checking all `SKILL.md` files under `.github/skills/`:

### Rules to verify

1. **Directory name matches pattern**: `^kit-[a-z]+-[a-z]+(-[a-z]+)*$`
2. **Frontmatter `name:` matches directory**: the `name` field must be identical to the parent directory name
3. **Length limit**: name must be ≤ 64 characters

### Quick validation command

Run from the repository root:

```bash
find .github/skills -name "SKILL.md" -type f | while read -r f; do
  dir=$(basename "$(dirname "$f")")
  name=$(head -20 "$f" | sed -n '/^---$/,/^---$/p' | grep -E '^name:' | head -1 | sed "s/^name:[[:space:]]*//" | tr -d "'" | tr -d '"')
  if [[ ! "$dir" =~ ^kit-[a-z]+-[a-z]+(-[a-z]+)*$ ]]; then
    echo "FAIL  $f — directory '$dir' does not match naming pattern"
  elif [[ "$name" != "$dir" ]]; then
    echo "FAIL  $f — frontmatter name '$name' ≠ directory '$dir'"
  elif (( ${#dir} > 64 )); then
    echo "FAIL  $f — name exceeds 64 chars (${#dir})"
  else
    echo "OK    $f"
  fi
done
```

If any check fails, rename the directory and update the `name:` field before committing.
