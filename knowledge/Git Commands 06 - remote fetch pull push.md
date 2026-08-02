---
tags:
  - software
  - cli
  - git
summary: git remote, git fetch, git pull, git push
use_cases:
  - development
---

# Git Commands 06 - remote fetch pull push

## Synchronize Remotes

### git remote

`git remote` manages connections to remote repositories.

**Common parameters:**

| Parameter                                    | Meaning                                    |
| -------------------------------------------- | ------------------------------------------ |
| `-v`                                         | Show remote names with fetch and push URLs |
| `add <remote-name> <repository-url>`         | Add a remote repository                    |
| `remove <remote-name>`                       | Remove a remote repository                 |
| `rename <old-remote-name> <new-remote-name>` | Rename a remote repository                 |
| `set-url <remote-name> <repository-url>`     | Change a remote URL                        |
| `show <remote-name>`                         | Show details of a remote                   |

**Common commands:**

```bash
# List remote names or remotes with URLs
git remote
git remote -v

# Add a remote repository
git remote add origin https://github.com/baoyunfan0101/second-brain.git

# Remove a remote repository
git remote remove origin

# Rename a remote repository
git remote rename origin upstream

# Change a remote URL
git remote set-url origin https://github.com/baoyunfan0101/second-brain.git

# Show details of a remote
git remote show origin
```

### git fetch

`git fetch` downloads remote history without changing local branches, the staging area, or the working directory.

It updates remote-tracking branches to the corresponding fetched remote branch tips without merging them into local branches or producing file conflicts.

**Common parameters:**

| Parameter         | Meaning                                                                                                                  |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `[<remote-name>]` | Fetch from a specific remote (configured remote by default)                                                              |
| `--all`           | Fetch from all remote repositories                                                                                       |
| `--prune`         | Remove stale remote-tracking references (remote-tracking references whose corresponding remote branches no longer exist) |
| `--tags`          | Fetch tags from the remote                                                                                               |
| `--dry-run`       | Show what would be fetched without actually fetching                                                                     |

**Common commands:**

```bash
# Fetch from the configured remote or a specific remote
git fetch
git fetch origin

# Fetch from all remotes
git fetch --all

# Fetch and remove stale remote-tracking references
git fetch --prune origin

# Fetch tags
git fetch --tags origin

# Preview fetch results
git fetch --dry-run origin
```

### git pull

`git pull` fetches remote updates and integrates the current branch with its upstream branch.

It is equivalent to updating all fetched remote-tracking branches, then integrating only the current branch with its upstream branch through merge or rebase.

```text
git pull = git fetch + git merge
git pull --rebase = git fetch + git rebase
```

- Local is behind upstream:

```text
fast-forward local
```

- Local is ahead of upstream:

```text
already up to date
```

- Local and upstream have diverged:

```text
--rebase    -> rebase local commits onto upstream
--no-rebase -> three-way merge
--ff-only   -> stop
```

**Common parameters:**

| Parameter                     | Meaning                                          |
| ----------------------------- | ------------------------------------------------ |
| `<remote-name> <branch-name>` | Pull from a specific remote branch               |
| `--rebase`                    | Rebase local commits onto the fetched branch     |
| `--no-rebase`                 | Merge the fetched branch into the current branch |
| `--ff-only`                   | Allow only a fast-forward update                 |
| `--autostash`                 | Temporarily stash local changes before pulling   |

**Common commands:**

```bash
# Pull from the configured upstream branch or a specific remote branch
git pull
git pull origin main

# Pull with rebase or merge
git pull --rebase origin main
git pull --no-rebase origin main

# Pull only if fast-forward is possible
git pull --ff-only origin main

# Pull with automatic temporary stash
git pull --rebase --autostash origin main
```

### git push

`git push` uploads the current branch to its corresponding remote branch.

**Common parameters:**

| Parameter                     | Meaning                                                           |
| ----------------------------- | ----------------------------------------------------------------- |
| `<remote-name> <branch-name>` | Push a local branch to a remote                                   |
| `-u` / `--set-upstream`       | Push and set the upstream branch                                  |
| `--delete <branch-name>`      | Delete a remote branch                                            |
| `--tags`                      | Push tags                                                         |
| `--force-with-lease`          | Force push only if the remote branch has not changed unexpectedly |
| `--dry-run`                   | Show what would be pushed without actually pushing                |

**Common commands:**

```bash
# Push to the configured upstream branch or a specific remote branch
git push
git push origin main

# Push and set the upstream branch
git push -u origin main

# Delete a remote branch
git push origin --delete feature/login

# Push tags
git push origin --tags

# Safer force push
git push --force-with-lease origin feature/login

# Preview push results
git push --dry-run origin main
```
