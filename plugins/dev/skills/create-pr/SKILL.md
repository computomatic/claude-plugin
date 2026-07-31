---
name: create-pr
description: Creates a GitHub PR for current work. Handles branch creation, committing, pushing, and PR creation.
argument-hint: "[optional: PR description]"
allowed-tools: Bash(git:*), Bash(gh pr create:*), Read, Glob
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
!`if git rev-parse --verify HEAD >/dev/null 2>&1; then git log --oneline -5; else echo "(no commits yet)"; fi`

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
2. Write a clear, concise commit message. The subject line follows the title conventions in Step 6
3. Commit the changes

### Step 4: Push to Remote

Push the branch to origin:
```
git push -u origin <branch-name>
```

### Step 5: Check for PR Template

Search for a PR template in the repository. Check these locations in priority order (filenames are case-insensitive):

1. `.github/pull_request_template.md`
2. `.github/PULL_REQUEST_TEMPLATE/` (directory with multiple templates)
3. `docs/pull_request_template.md`
4. `pull_request_template.md` (repository root)

Use `Glob` to find matching files and `Read` to read the template content.

If `.github/PULL_REQUEST_TEMPLATE/` contains multiple templates, pick the one most relevant to the changes (e.g., a bug fix template for fixes, a feature template for new features). If unsure which template fits, ask the user.

### Step 6: Create the PR

Create the PR using `gh pr create`. If an argument was provided, use it to inform the description.

**Title:** Write a capitalized imperative sentence saying what the change does, such as `Add init-worktrees skill` or `Handle empty repo in create-pr`. Do not reach for a conventional-commit prefix like `feat:`, `chore:`, or `docs(create-pr):`. The parenthesised scope only repeats what the changed paths already show, and the type is frequently wrong: a prompt, skill definition, or config file is the implementation of a behavior rather than documentation describing it, so `docs` gets attached to changes that are nothing of the kind. The one exception is a repository whose own history uses prefixes, which the recent commits listed above will show; there, match the convention already in use.

Where the repository squash-merges, the PR title becomes the commit subject on the default branch, so the title and the commit subject from Step 3 should agree.

**If a PR template was found:** Use the template's structure for the body, filling in each section based on the conversation context and the changes being submitted.

**If no PR template was found:** Write a plain text body of 1-2 paragraphs describing what and why. No headers, no "Test Plan" section, no markdown formatting in the body.

**Line breaks:** GitHub renders every newline inside a paragraph as a visible line break, unlike most markdown renderers. Never wrap prose to a column width. Write each paragraph, list item, table row, and heading as one continuous line however long it runs, and separate blocks with a blank line. This applies to the template case and the plain prose case alike.

Wrong, because the newline before `entry` renders as a break mid-sentence:

```
Closes #834. Give the service a way to remove a scalar
entry. Delete is table stakes for a key-value API.
```

Right:

```
Closes #834. Give the service a way to remove a scalar entry. Delete is table stakes for a key-value API.
```

```
gh pr create --title "..." --body "..."
```

## Guidelines

- Titles are capitalized imperative sentences, not conventional-commit prefixes, unless the repository's own history uses prefixes
- When a PR template is found, respect its structure and fill in all sections
- When no template is found, keep descriptions as plain prose (1-2 paragraphs) with no headers or sections
- Never break a line in the middle of a paragraph or list item; blank lines between blocks are the only line breaks GitHub renders as intended
- Focus on what changed and why, not how
- If multiple unrelated changes exist, only include those relevant to the conversation or argument
- Always push before creating the PR
- Never add a signature line like "Generated with Claude Code" or similar to the PR description
