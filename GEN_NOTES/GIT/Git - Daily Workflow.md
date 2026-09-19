*Part of the Git notes. See also: [[Git - Concepts]], [[Git - Setup]], [[Git - Undo (Before Commit)]], [[Git - Undo (After Commit)]], [[Git - Inspecting]]*

>[!Contents]
>- [[#Main flow]]
>- [[#git add and git commit]]
>- [[#GIT PUSH]]
>- [[#GIT CLONE]]
>- [[#GIT PULL]]

---

### Main flow

```
git init  →  Working Dir  →  git add  →  Staging Area  →  git commit  →  History  →  git push
  (setup)      (your changes)              (preparing)                   (save point)   (share online)
```

This is the core loop of Git — the normal, day-to-day sequence. `git init` is a one-time step, covered in [[Git - Setup]]. If something goes wrong at any point in this flow, check [[Git - Undo (Before Commit)]] or [[Git - Undo (After Commit)]] depending on whether you've committed yet.

---

### git add and git commit

|                                             | Desc                                                                          |
| :------------------------------------------- | -------------------------------------------------------------------------------- |
| `git add . `<br>`git add file1 file2 etc`   | - the dot `.` = all files<br>- you can specify each file that has changes only  |
| `git commit -m "Your message here" `        | `-m` = your message or notes                                                     |

---

### GIT PUSH

A command used to upload the local repo to the remote repository

##### **Remote Github method**
`Github` is one of the platforms that everyone can use to upload their repo publicly
1. Go to Github
2. Click **New Repo**
3. Do not initialize, or do initialize a *ReadMe* (for a beginner's workflow)
4. Then Done
5. You will see instructions like:
```Bash
 git remote add origin https://github.com/username/project.git
	 # the `origin` is NOT special, it's just like a shortcut name
	 # it's like telling the local repo this is its new remote home
	 # you could do:
	 # git remote add randomBS https://github.com/username/project.git
	 
 git branch -M main  
	 # renames the current branch to `main` 
	 # unnecessary if your branch name is already main
	 
 git push -u origin main  
	 # push the local branch named `main` to remote origin
	 # the flag -u means it remembers the connection
	 # with flag -u you can just do `git push` next time 
	 # the `origin` is NOT special, it's just like a shortcut name
```

---

##### **Self-hosted remote**

With GitHub, we normally create the remote repository through the webpage UI. This is what to do instead if you're hosting your own remote (e.g. on your homelab).

>==**Creating a bare repository on a personal server**==

Abstract Architecture:
```
             Git
              │
        git clone/push/pull
              │
              ▼
        ┌───────────┐
        │ Transport │
        └───────────┘
          │       │
       HTTPS     SSH
          │       │
          ▼       ▼
       Server   Server
```

> On the SSH server side:
```bash
mkdir -p ~/git
cd ~/git
git init --bare <project-name.git>
```
`--bare` is specifically meant to be a remote repo. It DOESN'T HAVE A WORKING DIRECTORY containing the actual project files.

---

>On the local machine side:
Add the server as the remote:
```bash
git branch -M main
git remote add randomBS username@your-server:~/git/my-project.git
git push -u randomBS main
```

---

### GIT CLONE

*Use for first-time download/copy of a certain repo to your local machine — this is how you enter the [[#Main flow]] when a remote repo already exists.*

```
             Git
              │
        git clone/push/pull
              │
              ▼
        ┌───────────┐
        │ Transport │
        └───────────┘
          │       │
       HTTPS     SSH
          │       │
          ▼       ▼
       Server   Server
```

---

>This creates a new directory in the current directory location.
It uses `HTTPS` as its transport protocol
```
git clone https://github/com/username/reponame.git
```

>This uses `SSH` as its transport protocol
```
git clone ssh://username@server.com:custom_port/path/to/repo.git
```
***Note***: You don't necessarily need a dedicated **"Git service"** running on the remote machine.
It only needs:
- Git installed on the remote machine
```
Your machine                         Remote machine
─────────────                        ───────────────

git clone
    │
    │ SSH connection
    ├──────────────────────────────► sshd
    │
    │                              authenticates user
    │
    │                              runs Git command
    │
    │◄───────────────────────────── Git data
    │
local .git + working tree
```

>If you want a different folder name
```
git clone https://github/com/username/reponame.git reponamecopy
```

---

### GIT PULL

*Use if you already have an existing local copy of a certain remote repo and you want to get the latest changes from the remote.*

*(Details still to be expanded — see [[#GIT CLONE]] for the transport diagram, which applies here too.)*

---
