---
name: claude-md-conventions
description: Use when creating or updating CLAUDE.md files - defines what to include, what to exclude, and structure conventions for metaschema-framework repositories
---

# CLAUDE.md Conventions

## When to Use

- Creating a new CLAUDE.md for a repository
- Updating existing CLAUDE.md content
- Running `/init` command to generate CLAUDE.md

## Purpose of CLAUDE.md

CLAUDE.md provides guidance to Claude Code when working in a repository. It should contain:
- **Project-specific** information that Claude needs to work effectively
- **Non-obvious** conventions that aren't discoverable from code
- **Commands** for building, testing, and quality checks
- **Architecture** overview for quick orientation

## Required Sections

### 1. Header

```markdown
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
```

### 2. Overview (Brief)

One paragraph describing:
- What the project does
- Key technologies or frameworks
- Any domain-specific context needed

### 3. Build Commands

Essential commands grouped logically:

```markdown
## Build Commands

\`\`\`bash
# Build all modules
mvn install

# CI build (required before pushing)
mvn install -PCI -Prelease

# Run tests
mvn test

# Run single test
mvn -pl module test -Dtest=TestClass#testMethod
\`\`\`
```

### 4. Project Architecture

Brief module/package overview. Use `text` language identifier for ASCII diagrams:

```markdown
## Project Architecture

\`\`\`text
project-root
├── module-a  - Description
├── module-b  - Description
└── module-c  - Description
\`\`\`
```

### 5. Key Conventions (Blocking Items)

Document conventions that **must** be followed, marked as BLOCKING:

```markdown
## Code Style

- **Javadoc**: 100% coverage on public/protected (BLOCKING)
- **TDD**: Tests must be written first (BLOCKING)

## Git Workflow

- **All PRs MUST target `develop` branch** (BLOCKING)
- **All PRs MUST be from personal fork** (BLOCKING)
```

### 6. Available Skills

Reference skills from installed plugins:

```markdown
## Available Skills

### Development (dev-metaschema plugin)
- `dev-metaschema:development-workflow` - TDD, debugging, PRD workflow
- `dev-metaschema:javadoc-style-guide` - Javadoc requirements and patterns
```

## What to Include

| Include | Example |
|---------|---------|
| Build/test commands | `mvn install -PCI -Prelease` |
| Module hierarchy | ASCII diagram of modules |
| Key interfaces | `IModule`, `IDefinition` |
| Blocking requirements | Javadoc, TDD, PR targets |
| Non-obvious conventions | Worktree usage, fork workflow |
| Available skills | Links to plugin skills |
| PRD tracking | Active/Completed PRD tables |

## What to Exclude

| Exclude | Why |
|---------|-----|
| Generic best practices | Obvious ("write clean code") |
| Full API documentation | Use Javadoc instead |
| Detailed style guides | Use skills instead |
| Every file/class listing | Discoverable from code |
| Time estimates | Plans should be timeless |
| AI-specific instructions | "For Claude:" or meta-commentary |

## Formatting Rules

### Code Blocks

All fenced code blocks MUST have a language identifier:

| Content | Language ID |
|---------|-------------|
| Shell commands | `bash` |
| Java code | `java` |
| XML/JSON/YAML | `xml`, `json`, `yaml` |
| Directory trees | `text` |
| Plain text | `text` |

### Headings

Use markdown headings (`##`, `###`), not bold text (`**text**`) for structure.

### Conciseness

- Use bullet points over paragraphs
- Consolidate related commands
- Reference skills instead of duplicating content
- Remove redundancy between sections

## Section Order

1. Header and overview
2. Build commands
3. Static analysis tools (brief)
4. Project architecture
5. Testing requirements
6. Code style (brief, reference skills)
7. Git workflow
8. PRD workflow and tracking
9. Available skills

## Maintenance

### When Adding Content

Ask: "Is this project-specific and non-obvious?"
- If yes → Add to CLAUDE.md
- If no → Consider a skill or omit

### When Removing Content

Remove if:
- Duplicated elsewhere (README, skills, rules)
- Generic best practice
- Easily discoverable from code
- Time-based or will become stale

### PRD Tables

Update PRD tables when:
- Starting a PRD → Add to Active PRDs
- Completing a PRD → Move to Completed PRDs

## Example Structure

```markdown
# CLAUDE.md

This file provides guidance to Claude Code...

## Overview

[One paragraph about the project]

## Build Commands

[Essential commands]

## Project Architecture

[Module hierarchy diagram]

## Testing

[TDD requirements, test commands]

## Code Style

[Brief, reference skills]

## Git Workflow

[Fork requirement, branch targets]

## PRD Workflow

[PRD tables]

## Available Skills

[Skill references by plugin]
```

## Anti-Patterns

| Anti-Pattern | Better Approach |
|--------------|-----------------|
| Long prose paragraphs | Bullet points |
| Duplicating skill content | Reference the skill |
| Listing every class | Show key interfaces only |
| Generic advice | Project-specific guidance |
| Unspecified code blocks | Always add language ID |
| "For Claude:" instructions | Write as documentation |
