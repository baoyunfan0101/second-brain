---
tags:
  - software
  - cli
  - git
summary: git add, git commit
use_cases:
  - development
---

# Git Commands 03 - add commit

## Stage and Commit

### git add

`git add` moves changes from the working directory to the staging area.

**Common parameters:**

| Parameter          | Meaning                                                                     |
| ------------------ | --------------------------------------------------------------------------- |
| `<path>`           | Add changes from a specific file or directory (`.` means current directory) |
| `-A` / `--all`     | Add all changes in the repository (not limited by the current directory)    |
| `-u` / `--update`  | Add modified and deleted tracked files                                      |
| `-p` / `--patch`   | Interactively choose parts of changes to add                                |
| `-n` / `--dry-run` | Show what would be added without actually adding it                         |
| `-f` / `--force`   | Add ignored files by force                                                  |

**Common commands:**

```bash
# Add changes from a specific file, directory, or the current directory
git add README.md
git add src/
git add .

# Add all changes in the repository
git add -A

# Add modified and deleted tracked files
git add -u

# Interactively choose parts of changes to add
git add -p

# Preview what would be added
git add -n .

# Add an ignored file by force
git add -f .env
```

> [!note] Patch mode
> 
> Git splits changes into hunks and asks whether each hunk should be staged:
> 
> ```text
> (<current>/<total>) Stage this hunk [y,n,q,a,d,j,J,g,/,e,p,?]?
> ```
> 
> **Common choices:**
> 
> | Choice | Meaning |
> |---|---|
> | `y` | Stage this hunk |
> | `n` | Do not stage this hunk |
> | `q` | Quit patch mode |
> | `a` | Stage this hunk and all remaining hunks in this file |
> | `d` | Do not stage this hunk or any remaining hunks in this file |
> | `e` | Manually edit this hunk |
> | `?` | Show help |

### git commit

`git commit` saves staged changes into local Git history.

A normal commit uses the current `HEAD` commit as its parent.

**Common parameters:**

| Parameter        | Meaning                                                                                                          |
| ---------------- | ---------------------------------------------------------------------------------------------------------------- |
| `-m "<message>"` | Use the given commit message                                                                                     |
| `-a`             | Automatically stage modified and deleted tracked files before committing (skip `git add` for tracked files only) |
| `--amend`        | Replace the latest commit with a new commit (commit ID changes)                                                  |
| `--no-edit`      | Reuse the existing commit message when amending                                                                  |
| `--allow-empty`  | Create a commit without file changes                                                                             |

**Common commands:**

```bash
# Commit staged changes with a message
git commit -m "feat(search): add keyword filter"

# Stage modified and deleted tracked files, then commit
git commit -am "fix(ui): correct button alignment"

# Add staged changes to the latest commit
git commit --amend

# Change the latest commit message
git commit --amend -m "docs(api): clarify request parameters"

# Amend the latest commit without changing its message
git commit --amend --no-edit

# Create an empty commit
git commit --allow-empty -m "chore(release): trigger deployment"
```

> [!note] Commit message convention
> 
> **Common commit message format:**
> 
> ```text
> <type>(<scope>): <description>
> ```
> 
> **Common types:**
> 
> | Type | Meaning |
> |---|---|
> | `feat` | New feature |
> | `fix` | Bug fix |
> | `docs` | Documentation |
> | `style` | Formatting only |
> | `refactor` | Code restructuring without behavior changes |
> | `test` | Test changes |
> | `chore` | Maintenance work |
> 
> **Examples:**
> 
> ```bash
> git commit -m "feat(auth): add login form"
> git commit -m "fix(api): handle null response"
> git commit -m "docs(readme): update setup guide"
> git commit -m "refactor(user): simplify validation logic"
> git commit -m "test(order): add payment failure case"
> ```
