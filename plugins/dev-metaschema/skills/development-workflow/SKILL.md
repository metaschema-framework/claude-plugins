---
name: development-workflow
description: Use when working in metaschema-framework repositories - covers TDD requirements, PRD workflow, debugging practices, and testing patterns
---

# Development Workflow

> **Scope:** This document describes development workflows for AI agents (Claude + superpowers plugin) and automated code review. Human developers should adapt these TDD, parallel-execution, and review principles to their tooling and IDE.

## Skill Usage Protocol (MANDATORY)

Before responding to ANY task, complete this checklist:

1. **Check for relevant skills** - Does any skill match this request?
2. **If skill exists** → Use `Skill` tool to load and run it
3. **Announce usage** - "I'm using [skill] to [action]"
4. **Follow skill exactly** - Don't adapt away the discipline

### Checklist Tracking Rule
If a skill has a checklist, create **TodoWrite todos for EACH item**. Never:
- Work through checklists mentally
- Skip todos "to save time"
- Batch multiple items into one todo

### Common Rationalizations (RED FLAGS)
If you think any of these, STOP and check for skills:
- "This is just a simple question"
- "Let me gather information first"
- "This doesn't need a formal skill"
- "The skill is overkill for this"
- "I'll just do this one thing first"

**Why:** Skills document proven techniques. Not using available skills = repeating solved problems.

---

## Test-Driven Development (MANDATORY - BLOCKING)

**ALL code changes MUST follow TDD. No exceptions. This is BLOCKING.**

### The Iron Law of TDD

```text
TESTS MUST BE WRITTEN AND FAIL BEFORE ANY IMPLEMENTATION CODE EXISTS
```

This is non-negotiable. Implementation code written before tests is a violation.

### The TDD Cycle

1. **Write the test first** - Before writing any implementation code
2. **Watch it fail** - Run the test to verify it fails for the right reason
3. **Write minimal code** - Implement just enough to make the test pass
4. **Refactor** - Clean up while keeping tests green
5. **Repeat** - For each new behavior

### Enforcement Gate

**Before writing ANY implementation code, you MUST have:**
- [ ] A test file created for the new functionality
- [ ] At least one failing test that exercises the code path
- [ ] Verification that the test fails for the expected reason (not a syntax error)

**If you haven't completed these steps, STOP. Go back and write tests first.**

### Red Flags (You're Skipping TDD)

If you catch yourself doing ANY of these, STOP IMMEDIATELY:
- Writing implementation code before tests
- "I'll add tests after I get it working"
- "This is too simple to need tests"
- "Let me just make this small change first"
- Dispatching implementation agents before test agents complete
- Dispatching tests and implementation in the same parallel batch
- Modifying code without first verifying existing test coverage

### When Tests Already Exist

When modifying existing code:
1. **Run existing tests first** - Verify they pass before changes
2. **Add tests for new behavior** - Write failing tests for new functionality
3. **Make changes** - Implement while keeping all tests passing

---

## PRD-Based Development Lifecycle

For new development work, follow this structured lifecycle:

### Phase 1: PRD Development

1. **Use `superpowers:brainstorming`** to refine requirements
2. **Use `prd-construction` skill** for templates and methodology
3. **Create PRD directory**: `PRDs/[date]-[name]/`
4. **Create PRD documents** using skill templates:
   - `PRD.md` - Problem statement, goals, requirements, success metrics
   - `implementation-plan.md` - Detailed PR breakdown with acceptance criteria
5. **Add supporting documents** to the directory as needed

### Phase 2: User Approval
- Present PRD to user for review
- Incorporate feedback and iterate
- **Do NOT proceed to development until PRD is approved**

### Phase 3: Development

Execute the plan using these skills:
- `superpowers:executing-plans` - Execute tasks in batches with review checkpoints
- `superpowers:test-driven-development` - Write tests first, then implementation
- `superpowers:subagent-driven-development` - Quality-gated autonomous work
- `superpowers:dispatching-parallel-agents` - Concurrent work on independent tasks

**Update PRD documents as you work:**
- Mark acceptance criteria as `[x]` when completed
- Update PR status in implementation-plan.md
- Add notes about deviations from plan

### Phase 4: Code Review Cycle
1. **Run code review agents** (can run multiple in parallel)
2. **Consolidate review findings** into a report of identified issues
3. **Work the issues** and apply TDD for any gaps found
4. **Re-review** - Run code review again on changes
5. **Repeat** until all issues are resolved

### Phase 5: Verification & PR
- `superpowers:verification-before-completion` - Confirm all tests pass
- Verify all code review issues are resolved
- Create PR against `develop` branch

---

## Debugging Workflow (MANDATORY)

**ALL debugging MUST use `superpowers:systematic-debugging` skill. No exceptions.**

### Step 0: Invoke the Debugging Skill (REQUIRED FIRST STEP)

**BEFORE ANY investigation or fix attempts:**
1. Use `Skill` tool to invoke `superpowers:systematic-debugging`
2. Follow the four phases exactly as specified
3. Create TodoWrite todos for each phase

**Red Flags (You're Skipping the Skill):**
- "Let me just check this quickly"
- "This is a simple bug"
- "I can see the problem, let me fix it"
- "One quick fix to try first"

### Step 1: Identify Root Cause (REQUIRED)
**Do NOT attempt fixes until root cause is identified.**

Use these skills:
- `superpowers:systematic-debugging` - 4-phase framework
- `superpowers:root-cause-tracing` - Trace bugs backward through call stack

### Step 2: Verify Test Coverage
Once root cause is confirmed:
1. **Check for existing test** - Does a test cover this scenario?
2. **If no test exists** - Use TDD to write a failing test that reproduces the bug

### Step 3: Implement Fix
- Fix the identified root cause
- Run the failing test - it should now pass
- Run all related tests

### Step 4: Verification & PR
- Use `superpowers:verification-before-completion` before claiming fixed
- Create PR against `develop` branch

---

## Testing Best Practices

When writing or modifying tests, apply `superpowers:testing-anti-patterns`:

### Iron Laws
1. **NEVER test mock behavior** - Assert on real component behavior, not mock existence
2. **NEVER add test-only methods to production classes** - Put test utilities in test files
3. **NEVER mock without understanding dependencies** - Know what side effects tests depend on

### Quick Reference

| Anti-Pattern | Fix |
|--------------|-----|
| Assert on mock elements | Test real component or unmock |
| Test-only methods in production | Move to test utilities |
| Mock without understanding | Understand deps first, mock minimally |
| Tests as afterthought | TDD - tests first |

---

## When to Use PRD Workflow
- New features
- Significant refactoring
- Complex bug fixes requiring architectural changes

## When to Skip PRD
- Simple bug fixes with clear solutions
- Minor UI tweaks
- Minor documentation updates
