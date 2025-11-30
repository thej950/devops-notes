# 🟦 Git Branch Delete – Hard Delete vs Soft Delete

---

# ✅ **1. Hard Delete (`-D`)**

### Meaning:

* **Child branch is NOT merged** into master/main.
* When you delete such a branch, Git **force deletes** it — even if commits are not merged anywhere.
* ⚠ This may result in **commit loss** if not pushed anywhere.

### Command:

```bash
git branch -D branchname
```

### When used in real-time?

* Branch is experimental
* Mistaken or unnecessary branch
* You knowingly want to remove unmerged commits

---

# 🟦 **2. Soft Delete (`-d`)**

### Meaning:

* Child branch **is already merged** into master/main.
* Git safely deletes the branch only if:
  ✔ merged to master
  ✔ no unique (unmerged) commits

### Command:

```bash
git branch -d branchname
```

### Why Git calls it “safe delete”?

* No data loss
* All commits already exist in master branch

---

# 🟦 Short Example

| Branch        | Merged? | Delete Type | Command                       |
| ------------- | ------- | ----------- | ----------------------------- |
| feature/login | YES     | Soft Delete | `git branch -d feature/login` |
| feature/test  | NO      | Hard Delete | `git branch -D feature/test`  |

---

# 🚀 **Interview Tip**

**`-d` = safe delete only if merged.
`-D` = force delete even if NOT merged.**

---
