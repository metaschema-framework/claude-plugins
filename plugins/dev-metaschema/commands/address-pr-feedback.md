# Address PR Feedback Command

Address review feedback on the current PR, fix issues, and close resolved conversations.

## Instructions

Execute the following steps to systematically address PR feedback:

### Step 1: Identify the PR

Determine the current PR number:

```bash
# Get current branch
git branch --show-current

# Find PR for current branch
gh pr list --head $(git branch --show-current) --json number,title,url --jq '.[0]'
```

If no PR exists, STOP and inform the user.

### Step 2: Fetch All Comments

Retrieve all comments on the PR (both file-level and general):

```bash
# Get PR number and repository info
PR_NUMBER=$(gh pr list --head $(git branch --show-current) --json number --jq '.[0].number')
REPO=$(gh repo view --json nameWithOwner --jq '.nameWithOwner')

# Fetch file-level review comments (line-specific)
echo "=== File Review Comments ==="
gh api repos/$REPO/pulls/$PR_NUMBER/comments \
  --jq '.[] | {id: .id, type: "review", path: .path, line: .line, body: .body[0:300], user: .user.login, in_reply_to_id: .in_reply_to_id}'

# Fetch general PR comments (not tied to specific lines)
echo "=== General PR Comments ==="
gh api repos/$REPO/issues/$PR_NUMBER/comments \
  --jq '.[] | {id: .id, type: "issue", body: .body[0:300], user: .user.login}'
```

**Comment Types:**

| Type | API Endpoint | Description |
|------|--------------|-------------|
| Review comments | `/pulls/{id}/comments` | Line-specific comments on files |
| Issue comments | `/issues/{id}/comments` | General PR-level discussion |

### Step 3: Categorize Comments

**Reference**: See `.claude/skills/pr-feedback.md` for detailed categorization guidance.

Before implementing any feedback, **verify technical claims** (Step 2 in the skill):
- Automated reviewers can be wrong
- Check official docs or schema introspection before implementing
- Note incorrect feedback for the summary

Organize comments into categories:

| Category | Description | Action Required |
|----------|-------------|-----------------|
| **Blocking** | Critical issues, bugs, security | Must fix before merge |
| **Suggestions** | Recommended improvements | Should address |
| **Nits** | Minor style/formatting | Address (shows attention to detail) |
| **Questions** | Needs clarification | Reply with answer |
| **Incorrect** | Verified as wrong | Explain why in response |

For each root comment (where `in_reply_to_id` is null), determine:
1. Is the feedback technically correct? (verify before implementing)
2. Is this already addressed in the code?
3. What action is needed?
4. What file/line needs changes?

### Step 4: Address Each Issue

**Reference**: See `.claude/skills/pr-feedback.md` Step 4 for detailed guidance.

For each unresolved comment:

1. **Read the relevant code** to understand the current state
2. **Verify the feedback** is technically correct before implementing
3. **Determine the fix** based on the feedback and project conventions
4. **Follow TDD for code changes**:
   - Write/update tests first if the fix involves code behavior
   - Verify tests fail for the right reason
   - Implement the fix
   - Verify tests pass
5. **Verify the fix** compiles and all tests pass

Track progress with a checklist:
- [ ] Comment 1: [description] - [status]
- [ ] Comment 2: [description] - [status]
- ...

**Batch related fixes** into a single commit when possible.

### Step 5: Reply to Close Conversations

For EACH addressed comment, reply **inline in the conversation thread** to close it.

**CRITICAL: Replies must be inline, not general PR comments.**
- Use the `in_reply_to` parameter to reply within the existing conversation thread
- Do NOT use `gh pr comment` which creates a new top-level comment
- Inline replies keep the conversation organized and allow proper thread resolution

**Use the reviewer's actual handle** from the comment's `user.login` field:

**For automated reviewers:**
```text
@<reviewer_handle> Addressed in commit <hash>. <Brief explanation of the resolution>
```

Examples:
- `@coderabbitai Addressed in commit abc123. Added @Nullable annotation.`
- `@copilot Addressed in commit def456. Fixed the null check.`
- `@sonarcloud Addressed in commit ghi789. Resolved the code smell.`

