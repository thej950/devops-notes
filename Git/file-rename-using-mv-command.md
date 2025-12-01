# Git mv

💬 **Definition:**
The `git mv` command is used to move or rename files and directories in a Git repository. It’s a shortcut for doing `mv` (move) + `git add` + `git rm` together.

### ⚙️ Purpose:

* Rename files while keeping Git history.
* Move files between folders in the repository.
* Ensure Git tracks the rename/move correctly.

### 🧠 Syntax:

```
git mv <old_filename> <new_filename>
```

### 🔧 Examples:

**1️⃣ Rename a file**

```
git mv app.js main.js
```

➡ Renames `app.js` to `main.js` and stages the change.

**2️⃣ Move a file to another directory**

```
git mv index.html public/
```

➡ Moves `index.html` into the `public` folder.

**3️⃣ Rename a folder**

```
git mv old_folder new_folder
```

➡ Renames the folder and stages all changes.

### 🧩 What actually happens:

Running:

```
git mv file1 file2
```

Is equivalent to manually doing:

```
mv file1 file2
git rm file1
git add file2
```

…but `git mv` does it all in one step.

### 🧠 Why use `git mv` instead of `mv`:

| Command    | Behavior                                                                    |
| ---------- | --------------------------------------------------------------------------- |
| **mv**     | Moves/renames at OS level only. You must run `git add` + `git rm` manually. |
| **git mv** | Moves/renames the file AND stages the change automatically.                 |

### 🧩 Interview Answer Example:

“`git mv` is used to rename or move files while maintaining Git’s tracking. For example, `git mv config.yaml app-config.yaml` renames the file and automatically stages the update.”

### 💡 Tip to Remember:

📦 Think of `git mv` as **“move file and tell Git about it.”**
