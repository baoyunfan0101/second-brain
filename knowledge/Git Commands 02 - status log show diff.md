---
tags:
  - software
  - cli
  - git
summary: git status, git log, git show, git diff
use_cases:
  - development
---

# Git Commands 02 - status log show diff

## Inspect

> [!info] Pager
> Some Git commands may open output in a pager when the result is long.
> 
> Press `q` to quit the pager and return to the terminal.

### git status

`git status` shows the current state of the working directory and staging area.

**Common parameters:**

| Parameter | Meaning |
|---|---|
| `-s` / `--short` | Show status in short format |
| `-b` / `--branch` | Show branch information |
| `-- <path>` | Show status for a specific file or directory |

**Common commands:**

```bash
# Show full repository status
git status

# Show short status with branch information
git status -sb

# Show status for a specific file
git status -- README.md
```

> [!note] Short Status:
> 
> **Short status format:**
> ```text
> XY <file>
> ```
> 
> | Position | Meaning |
> |---|---|
> | `X` | Staged file status: staging area vs. last commit |
> | `Y` | Unstaged file status: working directory vs. staging area |
> 
> **Common status symbols:**
> 
> | Symbol | Meaning |
> |---|---|
> | space | No change in this position |
> | `?` | Untracked |
> | `M` | Modified |
> | `A` | Added |
> | `D` | Deleted |
> | `R` | Renamed |
> | `U` | Unmerged |
> 
> **Example output:**
> 
> ```text
> ?? temp.txt
>  M README.md
> M  src/App.java
> MM package.json
> A  Main.java
> D  old.txt
> R  old-name.txt -> new-name.txt
> UU conflict.txt
> ```
> 
> **Example explanation:**
> 
> - `?? temp.txt`: untracked.
> - ` M README.md`: modified but not staged.
> - `M  src/App.java`: modified and staged.
> - `MM package.json`: modified and staged, then modified again.
> - `A  Main.java`: added and staged.
> - `D  old.txt`: deleted and staged.
> - `R  old-name.txt -> new-name.txt`: renamed and staged.
> - `UU conflict.txt`: unmerged conflict (appears during merge/rebase/cherry-pick/pull).

### git log

`git log` shows commit history.

**Common parameters:**

| Parameter | Meaning |
|---|---|
| `[<commit-id>]` | Show commit history from a specific commit (`HEAD` by default) |
| `--oneline` | Show each commit in one line |
| `-<number>` | Limit the number of commits shown |
| `--graph` | Show branch and merge structure |
| `--all` | Show commits from all branches |
| `--author="<author>"` | Show commits by a specific author |
| `--since="<date>"` / `--until="<date>"` | Show commits within a time range |
| `--grep="<keyword>"` | Search commit messages |
| `-- <path>` | Show commits related to a specific file or directory |
| `<commit-1>..<commit-2>` | Show commits reachable from `<commit-2>` but not from `<commit-1>` |

**Common commands:**

```bash
# Show commit history from HEAD or a specific commit
git log
git log HEAD~5

# Show compact commit history
git log --oneline

# Show the latest 5 commits
git log -5 --oneline

# Show commit history with branch structure from all branches
git log --oneline --graph --all

# Show commits by a specific author
git log --author="Yunfan Bao"

# Show commits within a specific time range
git log --since="2026-01-01" --until="2026-01-31"

# Search commit messages
git log --grep="login"

# Show commits that changed a specific file
git log -- README.md

# Show commits in feature/login but not in main
git log main..feature/login --oneline
```

### git show

`git show` displays details of a Git object.

For commits, it shows the commit metadata, the commit message, and the changes introduced by that commit.

**Common parameters:**

| Parameter | Meaning |
|---|---|
| `[<object>]` | Show a specific Git object (`HEAD` by default) |
| `--stat` | Show changed files with summary statistics |
| `--name-only` | Show only the names of changed files |
| `--name-status` | Show changed file names with change status |
| `--oneline` | Show commit summary in one line |
| `--no-patch` | Show commit metadata without file diff |
| `-- <path>` | Show changes related to a specific file or directory |

**Common commands:**

```bash
# Show HEAD or a specific Git object
git show
git show HEAD~1

# Show commit summary and file change statistics
git show --stat HEAD~1

# Show only changed file names
git show --name-only HEAD~1

# Show changed file names with status
git show --name-status HEAD~1

# Show commit summary in one line
git show --oneline HEAD~1

# Show commit metadata without diff
git show --no-patch HEAD~1

# Show changes to a specific file in a commit
git show HEAD~1 -- README.md
```

### git diff

`git diff` shows differences between versions.

**Common parameters:**

| Parameter                     | Meaning                                                                        |
| ----------------------------- | ------------------------------------------------------------------------------ |
| `[<commit-id>]`               | Compare the working directory with a specific commit (staging area by default) |
| `<commit-id-1> <commit-id-2>` | Compare two commits                                                            |
| `--staged` / `--cached`       | Show staged changes                                                            |
| `--stat`                      | Show changed files with summary statistics                                     |
| `--name-only`                 | Show only the names of changed files                                           |
| `--name-status`               | Show changed file names with change status                                     |
| `--word-diff`                 | Show word-level differences                                                    |
| `--check`                     | Check whitespace errors before committing                                      |
| `-- <path>`                   | Show differences for a specific file or directory                              |

**Common commands:**

```bash
# Show unstaged changes or all changes against HEAD
git diff
git diff HEAD

# Show staged changes
git diff --staged

# Compare two commits
git diff HEAD~1 HEAD

# Show file change statistics
git diff --stat

# Show only changed file names
git diff --name-only

# Show changed file names with status
git diff --name-status

# Show word-level differences
git diff --word-diff

# Check whitespace errors
git diff --check

# Show differences for a specific file
git diff -- README.md

# Show differences for a specific file between two commits
git diff HEAD~1 HEAD -- README.md
```
