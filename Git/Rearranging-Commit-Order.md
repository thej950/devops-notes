# 🟦 Rearranging Commit Order (Git Rebase – Interactive)

---

## ✅ 1. What it is?

* To **rearrange commit order**, we use **interactive rebase**.
* Rebase is not only for fast-forward — it can also:

  * reorder commits
  * edit commits
  * squash commits
  * delete commits

---

## 🟦 2. Command to Rearrange Commits

```bash
git rebase -i HEAD~5
```

### Meaning:

* `-i` → interactive mode
* `HEAD~5` → pick last 5 commits from the top
* If you have **6 commits**, you select **5** because:
  ✔ the **first (oldest) commit cannot be rearranged**
  ✔ only the commits **after** that can be reordered

---

## 🟦 3. What happens after command?

Running:

```bash
git rebase -i HEAD~5
```

will open an editor (Vim) showing something like:

```
pick a1b2c3 commit message 1
pick d4e5f6 commit message 2
pick g7h8i9 commit message 3
pick j1k2l3 commit message 4
pick m4n5o6 commit message 5
```

### You can now:

* **reorder lines** → change commit order
* change `pick` to:

  * `reword` → edit message
  * `squash` → combine commits
  * `drop` → delete commit

Then save & exit → Git applies the new order.

---

## 🟦 4. Real-Time Example

If commit order is wrong:

```
commit1
commit3
commit2
commit4
```

You can reorder to:

```
commit1
commit2
commit3
commit4
```

by editing the rebase list.

---

## 🟦 5. IMPORTANT Rule

Interactive rebase **rewrites commit IDs** → do NOT use it on commits that are already **pushed** to a shared remote.

Only use it in:

* local branches
* feature branches
* before pushing

---

# 🚀 Interview Tip

**Interactive rebase is used for rewriting history — reorder, squash, edit or delete commits before pushing to remote.**

---

