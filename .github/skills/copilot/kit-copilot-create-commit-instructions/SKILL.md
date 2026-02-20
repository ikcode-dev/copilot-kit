---
name: kit-copilot-create-commit-instructions
description: "Generates a commit message instruction file for GitHub Copilot's VS Code commit message generation feature. Use when user asks to create, set up, or customize commit message instructions, commit conventions, commit message format, or Copilot commit generation settings."
argument-hint: Describe your commit message preferences (e.g., "conventional commits with scope", "include Jira ticket numbers", "emoji-enhanced").
user-invokable: true
disable-model-invocation: false
---

# Create Commit Message Instructions

## What This Skill Does

Generates a `.github/commit-message-instructions.md` file that customizes how GitHub Copilot generates commit messages in the VS Code Source Control panel. The generated file uses XML-like tags exclusively (no markdown headers, no bullet lists, no tables) because Copilot's commit message generation uses a lower-tier LLM that parses XML boundaries more reliably.

## Step-by-step Procedure

### Step 1: Gather Preferences

Determine the user's commit message preferences. If the user provides no specific preferences, default to Conventional Commits format.

<context-gathering>
Check existing project conventions before asking questions:
- Look for `CONTRIBUTING.md`, `README.md` for commit guidelines
- Check `.commitlintrc`, `commitlint.config.js` for existing linting rules
- Scan recent git history for established patterns (if accessible)
</context-gathering>

<questions>
1. Format preference — Conventional Commits, Simple Imperative, Ticket-First, or Emoji-Enhanced?
2. Should commits include a scope? (e.g., `feat(auth):` vs `feat:`)
3. Should commits reference issue/ticket numbers? What format? (e.g., `#123`, `JIRA-123`)
4. Should multi-line commits with body text be encouraged?
5. How should breaking changes be indicated?
6. What language should commit messages be written in?

If the user's request is clear enough, skip the questions and proceed directly.
</questions>

### Step 2: Determine Commit Style

Based on user input, select the appropriate commit message style:

<decision-guide name="commit-style">
- **Conventional Commits** (default): Structured, parseable commits. Example: `feat(api): add user authentication endpoint`
- **Simple Imperative**: Smaller projects, less formal. Example: `Add user authentication`
- **Ticket-First**: Issue-tracker-centric workflows. Example: `[PROJ-123] Add user authentication`
- **Emoji-Enhanced**: Visual categorization. Example: `✨ feat: add user authentication`
</decision-guide>

### Step 3: Build Instruction Content

Construct the instruction file content using **XML-like tags exclusively** — no markdown headers, no bullet lists, no tables. XML-like tags are the most reliable structure for lower-tier LLMs.

Follow this skeleton:

<template name="instruction-skeleton">
```xml
<commit-message-guidelines>

<instruction>
[High-level instruction and spec reference]
</instruction>

<format>
[Primary format specification]
</format>

<types>
[Each type as its own <type name="..."> block with a description]
</types>

<rules>
[Numbered rules]
</rules>

<breaking-changes>
[How to indicate breaking changes]
</breaking-changes>

<examples>
[Comprehensive examples for EVERY type prefix — see critical rule below]
</examples>

</commit-message-guidelines>
```
</template>

<rules>
The commit message generation feature uses a lower-tier LLM that performs significantly better with abundant examples. Always include at least one realistic example for EVERY type prefix defined in the instruction. This is non-negotiable — sparse examples lead to inconsistent output. For Conventional Commits, provide examples for: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, and `revert`.
</rules>

### Step 4: Write the File

Write the generated instruction content to `.github/commit-message-instructions.md` in the workspace root. If the file already exists, overwrite it with the new content.

<rules>
Do NOT paste the content as a code block in chat — create the actual file.
</rules>

### Step 5: Guide User on Settings Configuration

After the file is created, display this message to the user:

<user-message>
Add the following to your VS Code workspace settings (`.vscode/settings.json`):

```json
{
  "github.copilot.chat.commitMessageGeneration.instructions": [
    { "file": ".github/commit-message-instructions.md" }
  ]
}
```

Once this setting is in place, staging changes and clicking the sparkle icon in Source Control will generate commit messages following the instructions in the file.
</user-message>

## Default Template (Conventional Commits)

If the user provides no specific preferences, generate this content for `.github/commit-message-instructions.md`:

