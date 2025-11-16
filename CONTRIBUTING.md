# Contributing to Copilot Kit

Thank you for your interest in contributing to Copilot Kit! This repository thrives on community contributions of battle-tested prompts, instructions, and configurations.

## 🎯 What We're Looking For

- **Custom prompts** for common development workflows
- **Custom instructions** for specific frameworks or patterns
- **Chat modes/agents** with specialized behaviors
- **Documentation improvements** and examples
- **Bug fixes** for existing prompts or configurations

## 📋 Contribution Guidelines

### General Requirements

All contributions should:

- ✅ Be battle-tested in real-world development scenarios
- ✅ Follow existing file structure and naming conventions
- ✅ Include clear documentation and examples
- ✅ Work with GitHub Copilot Chat in VS Code
- ✅ Be general-purpose (avoid company/project-specific details)

### Prompt Contributions

When contributing custom prompts:

#### Quality Standards

- **YAML frontmatter**: Include all required fields (`name`, `description`, `agent`, `argument-hint`)
- **Clear goal**: State what the prompt accomplishes in 1-2 sentences
- **Context gathering**: Specify what information the prompt needs to work effectively
- **Concrete protocol**: Provide step-by-step instructions, not vague guidance
- **Output format**: Include templates or examples of expected results
- **Edge cases**: Document how the prompt handles unusual situations

#### File Structure

```markdown
---
name: kebab-case-name
description: "One-line summary (30-60 chars)"
agent: agent
argument-hint: "Optional guidance for users"
tools: []
---

## Goal
[What this accomplishes]

## Inputs & Context Gathering
[What information is needed]

## Protocol
[Step-by-step instructions]

## Expected Output Format
[Template or example]

## Guidance
[Edge cases, best practices]
```

#### Naming Conventions

- Use **kebab-case**: `feature-name.prompt.md`
- Be descriptive: `generate-api-tests.prompt.md` not `tests.prompt.md`
- Place in `.github/prompts/` directory
- Verb-based for actions (`create-`, `generate-`, `refactor-`)
- Noun-based for reviews/analysis (`code-review`, `security-audit`)

#### Testing Your Prompt

Before submitting, verify:

1. ✅ YAML frontmatter is valid (no syntax errors)
2. ✅ Prompt invokes successfully: `/your-prompt-name`
3. ✅ Works on at least 3 different real-world scenarios
4. ✅ All internal file references are correct
5. ✅ Examples are copy-paste runnable
6. ✅ Output matches documented format

### Custom Instructions

For custom instructions (`.github/instructions/`):

- Target specific frameworks, languages, or patterns
- Include setup requirements (SDKs, tools, versions)
- Provide before/after examples
- Document which Copilot features they enhance

### Chat Modes / Agents

For custom agents (`.github/agents/`):

- Use `.agent.md` extension
- Define clear scope and boundaries
- Specify tools/capabilities the agent should have
- Include example conversations or use cases
- Follow template structure (use `/create-prompt` if needed)

### MCP Server Configurations

If contributing MCP server configs:

- Test configuration works in `.vscode/mcp.json`
- Document prerequisites (API keys, Node.js version, etc.)
- Include setup instructions
- Specify which prompts benefit from the server
- Note any security/privacy considerations

## 🚀 Submission Process

### 1. Fork & Clone

```bash
# Fork the repo on GitHub, then:
git clone https://github.com/YOUR_USERNAME/copilot-kit.git
cd copilot-kit
```

### 2. Create a Branch

```bash
git checkout -b prompt/your-prompt-name
# or
git checkout -b instruction/your-feature
# or
git checkout -b agent/your-agent-name
```

### 3. Add Your Contribution

- Create your prompt/instruction/agent file
- Follow the quality standards above
- Test thoroughly

### 4. Update Documentation

**Update README.md** to list your contribution:

For prompts, add a row to the Prompts table:

```markdown
| [your-prompt-name](.github/prompts/your-prompt-name.prompt.md) | Brief description | Category |
```

For instructions, add to Custom Instructions table.

For agents, add to Chat Modes table.

### 5. Commit & Push

```bash
git add .
git commit -m "Add [type]: [name] - [brief description]"
# Examples:
# "Add prompt: generate-api-tests - Create integration tests for REST APIs"
# "Add instruction: nestjs-best-practices - NestJS development guidelines"

git push origin prompt/your-prompt-name
```

### 6. Create Pull Request

- Go to your fork on GitHub
- Click "New Pull Request"
- Provide a clear description:
  - **What** it does
  - **Why** it's useful
  - **How** to use it
  - **Testing** you performed

**PR Template:**

```markdown
## Description
[Brief description of what this adds]

## Type of Contribution
- [ ] Custom Prompt
- [ ] Custom Instruction
- [ ] Chat Mode/Agent
- [ ] MCP Configuration
- [ ] Documentation
- [ ] Bug Fix

## Use Case
[When/why would someone use this?]

## Testing Performed
- [ ] Tested on [X] different scenarios
- [ ] YAML frontmatter validates
- [ ] Works in VS Code Copilot Chat
- [ ] Documentation updated

## Screenshots/Examples (optional)
[If helpful, show before/after or example output]
```

### 7. Respond to Feedback

- Maintainers may request changes
- Address feedback promptly
- Update your PR with requested modifications

## 🎨 Style Guidelines

### Markdown

- Use proper heading hierarchy (H1 → H2 → H3)
- Add language identifiers to code fences: ` ```typescript `
- Use **bold** for emphasis, `code` for technical terms
- Include blank lines between sections
- Keep lines under 120 characters when possible

### Writing Style

- **Clear and concise**: Avoid unnecessary jargon
- **Action-oriented**: Use active voice ("Generate tests" not "Tests will be generated")
- **Specific**: Provide concrete examples, not abstract concepts
- **Inclusive**: Use "we" and "you", avoid assumptions about skill level

### Code Examples

- Must be syntactically correct
- Include all necessary imports/setup
- Use realistic variable names
- Add comments for complex logic
- Show expected output when relevant

## ❌ What Not to Submit

Please avoid:

- ❌ Company-specific or proprietary prompts
- ❌ Prompts that require paid services without free tiers
- ❌ Untested or experimental prompts
- ❌ Duplicates of existing prompts (search first!)
- ❌ Prompts with hardcoded secrets/credentials
- ❌ Overly broad prompts ("make code better")
- ❌ Framework-specific prompts without clear value

## 🐛 Reporting Issues

Found a bug or have a suggestion?

1. **Search existing issues** first to avoid duplicates
2. **Open a new issue** with:
   - Clear, descriptive title
   - Steps to reproduce (for bugs)
   - Expected vs. actual behavior
   - VS Code version, Copilot version
   - Prompt name and version (if applicable)

## 💡 Questions?

- Open an issue with the `question` label
- Check existing documentation first
- Be specific about what you're trying to achieve

## 📜 Code of Conduct

- Be respectful and professional
- Provide constructive feedback
- Focus on the contribution, not the contributor
- Help create a welcoming environment for all

## 🏆 Recognition

Contributors will be:

- Listed in PR history
- Credited in release notes (for significant contributions)
- Mentioned in related videos/content (when applicable)

## 📄 License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

**Thank you for helping make Copilot Kit better for everyone!** 🚀

If you have questions about contributing, feel free to open an issue or reach out:

- **YouTube**: [IKcode Igor Wnek](https://youtube.com/@IKcodeIgorWnek)
- **BlueSky**: [@ikcode.dev](https://bsky.app/profile/ikcode.dev)
