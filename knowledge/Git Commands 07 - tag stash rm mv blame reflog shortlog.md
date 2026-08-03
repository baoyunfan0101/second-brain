---
tags:
  - software
  - cli
  - git
summary: git tag, git stash, git rm, git mv, git blame, git reflog, git shortlog
use_cases:
  - development
---

# Git Commands 07 - tag stash rm mv blame reflog shortlog

## Auxiliary Operations

### git tag

`git tag` creates fixed names for specific commits.

- **Lightweight tag:** A reference directly to a commit.

- **Annotated tag:** A reference to a tag object containing the target object, tag name, tagger identity, timestamp, message, and optional signature; the tag object then references the commit.

**Common parameters:**

| Parameter                     | Meaning                                                                            |
| ----------------------------- | ---------------------------------------------------------------------------------- |
| `<tag-name> [<commit-id>]`    | Create a lightweight tag on a specific commit (`HEAD` by default)                  |
| `-a <tag-name> [<commit-id>]` | Create an annotated tag on a specific commit (`HEAD` by default)                   |
| `-m "<message>"`              | Create an annotated tag with the specified message                                 |
| `-d <tag-name>`               | Delete a local tag                                                                 |
| `--list [<tag-pattern>]`      | List tags, optionally matching a pattern                                           |
| `--contains [<commit-id>]`    | List tags whose referenced histories contain a specific commit (`HEAD` by default) |

**Common commands:**

```bash
# List all tags or tags matching a pattern
git tag
git tag --list "v1.*"

# Create a lightweight tag on HEAD or a specific commit
git tag v1.0.0
git tag v0.9.0 HEAD~1

# Create an annotated tag on HEAD or a specific commit
git tag -a v1.0.0 -m "Release v1.0.0"
git tag -a v0.9.0 -m "Release v0.9.0" HEAD~1

# List tags containing HEAD or a specific commit
git tag --contains
git tag --contains HEAD~1

# Delete a local tag
git tag -d v1.0.0
```

### git stash

`git stash` temporarily saves uncommitted changes.

- **Push:** By default, it saves staged and unstaged changes in tracked files as a snapshot, adds it to the stash list from newest to oldest, and restores those files to `HEAD`. The `stash@{n}` numbers change when stashes are added or removed.

- **Apply:** By default, it reapplies both the stashed staged and unstaged changes to the current working directory; they may conflict with changes already present in the current working directory.

**Common parameters:**

| Parameter                    | Meaning                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------- |
| `push`                       | Save uncommitted changes to a new stash                                          |
| `-m "<message>"`             | Add a message to the stash                                                       |
| `-u` / `--include-untracked` | Include untracked files                                                          |
| `-a` / `--all`               | Include ignored and untracked files                                              |
| `--staged`                   | Stash only staged changes                                                        |
| `list`                       | List stashes                                                                     |
| `show [<stash-id>]`          | Show stash changes (`stash@{0}` by default)                                      |
| `apply [<stash-id>]`         | Apply a stash without removing it (`stash@{0}` by default)                       |
| `pop [<stash-id>]`           | Apply a stash and remove it if the application succeeds (`stash@{0}` by default) |
| `drop [<stash-id>]`          | Remove a stash (`stash@{0}` by default)                                          |
| `clear`                      | Remove all stashes                                                               |

**Common commands:**

```bash
# Stash tracked changes, with a message, or with untracked files
git stash
git stash push -m "WIP: update login form"
git stash push -u -m "WIP: include new files"

# Stash ignored and untracked files or only staged changes
git stash push -a -m "WIP: include ignored files"
git stash push --staged -m "WIP: staged changes"

# List stashes
git stash list

# Show the latest stash or a specific stash
git stash show
git stash show stash@{1}

# Apply the latest stash or a specific stash
git stash apply
git stash apply stash@{1}

# Apply and, if successful, remove the latest stash or a specific stash
git stash pop
git stash pop stash@{1}

# Drop the latest stash or a specific stash
git stash drop
git stash drop stash@{1}

# Clear all stashes
git stash clear
```

### git rm

`git rm` removes files from Git tracking.

By default, it removes tracked files from the staging area and working directory.

**Common parameters:**

| Parameter          | Meaning                                                                                         |
| ------------------ | ----------------------------------------------------------------------------------------------- |
| `<path>`           | Remove a tracked file from Git and the working directory                                        |
| `--cached`         | Stop tracking the file but keep it in the working directory                                     |
| `-r`               | Remove directories recursively (remove all tracked files in a directory and its subdirectories) |
| `-f`               | Force removal (remove files even if they have staged or unstaged changes)                       |
| `-n` / `--dry-run` | Preview removal without actually removing files                                                 |

