# Sequential Thinking MCP Server Documentation

Based on my analysis of the Sequential Thinking MCP server, here's a concise, universal documentation you can use as context for your custom agents:

---

## Sequential Thinking Tool - Agent Instructions

### Overview

The `sequentialthinking` tool enables structured, dynamic problem-solving through a step-by-step thinking process. It allows you to break down complex problems, revise your reasoning, branch into alternative approaches, and systematically arrive at verified solutions.

### Tool Name

`sequentialthinking`

### When to Use This Tool

Use the Sequential Thinking tool when you encounter:

| Scenario | Why Use Sequential Thinking |
|----------|----------------------------|
| **Complex multi-step problems** | Break down into manageable, traceable steps |
| **Planning and design tasks** | Allow room for revision as understanding deepens |
| **Unclear problem scope** | Dynamically adjust approach as you learn more |
| **Analysis requiring course correction** | Revise previous conclusions when new insights emerge |
| **Context-dependent reasoning** | Maintain state across multiple thinking steps |
| **Information filtering needed** | Systematically separate relevant from irrelevant data |
| **Hypothesis-driven work** | Generate, verify, and iterate on solution hypotheses |

### Input Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `thought` | string | ✅ | Your current thinking step (analysis, revision, question, hypothesis, or verification) |
| `nextThoughtNeeded` | boolean | ✅ | `true` if more thinking is needed; `false` only when a satisfactory answer is reached |
| `thoughtNumber` | integer | ✅ | Current thought number in sequence (starts at 1, can exceed `totalThoughts`) |
| `totalThoughts` | integer | ✅ | Estimated total thoughts needed (can be adjusted up/down dynamically) |
| `isRevision` | boolean | ❌ | Set to `true` if this thought revises previous thinking |
| `revisesThought` | integer | ❌ | Which thought number is being reconsidered (use with `isRevision`) |
| `branchFromThought` | integer | ❌ | The thought number where you're branching to explore an alternative path |
| `branchId` | string | ❌ | Identifier for the current branch (use with `branchFromThought`) |
| `needsMoreThoughts` | boolean | ❌ | Set to `true` if you realize more thoughts are needed beyond the initial estimate |

### Output

The tool returns:

- `thoughtNumber`: Current thought number
- `totalThoughts`: Updated total thoughts estimate
- `nextThoughtNeeded`: Whether more thinking is required
- `branches`: List of branch identifiers created
- `thoughtHistoryLength`: Total number of thoughts recorded

### How to Use This Tool

**Step-by-step process:**

1. **Initialize**: Start with an initial estimate of needed thoughts (e.g., `totalThoughts: 5`)
2. **Iterate**: Submit each thinking step with incrementing `thoughtNumber`
3. **Adapt**: Adjust `totalThoughts` up or down as understanding evolves
4. **Revise**: Mark thoughts that reconsider previous conclusions with `isRevision: true`
5. **Branch**: Explore alternative approaches using `branchFromThought` and `branchId`
6. **Hypothesize**: Generate solution hypotheses in your thought content
7. **Verify**: Validate hypotheses against previous Chain of Thought steps
8. **Conclude**: Set `nextThoughtNeeded: false` only when a satisfactory answer is reached

### Key Behaviors

- **Non-linear thinking**: You don't have to build linearly—backtrack or branch as needed
- **Dynamic scope**: Increase `totalThoughts` if the problem is more complex than initially estimated
- **Explicit revision**: Use `isRevision` to clearly mark when you're reconsidering earlier conclusions
- **Uncertainty is valid**: Express uncertainty and explore alternatives within your thoughts
- **Filter noise**: Actively ignore information irrelevant to the current step

### Example Usage Pattern

```json
// Thought 1: Initial analysis
{
  "thought": "The user wants to implement authentication.  Let me first identify the key components: user model, session management, and password hashing.",
  "thoughtNumber": 1,
  "totalThoughts": 5,
  "nextThoughtNeeded": true
}

// Thought 2: Deepening analysis
{
  "thought": "For session management, we have two options: JWT tokens or server-side sessions. Given the stateless API requirement, JWT is more appropriate.",
  "thoughtNumber": 2,
  "totalThoughts": 5,
  "nextThoughtNeeded": true
}

// Thought 3: Revision (realizing previous thought needs reconsideration)
{
  "thought": "Actually, the requirement mentions refresh tokens, which complicates pure JWT.  We need a hybrid approach with short-lived JWTs and stored refresh tokens.",
  "thoughtNumber": 3,
  "totalThoughts": 6,
  "isRevision": true,
  "revisesThought": 2,
  "nextThoughtNeeded": true
}

// Final thought: Conclusion
{
  "thought": "Final solution: Use bcrypt for password hashing, short-lived JWTs (15min) for authentication, and database-stored refresh tokens (7 days) for token renewal.",
  "thoughtNumber": 6,
  "totalThoughts": 6,
  "nextThoughtNeeded": false
}
```

### Best Practices for Agents

1. **Start conservatively** with `totalThoughts` estimate, then adjust upward if needed
2. **Don't hesitate to revise** — marking revisions improves reasoning transparency
3. **Use branching** to explore "what if" scenarios without losing the main thread
4. **Keep each thought focused** on a single aspect or decision
5. **Generate hypotheses explicitly** before verification steps
6. **Only set `nextThoughtNeeded: false`** when you have a verified, satisfactory answer

---

This documentation should work universally across different agent types (software engineer, architect, analyst, etc.) as the tool itself is domain-agnostic and focuses on the *process* of structured thinking rather than specific domains.
