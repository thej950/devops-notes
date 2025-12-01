# 🌿 Git Branching — Clear, Professional Guide

## 💬 What Is Branching in Git?

**Branching** in Git allows you to work on multiple versions or features of a project simultaneously. Each branch represents an independent line of development.

**Switching branches** changes your working directory and updates your files to match the state of the selected branch.

---

## ⚙️ Essential Branching Commands

| Command                        | Description                                    |
| ------------------------------ | ---------------------------------------------- |
| `git branch`                   | List all branches in the repository            |
| `git switch <branch>`          | Switch to an existing branch (**recommended**) |
| `git checkout <branch>`        | Older command to switch branches (still works) |
| `git switch -c <new_branch>`   | Create and switch to a new branch              |
| `git checkout -b <new_branch>` | Older equivalent for creating and switching    |

---

## 🧠 Practical Examples

```bash
# List all branches
git branch

# Create a new branch and switch to it
git switch -c feature/login

# Switch back to the main branch
git switch main

# Older equivalent
git checkout main
```

---

## ⚡ How Branch Switching Works

When you switch branches, Git performs the following:

* 🔄 **Updates your working directory** to match the target branch's files
* 🎯 **Moves the HEAD pointer** to point to the new branch
* ⚠️ If your uncommitted changes conflict with the branch you're switching to, Git may block the switch to protect your work

---

## ⚠️ Handling Uncommitted Changes

If Git prevents branch switching due to uncommitted changes, you can either:

* **Commit** your changes:

  ```bash
  git add .
  git commit -m "WIP"
  ```
* **Stash** your changes:

  ```bash
  git stash
  ```

  Then switch branches safely.

---

## 🎤 Interview-Ready Explanation

> **“Switching branches in Git means moving between independent lines of development. When you switch, Git updates your working directory to match the target branch. The modern command for switching is `git switch`, though `git checkout` is still widely used.”**

---
