*Part of the Git notes. See also: [[Git - Concepts]], [[Git - Setup]], [[Git - Daily Workflow]], [[Git - Undo (After Commit)]], [[Git - Inspecting]]*

**This file is for undoing things that have NOT been committed yet** — working dir or staging area only. If the problem is already in a commit, go to [[Git - Undo (After Commit)]] instead.

>[!Contents]
>- [[#Quick index]]
>- [[#GIT RESTORE]]

---

### Quick index

- Accidentally staged a file you didn't mean to? → [[#GIT RESTORE]] (`--staged`)
- Want to undo changes in your working dir (not added yet)? → [[#GIT RESTORE]]

---

### GIT RESTORE

- Used to undo or revert changes in the `working dir` or `staging area`.
- Not for undoing commits. Refer to [[Git - Undo (After Commit)]] for that.
- **Designed to manipulate files** (working directory and staging area) without touching your commit history.

***Basic Concept***
- There are two places where changes can exist:
	1. Working dir - files that changed but haven't been `git add`-ed yet.
	2. Staging Area - files that have been added but not yet committed.

---

**COMMON COMMANDS**

| Commands | Description | Notes |
| :--- | :--- | :--- |
| `git restore file.txt` | Restore the file to its latest committed version | -*undo changes in the working dir (not yet committed)*<br>-brings the file back from the latest commit into your working dir.<br>-if you edited something and want to discard it, use this. |
| `git restore --staged file.txt` | Remove the file from the staging area, but doesn't change the working directory | Useful when you added a file by accident — *undo something you `git add`-ed*, without changing the working dir. |
| `git restore --source=HEAD file.txt` | Restore the file using the specified commit as reference point — directly applies to working dir | -undo from latest commit to working dir<br>-the file in the working dir reflects the version from the specified commit. |
| `git restore --source=HEAD --staged --worktree file.txt` | Restore the file to the exact state of the last commit. Applies to both staging and working dir. | *the target file can be changed to `.` to target all files in current folder.* |

---

**FLAGS USE**

| Flag | Description |
| :--- | :--- |
| `--source=<tree>` | Reference point for which commit you want to pull from. The `<tree>` can be a commit ID |
| `--worktree` | Use to apply the changes to the working directory |
| `--staged` | Use to apply the changes to the staging area only |

---
