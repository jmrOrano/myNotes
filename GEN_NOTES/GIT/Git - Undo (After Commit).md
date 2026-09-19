*Part of the Git notes. See also: [[Git - Concepts]], [[Git - Setup]], [[Git - Daily Workflow]], [[Git - Undo (Before Commit)]], [[Git - Inspecting]]*

**This file is for undoing or fixing things that ARE already committed** — this changes history. If the problem is only in your working dir or staging area (not committed yet), go to [[Git - Undo (Before Commit)]] instead.

>[!Contents]
>- [[#Quick index]]
>- [[#GIT COMMIT --AMEND]]
>- [[#GIT RESET]]
>- [[#GIT SWITCH]]
>- [[#GIT CHECKOUT]]

---

### Quick index

- Typo in a commit you *just* made? → [[#GIT COMMIT --AMEND]]
- The problem is a few commits back already? → [[#GIT RESET]]
- Just want to look at an old commit, no editing? → [[#GIT SWITCH]] (`--detach`)
- Want to create or move to a branch? → [[#GIT SWITCH]]

---

### GIT COMMIT --AMEND

Practical scenario to use it: If you commit to a repo, but after some time you notice some typos, or the commit needs formatting changes.

- It lets you fix the last commit **in place**:
	- edit the files,
	- stage the changes,
	- then amend, and Git just updates the last commit as if the typo never happened.
```Bash
[edit files]
git add 
git commit --amend
```

---

### GIT RESET

- undoes at the commit level — moves HEAD, rewrites history

>**Does `git reset` change the working directory?**
Depends on the flag — this is actually the core difference between them:

- **`--soft`** — moves [[Git - Concepts#Difference of main HEAD pointer|HEAD]] only. Your files in the working directory and staging area are **untouched**
- **`--mixed`** _(default, if you don't pass a flag)_ — moves HEAD and clears the staging area, but working directory files are **untouched**
- **`--hard`** — moves HEAD, clears staging, AND **overwrites your working directory** to match the commit. Changes are gone

---

>Scenario 1: You committed, but you noticed some typos, and need to fix them without adding new commits.
```Bash
git reset --soft HEAD~1
[fix typos]
git add .
git commit -m "Your message"
```
This is the manual way of doing it. Use [[#GIT COMMIT --AMEND]] instead.

>Scenario 2: You committed something sensitive, like a file with an API key or password.
```Bash
# this wipes that commit and its changes entirely
git reset --hard HEAD~1 
```

>Scenario 3: You went too far down a wrong path. You made 3 commits experimenting with a feature, it didn't work out, and you want to scrap all of it and start fresh.
```Bash
# go back 3 commits, discard everything
git reset --hard HEAD~3  
```

>Scenario 4: You committed too early / want to reorganize. You made 4 small messy commits locally and want to clean them up into 1 clean commit before pushing.
```Bash
# undo 4 commits, but keep all changes staged
git reset --soft HEAD~4  
git commit -m "one clean, proper commit"
```

---

### GIT SWITCH

*Overview: a command specialized for branch operations*

>[!Example]
>```Bash
>git switch -c firstbranchName commitStartingPoint
># -c = create
># firstBranchName = the name of the branch to create
># commitStartingPoint = starting point/parent of the branch (commit id)
>```

| Commands | Description | Notes |
| :--- | :--- | :--- |
| `git switch main` | moves to the branch named `main` | can also be used to move from other branches, e.g. `git switch experimental-Branch` |
| `git switch -c newBranch Commit2` | Create a branch named `newbranch` starting from `Commit2` (`-c` = create) | use this for experimentation from old commits |
| `git switch --detach c2` | Equivalent of `git checkout c2` | More explicit |

---

### GIT CHECKOUT

*Overview:* An old way of switching to view commits and perform branching.
```Bash
git checkout <commitID>
```
- reflects in the working dir whatever this commit contains.
- the `HEAD` moves to that commit
- but the main pointer is linear only.

>[!Example]
```Bash
c1<-----c2<-----c3 (main)
                    ^HEAD   
git checkout c2

c1<-----c2<-----c3 (main)
         ^HEAD
```

>[!Question] *Can you add and commit while in a detached HEAD state?*
>Yes, but it's not recommended, because the branch pointer isn't pointing to that commit as its parent commit, unless you perform a git switch.

---

>[!Warning] *Example of a NOT RECOMMENDED workflow*
>```Bash
>c1<-----c2<-----c3 (main)
>                     ^HEAD
>```
>
>```Bash
>git checkout c2 #DETACH THE HEAD TO COMMIT 2
>#then edit some files
>git commit 
>```
>
>It creates a temporary timeline:
>```Bash
>c1<-----c2<-----c3 (main)
>          \
>            c4 (HEAD)
>```
>
>Now if you switch to main
>```Bash
>git switch main
>```
>
>It becomes
>```Bash
>c1<-----c2<-----c3 (main)
>                      ^HEAD
>c4 <- a floating orphan
>```

---

>[!Note] Correct way to use git checkout, if you still want to use it.
>*Just don't forget to do a switch after performing the checkout.*
>
>***Example Flow from start***
>```Bash
>c1<-----c2<-----c3 (main)
>                  ^(HEAD)
>```
>
>Then
>```Bash
>git checkout c2
>```
>
>The important part is here:
>```Bash
>#Perform a switch first before editing
>git switch -c "YouNewBranchName"
># or 
>git branch YournewBranchName
>#then edit your files at this point
>commit -m "YourMessage Here"
>```

---
