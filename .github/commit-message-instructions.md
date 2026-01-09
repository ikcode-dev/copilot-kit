Generate commit messages following the Conventional Commits specification (https://www.conventionalcommits.org/).

## Format

<type>[optional scope]: <description>

[optional body]

[optional footer(s)]

## Type Prefixes

- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that do not affect the meaning of the code (formatting, missing semi-colons, etc.)
- `refactor`: A code change that neither fixes a bug nor adds a feature
- `perf`: A code change that improves performance
- `test`: Adding missing tests or correcting existing tests
- `build`: Changes that affect the build system or external dependencies
- `ci`: Changes to CI configuration files and scripts
- `chore`: Other changes that don't modify src or test files
- `revert`: Reverts a previous commit

## Rules

1. Use the imperative mood in the subject line ("add" not "added" or "adds")
2. Do not capitalize the first letter of the subject line
3. Do not end the subject line with a period
4. Limit the subject line to 50 characters when possible, max 72
5. Separate subject from body with a blank line (if body is present)
6. Use the body to explain what and why, not how
7. Wrap the body at 72 characters

## Breaking Changes

Indicate breaking changes by:
- Adding `!` after the type/scope: `feat!: remove deprecated API`
- Adding `BREAKING CHANGE:` footer in the body

## Examples

### feat - New features
feat(auth): add OAuth2 login support
feat(cart): implement guest checkout flow
feat: add dark mode toggle to settings

### fix - Bug fixes
fix(api): resolve null pointer exception in user service
fix(ui): correct button alignment on mobile viewport
fix: prevent duplicate form submissions

### docs - Documentation
docs(readme): update installation instructions
docs(api): add endpoint usage examples
docs: add contributing guidelines

### style - Code style (no logic changes)
style: format code with prettier
style(components): fix indentation in Button component
style: remove trailing whitespace

### refactor - Code restructuring
refactor(api): extract validation logic to middleware
refactor: simplify conditional rendering in Dashboard
refactor(db): rename user table columns for clarity

### perf - Performance improvements
perf(images): implement lazy loading for gallery
perf(api): add database query caching
perf: reduce bundle size by code splitting

### test - Tests
test(auth): add unit tests for login validation
test: increase coverage for utils module
test(e2e): add checkout flow integration tests

### build - Build system
build(deps): upgrade React to v19
build: configure webpack for production optimization
build(docker): update base image to node 22

### ci - CI/CD changes
ci: add GitHub Actions workflow for testing
ci(deploy): configure automatic staging deployments
ci: add code coverage reporting to pipeline

### chore - Maintenance tasks
chore: update .gitignore patterns
chore(deps): bump minor dependency versions
chore: remove deprecated config files

### revert - Reverting changes
revert: revert "feat(auth): add OAuth2 login support"
revert(api): undo breaking change to user endpoint

### Breaking changes
feat(api)!: change response format for user endpoints

BREAKING CHANGE: User endpoint now returns data wrapped in `data` property

refactor!: drop support for Node.js 16
