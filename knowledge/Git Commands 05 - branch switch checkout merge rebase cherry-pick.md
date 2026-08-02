---
tags:
  - software
  - cli
  - git
summary: git branch, git switch, git checkout, git merge, git rebase, git cherry-pick
use_cases:
  - development
---

# Git Commands 05 - branch switch checkout merge rebase cherry-pick

## Branch and Integrate

### git branch

`git branch` manages branches.

It does not switch branches.

**Common parameters:**

| Parameter                                  | Meaning                                                                 |
| ------------------------------------------ | ----------------------------------------------------------------------- |
| `<branch-name>`                            | Create a new branch at the current `HEAD`                               |
| `-a`                                       | Show local branches and remote-tracking branches                        |
| `-r`                                       | Show remote-tracking branches only                                      |
| `-v`                                       | Show each branch with its latest commit                                 |
| `-vv`                                      | Show each branch with upstream and ahead/behind information             |
| `--show-current`                           | Show the current branch name                                            |
| `-d <branch-name>`                         | Delete a branch only if it is already merged                            |
| `-D <branch-name>`                         | Force delete a branch even if it is not merged                          |
| `-m [<old-branch-name>] <new-branch-name>` | Rename a branch (rename the current branch by default)                  |
| `--merged [<commit-id>]`                   | Show branches already merged into a specific commit (`HEAD` by default) |
| `--no-merged [<commit-id>]`                | Show branches not yet merged into a specific commit (`HEAD` by default) |

**Common commands:**

```bash
# Show local, all, or remote-tracking branches
git branch
git branch -a
git branch -r

# Show branches with latest commit or upstream information
git branch -v
git branch -vv

# Show the current branch name
git branch --show-current

# Create a new branch at the current HEAD
git branch feature/login

# Delete a merged branch or force delete an unmerged branch
git branch -d feature/login
git branch -D experiment/cache-test

# Rename the current branch or a specific branch
git branch -m feature/user-profile
git branch -m feature/login feature/auth-login

# Show branches merged or not merged into HEAD or main
git branch --merged
git branch --no-merged
git branch --merged main
git branch --no-merged main
```

### git switch

`git switch` switches branches.

It moves `HEAD` and updates the staging area and working directory accordingly.

Local changes are preserved if they do not conflict with the target branch; otherwise, Git stops the switch.

Know more at `Git -> Git Behaviors -> Switch Branches with Local Changes`.

**Common parameters:**

| Parameter                       | Meaning                                                                                                 |
| ------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `<branch-name>`                 | Switch to an existing branch                                                                            |
| `-c <new-branch> [<commit-id>]` | Create a new branch from a specific commit and switch to it (`HEAD` by default)                         |
| `-C <new-branch> [<commit-id>]` | Create or reset a branch from a specific commit and switch to it (`HEAD` by default)                    |
| `-`                             | Switch back to the previous branch                                                                      |
| `--detach [<commit-id>]`        | Switch directly to a specific commit without a branch (`HEAD` by default)                               |
| `--track <remote-branch>`       | Create a local branch tracking a remote one (uses the same name by default; fails if it already exists) |
| `--discard-changes`             | Discard conflicting local changes when switching                                                        |

**Common commands:**

```bash
# Switch to an existing branch
git switch main

# Create a new branch from HEAD or a specific commit and switch to it
git switch -c feature/login
git switch -c feature/login HEAD~1

# Create or reset a branch from HEAD or a specific commit and switch to it
git switch -C feature/login
git switch -C feature/login HEAD~1

# Switch back to the previous branch
git switch -

# Switch directly to HEAD or a specific commit without a branch
git switch --detach
git switch --detach HEAD~1

# Create a local branch tracking a remote branch
git switch --track origin/feature/login

# Create a local branch with a specified name tracking a remote branch
git switch -c feature/login --track origin/feature/auth-login

# Force switch and discard conflicting local changes
git switch --discard-changes main
```

### git checkout

`git checkout` is an older command used to switch branches or restore files.

Know more at `Git -> Git Behaviors -> Restore File Versions & Switch Branches with Local Changes`.

**Common parameters:**

