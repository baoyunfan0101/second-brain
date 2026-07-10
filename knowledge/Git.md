---
tags:
  - software
  - cli
summary: Git concepts and commands
use_cases:
  - development
---

# Git

## Part 1: Core Concepts

### What Is Git?

Git is a distributed version control system.

Git helps with three things:

- Recording project changes as version history.
- Moving safely between different versions of the project.
- Supporting parallel development through branches and remote repositories.

### Git vs Git Hosting Platforms

Git is the version control tool.

GitHub, GitLab, Bitbucket, and Gitee are platforms that host Git repositories online.

**Local operations:**

```bash
git log
git status
git diff
git commit
git branch
```

**Remote synchronization operations:**

```bash
git fetch
git pull
git push
```

### Repository

A repository is a project managed by Git.

A Git repository contains:

- Project files.
- A hidden `.git` directory: Git's internal information, including commit history, branches, tags, remote repository configuration, and local Git settings.

### Commit

A commit is a saved version of the project.

**Commit object parts:**

| Part        | Role                                | Format                                            |
| ----------- | ----------------------------------- | ------------------------------------------------- |
| `tree`      | Points to the project snapshot      | `tree <tree-object-id>`                           |
| `parent`    | Links to previous commit(s)         | `parent <commit-id>`                              |
| `author`    | Records the original change writer  | `author <name> <email> <timestamp> <timezone>`    |
| `committer` | Records the commit object creator   | `committer <name> <email> <timestamp> <timezone>` |
| `message`   | Describes the reason for the change | Free-form text after a blank line                 |

**Commit ID:** Hash of the commit object content.

**Commit Types:**

```text
root commit = no parent
normal commit = one parent
merge commit = multiple parents
```

### Branch

A branch is a movable pointer to a commit.

**Example:**

```text
main:    A---B---C
                  \
feature:           D---E
```

Here:

- `main` points to commit `C`.
- `feature` points to commit `E`.
- `feature` was created from `main`.

**Common branch names:**

```text
main = stable main branch
develop = integration branch for ongoing development
feature/<name> = branch for a new feature
bugfix/<name> = branch for a normal bug fix
hotfix/<name> = branch for an urgent production fix
```

### HEAD

`HEAD` represents your current position in the repository.

`HEAD` usually points to the current branch; in detached HEAD state, it points directly to a specific commit.

### Remote Repository

A remote repository is another Git repository stored outside your local machine.

Default remote name: `origin`

## Part 2: Local File Model

### Working Directory, Staging Area, and Local Repository

Git organizes local changes through three areas:

```text
Working Directory -> Staging Area -> Local Repository
```

| Area              | Stores                               | Updated by    |
| ----------------- | ------------------------------------ | ------------- |
| Working Directory | Current file edits                   | Editing files |
| Staging Area      | Changes prepared for the next commit | `git add`     |
| Local Repository  | Committed project history            | `git commit`  |

### File States

**File states in the working directory:**

| State     | Meaning                                        |
| --------- | ---------------------------------------------- |
| Untracked | File seen but not managed by Git               |
| Modified  | Tracked file has unstaged changes              |
| Staged    | File changes are added to the staging area     |
| Committed | File changes are saved in the local repository |
| Ignored   | File is intentionally ignored by Git           |

### .gitignore

`.gitignore` tells Git which files should not be tracked.

It affects untracked files only.

If a file is already tracked by Git, adding it to `.gitignore` does not stop Git from tracking it.

**`.gitignore` syntax:**

| Pattern         | Meaning                                                      |
| --------------- | ------------------------------------------------------------ |
| `# comment`     | Comment line                                                 |
| `file.txt`      | Ignore any `file.txt` in any directory                       |
| `/file.txt`     | Ignore `file.txt` only at the repository root                |
| `logs/`         | Ignore directories named `logs`                              |
| `*.log`         | Ignore all files ending with `.log`                          |
| `temp?.txt`     | Ignore `temp1.txt`, `tempa.txt`, etc.                        |
| `build/**`      | Ignore everything under `build/` recursively                 |
| `docs/**/*.pdf` | Ignore all `.pdf` files under `docs/` at any depth           |
| `!keep.log`     | Re-include `keep.log` if its parent directory is not ignored |
| `\#file.txt`    | Ignore a file literally named `#file.txt`                    |