**Common commands:**

```bash
# Remove a tracked file or directory from Git and the working directory
git rm README.md
git rm -r src/legacy/

# Stop tracking a file but keep the local file
git rm --cached .env

# Force removal
git rm -f build/output.log

# Preview removal
git rm --dry-run README.md
```

### git mv

`git mv` moves or renames a tracked file.

It moves or renames tracked files in the working directory and adds the change to the staging area.

**Common parameters:**

| Parameter                | Meaning                                        |
| ------------------------ | ---------------------------------------------- |
| `<source> <destination>` | Move or rename a tracked file or directory     |
| `-f`                     | Force the move or rename                       |
| `-n` / `--dry-run`       | Preview the move without actually moving files |

**Common commands:**

```bash
# Rename or move a tracked file
git mv README.md README.old.md
git mv src/App.js src/components/App.js

# Force rename or move
git mv -f old-name.txt new-name.txt

# Preview rename or move
git mv --dry-run src/App.js src/components/App.js
```

### git blame

`git blame` shows which commit last changed each line of a file.

**Common parameters:**

| Parameter                    | Meaning                                                                                                   |
| ---------------------------- | --------------------------------------------------------------------------------------------------------- |
| `[<commit-id>]`              | Show blame from a specific commit (`HEAD` by default)                                                     |
| `<path>`                     | Show blame for a specific file                                                                            |
| `-L <start-line>,<end-line>` | Show blame information for a line range                                                                   |
| `-w`                         | Ignore whitespace changes                                                                                 |
| `-M`                         | Detect moved lines within a file (to blame the original change rather than the move)                      |
| `-C`                         | Detect copied or moved lines from other files (to blame the original change rather than the copy or move) |
| `-e` / `--show-email`        | Show author email instead of author name                                                                  |
| `-f` / `--show-name`         | Show the original file name                                                                               |

**Common commands:**

```bash
# Show line-by-line history of a file from HEAD or a specific commit
git blame README.md
git blame HEAD~1 -- README.md

# Show blame for a line range
git blame -L 10,20 README.md

# Ignore whitespace-only changes
git blame -w README.md

# Detect moved lines within the file or copied lines from other files
git blame -M README.md
git blame -C README.md

# Show author email or original file name
git blame --show-email README.md
git blame --show-name README.md
```

### git reflog

`git reflog` shows recent movements of `HEAD` and branch references.

Older entries may expire according to reflog expiration settings and be removed when Git runs maintenance, `git gc`, or `git reflog expire`.

**Common parameters:**

| Parameter              | Meaning                                                  |
| ---------------------- | -------------------------------------------------------- |
| `show [<ref>]`         | Show reflog for a specific reference (`HEAD` by default) |
| `--all`                | Show reflogs for all references                          |
| `--date=<date-format>` | Show reflog dates in a specific format                   |
| `-<number>`            | Limit the number of entries shown                        |

**Common commands:**

```bash
# Show HEAD reflog or a branch reflog
git reflog
git reflog show main

# Show reflogs for all references
git reflog --all

# Show reflog with ISO dates
git reflog --date=iso

# Show limited reflog entries
git reflog -5
```

### git shortlog

`git shortlog` summarizes commit history by author.

**Common parameters:**

| Parameter                               | Meaning                                                                |
| --------------------------------------- | ---------------------------------------------------------------------- |
| `[<commit-range>]`                      | Summarize commits in a specific revision range                         |
| `-s`                                    | Show only commit counts                                                |
| `-n`                                    | Sort by number of commits                                              |
| `-e`                                    | Show author email addresses                                            |
| `--all`                                 | Include commits reachable from all references (from `HEAD` by default) |
| `--since="<date>"` / `--until="<date>"` | Include commits within a time range                                    |
| `-- <path>`                             | Summarize commits related to a specific file or directory              |

**Common commands:**

```bash
# Summarize commits by author
git shortlog
git shortlog HEAD~10..HEAD

# Show commit counts by author, sorted by count, or with email addresses
git shortlog -s
git shortlog -s -n
git shortlog -s -n -e

# Include all references
git shortlog -s -n --all

# Summarize commits within a time range
git shortlog -s -n --since="2026-01-01" --until="2026-01-31"

# Summarize commits related to a specific file
git shortlog -- README.md
```
