---
name: create-worktree
description: Creates a new git worktree for parallel development. Use when the user wants to work on something in a separate worktree, start parallel work, or isolate changes.
argument-hint: "[optional: branch name or description of work]"
allowed-tools: Bash(git:*), Bash(ls:*), Read, Glob, Grep
---

# Create Worktree

Creates a git worktree so the user can work on a separate branch in parallel without disturbing their current working directory.

## Current State

Current directory:
!`pwd`

Current branch:
!`git branch --show-current`

Worktree list:
!`git worktree list`

Git root:
!`git rev-parse --show-toplevel`

Git common dir:
!`git rev-parse --git-common-dir`

## Workflow

### Step 1: Determine the Branch

**Check for project branch naming conventions** by scanning CLAUDE.md, CONTRIBUTING.md, or similar project docs for branch naming policies. If a convention exists, follow it. If not, use bare `<short-name>` without prefix segments (no `feat/`, `fix/`, etc.).

**Determine the branch based on current state:**

- **On `main`/`master`, or already in a worktree:** Infer the scope of work from conversation context or the provided argument. Create a new branch name from that. Do not ask the user unless the scope is genuinely unclear.
- **On a non-main branch, not in a worktree:** Ask the user:
  1. Whether to move this branch to the worktree, or create a new branch
  2. Whether to branch off the current branch or off main

If the user provided an argument, use it to determine or influence the branch name.

### Step 2: Determine the Worktree Location

Determine the parent directory for the worktree:

- **If already in a worktree:** Place the new worktree in the same parent directory as the current worktree (a sibling directory).
- **If in the primary clone directory:** Place it at `<repo-parent>/worktrees/<dir-name>`, where `<repo-parent>` is the directory containing the repository root.

**Directory naming rules:**
- If the branch name is a single segment (e.g., `implement-login`), use it as the directory name
- If the branch name has a prefix segment (e.g., `feat/implement-login`), use only the final segment (`implement-login`)
- If the directory already exists, append a numeric suffix (e.g., `implement-login-2`)

Before creating, verify the target directory does not already exist with `ls`.

### Step 3: Create the Worktree

1. Create the worktree with:
   ```
   git worktree add <path> -b <branch-name>
   ```
   Or if the branch already exists:
   ```
   git worktree add <path> <branch-name>
   ```
   `git worktree add` creates intermediate directories automatically.

2. Verify creation:
   ```
   git worktree list
   ```

### Step 4: Report to User

Tell the user:
- The full path to the new worktree
- The branch name
- How to navigate to it (e.g., `cd <path>`)
- Remind them they can open it in a new editor window

## Guidelines

- Prefer acting autonomously over asking questions. Only ask when genuinely ambiguous.
- Multiple projects may share the same `worktrees/` directory -- this is expected.
- The worktree directory name and branch name may differ (e.g., branch `feat/login` in directory `worktrees/login`).
- Never create a worktree inside the current repository.