**Example:**

```gitignore
# Ignore build outputs
target/
build/
dist/

# Ignore logs
*.log
!important.log

# Ignore environment files
.env
.env.*

# Ignore IDE and OS files
.idea/
.vscode/
.DS_Store

# Ignore temporary files
*.tmp
temp?.txt
```

## Part 3: Basic Setup

### git --version

`git --version` checks the installed Git version.

**Common commands:**

```bash
git --version
```

### git config

`git config` manages Git configuration.

**Common parameters:**

| Parameter       | Meaning                                        |
| --------------- | ---------------------------------------------- |
| `--system`      | Use machine-level configuration                |
| `--global`      | Use current-user configuration                 |
| `--local`       | Use current-repository configuration           |
| `--list`        | Show configuration values                      |
| `--show-origin` | Show where each configuration value comes from |

**Common commands:**

```bash
# Set personal identity
git config --global user.name "<your-name>"
git config --global user.email "<your-email>"

# Read effective configuration
git config --list
git config --list --show-origin
git config user.name
git config user.email

# Read configuration from a specific level
git config --global --list
git config --global user.name
git config --global user.email

# Set common defaults
git config --global init.defaultBranch <branch-name>
git config --global core.editor "<editor-command>"  # Example: "code --wait"
```

### git init

`git init` creates a new Git repository in the current directory.

It adds a hidden `.git` directory.

**Common parameters:**

| Parameter                        | Meaning                     |
| -------------------------------- | --------------------------- |
| `--initial-branch=<branch-name>` | Set the initial branch name |

**Common commands:**

```bash
# Initialize the current directory as a Git repository
git init

# Initialize with a specific initial branch name
git init --initial-branch=<branch-name>
```

### git clone

`git clone` copies a remote repository to your local machine.

By default, it creates a new directory under the current directory.

**Common commands:**

```bash
# Clone into a directory named after the remote repository
git clone <repository-url>

# Clone into a custom local directory
git clone <repository-url> <directory-name>

# Clone into the current directory
git clone <repository-url> .
```

After cloning, the new local repository contains project files, commit history, and a remote named `origin`.

## Part 4: Inspecting Repository State

> [!info] Pager
> Some Git commands may open output in a pager when the result is long.
> 
> Press `q` to quit the pager and return to the terminal.

### git status

`git status` shows the current state of the working directory and staging area.

**Common parameters:**

| Parameter   | Meaning                                      |
| ----------- | -------------------------------------------- |
| `-s`        | Show status in short format                  |
| `--short`   | Same as `-s`                                 |
| `--branch`  | Show branch information                      |
| `-- <path>` | Show status for a specific file or directory |

**Common commands:**

```bash
# Show full repository status
git status

# Show short status
git status -s

# Show status with branch information
git status --branch

# Show status for a specific path
git status -- <path>
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

| Parameter                | Meaning                                                            |
| ------------------------ | ------------------------------------------------------------------ |
| `--oneline`              | Show each commit in one line                                       |
| `--graph`                | Show branch and merge structure                                    |
| `--all`                  | Show commits from all branches                                     |
| `-<number>`              | Limit the number of commits shown                                  |
| `--author="<author>"`    | Show commits by a specific author                                  |
| `--since="<date>"`       | Show commits after a specific date                                 |
| `--until="<date>"`       | Show commits before a specific date                                |
| `--grep="<keyword>"`     | Search commit messages                                             |
| `-- <path>`              | Show commits related to a specific file or directory               |
| `<commit-1>..<commit-2>` | Show commits reachable from `<commit-2>` but not from `<commit-1>` |

**Common commands:**

```bash
# Show full commit history
git log

