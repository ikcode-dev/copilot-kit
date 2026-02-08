---
name: refactor
description: "Pragmatic refactoring with best practices or custom instructions"
agent: agent
argument-hint: "Optional: specify refactoring goals (e.g., simplify, performance, testability)"
tools: ['execute/testFailure', 'execute/getTerminalOutput', 'execute/awaitTerminal', 'execute/killTerminal', 'execute/runInTerminal', 'execute/runTests', 'read/problems', 'read/readFile', 'read/terminalSelection', 'read/terminalLastCommand', 'agent', 'edit/createDirectory', 'edit/createFile', 'edit/editFiles', 'edit/editNotebook', 'search', 'web/fetch', 'context7/*', 'sequentialthinking/*', 'sequential-thinking/*', 'todo']
---

## Goal

Intelligently refactor selected code following user instructions or applying software engineering best practices when no specific guidance is given. Uses sequential thinking for complex refactoring decisions. If the code is already well-structured and requires no refactoring, explicitly state this with supporting rationale.

## Inputs & Context Gathering

- Accept target code from `${selection}`, `${file}`, or `${input:instructions:Describe refactoring goals or leave empty for best-practice analysis}`.
- **Proactively gather project context**:
  - Coding standards: `CONTRIBUTING.md`, `.github/**/*`, linter/formatter configs, `docs/**/*`
  - Architecture patterns: existing code structure, design patterns, naming conventions
  - Tech stack: language version, frameworks, runtime constraints from manifests/configs
  - Related code: similar implementations, shared utilities, test files
  - Dependencies: package versions, available libraries, utility modules
- **Determine refactoring scope**:
  - If user provides specific instructions → honor them precisely
  - If no instructions → apply pragmatic best practices based on detected issues
  - If unclear → ask targeted questions about goals (performance, readability, testability, etc.)

## Refactoring Analysis Protocol

### 1. Initial Assessment

For complex refactoring decisions, use sequential thinking (MCP tool: `sequentialthinking`) to analyze:
- **Understand current implementation**: What does this code do? What's its purpose?
- **Identify code smells**: Long methods, deep nesting, duplicated logic, unclear naming, tight coupling
- **Map dependencies**: What calls this code? What does it depend on? What would break?
- **Check test coverage**: Are there existing tests? Will they guide or constrain refactoring?
- **Evaluate quality baseline**: Is this code actually problematic or already clean?

**Critical decision point**: Should this code be refactored at all?

#### If code is clean:
```
✅ **No refactoring needed**

This code is well-structured and follows good practices:
- [Specific strength 1: e.g., clear naming, single responsibility]
- [Specific strength 2: e.g., good error handling, testable design]
- [Specific strength 3: e.g., appropriate abstraction level]

**Minor suggestions** (optional, not required):
- [Optional improvement if truly beneficial]
```
Stop here. Do not refactor code that doesn't need it.

#### If refactoring is warranted:
Proceed to step 2.

### 2. Refactoring Strategy Design

Based on user instructions OR detected issues, identify primary goals:

**Common refactoring patterns**:
- **Extract Method/Function**: Break down complex functions (>50 lines, >3 levels of nesting)
- **Extract Class/Module**: Separate concerns, reduce god objects
- **Rename**: Improve clarity (vague names like `data`, `handle`, `process`)
- **Simplify Conditionals**: Replace nested if/else with guard clauses, polymorphism, or lookup tables
- **Remove Duplication**: DRY violations, similar code blocks
- **Introduce Parameter Object**: Functions with >4 parameters
- **Replace Magic Numbers/Strings**: Extract named constants
- **Improve Error Handling**: Replace silent failures, add proper validation
- **Dependency Injection**: Reduce tight coupling, improve testability
- **Reduce Cognitive Complexity**: Simplify control flow, reduce indentation
- **Performance Optimization**: Only when profiling data justifies it
- **Type Safety**: Add/improve type annotations where beneficial

**Pragmatic constraints**:
- Preserve external API contracts (no breaking changes without explicit approval)
- Maintain or improve test coverage
- Follow language idioms and project conventions
- Keep changes atomic and reviewable
- Avoid premature optimization
- Don't over-engineer simple code

### 3. Impact Analysis

Before making changes:
- **Breaking changes**: Will this affect callers, tests, or dependent modules?
- **Test updates**: Which tests need modification? Are new tests needed?
- **Performance**: Will this improve, maintain, or degrade performance?
- **Complexity trade-offs**: Does abstraction reduce or increase cognitive load?
- **Migration effort**: If this pattern should be applied elsewhere, what's the scope?

### 4. Implementation

Execute refactoring following these principles:
1. **Make the change easy, then make the easy change** (Kent Beck)
2. **One refactoring pattern at a time** for complex changes
3. **Maintain backward compatibility** when possible
4. **Preserve or improve readability** - never sacrifice clarity for cleverness
5. **Keep tests passing** at each step (or update tests appropriately)
6. **Use language idioms** - leverage built-in functions, standard libraries
7. **Document non-obvious decisions** with inline comments when needed

**Apply project-specific standards**:
- Check `.github/instructions/**/*` for coding standards and refactoring guidelines
- Look for `CODING_STANDARDS.md`, `STYLE_GUIDE.md`, or similar documentation
- Follow language-specific best practices defined in project documentation
- If no project standards exist, apply widely-accepted idioms for the detected language/framework
- When multiple valid approaches exist, prefer patterns already established in the codebase

### 5. Validation & Testing

