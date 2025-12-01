# 🧩 Git Aliases — Comprehensive, Professional Guide

## 💬 What Are Git Aliases?

**Git aliases** are customizable shortcuts that allow you to replace long or frequently used Git commands with short, memorable alternatives. They streamline your workflow, reduce typing effort, and help maintain consistency in your daily Git usage.

---

## ⚙️ Why Use Git Aliases?

* ⏱️ **Save Time:** Shorten lengthy commands and speed up repetitive tasks.
* 🎯 **Reduce Errors:** Avoid typo‑prone commands by using simple shortcuts.
* 🛠️ **Customize Your Workflow:** Tailor Git to match your preferred development style.
* 🔁 **Boost Productivity:** Improve efficiency during everyday version‑control operations.

---

## 🧠 Creating Git Aliases

Create aliases using the `git config` command:

```bash
git config --global alias.st status
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.co checkout
git config --global alias.last "log -1 HEAD"
```

### Usage Examples

```bash
git st            # git status
git ci -m "msg"   # git commit -m "msg"
git br            # git branch
git co main       # git checkout main
git last          # view the latest commit
```

---

## 🔍 Viewing Existing Aliases

```bash
git config --get-regexp alias
```

## 🗑️ Removing an Alias

```bash
git config --global --unset alias.st
```

---

## 💡 Commonly Used Git Aliases

| Alias | Expands To                               | Purpose                        |
| ----- | ---------------------------------------- | ------------------------------ |
| `st`  | `status`                                 | Show working tree status       |
| `ci`  | `commit`                                 | Commit changes                 |
| `br`  | `branch`                                 | Manage or list branches        |
| `co`  | `checkout`                               | Switch branches                |
| `lg`  | `log --oneline --graph --decorate --all` | Clean, readable commit history |

### Create the popular `lg` alias:

```bash
git config --global alias.lg "log --oneline --graph --decorate --all"
```

---

## 🎤 Interview-Ready Definition

> **“Git aliases are user-defined shortcuts that replace long or frequently used Git commands with shorter, efficient alternatives. They enhance productivity by making Git usage faster, cleaner, and more personalized.”**

