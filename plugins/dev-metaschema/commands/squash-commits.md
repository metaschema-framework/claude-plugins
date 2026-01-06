# Squash Commits Command

Squash all commits in the current branch into a single commit with a comprehensive message.

## Instructions

Execute the following steps to squash commits safely:

### Step 1: Validate State

First, verify the repository state is clean and identify the base branch:

```bash
# Check for uncommitted changes
git status --porcelain

# Identify the base branch (develop or main)
git branch -r | grep -E 'origin/(develop|main)$' | head -1
```

If there are uncommitted changes, STOP and inform the user they must commit or stash changes first.

### Step 2: Identify Commits to Squash

Determine the merge base and list all commits:

```bash
# Find the merge base with the target branch (adjust branch name as needed)
git merge-base HEAD origin/develop

# Count commits since merge base
git rev-list --count $(git merge-base HEAD origin/develop)..HEAD

# List all commits with full messages
git log $(git merge-base HEAD origin/develop)..HEAD --format="=== COMMIT %h ===%n%B"
```

If there is only 1 commit, inform the user there is nothing to squash.

### Step 3: Analyze Commit Messages

Read and analyze all commit messages from Step 2. Create a comprehensive combined message that:

1. **Summarizes the overall change** in a clear subject line (50 chars max if possible)
2. **Preserves important details** from each original commit
3. **Groups related changes** logically
4. **Maintains any issue/PR references** (e.g., #123, fixes #456)
5. **Removes redundant information** (e.g., repeated "fix typo" commits can be consolidated)

Format the message as:
```text
<type>: <concise summary of all changes>

<Detailed description organized by logical groupings>

- <Key change 1>
- <Key change 2>
- ...

<Any preserved references or metadata>
```

### Step 4: Perform the Squash

Use soft reset to squash (preserves all changes in staging):

```bash
# Store the merge base commit
MERGE_BASE=$(git merge-base HEAD origin/develop)

# Soft reset to merge base (keeps changes staged)
git reset --soft $MERGE_BASE

# Verify staged changes match expected
git diff --cached --stat
```

### Step 5: Create the Combined Commit

Create the new commit with the comprehensive message:

```bash
git commit -m "<comprehensive message from Step 3>"
```

### Step 6: Verify Result

Confirm the squash was successful:

```bash
# Should show only 1 commit ahead of base
git rev-list --count $(git merge-base HEAD origin/develop)..HEAD

# Show the new commit
git log -1 --format=full

# Verify no changes were lost (should show same diff as before)
git diff origin/develop --stat
```

### Step 7: Update Remote (if applicable)

If the branch was already pushed, force push is required:

```bash
# Force push with lease (safer than --force)
git push --force-with-lease
```

**IMPORTANT**: Only force push if:
- This is a feature branch (NOT main/develop)
- The user explicitly confirms they want to update the remote
- No one else is working on this branch

## Safety Checks

Before performing any destructive operations:

1. **Verify branch name** - Never squash on main/develop/master
2. **Check for uncommitted work** - Must have clean working directory
3. **Confirm commit count** - User should know how many commits will be squashed
4. **Backup option** - Inform user they can recover with `git reflog` if needed

## Example Output

For a branch with commits:
- "feat: add user authentication"
- "fix: correct login redirect"
- "docs: add auth documentation"
- "fix: typo in error message"

Combined message might be:
```text
feat: add user authentication with documentation

Implement user authentication system including:
- Login/logout functionality with proper redirects
- Authentication documentation
- Error message handling

Fixes minor issues:
- Corrected login redirect behavior
- Fixed typo in error message
```
