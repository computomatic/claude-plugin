---
name: init-worktrees
description: Restructures a git project's directories so all worktrees (including main) are siblings under a common parent. Use when setting up a project for the sibling-worktree layout.
disable-model-invocation: true
allowed-tools: Bash(git:*), Bash(ls:*), Bash(mv:*), Bash(mkdir:*), Bash(cat:*), Bash(realpath:*), Bash(rmdir:*), Read, Glob, Grep
---

# Initialize Sibling Worktree Layout

Restructures a project so that all worktrees (including the main one) live as sibling directories under a common parent:

```
~/projects/my-repo/        # parent container (not a git repo)
  main/                    # primary worktree
  feature-branch/          # linked worktree
  bugfix/                  # linked worktree
```

## Current State

Current directory:
!`pwd`

Git root:
!`git rev-parse --show-toplevel`

Git common dir:
!`git rev-parse --git-common-dir`

Worktree list:
!`git worktree list`

.git type:
!`test -f .git && echo "file (linked worktree)" || echo "directory (main worktree)"`

## Workflow

### Step 1: Detect Scenario

Determine which scenario applies:

1. **Already structured** -- The parent directory of the current git root contains at least one sibling directory that is a worktree of the same repo (check sibling `.git` files pointing to the same git common dir). Report this and stop.
2. **Simple clone** -- `.git` is a directory and `git worktree list` shows only one entry. Needs wrapping in a parent directory.
3. **Clone with external worktrees** -- `.git` is a directory and `git worktree list` shows multiple entries. Needs wrapping plus relocation of linked worktrees.

If the current directory is a linked worktree (`.git` is a file), resolve the main worktree location from the git common dir and assess from there.

### Step 2: Confirm with User

Present the detected scenario and the planned directory moves. For scenarios 2 and 3, list every move that will happen. Warn about:
- The current shell's working directory becoming invalid after the move
- IDE/editor workspace paths needing manual update
- Submodules (if `.gitmodules` exists) potentially needing re-initialization

Ask the user to confirm before proceeding. For scenario 1, just report and stop.

### Step 3: Execute (Simple Clone)

Use absolute paths for all operations -- CWD becomes invalid after the first `mv`.

```bash
REPO_DIR=$(git rev-parse --show-toplevel)
REPO_NAME=$(basename "$REPO_DIR")
PARENT_DIR=$(dirname "$REPO_DIR")
TEMP_NAME="${REPO_NAME}__init_worktrees_tmp"

mv "$REPO_DIR" "$PARENT_DIR/$TEMP_NAME"
mkdir -p "$PARENT_DIR/$REPO_NAME"
mv "$PARENT_DIR/$TEMP_NAME" "$PARENT_DIR/$REPO_NAME/main"
```

No internal git references need updating since there are no linked worktrees. Skip to Step 5.

### Step 4: Execute (Clone with External Worktrees)

**Phase A: Record worktree info.** Parse `git worktree list --porcelain` to collect each worktree's absolute path and branch. The first entry is always the main worktree.

**Phase B: Move the main worktree.** Same as Step 3 -- move via temp name into `<parent>/<repo-name>/main`.

**Phase C: Fix linked worktree references.** After moving main, each linked worktree's `.git` file contains a `gitdir:` path pointing to the old main location. Update each one:

```bash
# For each linked worktree at $LINKED_PATH:
OLD_GITDIR=$(cat "$LINKED_PATH/.git" | sed 's/gitdir: //')
NEW_GITDIR=$(echo "$OLD_GITDIR" | sed "s|$OLD_REPO_DIR/.git|$NEW_MAIN_DIR/.git|")
echo "gitdir: $NEW_GITDIR" > "$LINKED_PATH/.git"
```

**Phase D: Move linked worktrees.** From the new main directory, use `git worktree move` for each linked worktree:

```bash
cd "$NEW_MAIN_DIR"
git worktree move "$OLD_LINKED_PATH" "$NEW_PARENT/$DIRNAME"
```

Directory names use the final segment of the branch name (e.g., `feat/login` becomes `login`). Append a numeric suffix if the name collides with an existing directory.

**Phase E: Clean up.** Remove empty directories left behind by the moves using `rmdir` (non-recursive, safe to fail).

### Step 5: Verify

From the new main directory:

```bash
cd "$NEW_MAIN_DIR"
git worktree list
git status
```

For each linked worktree, verify `git status` works from within it.

### Step 6: Report

Tell the user:
- The new directory structure (list all worktree paths)
- How to navigate to main: `cd <new-main-path>`
- Reminder to update IDE/editor workspace settings pointing to the old location
- That `/create-worktree` will now place new worktrees as siblings automatically

## Guidelines

- Always confirm with the user before making changes.
- Use absolute paths for all operations after the first move.
- If any `mv` or `git worktree move` command fails, stop and report the error. Do not attempt partial rollback.
- Directory names for linked worktrees use the final segment of the branch name. Append a numeric suffix on collision.
- Never run from inside a linked worktree -- resolve to the main worktree first.