| Parameter                            | Meaning                                                                              |
| ------------------------------------ | ------------------------------------------------------------------------------------ |
| `<branch-name>`                      | Switch to an existing branch                                                         |
| `-b <new-branch-name> [<commit-id>]` | Create a new branch from a specific commit and switch to it (`HEAD` by default)      |
| `-B <branch-name> [<commit-id>]`     | Create or reset a branch from a specific commit and switch to it (`HEAD` by default) |
| `-`                                  | Switch back to the previous branch                                                   |
| `--detach [<commit-id>]`             | Switch directly to a specific commit without a branch (`HEAD` by default)            |
| `[<commit-id>]`                      | Restore the working directory from a specific commit (the staging area by default)   |
| `-- <path>`                          | Specify the file or directory target for checkout                                    |

**Common commands:**

```bash
# Switch to an existing branch
git checkout main

# Create a new branch from HEAD or a specific commit and switch to it
git checkout -b feature/login
git checkout -b feature/login HEAD~1

# Create or reset a branch from HEAD or a specific commit and switch to it
git checkout -B feature/login
git checkout -B feature/login HEAD~1

# Switch back to the previous branch
git checkout -

# Switch directly to HEAD or a specific commit without a branch
git checkout --detach
git checkout --detach HEAD~1

# Restore a specific path from the staging area or a specific commit
git checkout -- README.md
git checkout HEAD~1 -- README.md
```

### git merge

`git merge` combines another branch into the current branch.

Know more at `Git -> Git Behaviors -> Merge Branches`.

**Common parameters:**

| Parameter       | Meaning                                                                                                                                                                                          |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `<branch-name>` | Merge another branch into the current branch                                                                                                                                                     |
| `--ff-only`     | Allow only a fast-forward merge (only when the current branch points to an ancestor of the target branch; no merge commit is created, and the current branch pointer moves to the target branch) |
| `--no-ff`       | Always create a merge commit (two or more parent commits, one from each merged branch)                                                                                                           |
| `--squash`      | Combine changes without creating a merge commit (no merge commit is created, and the merge result is placed in the staging area and working directory)                                           |
| `--continue`    | Continue the current merge operation after resolving conflicts                                                                                                                                   |
| `--abort`       | Abort the current merge operation                                                                                                                                                                |

**Common commands:**

```bash
# Merge another branch into the current branch
git merge feature/login

# Merge only if fast-forward is possible or always create a merge commit
git merge --ff-only feature/login
git merge --no-ff feature/login

# Merge changes without creating a merge commit
git merge --squash feature/login

# Abort or continue an in-progress merge
git merge --abort
git merge --continue
```

> [!note] Merge conflicts
> 
> A merge conflict happens when Git cannot automatically combine changes from different branches.
> 
> - The staging area stores multiple entries for the same path:
>     - Stage 1: base version
>     - Stage 2: current version
>     - Stage 3: target version
> - The working directory contains a conflict file with conflict markers:
> 
> ```text
> <<<<<<< HEAD
> current branch content
> =======
> incoming branch content
> >>>>>>> <branch-name>
> ```
> 
> **Resolution flow:**
> 
> ```bash
> # Check conflicted files
> git status
> 
> # Manually edit the working directory file and remove conflict markers
> 
> # Mark a conflicted file as resolved: add a stage 0 entry (normal entry)
> git add <path>
> 
> # Complete the merge
> git commit
> ```

### git rebase

`git rebase` moves commits onto a new base commit.

The selected commits are replayed onto the new base one by one, and each commit may cause a conflict.

Know more at `Git -> Git Behaviors -> Cherry-pick / Rebase Commits`.

**Common parameters:**

| Parameter                                      | Meaning                                                                                                 |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `<base-branch-name>`                           | Rebase the current branch onto another branch                                                           |
| `-i <commit-id>`                               | Start an interactive rebase for commits after a specific commit                                         |
| `--onto <new-base> <upstream> [<branch-name>]` | Switch to the specified branch (`HEAD` by default), then rebase a selected commit range onto a new base |
| `--continue`                                   | Continue the current rebase operation after resolving conflicts                                         |
| `--abort`                                      | Abort the current rebase operation                                                                      |
| `--skip`                                       | Skip the current commit and continue the rebase operation                                               |

**Common commands:**

```bash
# Rebase the current branch onto another branch
git rebase main

# Start an interactive rebase
git rebase -i HEAD~3

# Rebase a branch from one base to another base
git rebase --onto main develop feature/login

# Continue, abort, or skip during an in-progress rebase
git rebase --continue
git rebase --abort
git rebase --skip
```

