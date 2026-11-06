---
name: commit-message-generator
description: Generates concise, conventional commit messages from git diffs. Invoke when the user asks to create a commit message, review changes, or automate commit writing.
license: MIT
metadata:
  author: trae-community
  version: "1.0.0"
  category: developer-tools
---

# Commit Message Generator

Generates well-structured commit messages following the Conventional Commits
specification by analyzing staged or working changes in a git repository.

## When to use

Invoke this skill when:
- The user asks to "generate a commit message" or "write a commit message"
- The user has made changes and wants to commit
- The user mentions "commit", "git commit", or "提交信息"
- The user wants commit messages following a specific convention

Do NOT use this skill when:
- The user only wants to view diffs without committing
- The repository has no git history
- The user explicitly provides their own commit message

## Instructions

### Step 1: Gather context

1. Check if the current directory is a git repository:
   ```
   git rev-parse --is-inside-work-tree
   ```
2. Get the staged changes (preferred):
   ```
   git diff --cached
   ```
3. If no staged changes, get working directory changes:
   ```
   git diff
   ```
4. Get the current branch name and recent commit history for context:
   ```
   git log --oneline -5
   ```

### Step 2: Analyze the changes

Classify the changes based on Conventional Commits types:

| Type | Description |
|------|-------------|
| `feat` | New feature or functionality |
| `fix` | Bug fix |
| `docs` | Documentation only changes |
| `style` | Formatting, missing semicolons, etc. (no code change) |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Code change that improves performance |
| `test` | Adding missing tests or correcting existing tests |
| `chore` | Changes to build process, tooling, or dependencies |
| `ci` | CI configuration changes |
| `revert` | Reverts a previous commit |

Identify the scope (affected module/component) from the changed files.

### Step 3: Generate commit message options

Produce **3 candidate commit messages** with different levels of detail:

1. **Short & sweet** — One line, type + scope + summary
2. **Standard** — Type + scope + summary + body with bullet points
3. **Detailed** — Full conventional commit with body and footer

Format each candidate following this structure:

```
<type>(<scope>): <subject>

<body>

<footer>
```

Rules:
- Subject line: max 72 characters, imperative mood, no period at end
- Body: wrap at 72 characters, explain WHAT and WHY (not HOW)
- Use bullet points (`- `) for multiple changes in the body
- Include `BREAKING CHANGE:` in footer if applicable
- Reference issue numbers if detected in changes (e.g., `Refs #123`)

### Step 4: Present to the user

Show all 3 candidates clearly numbered. Ask the user to pick one or
customize. If the user picks one, offer to run `git commit` with it
directly.

## Inputs

This skill operates on the current git repository. It reads:
- `git diff --cached` (staged changes)
- `git diff` (working changes, fallback)
- `git log --oneline -5` (recent history for style reference)

## Outputs

Three formatted commit message candidates following Conventional Commits.

## Example

**Input** (git diff):
```diff
diff --git a/src/auth/login.ts b/src/auth/login.ts
index abc123..def456 100644
--- a/src/auth/login.ts
+++ b/src/auth/login.ts
@@ -10,6 +10,8 @@ export async function login(username, password) {
+  if (!password || password.length < 8) {
+    throw new Error("Password must be at least 8 characters");
+  }
   const user = await findUser(username);
```

**Output** (Candidate 2 - Standard):
```
fix(auth): add password length validation

- Enforce minimum 8-character password requirement
- Throw descriptive error when validation fails
- Prevents weak passwords from being accepted

Closes #42
```
