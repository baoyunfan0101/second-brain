---
tags:
  - software
  - cli
summary: Git concepts and commands
use_cases:
  - development
---

# Git

## Core Concepts

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
A───B───C main
         \
          D───E feature
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

## Local File Model

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

## Git Behaviors

### Restore File Versions

Restoring copies file versions from a source (the staging area or a commit) to the staging area, the working directory, or both:

```text
staging area / current commit / specific commit
-> working directory / staging area / both
```

**Restoring Logic:**

```python
if file is tracked:

    # Restore to Working Directory
    if target is working directory:

        staging area = no changes
        working directory = source  # discard unstaged changes

    # Restore to Staging Area
    elif target is staging area:

        staging area = source  # discard staged changes
        working directory = no changes

    # Restore to Staging Area and Working Directory
    elif target is staging area and working directory:

        staging area = source  # discard staged changes
        working directory = source  # discard unstaged changes

# Untracked File
elif file is untracked:

    if source has a tracked file at the same path and this path is restored:
        working directory = source  # overwritten

    else:
        working directory = keep untracked file  # no changes

# Ignored (Untracked) File
elif file is ignored (and untracked):

    if source has a tracked file at the same path and this path is restored:
        working directory = source  # overwritten

    else:
        working directory = keep ignored file  # no changes
```

**Related Commands:**

```bash
git restore <path>
git restore --staged <path>
git restore --source=<commit-id> <path>
git restore --source=<commit-id> --staged --worktree <path>
git checkout [<commit-id>] -- <path>
git reset <commit-id> -- <path>
```

**Practical Rule:**

```text
Restoring overwrites the target area with the source content.
Uncommitted changes are lost.
```

### Switch Branches with Local Changes

Branch switching updates the current branch context:

```text
HEAD:
current branch -> target branch

staging area:
current commit + staged changes -> target commit + non-conflicting staged changes

working directory:
current commit + staged changes + unstaged changes -> target commit + non-conflicting staged changes + non-conflicting unstaged changes
```

**Switching Logic:**

```python
if file is tracked:

    # Committed File: The file has no local change.
    if working directory == staging area == current commit:

        staging area = target commit
        working directory = target commit

    # Modified File: The file has unstaged changes.
    elif working directory != staging area == current commit:

        if target commit == current commit:
            staging area = target commit  # no changes
            working directory = keep modified file  # no changes

        else:
            Git stops

    # Staged File: The file has staged changes.
    elif working directory == staging area != current commit:

        if target commit == current commit:
            staging area = keep staged file  # no changes
            working directory = keep staged file  # no changes

        elif staging area == target commit:
            staging area = target commit  # no changes
            working directory = target commit  # no changes

        else:
            Git stops

    # Staged and Modified File: The file has both staged and unstaged changes.
    elif working directory != staging area != current commit:

        if target commit == current commit:
            staging area = keep staged file  # no changes
            working directory = keep modified file  # no changes

        elif staging area == target commit:
            staging area = target commit  # no changes
            working directory = keep modified file  # no changes

        else:
            Git stops

elif file is untracked:

    if target commit has a tracked file at the same path:
        Git stops

    else:
        working directory = keep untracked file  # no changes

elif file is ignored (and untracked):

    if target commit has a tracked file at the same path:
        working directory = target commit  # overwritten

    else:
        working directory = keep ignored file  # no changes
```

**Related Commands:**

```bash
git switch <branch>
git switch -
git switch --detach <commit-id>
git checkout <branch>
git checkout -
git checkout <commit-id>
```

**Practical Rule:**

```python
if target commit == current commit:
    switch succeeds
    local changes are preserved

elif target commit == staging area:
    switch succeeds
    staged changes become clean
    unstaged changes are preserved

else:
    Git stops
```

### Merge Branches

Merging combines the changes introduced by the current and target branches (each branch tip compared with their common ancestor) onto the current branch.

This is also known as a three-way merge.

```text
base commit + current commit + target commit -> merge result

current branch:
current commit -> new merge commit

target branch:
unchanged

staging area:
current commit -> merge result

working directory:
current commit -> merge result
```

**Example:**

```bash
git switch current
git merge target
```

Before:

```text
     current changes
         (B → D)
            ↓
A───B───C───D  current branch
      \
       E───F  target branch
           ↑
     target changes
        (B → F)
```

After:

