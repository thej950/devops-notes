# 🔄 Git Restore — Clear & Professional Guide

## 💬 What Is `git restore`?

`git restore` is a Git command introduced in **Git 2.23** to simplify undoing changes in your working directory or staging area. It provides a safer, more intuitive alternative to using `git checkout` for undoing modifications.

---

## 🎯 Purpose of `git restore`

* Undo modifications in files
* Unstage previously staged files
* Restore files from a specific commit, branch, or reference

---

## 🧠 Core Use Cases

### **1️⃣ Discard changes in the working directory**

Revert a file back to the last committed version:

```bash
git restore <file>
```

**Example:**

```bash
git restore app.js
```

This removes any uncommitted changes in `app.js`.

---

### **2️⃣ Unstage a file (move from staged → unstaged)**

Equivalent to `git reset HEAD <file>`:

```bash
git restore --staged <file>
```

**Example:**

```bash
git restore --staged index.html
```

This keeps your modifications but removes the file from staging.

---

### **3️⃣ Restore a file from a specific commit**

```bash
git restore --source=<commit_hash> <file>
```

**Example:**

```bash
git restore --source=abc123 main.py
```

This brings back the version of `main.py` from commit `abc123`.

---

## ⚡ Common Flags

| Flag                | Description                                             |
| ------------------- | ------------------------------------------------------- |
| `--staged`          | Unstages files (moves from index to working area)       |
| `--source=<commit>` | Restore from a specific commit or reference             |
| `--worktree`        | Restore file in working directory (default)             |
| `--ignore-unmerged` | Ignore unmerged entries (useful during merge conflicts) |

---

## 💡 `git restore` vs `git reset`

| Command       | Use Case                                           | Effect                                    |
| ------------- | -------------------------------------------------- | ----------------------------------------- |
| `git restore` | Undo changes in files or unstage files             | Affects working directory or staging area |
| `git reset`   | Move HEAD to another commit, modify commit history | Affects commit history and staging        |

---

## 🎤 Interview-Ready Explanation

> **“`git restore` safely discards changes or unstages files. For example, `git restore file.txt` reverts uncommitted changes, while `git restore --staged file.txt` removes a file from the staging area but keeps the changes. It was introduced to simplify workflows previously achieved by `git checkout` and `git reset`.”**

---

## 🧠 Easy Way to Remember

🗂️ **Think of `git restore` as: *“restore my file(s) to a previous safe state.”***

---

