---
tags:
  - software
  - cli
  - git
summary: git config, git init, git clone
use_cases:
  - development
---

# Git Commands 01 - config init clone

## Setup

### git config

`git config` manages Git configuration.

**Common parameters:**

| Parameter                           | Meaning                                         |
| ----------------------------------- | ----------------------------------------------- |
| `<key>`                             | Read the effective value of a configuration key |
| `<key> <value>`                     | Set a configuration key to a value              |
| `--global` / `--local` / `--system` | Choose the configuration level                  |
| `--list`                            | Show configuration values                       |
| `--show-origin`                     | Show where each configuration value comes from  |

**Common commands:**

```bash
# Read effective configuration values
git config user.name
git config user.email

# Set global personal identity
git config --global user.name "Yunfan Bao"
git config --global user.email "baoyunfan0101@gmail.com"

# Show global configuration values and their sources
git config --global --list
git config --global --list --show-origin
```

### git init

`git init` creates a new Git repository.

It adds a hidden `.git` directory.

**Common parameters:**

| Parameter                                             | Meaning                                                                                                                       |
| ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `[<directory>]`                                       | Create the repository in a specific directory (current directory by default)                                                  |
| `-b <branch-name>` / `--initial-branch=<branch-name>` | Set the initial branch name (`init.defaultBranch` by default)                                                                 |
| `--bare`                                              | Create a bare repository without a working directory (do not create a `.git` directory; use the specific directory as `.git`) |

**Common commands:**

```bash
# Create a Git repository in the current directory or a specific directory
git init
git init my-project

# Create a Git repository with main as the initial branch
git init -b main

# Create a bare repository
git init --bare my-project.git
```

### git clone

`git clone` copies an existing repository into a new local directory.

After cloning, the new local repository contains project files, commit history, and a remote named `origin`.

**Common parameters:**

| Parameter                                     | Meaning                                                                                       |
| --------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `<repository-url> [<directory>]`              | Clone a repository into a specific directory (current directory + repository name by default) |
| `-b <branch-name>` / `--branch <branch-name>` | Switch to the specified branch after cloning                                                  |
| `--depth <number>`                            | Create a shallow clone with limited commit history                                            |
| `--single-branch`                             | Clone only one branch's history                                                               |
| `--recurse-submodules`                        | Clone submodules after cloning the repository                                                 |

**Common commands:**

```bash
# Clone into the default directory or a specific directory
git clone https://github.com/git/git.git
git clone https://github.com/git/git.git git-source

# Clone and switch to a specific branch
git clone -b master https://github.com/git/git.git

# Clone only one branch's history
git clone -b master --single-branch https://github.com/git/git.git

# Clone with limited commit history
git clone --depth 1 https://github.com/git/git.git

# Clone submodules after cloning the repository
git clone --recurse-submodules https://github.com/libgit2/libgit2.git
```