# Show full commit history without opening a pager
git --no-pager log

# Show compact commit history
git log --oneline

# Show commit history with branch structure
git log --oneline --graph --all

# Show the latest <number> commits
git log -<number> --oneline

# Show commits by <author>
git log --author="<author>"

# Show commits within a time range
git log --since="<date>" --until="<date>"

# Search commit messages
git log --grep="<keyword>"

# Show commits that changed <path>
git log -- <path>

# Show commits in <commit-2> but not in <commit-1>
git log <commit-1>..<commit-2> --oneline
```

### git show

`git show` displays details of a Git object.

For commits, it shows commit metadata, commit message, and the changes introduced by that commit.

**Common parameters:**

| Parameter       | Meaning                                              |
| --------------- | ---------------------------------------------------- |
| `--stat`        | Show changed files with summary statistics           |
| `--name-only`   | Show only the names of changed files                 |
| `--name-status` | Show changed file names with change status           |
| `--oneline`     | Show commit summary in one line                      |
| `--no-patch`    | Show commit metadata without file diff               |
| `-- <path>`     | Show changes related to a specific file or directory |

**Common commands:**

```bash
# Show the current commit
git show

# Show the current commit
git show HEAD

# Show a specific commit
git show <commit-id>

# Show commit summary and file change statistics
git show --stat <commit-id>

# Show only changed file names
git show --name-only <commit-id>

# Show changed file names with status
git show --name-status <commit-id>

# Show commit metadata without diff
git show --no-patch <commit-id>

# Show changes to a specific path in a commit
git show <commit-id> -- <path>
```

### git diff

`git diff` shows differences between versions.

By default, it shows unstaged changes in the working directory.

**Common parameters:**

| Parameter       | Meaning                                           |
| --------------- | ------------------------------------------------- |
| `--staged`      | Show staged changes                               |
| `--cached`      | Same as `--staged`                                |
| `--stat`        | Show changed files with summary statistics        |
| `--name-only`   | Show only the names of changed files              |
| `--name-status` | Show changed file names with change status        |
| `--word-diff`   | Show word-level differences                       |
| `--check`       | Check whitespace errors before committing         |
| `-- <path>`     | Show differences for a specific file or directory |

**Common commands:**

```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged

# Show all uncommitted changes against the latest commit
git diff HEAD

# Show differences between two commits
git diff <commit-id-1> <commit-id-2>

# Show differences between two branches
git diff <branch-1> <branch-2>

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

# Show differences for a specific path
git diff -- <path>

# Show differences for a specific path between two commits
git diff <commit-id-1> <commit-id-2> -- <path>
```

## Part 5: Staging and Committing

### git add

`git add` moves changes from the working directory to the staging area.

**Common parameters:**

| Parameter          | Meaning                                                                       |
| ------------------ | ----------------------------------------------------------------------------- |
| `-A` / `--all`     | Add all changes in the repository, including new, modified, and deleted files |
| `-u` / `--update`  | Add changes to tracked files only                                             |
| `-p` / `--patch`   | Select parts of changes interactively                                         |
| `-n` / `--dry-run` | Show what would be added without actually adding it                           |
| `-f` / `--force`   | Add ignored files by force                                                    |

**Common commands:**

```bash
# Add a specific file
git add <file>

# Add a specific directory
git add <directory>

# Add changes under the current directory
git add .

# Add all changes in the repository
git add -A

# Add changes to tracked files only
git add -u

# Select parts of changes interactively
git add -p

# Preview what would be added
git add -n .

# Add an ignored file by force
git add -f <file>
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
| `--no-edit`      | Reuse the existing commit message                                                                                |
| `--allow-empty`  | Create a commit without file changes                                                                             |

**Common commands:**

