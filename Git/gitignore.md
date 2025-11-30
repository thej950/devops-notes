# 🟦 **.gitignore (Simple Explanation)**

---

## ✅ **1. What is `.gitignore`?**

* `.gitignore` is a **special configuration file** in Git.
* Any file/folder name written inside `.gitignore` will be **ignored by Git**.
* Git will **not track, not stage, not commit** those files.

Useful for:

* Private files
* Log files
* Build folders
* Temporary files
* Credentials

---

# 🟦 Steps (Based on your example)

### **1. Create a few files**

```bash
touch file1 file2 file3 file4
```

### **2. Check status**

```bash
git status
```

Git will show:

```
Untracked files:
  file1
  file2
  file3
  file4
```

---

### **3. Create `.gitignore` and add file names**

```bash
cat > .gitignore
file1
file2
file3
file4
```

(Press **Ctrl + D** to save)

This tells Git:
“Do not track file1, file2, file3, file4.”

---

### **4. Check status again**

```bash
git status
```

Git will **no longer show file1–file4**, because they are now ignored.

---

# 🟦 VERY IMPORTANT NOTE

* If a file is **already committed earlier**, Git will not ignore it automatically.
* `.gitignore` works **only for untracked files**.

---

# 🚀 **Interview Tip**

**`.gitignore` prevents unnecessary or sensitive files from entering your repository.
It only hides files that are untracked.**

---
