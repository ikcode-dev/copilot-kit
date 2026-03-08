# Example: Minimal Skill

A simple skill with no bundled resources — just frontmatter and a short body. This is appropriate for skills that automate a straightforward, self-contained task.

---

```markdown
---
name: generate-changelog
description: "Generates a changelog entry from recent git commits. Use when user asks to create, generate, draft, or update a changelog, release notes, or commit summary."
argument-hint: Describe the version or date range for the changelog
user-invocable: true
disable-model-invocation: false
---

# Generate Changelog

## What This Skill Does

Generates a structured changelog entry by analyzing recent git commits. Groups changes by type (features, fixes, breaking changes) and formats them following [Keep a Changelog](https://keepachangelog.com/) conventions.

## Step-by-step Procedure

### Step 1: Gather Context

<context-gathering>
1. Check for an existing `CHANGELOG.md` — match its formatting conventions.
2. Read `package.json`, `Cargo.toml`, or equivalent for the current version number.
3. Identify the git log range (last tag to HEAD, or user-specified range).
</context-gathering>

### Step 2: Analyze Commits

<rules>
- Parse commit messages using Conventional Commits format if present.
- Group by type: Added, Changed, Deprecated, Removed, Fixed, Security.
- Skip merge commits and CI-only changes.
- Preserve original commit message wording — do not rephrase.
</rules>

### Step 3: Generate Entry

Format the changelog entry:

```
## [version] - YYYY-MM-DD

### Added
- Feature description (#PR)

### Fixed
- Bug fix description (#PR)

### Changed
- Change description (#PR)
```

### Step 4: Validate

<validation>
- [ ] Version number is correct
- [ ] Date is today's date (unless user specified otherwise)
- [ ] All commit types are categorized
- [ ] PR/issue numbers are linked where available
- [ ] Formatting matches existing CHANGELOG.md conventions
</validation>
```

---

**Why this works as a minimal skill:**
- Short, focused body (~60 lines)
- No bundled resources needed — the procedure is self-contained
- Uses `<context-gathering>`, `<rules>`, and `<validation>` tags appropriately
- Description has clear "what + when" pattern with multiple trigger phrases
