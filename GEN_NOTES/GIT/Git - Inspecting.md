*Part of the Git notes. See also: [[Git - Concepts]], [[Git - Setup]], [[Git - Daily Workflow]], [[Git - Undo (Before Commit)]], [[Git - Undo (After Commit)]]*

**This file is for looking at and comparing things — no changes are made here.** Use this when you want to check what's different before deciding whether to use [[Git - Undo (Before Commit)]] or [[Git - Undo (After Commit)]].

>[!Contents]
>- [[#FOR VIEWING AND COMPARING]]
>- [[#LISTING BRANCHES]]

---

### FOR VIEWING AND COMPARING

| Commands | Desc |
| :--- | :--- |
| `git log` | show commit history |
| `git log --oneline --graph --all` | for a compact view |
| `git diff` | show diff of **working dir vs last staged/git add** — has `-` and `+` signs for what was added and removed |
| `git diff --staged`<br>or<br>`git diff --cached` | Show diff of **staged vs last commit** — *usually done before commit* |
| `git diff HEAD` | show diff of **working dir + staging area vs last commit** |

---

| Commands | Desc |
| :--- | :--- |
| `git diff HEAD~1 HEAD`<br>`HEAD~1` - one commit before head<br>`HEAD~2` - two commits before head | compare previous commit to current commit |
| `git diff <commit>`<br>- for the `<commit>` argument, you can pass a `commit hash`, `partial hash`, `tag`, `main`, or `branch name` | show diff of **working dir + staging area vs specified commit** |
| `git diff commit1 commit2` | compare two commits |
| `git diff --color-words` | only show the changed words, not the whole line diff (*note: original had it as `--colors-words`, corrected here — verify against `git diff --help` on your machine*) |
| `git show <commitID>` | inspecting history |
| `git show-ref` | |

---

### LISTING BRANCHES

| Commands | Description |
| :--- | :--- |
| `git branch -r` | Remote branches on the remote repo |
| `git branch -a` | All branches (local + remote) |
| `git show-branch` | For a quick view |

---