**For human reviewers:**
```text
Addressed in commit <hash>. <Brief explanation>
```
(No @ mention needed for human reviewers)

Use the GitHub CLI to reply inline:
```bash
# Get repository info (if not already set)
REPO=$(gh repo view --json nameWithOwner --jq '.nameWithOwner')

# Reply inline to a review comment using in_reply_to parameter
# The COMMENT_ID is the id of the comment you are replying to
gh api repos/$REPO/pulls/$PR_NUMBER/comments \
  -X POST \
  -F in_reply_to=<COMMENT_ID> \
  -f body="@coderabbitai Addressed in commit abc1234. <explanation>"
```

**Important API notes:**
- Use `-F in_reply_to=<id>` (capital F for integer parameter)
- The `in_reply_to` value must be the numeric comment ID
- This creates a reply within the existing conversation thread

### Step 6: Generate Summary Report

After addressing all feedback, produce a summary table:

```markdown
## PR Feedback Summary

| File | Comment | Status |
|------|---------|--------|
| `path/to/file.java` | Description of issue | ✅ Addressed (commit abc123) |
| `path/to/other.java` | Another issue | ✅ Addressed (commit def456) |
| `path/to/file.java` | Nitpick suggestion | ⏭️ Deferred (out of scope) |

**Actions Taken:**
- Fixed X by doing Y
- Updated Z to address W
- Deferred A because B
```

### Step 7: Verify and Commit

After all changes:

```bash
# Run tests to verify fixes don't break anything
mvn -pl <affected-modules> test

# Check for any style issues
mvn checkstyle:check

# Stage and commit changes
git add -A
git commit -m "fix: address PR review feedback

- <list of changes made>
"

# Push to update PR
git push
```

## Status Icons

Use these icons in the summary table:

| Icon | Meaning |
|------|---------|
| ✅ | Addressed - fix committed |
| ❌ | Rejected - feedback incorrect, explained why |
| ⏭️ | Deferred - out of scope, will address later |
| 🔄 | In Progress - working on it |
| ❓ | Needs Clarification - asked reviewer |

## Automated Reviewer Conventions

When responding to automated reviewers, **use the reviewer's actual handle** from the comment.

### General Pattern
```text
@<reviewer_handle> Addressed in commit <hash>. <Brief explanation>
```

### Common Automated Reviewers

| Reviewer | Handle | Notes |
|----------|--------|-------|
| CodeRabbit | `@coderabbitai` | Auto-resolves when it detects fixes |
| GitHub Copilot | `@copilot` | May require manual resolution |
| SonarCloud | `@sonarcloud` | Links to SonarCloud dashboard |
| Codecov | `@codecov` | Coverage-related comments |

### Example Responses
```text
@coderabbitai Addressed in commit e45cabea. Aligned with the @NonNull contract from AbstractDeserializer.
@sonarcloud Addressed in commit abc1234. Extracted method to reduce complexity.
```

## Safety Checks

Before making changes:

1. **Understand the feedback** - Don't make changes you don't understand
2. **Check project conventions** - Align fixes with existing patterns
3. **Run tests** - Verify fixes don't introduce regressions
4. **Don't over-fix** - Address only what was requested

## Example Workflow

```text
1. Fetch PR #597 comments
2. Found 5 root comments from @coderabbitai:
   - PathTracker.java: Add @Nullable to pop()
   - DefaultXmlDeserializer.java: Null-safety annotation
   - ValidationErrorMessageTest.java: Inconsistent null handling
   - DefaultXmlDeserializer.java: Document null behavior
   - DefaultJsonDeserializer.java: Null-safety annotation

3. Address each:
   - Added @Nullable annotation to pop()
   - Aligned with @NonNull contract from AbstractDeserializer
   - Updated test to use valid URI
   - Removed null-handling code (aligned with contract)
   - Removed null-handling code (aligned with contract)

4. Reply to each with @coderabbitai mention

5. Generate summary table

6. Commit and push
```
