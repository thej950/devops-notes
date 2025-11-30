
# 🟦 Git Stash 

---

# ✅ **1. What is Stash?**

* **Stash temporarily hides your uncommitted changes**.
* Once files are stashed, **Git will not touch those changes** until you bring them back.
* Stash works **area-wise** (not file-by-file):

  * Staging area (Index)
  * Working directory (untracked files)
  * Even .gitignore files (using `-a`)
* It is like keeping your work in a **temporary storage box** inside Git.

---

# 🟦 **2. Why do we use Stash?**

* When you want to **switch branch** but your current branch has uncommitted changes.
* When you want to **test or pull the latest code** without losing your current modifications.
* When you want to **keep your work safe** temporarily.

---

# 🟩 **3. Stash Commands**

## ✔ 1. Stash only staged (tracked) files

```bash
git stash
```

This hides:

* Files in staging area
* Modified tracked files

---

## ✔ 2. Stash everything (tracked + untracked + ignored)

```bash
git stash -a
```

This hides:

* Staged files
* Untracked files
* .gitignore files

*(This is what you use when you have many untracked files)*

---

## ✔ 3. View all stashes

```bash
git stash list
```

Output example:

```
stash@{0}: WIP on master
stash@{1}: WIP on dev
```

---

## ✔ 4. Apply latest stash & remove it (POP)

```bash
git stash pop
```

* Restores hidden changes
* Removes stash from list

---

## ✔ 5. Apply older stash

```bash
git stash pop stash@{stash_no}
```

Example:

```bash
git stash pop stash@{2}
```

---

# 🟦 Additional Notes

* Stash does **not** delete your changes — it just **hides them temporarily**.
* Stash acts like a **stack** (LIFO → last in, first out).
* You can also keep stashes permanently using:

```bash
git stash apply
```

(This applies changes but does not remove stash from list.)

---

# 🚀 **Interview Tip**

**Stash is used when you want to switch branches but keep your uncommitted work safe.
`stash` hides changes; `pop` restores them.**

---
