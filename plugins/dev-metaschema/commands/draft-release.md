# Draft Release Command

Generate comprehensive GitHub release notes for a given tag, creating a new draft release or updating an existing one.

## Arguments

- `tag` (required): The release tag (e.g., `v3.0.0.M1`)

## Instructions

Execute the following steps to generate and publish draft release notes:

### Step 1: Validate Environment

Verify we're in a git repository with a GitHub remote:

```bash
# Check we're in a git repo
git rev-parse --git-dir

# Get the GitHub remote URL and extract owner/repo
git remote get-url origin
```

Extract the repository in `owner/repo` format from the remote URL. If not a GitHub repository, STOP and inform the user.

### Step 2: Validate Tag Exists

```bash
# Verify the tag exists
git rev-parse --verify "$TAG" 2>/dev/null
```

If the tag doesn't exist, STOP and inform the user the tag was not found.

### Step 3: Find Previous Tag

```bash
# Find the most recent tag before the target tag
git describe --tags --abbrev=0 "$TAG^" 2>/dev/null

# If no previous tag, use the initial commit
git rev-list --max-parents=0 HEAD
```

Inform the user: `Analyzing release $TAG... Previous tag: $PREVIOUS_TAG`

### Step 4: Get Date Range for PR Query

```bash
# Get the date of the previous tag
git log -1 --format=%cI "$PREVIOUS_TAG"

# Get the date of the current tag
git log -1 --format=%cI "$TAG"
```

### Step 5: Fetch Merged PRs

Use the GitHub CLI to fetch all PRs merged between the tags:

```bash
gh pr list \
  --repo "$OWNER/$REPO" \
  --state merged \
  --limit 500 \
  --json number,title,author,body,mergedAt,additions,deletions,labels \
  --jq ".[] | select(.mergedAt >= \"$PREV_DATE\" and .mergedAt <= \"$TAG_DATE\")"
```

Inform the user: `Found $COUNT PRs merged between tags`

### Step 6: Categorize PRs

Analyze each PR and categorize using conventional commit prefixes in the title:

| Title Prefix | Category |
|--------------|----------|
| `feat:` or `feat(` | new_features |
| `fix:` or `fix(` | bug_fixes |
| `docs:` or `docs(` | documentation |
| `build:` or `build(` or `ci:` or `ci(` | build_ci |
| `test:` or `test(` | test_coverage |
| `refactor:` or `refactor(` or `perf:` or `perf(` | build_ci |
| `build(deps):` OR author login is `dependabot` or `renovate` | dependencies |

**Secondary Analysis - Scan PR body for:**

1. **Breaking Changes:** Look for these patterns in the PR body:
   - `BREAKING CHANGE:` followed by description
   - `BREAKING:` followed by description
   - Lines starting with `⚠️`
   - Section headers containing "breaking"

   Extract the content following these markers for the Breaking Changes section.

2. **Highlight Candidates:** Flag PRs that match ANY of:
   - Body contains: "major", "significant", "redesign", "architecture", "overhaul", "new API"
   - Title contains: "redesign", "overhaul", "major"
   - Diff size (additions + deletions) > 500 lines
   - Has label: "highlight", "major", "significant"

**Fallback:** PRs without a recognized prefix go into "other_changes" category.

### Step 7: Sub-categorize Dependencies

For PRs in the `dependencies` category, further classify:

1. **GitHub Actions:** Title contains `actions/` or body mentions GitHub Actions
2. **Maven Plugins:** Package name contains `-maven-plugin`, `-plugin`, or is in `org.apache.maven.plugins`
3. **Java Libraries:** Everything else

Extract version transition from title/body using pattern: `from X.Y.Z to A.B.C` or `X.Y.Z → A.B.C`

### Step 8: Select Highlights

Present highlight candidates to the user using AskUserQuestion:

```
Based on PR analysis, I found these potential highlights:

1. [PR Title] (#number) - [reason flagged: large diff / keyword match]
2. [PR Title] (#number) - [reason flagged]
...

Select up to 5 PRs to feature in the Highlights section.
```

Use multiSelect: true to allow multiple selections. Include option descriptions showing why each was flagged as a potential highlight.

### Step 9: Confirm Pre-release Status