```xml
<commit-message-guidelines>

<instruction>
Generate commit messages following the Conventional Commits specification (https://www.conventionalcommits.org/).
</instruction>

<format>
type(optional scope): description

[optional body]

[optional footer(s)]
</format>

<types>
  <type name="feat">A new feature</type>
  <type name="fix">A bug fix</type>
  <type name="docs">Documentation only changes</type>
  <type name="style">Changes that do not affect the meaning of the code (formatting, missing semi-colons, etc.)</type>
  <type name="refactor">A code change that neither fixes a bug nor adds a feature</type>
  <type name="perf">A code change that improves performance</type>
  <type name="test">Adding missing tests or correcting existing tests</type>
  <type name="build">Changes that affect the build system or external dependencies</type>
  <type name="ci">Changes to CI configuration files and scripts</type>
  <type name="chore">Other changes that don't modify src or test files</type>
  <type name="revert">Reverts a previous commit</type>
</types>

<rules>
1. Use the imperative mood in the subject line ("add" not "added" or "adds")
2. Do not capitalize the first letter of the subject line
3. Do not end the subject line with a period
4. Limit the subject line to 50 characters when possible, max 72
5. Separate subject from body with a blank line (if body is present)
6. Use the body to explain what and why, not how
7. Wrap the body at 72 characters
</rules>

<breaking-changes>
Indicate breaking changes by:
- Adding "!" after the type/scope: feat!: remove deprecated API
- Adding a BREAKING CHANGE: footer in the body
</breaking-changes>

<examples>
  <example type="feat">
    feat(auth): add OAuth2 login support
    feat(cart): implement guest checkout flow
    feat: add dark mode toggle to settings
  </example>

  <example type="fix">
    fix(api): resolve null pointer exception in user service
    fix(ui): correct button alignment on mobile viewport
    fix: prevent duplicate form submissions
  </example>

  <example type="docs">
    docs(readme): update installation instructions
    docs(api): add endpoint usage examples
    docs: add contributing guidelines
  </example>

  <example type="style">
    style: format code with prettier
    style(components): fix indentation in Button component
    style: remove trailing whitespace
  </example>

  <example type="refactor">
    refactor(api): extract validation logic to middleware
    refactor: simplify conditional rendering in Dashboard
    refactor(db): rename user table columns for clarity
  </example>

  <example type="perf">
    perf(images): implement lazy loading for gallery
    perf(api): add database query caching
    perf: reduce bundle size by code splitting
  </example>

  <example type="test">
    test(auth): add unit tests for login validation
    test: increase coverage for utils module
    test(e2e): add checkout flow integration tests
  </example>

  <example type="build">
    build(deps): upgrade React to v19
    build: configure webpack for production optimization
    build(docker): update base image to node 22
  </example>

  <example type="ci">
    ci: add GitHub Actions workflow for testing
    ci(deploy): configure automatic staging deployments
    ci: add code coverage reporting to pipeline
  </example>

  <example type="chore">
    chore: update .gitignore patterns
    chore(deps): bump minor dependency versions
    chore: remove deprecated config files
  </example>

  <example type="revert">
    revert: revert "feat(auth): add OAuth2 login support"
    revert(api): undo breaking change to user endpoint
  </example>

  <example type="breaking-change">
    feat(api)!: change response format for user endpoints

    BREAKING CHANGE: User endpoint now returns data wrapped in "data" property

    refactor!: drop support for Node.js 16
  </example>
</examples>

</commit-message-guidelines>
```

## Common Customizations

<decision-guide name="customizations">
| Request | Adjustment |
|---------|------------|
| Include Jira ticket | Add rule: Include Jira ticket number at the start: `[PROJ-XXX] type: description` |
| No scope required | Remove scope from format, simplify to `type: description` |
| Max 50 chars | Emphasize character limit in rules |
| Include emoji | Add emoji mapping to types (e.g., `✨ feat`, `🐛 fix`) |
| Multi-language team | Specify commit language: "Write all commit messages in English" |
| Link to PR | Add footer instruction for PR references |
</decision-guide>

<rules>
- **Always file-based** — always write instructions to `.github/commit-message-instructions.md`. Never output them inline in the chat.
- **XML-like tags only** — the generated file must use exclusively XML-like tags for structure. No markdown headers, bullet lists, or tables in the output file.
- **Examples are critical** — always provide at least one example per type prefix. More examples = better results. This is the single most impactful factor for output quality.
- **Keep rules concise** — while examples should be comprehensive, keep textual rules short and clear.
- **Consider tooling** — if the project uses commitlint, semantic-release, or similar tools, ensure compatibility.
</rules>
