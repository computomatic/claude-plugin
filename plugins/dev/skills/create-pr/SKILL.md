---
name: create-pr
description: Creates a GitHub PR for current work. Handles branch creation, committing, pushing, and PR creation.
argument-hint: [optional: PR description]
disable-model-invocation: true
allowed-tools: Bash(git *), Bash(gh pr create*)
---

# Create Pull Request

Creates a complete PR from current work: branch, commit, push, and open the PR.

## Git State

Status:
!`git status`

Changes:
!`git diff`

Current branch:
!`git branch --show-current`

Recent commits:
!`git log --oneline -5`

## Workflow

### Step 1: Check Branch State

Determine the current branch:
- If on `main` or `master`, create a new feature branch
- If already on a feature branch, continue on that branch

**Branch naming:** Generate from conversation context using format `{category}/{short-description}`:
- `feat/add-user-auth`
- `fix/login-validation`
- `refactor/extract-utils`

### Step 2: Identify Changes to Include

Review the conversation context and git diff:
- If an argument was provided, use it to determine which changes are in scope
- If no argument, infer scope from the conversation context
- Only stage files related to the intended PR scope
- Unrelated changes should NOT be staged

If uncertain about which files belong, ask the user.

### Step 3: Stage and Commit

1. Stage only the relevant files with `git add`
2. Write a clear, concise commit message
3. Commit the changes

### Step 4: Push to Remote

Push the branch to origin:
```
git push -u origin <branch-name>
```

### Step 5: Create the PR

Create the PR using `gh pr create`:
- Title: Clear, imperative summary
- Body: Plain text, 1-2 paragraphs describing what and why
- No headers, no "Test Plan" section, no markdown formatting in the body
- If argument was provided, use it to inform the description

```
gh pr create --title "..." --body "..."
```

## Guidelines

- Keep PR descriptions as plain prose (1-2 paragraphs)
- No headers or sections in the PR body
- Focus on what changed and why, not how
- If multiple unrelated changes exist, only include those relevant to the conversation or argument
- Always push before creating the PR
