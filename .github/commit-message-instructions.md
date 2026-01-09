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

feat(auth): add OAuth2 login support

fix: resolve null pointer exception in user service

docs(readme): update installation instructions

refactor(api)!: change response format for user endpoints

BREAKING CHANGE: User endpoint now returns data wrapped in `data` property
