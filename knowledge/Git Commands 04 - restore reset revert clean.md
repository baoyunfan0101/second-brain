---
tags:
  - software
  - cli
  - git
summary: git restore, git reset, git revert, git clean
use_cases:
  - development
---

# Git Commands 04 - restore reset revert clean

## Recover

### git restore

`git restore` restores tracked files in the working directory or staging area.

Know more at `Git -> Git Behaviors -> Restore File Versions`.

**Common parameters:**

| Parameter              | Meaning                                                          |
| ---------------------- | ---------------------------------------------------------------- |
| `<path>`               | Restore a specific file or directory                             |
| `--source=<commit-id>` | Restore from a specific commit                                   |
| `--worktree`           | Restore the working directory (from the staging area by default) |
| `--staged`             | Restore the staging area (from `HEAD` by default)                |
| `-p` / `--patch`       | Interactively choose parts of changes to restore                 |

**Common commands:**

```bash
# Discard unstaged changes in a file
git restore README.md

# Unstage a file but keep working directory changes
git restore --staged README.md

# Restore a file from a specific commit
git restore --source=HEAD~1 README.md

# Restore both staging area and working directory from a specific commit
git restore --source=HEAD~1 --staged --worktree README.md

# Interactively choose parts of changes to restore
git restore -p README.md
```

> [!note] Patch mode
> 
> Git splits changes into hunks and asks whether each hunk should be discarded from the working directory:
> 
> ```text
> (<current>/<total>) Discard this hunk from worktree [y,n,q,a,d,e,p,?]?
> ```
> 
> **Common choices:**
> 
> | Choice | Meaning |
> |---|---|
> | `y` | Discard this hunk from the working directory |
> | `n` | Keep this hunk in the working directory |
> | `q` | Quit patch mode |
> | `a` | Discard this hunk and all remaining hunks in this file |
> | `d` | Keep this hunk and all remaining hunks in this file |
> | `e` | Manually edit this hunk |
> | `?` | Show help |

### git reset

`git reset` resets the current branch.

It may update tracked files in the staging area and the working directory, and commits after the target commit become unreachable from the current branch.

**Common parameters:**

| Parameter     | Meaning                                                                                     |
| ------------- | ------------------------------------------------------------------------------------------- |
| `--soft`      | Move branch only; keep the staging area and the working directory unchanged                 |
| `--mixed`     | Move branch and reset the staging area; keep the working directory unchanged (default mode) |
| `--hard`      | Move branch and reset both the staging area and the working directory                       |
| `<commit-id>` | Reset to a specific commit                                                                  |
| `HEAD~<n>`    | Reset to the commit before `HEAD` by `<n>` steps                                            |
| `-- <path>`   | Reset the staging area for a specific file or directory; branch unchanged                   |

**Common commands:**

```bash
# Undo the latest commit but keep changes staged
git reset --soft HEAD~1

# Undo the latest commit and keep changes unstaged
git reset --mixed HEAD~1

# Undo the latest commit and discard tracked changes
git reset --hard HEAD~1

# Reset to commit a1b2c3d and discard tracked changes
git reset --hard a1b2c3d

# Unstage a specific file
git reset -- README.md

# Unstage a specific directory
git reset -- src/
```

### git revert

`git revert` creates a new commit that applies the inverse changes of a specific commit.

If commits after the target commit modified the same content, the revert may cause conflicts or require manual review.

**Common parameters:**

| Parameter            | Meaning                                                                                              |
| -------------------- | ---------------------------------------------------------------------------------------------------- |
| `<commit-id>`        | Revert a specific commit                                                                             |
| `--no-edit`          | Use the default revert commit message                                                                |
| `--edit`             | Open the editor to modify the revert commit message                                                  |
| `--no-commit`        | Apply inverse changes without creating a commit immediately                                          |
| `-m <parent-number>` | Choose the mainline parent when reverting a merge commit (revert commits from the non-mainline side) |
| `--continue`         | Continue the current revert operation after resolving conflicts                                      |
| `--abort`            | Abort the current revert operation                                                                   |

**Common commands:**

```bash
# Revert commit a1b2c3d
git revert a1b2c3d

# Revert commit a1b2c3d without opening editor
git revert --no-edit a1b2c3d

# Revert commit a1b2c3d and edit the revert commit message
git revert --edit a1b2c3d

# Apply inverse changes from a1b2c3d without committing immediately
git revert --no-commit a1b2c3d

# Revert merge commit c7d8e9f by choosing parent 1 as mainline
git revert -m 1 c7d8e9f

# Continue after resolving revert conflicts
git revert --continue

# Abort the revert operation
git revert --abort
```

### git clean

`git clean` removes untracked files from the working directory.

**Common parameters:**

| Parameter      | Meaning                                             |
| -------------- | --------------------------------------------------- |
| `-n`           | Preview files and directories that would be removed |
| `-f`           | Actually remove untracked files                     |
| `-d`           | Include untracked directories                       |
| `-i`           | Choose untracked files to remove interactively      |
| `-x`           | Include ignored files                               |
| `-X`           | Remove ignored files only                           |
| `-e <pattern>` | Keep files matching a specific pattern              |
| `-- <path>`    | Limit cleaning to a specific file or directory      |

**Common commands:**

```bash
# Preview untracked files that would be removed
git clean -n

# Remove untracked files
git clean -f

# Preview untracked files and directories that would be removed
git clean -nd

# Remove untracked files and directories
git clean -fd

# Choose untracked files to remove interactively
git clean -i

# Remove untracked files and ignored files
git clean -fx

# Remove ignored files only
git clean -fX

# Remove untracked files but keep files matching *.log
git clean -f -e "*.log"

# Remove untracked files under build/
git clean -f -- build/
```
