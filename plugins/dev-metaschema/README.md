# Dev Metaschema Plugin

Skills, commands, and agents for contributing to [metaschema-framework](https://github.com/metaschema-framework) repositories.

## Installation

```bash
claude plugin install metaschema-framework:dev-metaschema
```

## Skills

### repo-organization

Understanding of the metaschema-framework repository ecosystem:

- Repository overview and purposes
- Dependency hierarchy
- Common build patterns
- Cross-repo development workflow

### development-workflow

Development practices for metaschema-framework projects:

- TDD requirements (mandatory, blocking)
- PRD-based development lifecycle
- Debugging workflow
- Testing best practices

### prd-construction

Templates and methodology for Product Requirements Documents:

- PRD templates
- Implementation plan templates
- Directory structure
- Content guidelines

### unit-test-writing

Unit test patterns and coverage workflow:

- Edge case checklist
- Configuration/parsing edge cases
- Coverage expansion workflow
- Test naming conventions

### pr-feedback

Workflow for addressing PR review feedback:

- Fetching and categorizing feedback
- Verifying technical claims
- GitHub API for inline replies
- Resolving review threads

## Commands

### /squash-commits

Squash all commits in the current branch into a single commit with a comprehensive message.

### /address-pr-feedback

Address review feedback on the current PR, fix issues, and close resolved conversations.

## Target Repositories

This plugin provides development context for:

- [metaschema](https://github.com/metaschema-framework/metaschema) - Specification
- [metaschema-java](https://github.com/metaschema-framework/metaschema-java) - Java implementation
- [oscal-cli](https://github.com/metaschema-framework/oscal-cli) - OSCAL command-line tool
- [liboscal-java](https://github.com/metaschema-framework/liboscal-java) - OSCAL Java library
- [oss-maven](https://github.com/metaschema-framework/oss-maven) - Parent POM and build configuration
- [maven2](https://github.com/metaschema-framework/maven2) - Maven repository for releases

## Usage

Once installed, Claude will use these skills when working within metaschema-framework repositories.

## Related

- [metaschema](../metaschema) - Metaschema knowledge
- [metaschema-tools](../metaschema-tools) - CLI tooling
