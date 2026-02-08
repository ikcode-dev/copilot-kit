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
Rule 1: Use the imperative mood in the subject line ("add" not "added" or "adds").
Rule 2: Do not capitalize the first letter of the subject line.
Rule 3: Do not end the subject line with a period.
Rule 4: Limit the subject line to 50 characters when possible, max 72.
Rule 5: Separate subject from body with a blank line if a body is present.
Rule 6: Use the body to explain what and why, not how.
Rule 7: Wrap the body at 72 characters.
</rules>

<breaking-changes>
Indicate breaking changes by adding "!" after the type or scope, for example: feat!: remove deprecated API.
Indicate breaking changes by adding a BREAKING CHANGE: footer in the body.
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