```bash
# Commit staged changes
git commit -m "<message>"

# Stage tracked file changes and commit
git commit -am "<message>"

# Add staged changes to the latest commit
git commit --amend

# Change the latest commit message
git commit --amend -m "<message>"

# Amend the latest commit without changing its message
git commit --amend --no-edit

# Create an empty commit
git commit --allow-empty -m "<message>"
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

## Part 6: Undo and Recovery

### git restore

`git restore` restores files in the working directory or staging area.

It discards changes from the working directory, moves changes from the staging area back to the working directory, or copies files from a specific commit to the working directory.

**Common parameters:**

| Parameter              | Meaning                               |
| ---------------------- | ------------------------------------- |
| `--staged`             | Restore the staging area              |
| `--worktree`           | Restore the working directory         |
| `--source=<commit-id>` | Restore from a specific commit        |
| `--patch`              | Select parts of changes interactively |
| `-- <path>`            | Separate path from command parameters |

**Common commands:**

```bash
# Discard unstaged changes in a file
git restore <path>

# Unstage a file but keep the working directory changes
git restore --staged <path>

# Restore a file from a specific commit
git restore --source=<commit-id> <path>

# Restore both staging area and working directory from a specific commit
git restore --source=<commit-id> --staged --worktree <path>

# Interactively choose changes to restore
git restore --patch <path>
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

`git reset` moves the current branch pointer.

It updates the staging area and working directory, and commits after the target commit become unreachable from the current branch.

**Common parameters:**

| Parameter     | Meaning                                                                       |
| ------------- | ----------------------------------------------------------------------------- |
| `--soft`      | Move branch only; keep changes in the staging area                            |
| `--mixed`     | Move branch and reset the staging area; keep changes in the working directory |
| `--hard`      | Move branch and reset both the staging area and working directory             |
| `<commit-id>` | Reset to a specific commit                                                    |
| `HEAD~<n>`    | Reset to the commit before `HEAD` by `<n>` steps                              |
| `-- <path>`   | Reset changes for a specific file or directory                                |

**Common commands:**

```bash
# Undo the latest commit but keep changes staged
git reset --soft HEAD~1

# Undo the latest commit and keep changes unstaged
git reset --mixed HEAD~1

# Undo the latest commit and discard changes
git reset --hard HEAD~1

# Reset to a specific commit
git reset --hard <commit-id>

# Unstage a specific file
git reset -- <path>
```

### git revert

`git revert` creates a new commit that undoes an old commit.

It does not delete the original commit or rewrite commit history; it adds a new commit that applies the inverse changes of the target commit.

If later commits changed or depend on the same content, the revert may cause conflicts or require manual review.

**Common parameters:**

| Parameter            | Meaning                                                        |
| -------------------- | -------------------------------------------------------------- |
| `--no-edit`          | Use the default revert commit message                          |
| `--edit`             | Open the editor to modify the revert commit message            |
| `--no-commit`        | Apply the revert changes without creating a commit immediately |
| `-m <parent-number>` | Choose the mainline parent when reverting a merge commit       |
| `--continue`         | Continue after resolving revert conflicts                      |
| `--abort`            | Abort the revert operation                                     |

**Common commands:**

```bash
# Revert a specific commit
git revert <commit-id>

# Revert a commit and use the default commit message
git revert --no-edit <commit-id>

# Revert a commit and edit the revert commit message
git revert --edit <commit-id>

# Apply revert changes without committing immediately
git revert --no-commit <commit-id>

# Revert a merge commit by choosing the mainline parent
git revert -m <parent-number> <commit-id>

# Continue after resolving revert conflicts
git revert --continue

# Abort the revert operation
git revert --abort
```

### git clean

`git clean` removes untracked files from the working directory.

**Common parameters:**

