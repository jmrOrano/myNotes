*Part of the Git notes. See also: [[Git - Concepts]], [[Git - Daily Workflow]], [[Git - Undo (Before Commit)]], [[Git - Undo (After Commit)]], [[Git - Inspecting]]*

>[!Contents]
>- [[#First-time setup]]
>- [[#git init]]
>- [[#.gitignore]]
>- [[#Marking a dir as safe]]

---

### First-time setup

|                                                                                                    | Desc                                                        |
| :------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| First is install git. You can look it up online.                                                   |                                                              |
| `git config --global user.name "YourName"`<br><br>`git config --global user.email "yourEmail"`<br> | The initial setup<br>- for it to know who made the changes  |

---

### git init

|                                                          | Desc                                          |
| :-------------------------------------------------------- | ------------------------------------------------ |
| `navigate to your working dir` <br>then do: <br>`git init` | - initialize a git repo in your working dir      |
| `git status`                                               | - this is self explanatory                       |

Once you've run `git init`, the next steps (add, commit, push) are covered in [[Git - Daily Workflow]].

---

### .gitignore

- add a `.gitignore` file to your project — learn more about *why* it matters in [[Git - Concepts#Why is .gitignore included with git add?]]
- this is a one-time setup step per project, usually done right after `git init`

---

### Marking a dir as safe

Overview: *usually needed when a repo is stored on portable storage and plugged into different machines.*

```
git config --global --add safe.directory yourDriveLetter:/pathToyourRepo
```

**When to use this command?**
- When using **`git status`** and this message shows up:
```
	`$ git status fatal: detected dubious ownership in repository at 'F:/ObsidianNotes' 
	'F:/ObsidianNotes' is on a file system that does not record ownership To add an exception for this directory, call:
```

	git config --global --add safe.directory F:/ObsidianNotes`

---
