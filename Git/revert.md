# 🔄 Git Revert — Clear, Professional Guide

## 💬 What Is `git revert`?

**Git revert** is a safe way to undo changes made in a specific commit by creating a new commit that reverses those changes.

Unlike `git reset`, it **does not remove or rewrite history**, making it the recommended method for undoing mistakes in shared or remote branches.

---

## ⚙️ Simple Explanation

You want to undo a mistake **without rewriting history**.

`git revert` creates a new commit that cancels out the changes made by the problematic commit. The original commit remains in the history, but its effects are neutralized.

---

## 🧠 Example

Imagine your commit history is:

```
A — B — C — D (HEAD)
```

If commit `C` introduced a bug and you want to undo it:

```bash
git revert C
```

Git creates a new commit (let's call it **E**) that reverses the changes from C.

Your updated history becomes:

```
A — B — C — D — E
```

No commits were deleted—perfect for safe collaboration.

---

## ⚖️ `git reset` vs `git revert`

| Command      | Effect on History                     | Best Use Case                                      |
| ------------ | ------------------------------------- | -------------------------------------------------- |
| `git reset`  | Moves HEAD and can **remove commits** | Local changes, rewriting history (unsafe to share) |
| `git revert` | Creates a new **reversal commit**     | Safely undo changes on shared/remote branches      |

---

## ⚡ Useful Revert Commands

| Command                           | Description                                                                   |
| --------------------------------- | ----------------------------------------------------------------------------- |
| `git revert <commit>`             | Revert a single commit                                                        |
| `git revert --no-commit <commit>` | Apply the revert but do **not** commit yet (useful to batch multiple reverts) |
| `git revert HEAD~2..HEAD`         | Revert a **range of commits**                                                 |

---

## 🎤 Interview-Ready Explanation

> **“`git revert` safely undoes changes from a specific commit by creating a new commit that reverses it. It preserves history, making it ideal for shared branches. This differs from `git reset`, which rewrites history and can remove commits.”**

