*Part of the Git notes. See also: [[Git - Setup]], [[Git - Daily Workflow]], [[Git - Undo (Before Commit)]], [[Git - Undo (After Commit)]], [[Git - Inspecting]]*

>[!Contents]
>- [[#Version Control]]
>- [[#Version Control System But with Git]]
>- [[#Branch and Merging in Git]]
>- [[#Difference of main HEAD pointer]]
>- [[#What is origin]]
>- [[#Why is .gitignore included with git add]]

---

### Version Control
*A very useful method/system for reverting the state of a project in case of a mistake or unwanted changes.*

**Practical Example of How it Works | But No Git, Full Manual**
- Imagine you have a project with thousands of files across multiple directories. As you progress, you change a lot of things, then suddenly you make an error/mistake that you don't know how to fix.
- Good thing you created a copy of the whole project folder so you can restore it. *But this can be stressful and impractical if your project is large.*
- **Good thing there's [[#Version Control System But with Git|Git Version Control]].**

### Version Control System : But with Git
*Using the right tool for version control, like **Git**, is much more efficient because:*
- You no longer need to create multiple copies of the project.
- With git, it only logs the changes and differences in files.
- This doesn't just let you have multiple versions throughout your project's progress — it also allows for collaboration.
- It also has an added feature: [[#Branch and Merging in Git]]

### Branch and Merging in Git
- You can create experiments/possible outcomes for the project, or new features, then merge them into the main branch once they're ready.
- This doesn't affect the main branch of the project (usually called *main* or *master*) until you merge.

---

### Difference of `main`, `HEAD`, `pointer`

**MAIN**
Sample Model:
```Commits
A<---B<---C<---D
```
The `main` is only a branch name.
Example:
```
main -->
```
Meaning:
- The branch `main` currently points to commit `D`
- When you commit again it automatically moves.
- ```Commits
	  A<---B<---C<---D<---E 
                       ^main
  ```

**HEAD**
*This is where you currently are.*
Usually points to a branch, not directly to commits.

**POINTER**
*A general concept only*
- Branches are pointers
- `HEAD` is also a pointer
- This is because both of them point to commits or branches

---

### What is `origin`?
You'll most commonly see it show up when pushing to a remote repo (see [[Git - Daily Workflow#GIT PUSH]]).
`origin` is just a **nickname** for a remote repo's URL — nothing more. 

- When you do `git clone`, Git automatically creates this nickname for you and names it `origin` by default.
- It became the *default* name purely as a convenience — so you don't have to type or remember the full URL every time you push/pull. Instead of typing `git push git@github.com:username/project.git main` every time, you just do `git push origin main`.
- `origin` is not a reserved word — you can rename it, or use a completely different nickname for a different remote.

**Practical example: one project, two remotes**

You can have the same local repo push to two different destinations — like your personal homelab server AND GitHub — just by giving each one a different nickname:

```bash
git remote add homelab git@server:~/git/project.git   # your homelab
git remote add github https://github.com/username/project.git   # GitHub
```

Then you choose *where* to push using the nickname:
```bash
git push homelab main    # pushes to your homelab
git push github main    # pushes to GitHub
```

Same repo folder, same commits — just two different remote destinations.

---

### Why is .gitignore included with git add?

- **`.gitignore`** is a *configuration file* that tells Git which files/folders should not be tracked.
- **`.gitignore`** isn't just a place to list ignored files for **`git add .`** — it also acts as an automatic rule set for collaborators, to avoid accidentally committing build files or secrets.
- The **`.gitignore`** file is:
	- Not a personal preference
	- Not a system-level config
	- It's part of the project's structure

---
