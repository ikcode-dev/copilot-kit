---
name: 'Hook Naming Conventions'
description: 'Naming conventions for Copilot hook configuration files in copilot-kit'
applyTo: '.github/hooks/**/*.json'
---
# Hook Naming Conventions for copilot-kit

Apply these rules when creating a new Copilot hook configuration file or when a hook config rename is explicitly in scope.

## Naming Pattern

New or renamed hook config files in this repository must use this filename pattern:

`kit-{hook-purpose}(-{qualifier})*.json`

<rules>
- Store hook config files under `.github/hooks/`.
- Start every hook config filename with the `kit-` origin prefix.
- Keep the rest of the filename stem lowercase kebab-case.
- Use the `.json` extension for every hook config file.
- Name the file after the hook's purpose or scenario, not an implementation detail.
</rules>

## Rollout Boundary

<rules>
- These instructions are forward-looking governance for future hook config files in this repository.
- The repository currently has no live hook config files under `.github/hooks/`, so this file does not imply a migration.
- Keep this guidance focused on filename and path conventions only.
- Do not infer or document hook schema details or runtime behavior here unless the repository adds separate hook documentation.
</rules>

## Anti-Patterns

<anti-patterns>
| Avoid | Why | Use instead |
|-------|-----|-------------|
| `.github/hooks/pre-commit.json` | Missing the required repository origin prefix | `.github/hooks/kit-pre-commit.json` |
| `.github/hooks/Kit-Pre-Commit.json` | Hook config filenames must be lowercase kebab-case | `.github/hooks/kit-pre-commit.json` |
| `.github/hooks/kitPreCommit.json` | Camel case breaks the repository naming convention | `.github/hooks/kit-pre-commit.json` |
| `.github/hooks/kit-pre-commit.yaml` | Hook config files in this repository use the `.json` extension | `.github/hooks/kit-pre-commit.json` |
| `hooks/kit-pre-commit.json` | Hook config files belong under `.github/hooks/` | `.github/hooks/kit-pre-commit.json` |
</anti-patterns>

## Validation

<validation>
- [ ] File path matches `.github/hooks/<name>.json`
- [ ] Filename begins with `kit-`
- [ ] Filename stem uses lowercase kebab-case
- [ ] File lives under `.github/hooks/`
- [ ] Guidance stays limited to naming and placement, not undocumented hook behavior
</validation>