```text
         combined changes
        (B → D) + (B → F)
                ↓
A───B───C───D───M  current branch
     \         /
      E───────F  target branch
```

**Merging Logic:**

```python
if file is tracked:

    # Committed File: The file has no local change.
    if working directory == staging area == current commit:

        if current commit and target commit can be merged:
            staging area = merge result
            working directory = merge result

        else:
            # same path has 1/2/3 entries: base/current/target versions
            staging area = unmerged conflict state

            # file contains conflict markers
            working directory = conflict file

            Git stops

    # Modified File: The file has unstaged changes.
    elif working directory != staging area == current commit:

        if merge result == current commit:
            staging area = merge result
            working directory = keep modified file  # no changes

        else:
            Git stops

    # Staged File: The file has staged changes.
    elif working directory == staging area != current commit:

        if merge result == current commit:
            staging area = keep staged file
            working directory = keep staged file

        else:
            Git stops

    # Staged and Modified File: The file has both staged and unstaged changes.
    elif working directory != staging area != current commit:

        if merge result == current commit:
            staging area = keep staged file
            working directory = keep staged file

        else:
            Git stops

elif file is untracked:

    if merge result has a tracked file at the same path:
        Git stops

    else:
        working directory = keep untracked file  # no changes

elif file is ignored (and untracked):

    if merge result has a tracked file at the same path:
        working directory = merge result  # overwritten

    else:
        working directory = keep ignored file  # no changes
```

**Related Commands:**

```bash
git merge <branch>
git merge --no-ff <branch>
git pull --no-rebase
```

**Practical Rule:**

```text
clean tracked path:
    merge current commit and target commit from base commit

local changes:
    merge only if the merge result does not overwrite them

conflict:
    staging area = multiple versions in separate index entries
    working directory = conflict markers in a file
```

### Cherry-pick / Rebase Commits

Cherry-picking and rebasing replay the changes introduced by selected commits (each commit compared with its direct parent) onto a new base.

Other file-handling and conflict behaviors are the same as those of merging.

**Example 1:**

```bash
git switch destination
git cherry-pick E
```

Before:

```text
A───B───C  destination / HEAD
     \
      D───E  source
          ↑
       change E
       (D → E)
```

After:

```text
         change E
         (D → E)
            ↓
A───B───C───E'  destination / HEAD
     \
      D───E  source
```

**Example 2:**

```bash
git rebase --onto destination D source
```

Before:

```text
A───B───C  destination
     \
      D───E  source
          ↑
       change E
       (D → E)
```

After:

```text
         change E
         (D → E)
            ↓
A───B───C───E'  source / HEAD
     \  ↑
      \ destination
       \
        D───E  no branch reference
```

**Related Commands:**

```bash
git cherry-pick <commit-id>
git cherry-pick <start-commit-id>^..<end-commit-id>
git rebase <base-branch-name>
git rebase -i <commit-id>
git rebase --onto <new-base> <upstream> [<branch-name>]
git pull --rebase
```

## Git Commands

### git

`git` provides global options before a specific command.

**Common parameters:**

| Parameter           | Meaning                                       |
| ------------------- | --------------------------------------------- |
| `--version`         | Show Git version                              |
| `--help`            | Show Git help                                 |
| `-C <path>`         | Run Git as if started in a specific directory |
| `-c <name>=<value>` | Set a temporary config value for one command  |
| `-p`, `--paginate`  | Pipe output through a pager                   |
| `-P`, `--no-pager`  | Disable pager output                          |

**Common commands:**

```bash
# Show Git version
git --version

# Show Git help
git --help

# Show help for a specific command
git help log

# Show status as if running inside the parent directory
git -C .. status

# Use a temporary config value for one command
git -c color.ui=always status

# Show log without pager
git -P log --oneline
```

### Command Notes

- Setup
    [[Git Commands 01 - config init clone]]

- Inspect
    [[Git Commands 02 - status log show diff]]

- Stage and Commit
    [[Git Commands 03 - add commit]]

- Undo
    [[Git Commands 04 - restore reset revert clean]]

- Branch and Integrate
    [[Git Commands 05 - branch switch checkout merge rebase cherry-pick]]

- Synchronize Remotes
    [[Git Commands 06 - remote fetch pull push]]

- Auxiliary Operations
    [[Git Commands 07 - tag stash rm mv blame reflog shortlog]]
