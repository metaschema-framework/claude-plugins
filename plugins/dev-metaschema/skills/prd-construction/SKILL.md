---
name: prd-construction
description: Use when creating PRDs (Product Requirements Documents) or implementation plans for new features, significant refactoring, or complex bug fixes. Provides templates, methodology, and best practices for structured planning.
---

# PRD Construction Skill

Use this skill when planning work that requires structured documentation before implementation.

## When to Use This Skill

- New features
- Significant refactoring efforts
- Complex bug fixes requiring architectural changes
- Any work benefiting from upfront planning and user approval

## DOs and DONTs

### DO

- **DO** use the `superpowers:brainstorming` skill first to refine requirements before writing the PRD
- **DO** create a dedicated directory for each PRD with all related documents
- **DO** include measurable success metrics with current vs. target values
- **DO** group related changes into logical, cohesive PRs
- **DO** include acceptance criteria as checkboxes that can be marked complete
- **DO** list exact file paths to create/modify in the implementation plan
- **DO** specify dependencies between PRs when they exist
- **DO** update the PRD as work progresses (mark checkboxes, add notes)
- **DO** get user approval before starting implementation

### DON'T

- **DON'T** include AI-specific instructions in PRD documents (e.g., "For Claude:", "AI should...")
- **DON'T** include meta-commentary about the document itself
- **DON'T** skip the brainstorming phase - requirements need refinement
- **DON'T** create one PR per commit - group related commits into cohesive PRs
- **DON'T** split logically related changes across multiple PRs unnecessarily
- **DON'T** start implementation before user approves the PRD
- **DON'T** leave acceptance criteria vague - make them verifiable
- **DON'T** forget to update status as work completes

---

## PRD Template

```markdown
# PRD: [Feature/Initiative Name]

## Document Information

| Field | Value |
|-------|-------|
| **PRD ID** | [SHORT-ID-001] |
| **Status** | Draft / In Review / Approved / In Progress / Completed |
| **Author** | [Author Name] |
| **Created** | YYYY-MM-DD |
| **Last Updated** | YYYY-MM-DD |

---

## 1. Overview

### 1.1 Problem Statement

[Describe the problem being solved. What pain points exist? Why does this matter?]

### 1.2 Goals

[Numbered list of specific, measurable goals]

1. [Goal 1]
2. [Goal 2]
3. [Goal 3]

### 1.3 Non-Goals

[What is explicitly out of scope for this initiative?]

- [Non-goal 1]
- [Non-goal 2]

### 1.4 Success Metrics

| Metric | Current | Target |
|--------|---------|--------|
| [Metric 1] | [value] | [value] |
| [Metric 2] | [value] | [value] |

---

## 2. Background

### 2.1 Current State

[Describe the current system/behavior that will be changed]

### 2.2 Technical Context

[Relevant technical details: architecture, dependencies, constraints]

---

## 3. Requirements

### 3.1 Functional Requirements

#### FR-1: [Requirement Name]
[Description of the requirement]

#### FR-2: [Requirement Name]
[Description of the requirement]

### 3.2 Non-Functional Requirements

#### NFR-1: [Requirement Name]
[Description: performance, security, maintainability, etc.]

---

## 4. Implementation Phases

[High-level overview of implementation phases]

### Phase 1: [Phase Name]
[Brief description, reference to implementation-plan.md for details]

### Phase 2: [Phase Name]
[Brief description]

---

## 5. Testing Strategy

### 5.1 Test Approach
[How will changes be tested? TDD requirements, test types]

### 5.2 Verification Checklist
- [ ] [Verification item 1]
- [ ] [Verification item 2]

---

## 6. Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| [Risk 1] | High/Medium/Low | High/Medium/Low | [Mitigation strategy] |

---

## 7. Open Questions

[List any unresolved questions that need answers before or during implementation]

1. [Question 1]
2. [Question 2]

---

## 8. Related Documents

- [Implementation Plan](./implementation-plan.md)
- [Other supporting documents]
```

---

## Implementation Plan Template

```markdown
# Implementation Plan: [Feature/Initiative Name]

This document details each PR in the [initiative name].

---

## Prerequisites

[Any setup, dependencies, or preparation needed before starting]

---

## Test-Driven Development Requirement

**All functional code changes must follow TDD:**

1. Write or update tests first to capture expected behavior
2. Verify tests pass with existing implementation
3. Make the code changes
4. Verify tests still pass after changes

---

## Phase 1: [Phase Name]

### PR 1: [PR Title]

| Attribute | Value |
|-----------|-------|
| **Files Changed** | ~[count] |
| **Risk Level** | Low / Medium / High |
| **Dependencies** | None / PR X |
| **Target Branch** | develop |
| **Status** | Pending / In Progress / Completed |
| **Pull Request** | [Link when created] |

#### Files to Modify

| File | Changes |
|------|---------|
| `path/to/file.java` | [Description of changes] |

#### Implementation Approach

[Step-by-step approach for this PR]

1. [Step 1]
2. [Step 2]

#### Acceptance Criteria

- [ ] [Criterion 1 - specific and verifiable]
- [ ] [Criterion 2]
- [ ] All tests pass
- [ ] Build succeeds

---

### PR 2: [PR Title]

[Repeat structure for each PR]

---

## PR Summary Table

| PR | Description | Files | Risk | Dependencies | Status |
|----|-------------|-------|------|--------------|--------|
| 1 | [Description] | ~X | Low | None | Pending |
| 2 | [Description] | ~X | Medium | PR 1 | Pending |

**Total Estimated PRs**: X
**Total Estimated Files**: ~X
```

---

## Directory Structure

Each PRD should have its own directory containing:

```text
PRDs/[date]-[name]/
├── PRD.md                 # Main requirements document
├── implementation-plan.md # Detailed PR breakdown
└── [supporting-docs].md   # Analysis, research, diagrams as needed
```

The directory naming format (e.g., date format) is project-specific (see project rules).

---

## Workflow Integration

1. **Trigger**: User requests a new feature or significant change
2. **Brainstorm**: Use `superpowers:brainstorming` to refine requirements
3. **Create PRD**: Use this skill's templates to document requirements
4. **Create Plan**: Use `superpowers:writing-plans` for detailed implementation
5. **User Approval**: Present PRD for review, iterate as needed
6. **Execute**: Use `superpowers:executing-plans` to implement
7. **Track Progress**: Update PRD checkboxes and status as work completes

---

## Content Guidelines

### Writing Style

- Use clear, direct language
- Avoid jargon unless necessary (define terms when used)
- Be specific - vague requirements lead to vague implementations
- Include examples where they clarify intent

### Acceptance Criteria

Good acceptance criteria are:
- **Specific**: Exactly what must be true
- **Measurable**: Can be verified objectively
- **Actionable**: Clear what action satisfies them

Examples:
- Good: "All tests pass with `mvn test`"
- Bad: "Tests should work"
- Good: "No compiler warnings in modified files"
- Bad: "Code quality is good"

### PR Sizing

PRs should be **logically cohesive**, not artificially small:

- **Group related changes** - A feature and its tests belong in one PR
- **Avoid fragmentation** - Don't split cohesive work just to reduce file count
- **Consider reviewability** - Target ≤50 files, max 100 per PR; split at logical boundaries if needed
- **One PR can have multiple commits** - Use commits to show progression, not to create PRs

**Anti-patterns to avoid:**
- One PR per commit
- Splitting a feature across 5+ PRs when it could be 1-2
- Creating "prep" PRs that only make sense with follow-up PRs
