# 🟦 Git Reflog 

---

## ✅ **1. What is `git reflog`?**

* `git reflog` shows the **history of all actions done in Git**, including:

  * commits
  * checkouts
  * resets
  * merges
  * rebases
  * orphan commits
  * deleted commits
* Even if commits are **not visible in `git log`**, they appear in **reflog**.

---

## 🟦 **2. Use Case**

Reflog is mainly used for **recovery**:

* restore deleted commits
* recover after wrong reset
* undo rebase
* find old HEAD positions
* restore lost branches

Even commits that you think are “gone forever” can be found here.

---

## 🟦 **3. Command**

```bash
git reflog
```

This displays:

* all commit ids
* all HEAD movements
* checkout history
* rebase and reset history

---

## 🟦 Example Output (Simple Understanding)

```
a3c1d1 HEAD@{0}: commit: updated readme
b2e4f1 HEAD@{1}: reset: moving to HEAD~1
c5a8t9 HEAD@{2}: commit: added login api
...
```

This helps you pick any commit and restore it like:

```bash
git checkout <commit_id>
```

or restore branch:

```bash
git checkout -b recover <commit_id>
```

---

## 🟦 **4. Reflog vs Log**

| Command      | Shows                                             |
| ------------ | ------------------------------------------------- |
| `git log`    | Only commits that exist in current branch history |
| `git reflog` | **All actions**, including deleted/orphan commits |

---

# 🚀 Interview Tip

**Reflog is like Git’s black box — it records everything and helps recover lost commits after reset or rebase.**

---