Analyze the tag to detect pre-release patterns:
- Contains `M` followed by number (milestone): `v3.0.0.M1`
- Contains `RC` (release candidate): `v3.0.0-RC1`
- Contains `alpha` or `beta`: `v3.0.0-alpha.1`
- Contains `SNAPSHOT`

Ask user to confirm using AskUserQuestion:

```
Tag $TAG appears to be a [milestone/pre-release] (contains "M1").
Should this release be marked as a pre-release?
```

Options:
1. "Yes (Recommended)" - if pattern detected
2. "No" - mark as full release

### Step 10: Generate Release Notes

Build the markdown content following this structure. **Omit any sections that have no content.**

```markdown
## ✨ Highlights

[Brief introductory sentence summarizing the release theme, referencing the most important changes.]

### [Descriptive highlight title]

[Description of the highlight with context. Reference the PR.] ([#NUMBER](PR_URL))

[Repeat for each selected highlight using ### sub-headers]

## ⚠️ Breaking Changes

### [Descriptive title extracted from PR]

[Content extracted from BREAKING CHANGE section of PR body]

[If migration guidance exists in PR, include it]

([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

## ✨ New Features

- **[Feature name from title]** - [First sentence of PR body] ([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

## 📚 Documentation Improvements

- [PR title without prefix] ([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

## 🔧 Build & CI Improvements

- [PR title without prefix] ([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

## 🐛 Bug Fixes

- [PR title without prefix] ([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

## 🧪 Test Coverage

- [PR title without prefix] ([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

## Other Changes

- [PR title] ([#NUMBER](PR_URL)) by [@AUTHOR](AUTHOR_URL)

<details>
<summary>📦 Dependency Updates</summary>

### Java Libraries
- `PACKAGE_NAME`: OLD_VERSION → NEW_VERSION ([#NUMBER](PR_URL))

### Maven Plugins
- `PLUGIN_NAME`: OLD_VERSION → NEW_VERSION ([#NUMBER](PR_URL))

### GitHub Actions
- `ACTION_NAME`: OLD_VERSION → NEW_VERSION ([#NUMBER](PR_URL))

</details>
```

**Formatting Rules:**
- PR URLs format: `https://github.com/OWNER/REPO/pull/NUMBER`
- Author URLs format: `https://github.com/AUTHOR_LOGIN`
- Strip conventional commit prefix from titles when displaying
- For features, bold the feature name (text before first `-` or `:` after the prefix)
- Empty sections should be completely omitted, not shown with "None"

### Step 11: Check for Existing Release

```bash
# Check if a release already exists for this tag
gh release view "$TAG" --repo "$OWNER/$REPO" --json isDraft,body 2>/dev/null
```

If release exists:
- If it's a draft, we'll update it
- If it's published, warn user and ask if they want to update anyway

### Step 12: Create or Update Release

Write the generated markdown to a temporary file, then:

**If no existing release:**
```bash
# Create new draft release
gh release create "$TAG" \
  --repo "$OWNER/$REPO" \
  --draft \
  --title "$TAG" \
  --notes-file "$TEMP_FILE" \
  $(if $IS_PRERELEASE; then echo "--prerelease"; fi)
```

**If updating existing draft:**
```bash
# Update existing release
gh release edit "$TAG" \
  --repo "$OWNER/$REPO" \
  --draft \
  --notes-file "$TEMP_FILE" \
  $(if $IS_PRERELEASE; then echo "--prerelease"; else echo "--prerelease=false"; fi)
```

### Step 13: Report Success

```
✓ Draft release created/updated for $TAG
  URL: https://github.com/OWNER/REPO/releases/tag/$TAG

Summary:
- Highlights: N selected
- Breaking Changes: N
- New Features: N
- Bug Fixes: N
- Documentation: N
- Build/CI: N
- Tests: N
- Dependencies: N

Review the draft and publish when ready.
```

## Error Handling

- **Not a git repo:** "This command must be run from within a git repository."
- **Not a GitHub remote:** "This repository does not have a GitHub remote configured."
- **Tag not found:** "Tag '$TAG' was not found. Please ensure the tag exists."
- **No PRs found:** "No merged PRs found between $PREVIOUS_TAG and $TAG. The release notes will be minimal."
- **gh CLI not installed:** "GitHub CLI (gh) is required but not installed. Install from https://cli.github.com/"
- **Not authenticated:** "Please authenticate with GitHub CLI: gh auth login"

## Example Usage

```
User: /draft-release v3.0.0.M1