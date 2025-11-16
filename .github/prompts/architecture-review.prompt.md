---
name: architecture-review
description: "Quick sanity check of solution architecture with actionable feedback"
agent: agent
argument-hint: "Optional: specify focus area (scalability, security, maintainability, performance)"
tools: ['search', 'sequentialthinking/*', 'usages', 'changes', 'fetch', 'todos']
---

## Goal

Perform a focused architectural review of a proposed or existing solution to identify potential issues, validate design decisions, and suggest improvements. This prompt provides a critical but constructive analysis to help developers catch architectural problems early and explore better approaches when appropriate.

Uses Sequential Thinking MCP for structured, multi-dimensional analysis.

## Inputs & Context Gathering

**Primary input modes** (use what's available):
- `${selection}` - Review selected code/architecture diagram
- `${file}` - Review current file's architecture
- User description - Review a proposed design or idea
- Workspace - Review overall project architecture

**Context to gather proactively**:
1. Check for architecture documentation (README.md, docs/architecture.md, ADRs in docs/)
2. Identify tech stack and frameworks (package.json, requirements.txt, go.mod, etc.)
3. Look for existing patterns in the codebase to ensure consistency
4. Review related components/modules that interact with the target
5. Check for `.github/instructions/` or custom guidelines

**Clarifying questions** (ask only if critical context is missing):
- What is the primary concern? (scalability, security, maintainability, performance, cost)
- What are the key constraints? (team size, timeline, budget, existing infrastructure)
- Is this greenfield or brownfield? (helps calibrate recommendations)

If user provides a focus area in the argument, prioritize that dimension in the review.

## Architecture Review Protocol

Use `sequentialthinking` to structure the analysis:

### Step 1: Understanding Phase
- Identify what is being built/modified and why
- Map out key components, dependencies, and data flows
- Note stated or implied requirements (functional & non-functional)
- Document assumptions about scale, usage patterns, and constraints

### Step 2: Critical Analysis
Evaluate across these dimensions (prioritize based on user's focus):

**Design Soundness**
- Does the architecture solve the stated problem?
- Are there obvious gaps or logical flaws?
- Is complexity proportional to the problem size?

**Scalability**
- Will it handle growth in users/data/traffic?
- Are there bottlenecks or single points of failure?
- Is horizontal/vertical scaling feasible?

**Maintainability**
- Is the design easy to understand and modify?
- Are concerns properly separated?
- Will it resist becoming legacy spaghetti?

**Security & Reliability**
- Are there obvious security vulnerabilities?
- How does it handle failures/errors?
- Is data integrity protected?

**Performance**
- Are there unnecessary latency sources?
- Is resource usage (CPU, memory, network, storage) reasonable?
- Are there N+1 queries or other efficiency anti-patterns?

**Technology Fit**
- Are chosen technologies appropriate for the problem?
- Is the stack consistent with existing project patterns?
- Are there better-suited alternatives?

### Step 3: Pattern Recognition
- Identify architectural patterns in use (MVC, microservices, event-driven, etc.)
- Check for common anti-patterns (god objects, circular dependencies, tight coupling)
- Compare against industry best practices for this problem domain

### Step 4: Recommendations
For each identified issue:
- Assess severity (critical, important, minor, nitpick)
- Suggest specific, actionable fix
- Explain trade-offs if alternative approaches exist

## Expected Output Format

Deliver a concise review structured as:

```markdown
## Architecture Review Summary

**Scope:** [Brief description of what was reviewed]
**Overall Assessment:** [One-line verdict: Sound / Needs Refinement / Requires Rework]

---

### ✅ Strengths
- [Specific architectural decisions that work well]
- [Patterns/choices that are appropriate]
- [Things that demonstrate good design thinking]

### ⚠️ Concerns & Recommendations

#### [Concern Category] - [Severity: Critical/Important/Minor]
**Issue:** [Specific problem identified with evidence/examples]
**Impact:** [What could go wrong if not addressed]
**Recommendation:** [Actionable fix with code/diagram if applicable]
**Trade-offs:** [Optional: costs/benefits of the suggested approach]

[Repeat for each concern]

### 💡 Alternative Approaches (if applicable)
- **[Alternative 1]:** [Brief description + when it's better]
- **[Alternative 2]:** [Brief description + trade-offs]

### 🎯 Quick Wins
[1-3 easy improvements that provide immediate value]

---

**Next Steps:** [Prioritized action items]
```

**Example snippet:**
```markdown
### ⚠️ Concerns & Recommendations

#### Scalability - Important
**Issue:** User session data stored in-memory on single server instance
**Impact:** Won't survive restarts; limits horizontal scaling; memory leak risk
**Recommendation:** Move to Redis or similar external session store
**Trade-offs:** Adds infrastructure dependency but enables stateless app servers

#### Security - Critical
**Issue:** API keys visible in client-side code (line 47 of `config.js`)
**Impact:** Keys can be extracted and abused; potential data breach
**Recommendation:** Move sensitive config to backend; use environment variables
```

If no significant issues found:
```markdown
## Architecture Review Summary
**Overall Assessment:** Sound

The proposed architecture is appropriate for the stated requirements. The design demonstrates good separation of concerns and reasonable technology choices. No critical issues identified.

### 💡 Optional Enhancements
- [Minor optimization or future-proofing suggestion]
```

## Guidance

**Tone & Approach:**
- Be critical but constructive—frame issues as opportunities
- Cite specific evidence (file names, line numbers, patterns)
- Differentiate between "this is wrong" vs. "this could be better"
- Don't over-engineer—simple solutions are often best

**Severity Calibration:**
- **Critical:** Breaks functionality, major security hole, can't scale to stated requirements
- **Important:** Works now but will cause pain soon (tech debt, maintenance burden, performance cliff)
- **Minor:** Suboptimal but acceptable; quality-of-life improvement
- **Nitpick:** Stylistic preference; mention only if it aligns with project standards

**When Uncertain:**
- State assumptions explicitly (e.g., "Assuming 10K concurrent users...")
- Present multiple valid options with trade-offs
- Ask follow-up questions rather than guessing critical constraints

**Edge Cases:**
- If reviewing incomplete information, note what's missing and review what's available
- If the architecture is unconventional but justified, acknowledge the rationale
- If technology choices conflict with project stack, flag inconsistency

**Integration with Other Prompts:**
- For deep refactoring needs, suggest using a refactoring-focused prompt
- For security-specific deep dives, recommend dedicated security review
- For performance optimization, point to profiling/benchmarking workflows
