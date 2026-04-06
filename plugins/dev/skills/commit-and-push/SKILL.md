---
name: commit-and-push
allowed-tools: Bash(git add:*), Bash(git commit:*), Bash(git push:*)
description: Commit and push changes
---

## Git State

Status:
!`git status`

Changes:
!`git diff`

Recent commits:
!`git log --oneline -5 2>/dev/null || echo "(no commits yet)"`

## Task

Review the conversation context above to understand what the user was working on. Then:

1. **Identify related changes**: From the diff, determine which files are related to the work discussed in the conversation. Unrelated changes (e.g., unfinished work on a different feature) should NOT be staged.

2. **Stage selectively**: Only `git add` files that are related to the conversation context. If all changes appear related, stage everything.

3. **Generate commit message**: Write a clear commit message describing what was accomplished.

4. **Commit and push**: Commit the staged changes and push to the remote branch.

If you're unsure whether certain files are related, ask the user before staging.