> [!note] Interactive rebase mode
> 
> Git lists all commits after the specified commit and lets you choose how each commit should be replayed:
> 
> ```text
> pick <commit-id> <commit-message>
> pick <commit-id> <commit-message>
> ...
> 
> # Rebase <upstream>..<current-commit> onto <upstream> (<number> commands)
> #
> # Commands:
> # p, pick <commit> = use commit
> # r, reword <commit> = use commit, but edit the commit message
> # e, edit <commit> = use commit, but stop for amending
> # s, squash <commit> = use commit, but meld into previous commit
> # f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
> #                    commit's log message, unless -C is used, in which case
> #                    keep only this commit's message; -c is same as -C but
> #                    opens the editor
> # x, exec <command> = run command (the rest of the line) using shell
> # b, break = stop here (continue rebase later with 'git rebase --continue')
> # d, drop <commit> = remove commit
> # l, label <label> = label current HEAD with a name
> # t, reset <label> = reset HEAD to a label
> # m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
> #         create a merge commit using the original merge commit's
> #         message (or the oneline, if no original merge commit was
> #         specified); use -c <commit> to reword the commit message
> # u, update-ref <ref> = track a placeholder for the <ref> to be updated
> #                       to this position in the new commits. The <ref> is
> #                       updated at the end of the rebase
> #
> # These lines can be re-ordered; they are executed from top to bottom.
> #
> # If you remove a line here THAT COMMIT WILL BE LOST.
> #
> # However, if you remove everything, the rebase will be aborted.
> ```
> 
> **Common choices:**
> 
> | Choice | Meaning |
> |---|---|
> | `p` / `pick` | Replay the commit without intentionally changing it |
> | `r` / `reword` | Replay the commit and edit its commit message |
> | `e` / `edit` | Replay the commit and stop so the commit can be amended |
> | `s` / `squash` | Combine the commit with the previous commit and edit the combined commit message |
> | `f` / `fixup` | Combine the commit with the previous commit and normally discard this commit's message |
> | `fixup -C` | Combine the commit with the previous commit and keep this commit's message |
> | `fixup -c` | Combine the commit with the previous commit, keep this commit's message, and open the editor |
> | `x` / `exec` | Run a shell command at this point |
> | `b` / `break` | Stop at this point and continue later with `git rebase --continue` |
> | `d` / `drop` | Remove the commit |
> | `l` / `label` | Give the current `HEAD` a temporary label |
> | `t` / `reset` | Reset `HEAD` to a previously created label |
> | `m` / `merge` | Create a merge commit using a previously created label |
> | `u` / `update-ref` | Record a reference to be updated to this position when the rebase finishes |
> 
> Lines beginning with `#` are comments and are ignored.
> 
> Save and close the editor to start the rebase. In Vim, press `i` to enter insert mode, press `Esc` to return to normal mode, enter `:wq` to save and close, or enter `:q!` to close without saving.

### git cherry-pick

`git cherry-pick` applies the changes introduced by specific commits to the current branch.

The selected commits are replayed one by one starting from the current `HEAD`, and each commit may cause a conflict.

Know more at `Git -> Git Behaviors -> Cherry-pick / Rebase Commits`.

**Common parameters:**

| Parameter                             | Meaning                                                              |
| ------------------------------------- | -------------------------------------------------------------------- |
| `<commit-id>`                         | Apply one commit to the current branch                               |
| `<start-commit-id>^..<end-commit-id>` | Apply a range of commits to the current branch                       |
| `-n` / `--no-commit`                  | Apply the changes without creating a commit                          |
| `-x`                                  | Append the original commit ID to the new commit message              |
| `--continue`                          | Continue the current cherry-pick operation after resolving conflicts |
| `--abort`                             | Abort the current cherry-pick operation                              |
| `--skip`                              | Skip the current commit and continue the cherry-pick operation       |

**Common commands:**

```bash
# Apply one commit or a range of commits to the current branch
git cherry-pick feature/login
git cherry-pick feature/login~2^..feature/login

# Apply changes without committing
git cherry-pick -n feature/login

# Apply one commit and record the original commit ID
git cherry-pick -x feature/login

# Continue, abort, or skip during an in-progress cherry-pick
git cherry-pick --continue
git cherry-pick --abort
git cherry-pick --skip
```

