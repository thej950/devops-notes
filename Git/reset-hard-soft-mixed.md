# 🟦 Git Reset 

---

# ✅ **Git Reset – 3 Types**

1. **Hard Reset** (most commonly used by DevOps)
2. **Soft Reset**
3. **Mixed Reset**

Git reset basically **moves HEAD to another commit** → and based on type, it resets:

* Working directory
* Staging area
* Local repository

---

# 🟥 1. Hard Reset (`--hard`)

### ✔ Meaning

* Completely **overwrites working directory + staging + commit history**.
* Moves HEAD to target commit and **removes all changes after that**.

### ✔ Why DevOps use it?

* To clean unwanted commits
* To revert project to a clean earlier state

### ✔ Command

```bash
git reset --hard <commit_id>
```

### ✔ Effect

* Commit history moves to `<commit_id>`
* All later commits disappear from branch
* Working files also reset to that commit

---

# 🟧 2. Soft Reset (`--soft`) 

### ✔ Before understanding → know 3 Git file states:

* **Untracked files** → Working Directory
* **Index (STG)** → Staging area
* **Committed files (LR)** → Local Repository

### ✔ Meaning

Soft reset moves:
**Committed → Staging Area (Index)**
(1 step back)

### ✔ Purpose

* You want to **modify the last commit**
* You want to redo commit message
* You want to add/remove files again

### ✔ Command

```bash
git reset --soft <previous_commit_id>
```

### ✔ Example

If commits are:

```
A (old) → B → C (HEAD)
```

Running:

```
git reset --soft B
```

Result:

* C commit moves to **staging area**
* Use `git status` to verify

---

# 🟨 3. Mixed Reset (`--mixed`) – *Default reset*

### ✔ Meaning

Mixed reset moves:
**Committed → Working Directory (untracked)**
(2 steps back)

### ✔ Effect

* Removes commit(s)
* Removes staging
* Keeps working files for re-editing

### ✔ Command

```bash
git reset --mixed <previous_commit_id>
```

### ✔ Example

If commits:

```
A → B → C (HEAD)
```

Running:

```
git reset --mixed B
```

Result:

* C commit removed
* Changes from C appear as **untracked/modified** files

---

# 🟦 Summary Table (Easy)

| Reset Type | HEAD            | Staging Area                    | Working Directory              | Steps Back |
| ---------- | --------------- | ------------------------------- | ------------------------------ | ---------- |
| **Soft**   | Moves to commit | Keeps commit changes in STAGING | Keeps working files            | 1 step     |
| **Mixed**  | Moves to commit | Clears staging                  | Keeps working files            | 2 steps    |
| **Hard**   | Moves to commit | Clears staging                  | Resets working files to commit | Full reset |

---

# 🚀 **Interview Tip**

**Soft keeps everything. Mixed keeps only working files. Hard erases everything after that commit.**

