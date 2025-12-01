# ⚙️ Git Configurations — Professional, Interview-Ready Guide

## 💬 What Is `git config`?

`git config` is a Git command used to define configuration settings that control how Git behaves. These settings include user identity, default editor, merge tools, aliases, and many other preferences.

Git configurations can be applied at different levels, allowing fine-grained control over behavior globally or per repository.

---

## 🎯 Purpose of Git Configuration

Git configuration allows you to:

* Set user information (name, email)
* Define editor or merge tool preferences
* Configure default branch names
* Create Git aliases
* Handle line-ending conversions
* Control behavior globally, per-user, or per-repository

---

## 🧩 Git Config Levels

| Level      | Scope                        | File Location                            | Description                           |
| ---------- | ---------------------------- | ---------------------------------------- | ------------------------------------- |
| **System** | All users on the machine     | `/etc/gitconfig`                         | System-wide settings                  |
| **Global** | Current user only            | `~/.gitconfig` or `~/.config/git/config` | User-specific settings                |
| **Local**  | Specific Git repository only | `.git/config`                            | Repository-level settings (most used) |

---

## 🧠 Priority Order

**Local > Global > System**

When the same setting exists in multiple levels, Git uses the one that is closest to the repository, meaning **Local** settings override Global and System.

---

## 🧠 Common `git config` Commands (Interview-Friendly)

| Command                                             | Description                              |
| --------------------------------------------------- | ---------------------------------------- |
| `git config --global user.name "John Doe"`          | Set global username                      |
| `git config --global user.email "john@example.com"` | Set global email                         |
| `git config --list`                                 | Display all Git configurations           |
| `git config --global core.editor "code --wait"`     | Set VS Code as default editor            |
| `git config core.autocrlf true`                     | Manage line endings (Windows/Linux)      |
| `git config --global alias.st status`               | Create an alias (shortcut)               |
| `git config --global init.defaultBranch main`       | Set default branch name                  |
| `git config --show-origin`                          | Show which file a config value came from |

---

## 🧠 Practical Examples

```bash
# View current configuration
git config --list

# Set user identity for this repository only
git config user.name "Navathej"
git config user.email "navathej@example.com"

# Set a global alias for 'status'
git config --global alias.st status
```

---

## 🎤 Interview-Ready Definition

> **“`git config` controls Git’s behavior at system, global, and local levels. It is used to set user details, editor preferences, default branch names, aliases, and more. Local configuration always overrides global and system settings.”**

---

## ⚡ Bonus: DevOps / CI-CD Usage Example

In CI/CD pipelines, Git is often preconfigured before running automated tasks:

```bash
git config --global user.email "ci-bot@company.com"
git config --global user.name "CI Bot"
```

This ensures automated commits (e.g., version bumps, changelog updates) have a clear and consistent author identity.

---