| Parameter      | Meaning                                   |
| -------------- | ----------------------------------------- |
| `-n`           | Preview files that would be removed       |
| `-f`           | Force removal of untracked files          |
| `-d`           | Include untracked directories             |
| `-i`           | Remove files interactively                |
| `-x`           | Include ignored files                     |
| `-X`           | Remove only ignored files                 |
| `-e <pattern>` | Exclude files matching a specific pattern |
| `-- <path>`    | Limit cleaning to a specific path         |

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

# Remove untracked files interactively
git clean -i

# Remove untracked files, including ignored files
git clean -fx

# Remove only ignored files
git clean -fX

# Remove untracked files except files matching a pattern
git clean -f -e <pattern>

# Remove untracked files under a specific path
git clean -f -- <path>
```

## Part 7: Branching and Integration

### git branch

`git branch` manages branches.

**Common parameters:**

| Parameter                                  | Meaning                                                                                                |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `-a`                                       | Show local and remote-tracking branches                                                                |
| `-r`                                       | Show remote-tracking branches only                                                                     |
| `-v`                                       | Show each branch with its latest commit                                                                |
| `-vv`                                      | Show each branch with its upstream branch and ahead/behind status                                      |
| `--show-current`                           | Show the current branch name                                                                           |
| `-d <branch-name>`                         | Delete a merged branch (merged into its upstream branch, or into current `HEAD` if no upstream is set) |
| `-D <branch-name>`                         | Force delete a branch                                                                                  |
| `-m [<old-branch-name>] <new-branch-name>` | Rename a specific branch (rename current branch if old name is omitted)                                |
| `--merged [<commit-id>]`                   | Show branches already merged into a specific commit (`HEAD` is used by default)                        |
| `--no-merged [<commit-id>]`                | Show branches not yet merged into a specific commit (`HEAD` is used by default)                        |

**Common commands:**

```bash
# Show local branches
git branch

# Show local and remote-tracking branches
git branch -a

# Show remote-tracking branches only
git branch -r

# Show branches with latest commit
git branch -v

# Show branches with upstream tracking details
git branch -vv

# Show the current branch name
git branch --show-current

# Create a new branch
git branch <branch-name>

# Delete a merged branch
git branch -d <branch-name>

# Force delete a branch
git branch -D <branch-name>

# Rename the current branch
git branch -m <new-branch-name>

# Rename a specific branch
git branch -m <old-branch-name> <new-branch-name>

# Show branches already merged into current branch
git branch --merged

# Show branches not yet merged into current branch
git branch --no-merged
```

### git switch

`git switch` switches branches.

It moves `HEAD` and updates the working directory accordingly.

Local changes are preserved if they do not conflict with the target branch; otherwise, Git stops the switch.

**Common parameters:**

| Parameter                       | Meaning                                                                                                 |
| ------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `-c <new-branch> [<commit-id>]` | Create a new branch from a specific commit and switch to it (`HEAD` is used by default)                 |
| `-C <new-branch> [<commit-id>]` | Create or reset a branch from a specific commit and switch to it (`HEAD` is used by default)            |
| `-`                             | Switch back to the previous branch                                                                      |
| `--detach [<commit-id>]`        | Switch directly to a specific commit without a branch (`HEAD` is used by default)                       |
| `--track <remote-branch>`       | Create a local branch tracking a remote one (uses the same name by default; fails if it already exists) |
| `--discard-changes`             | Discard conflicting local changes when switching                                                        |

**Common commands:**

```bash
# Switch to an existing branch
git switch <branch-name>

# Switch to the main branch
git switch main

# Create a new branch and switch to it
git switch -c <new-branch>

# Create a new branch from a specific commit and switch to it
git switch -c <new-branch> <commit-id>

# Create a local branch tracking a remote branch
git switch --track origin/<branch-name>

# Create a local branch with a specified name tracking a remote branch
git switch -c <local-branch-name> --track origin/<branch-name>

# Switch back to the previous branch
git switch -

# Switch directly to a specific commit
git switch --detach <commit-id>

# Force switch and discard conflicting local changes
git switch --discard-changes <branch-name>
```
