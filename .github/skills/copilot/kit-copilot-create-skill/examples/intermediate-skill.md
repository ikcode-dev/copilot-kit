# Example: Intermediate Skill with Bundled Resources

A skill with multi-step interview, workspace scanning, XML tags, validation, and bundled template resources. This demonstrates the full structural patterns for a production skill.

---

```markdown
---
name: scaffold-react-component
description: "Scaffolds a React component with tests, stories, and styles following project conventions. Use when user asks to create, scaffold, generate, or set up a new component, UI element, or React module."
argument-hint: Component name and type (e.g., 'UserProfile page component')
user-invokable: true
disable-model-invocation: false
---

# Scaffold React Component

## What This Skill Does

Creates a complete React component directory with implementation file, tests, Storybook stories, and styles — all matching the project's existing conventions. Inspects the codebase to detect patterns (CSS Modules vs styled-components, Vitest vs Jest, barrel exports) and generates files that match.

## Key Concepts

<belongs-in-skill>
- Multi-file scaffolding with templates
- Convention detection from existing code
- Reusable across projects (adapts to each codebase)
</belongs-in-skill>

<does-not-belong-in-skill>
- React coding standards → custom instructions
- UI reviewer persona → custom agent
- One-off component generation → prompt file
</does-not-belong-in-skill>

<escalation-criteria>
If the user only needs a single component file with no tests or stories, recommend a prompt file instead — this skill is for full directory scaffolding.
</escalation-criteria>

## Step-by-step Procedure

### Step 1: Interview

<questions>
1. **Component name**: What should the component be called? (e.g., UserProfile, DataTable, NavigationBar)
2. **Component type**: Is this a page, layout, feature, or UI component?
3. **Functionality**: What should the component do? What props does it accept?
4. **Extras**: Do you need Storybook stories? Unit tests? Both?

Do NOT proceed until you have a clear answer to at least questions 1 and 3.
</questions>

### Step 2: Workspace Scan

<context-gathering>
1. **Detect component structure** — Search for existing components to identify the directory pattern (`src/components/`, `app/components/`, flat vs nested).
2. **Detect styling approach** — Check for CSS Modules (`.module.css`), styled-components, Tailwind, or other styling patterns.
3. **Detect testing setup** — Check for Vitest (`vitest.config`), Jest (`jest.config`), or Testing Library usage.
4. **Detect exports pattern** — Check if directories use barrel exports (`index.ts`) or direct imports.
5. **Check naming conventions** — Are components PascalCase directories? Do test files use `.test.` or `.spec.`?

Report findings: "I detected [styling approach], [test framework], [directory pattern]. I'll generate files matching these conventions."
</context-gathering>

### Step 3: Draft

Based on interview answers and workspace scan, determine the files to create:

```
ComponentName/
  ComponentName.tsx        # Component implementation
  ComponentName.test.tsx   # Unit tests (if requested)
  ComponentName.stories.tsx # Storybook stories (if requested)
  ComponentName.module.css  # Styles (matches detected approach)
  index.ts                  # Barrel export (if project uses them)
```

Use the bundled [component-template.tsx](./templates/component-template.tsx) as the structural starting point.

Present the file list and component interface to the user for confirmation.

### Step 4: Generate

<rules>
1. Create the component directory in the correct location (detected in Step 2).
2. Generate each file using the project's detected conventions.
3. Add the component to any parent barrel exports if the project uses them.
4. Match the exact import style, naming convention, and file structure of existing components.
</rules>

### Step 5: Validate

<validation>
- [ ] Component directory is in the correct location
- [ ] Component name is PascalCase
- [ ] Implementation file exports the component correctly
- [ ] Props interface is defined with TypeScript types
- [ ] Styles match the project's styling approach
- [ ] Test file imports from the correct path
- [ ] Test file has at least one render test
- [ ] Stories file has a default story matching the component
- [ ] Barrel export is updated (if project uses them)
- [ ] No unused imports or dependencies
</validation>

<user-message>
**Your new component is ready:**
1. Find it at `{component-path}/`
2. Import with: `import { ComponentName } from '{import-path}'`
3. Run tests: `npm test -- ComponentName`
4. View stories: `npm run storybook` → navigate to the component
</user-message>

## Reference

### Styling Patterns

<decision-guide name="styling-approach">
| Detected Pattern | Files to Generate | Template to Use |
|-----------------|-------------------|-----------------|
| CSS Modules (`.module.css`) | `Component.module.css` | Standard CSS module with `.root` class |
| styled-components | Styles inline in `.tsx` | `styled.div` wrapper pattern |
| Tailwind CSS | Classes inline in `.tsx` | Utility class pattern |
| Plain CSS | `Component.css` | Standard CSS with BEM naming |
</decision-guide>

### Testing Patterns

<decision-guide name="testing-approach">
| Detected Framework | File Extension | Import Pattern |
|-------------------|---------------|----------------|
| Vitest + Testing Library | `.test.tsx` | `import { render, screen } from '@testing-library/react'` |
| Jest + Testing Library | `.test.tsx` | Same as Vitest (compatible API) |
| Jest + Enzyme | `.spec.tsx` | `import { shallow } from 'enzyme'` |
</decision-guide>
```

---

**Why this works as an intermediate skill:**
- Uses all standard XML tags: `<questions>`, `<context-gathering>`, `<rules>`, `<validation>`, `<user-message>`, `<belongs-in-skill>`, `<does-not-belong-in-skill>`, `<escalation-criteria>`, `<decision-guide>`
- Has workspace scanning that adapts to the project's conventions
- References bundled template files via relative paths
- Decision guides help the agent choose the right approach
- Validation is comprehensive (10 items)
- Post-creation message tells the user how to use the result