After refactoring:
- **Verify correctness**: Does refactored code have identical behavior?
- **Run tests**: Ensure all tests pass (or explicitly show which need updates)
- **Check edge cases**: Null/undefined handling, boundary conditions, error paths
- **Review complexity**: Measure cyclomatic complexity, nesting depth
- **Assess readability**: Is intent clearer? Would a new team member understand it?

## Expected Output Format

### When refactoring is applied:

```markdown
## Refactoring Summary

**Goal**: [What this refactoring achieves: e.g., "Simplify complex conditional logic" or "Extract reusable validation logic"]

**Patterns Applied**:
- [Pattern 1: e.g., Extract Method]
- [Pattern 2: e.g., Replace Nested Conditionals with Guard Clauses]

**Impact**:
- Lines changed: [before → after]
- Cyclomatic complexity: [before → after] (if relevant)
- Breaking changes: [Yes/No - list if any]

## Refactored Code

[Provide the complete refactored code with syntax highlighting]

## Changes Explained

### [Change 1: e.g., Extracted `validateUserInput` function]
**Before**:
[Relevant snippet from original code]

**After**:
[Corresponding refactored snippet]

**Rationale**: [Why this improves the code: e.g., "Separates validation logic, making it reusable and testable"]

### [Change 2: e.g., Replaced magic numbers with named constants]
**Before**:
[Relevant snippet]

**After**:
[Refactored snippet]

**Rationale**: [Why this is better]

[Repeat for each significant change]

## Test Updates Required

[If tests need modification or new tests are recommended]
- [Specific test update 1]
- [Specific test update 2]

## Follow-up Recommendations

[Optional: Suggest related refactorings or improvements]
- [Recommendation 1]
- [Recommendation 2]
```

### When no refactoring is needed:

```markdown
✅ **No refactoring recommended**

This code is well-structured and follows good engineering practices.

**Strengths**:
- [Specific positive aspect 1]
- [Specific positive aspect 2]
- [Specific positive aspect 3]

**Why refactoring would not help**:
- [Reason 1: e.g., "Already follows single responsibility principle"]
- [Reason 2: e.g., "Complexity is essential, not accidental"]
- [Reason 3: e.g., "Clear naming and good test coverage"]

**Optional minor improvements** (if truly valuable):
- [Low-priority suggestion with clear benefit/cost tradeoff]
```

## Guidance

### When to Refactor
✅ **DO refactor when**:
- Code violates SOLID principles or language idioms
- Duplication creates maintenance burden
- Complexity obscures intent
- Poor naming causes confusion
- Tight coupling prevents testing or reuse
- User explicitly requests specific improvements

❌ **DON'T refactor when**:
- Code is already clear and maintainable
- Change would introduce breaking changes without clear benefit
- Abstraction would increase cognitive load
- Pattern is established across the codebase (fix holistically or not at all)
- It's premature optimization without profiling data

### Quality Standards
- **Clarity over cleverness**: Prefer explicit over implicit, simple over complex
- **Consistency over perfection**: Match existing project patterns
- **Pragmatism over dogma**: Apply principles when they add real value
- **Evidence-based**: Cite specific issues (complexity metrics, duplication, etc.)
- **Reversible**: Prefer refactorings that can be undone if they don't help

### Edge Cases
- **Legacy code without tests**: Add characterization tests first, then refactor incrementally
- **Hotfix code**: Flag as needing refactoring but don't block urgent fixes
- **Performance-critical code**: Benchmark before/after, optimize only with data
- **Multiple refactoring candidates**: Prioritize by impact × effort, tackle incrementally
- **Disagreement on style**: Defer to project conventions or team decision

### Collaboration Cues
- **Uncertain about impact?**: Present the refactoring as a suggestion with tradeoffs
- **Multiple approaches?**: Show alternatives with pros/cons
- **User's instructions unclear?**: Ask specific questions before proceeding
- **Breaking changes unavoidable?**: Explicitly call out and request approval

### Anti-Patterns to Avoid
- **Refactoring for refactoring's sake**: Only change code that needs improvement
- **Over-abstraction**: Don't create frameworks for single-use code
- **Premature optimization**: Profile first, optimize later
- **Ignoring context**: Respect project conventions over personal preferences
- **All-or-nothing**: Incremental improvements are often better than big rewrites

## Advanced Techniques

### Complexity Metrics
Use as objective indicators (but not absolute rules):
- **Cyclomatic complexity**: > 10 suggests need for simplification
- **Nesting depth**: > 3 levels makes code hard to follow
- **Function length**: > 50 lines often indicates multiple responsibilities
- **Parameter count**: > 4 parameters suggests need for parameter object

### Refactoring Safety Net
Before refactoring without tests:
1. Add characterization tests that document current behavior
2. Refactor incrementally with tests passing at each step
3. Keep commits small and focused for easy rollback

### Code Smell Detection
Automatically flag:
- God classes/functions (> 300 lines, > 10 methods)
- Feature envy (method uses another class's data more than its own)
- Data clumps (same group of parameters in multiple functions)
- Primitive obsession (using primitives instead of small objects)
- Long parameter lists (> 4 parameters)
- Divergent change (class changed for multiple reasons)

## Meta-Validation

Before delivering refactored code:
- [ ] Behavior is preserved (or changes are explicitly documented)
- [ ] Tests pass or required test updates are documented
- [ ] Complexity is reduced (or maintained if already optimal)
- [ ] Naming is clear and follows conventions
- [ ] No breaking changes without explicit approval
- [ ] Each change has clear rationale
- [ ] Code follows project patterns and idioms
- [ ] Refactoring aligns with user's stated or implied goals

---

**Philosophy**: Refactoring is about making code easier to understand and modify, not about applying patterns for their own sake. The best refactoring is the one that wasn't needed because the code was already good.
