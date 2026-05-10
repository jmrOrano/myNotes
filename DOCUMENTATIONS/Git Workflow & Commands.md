*Overview: I tend to forgot things when not doing them consistently. Git in particular is easy to understand but but easy also to forget. So ill be adding my questions, setups and etc. here  *

>[!Contents]
>**Overview**
>- [[#Version Control]]
>- [[#Version Control System But with Git]]
>- [[#Branch and Merging in Git]]
>
>**SETUPS**
>- [[#Initial workflow]]
>- [[#Git Command-Marking a dir as safe]]
>- [[#Creating a Git repo]]
>
>**COMMANDS**
>- [[#FOR VIEWING AND COMPARING|For Viewing and Comparing]]
>- [[#**Undoing Commits**]]
>- 
>
>**OTHERS**
>[[#Bakit isinasama ang .gitignore sa git add. ?]]
>
>
>
>
>
>

### **OVERVIEW**
##### Version Control
*Napaka useful method/system para ma revert ang state ng isang project kung sakaling may mistake or undesireable changes.*

**Practical Example of How it works | But no Git, full manual**
- Imagine mo may project ka na nasa thousands ang bilang ng files na nasa multiple directories. Then as you progress, marami ka nang nabago then biglang may mga error/mistake ka na nagawa and hindi mo na alam ayusin. 
- Buti na nalang may na-create ka ng copy ng buong folder ng project mo so you can restore it. *But this can be stressful and impractical kung ang laki ng size ng project mo*
- **Buti na lang may [[Feynman-Technique#Version Control System But with Git| Version Control System]] ka.

##### Version Control System :  But with Git
*Sa pag gamit ng tamang tools for Version Control System like **Git**. Mas efficient dahil :* 
- Hindi mo na need mag create ng multiple copies ng project. 
- With git, nag lo-log lang ito ng mga pag babago and differences sa files
- Not only this allows to have multiple versions throughout the progress ng project mo. Ma aallow pa nito ang collaborations.
- At also may dagdag feature na [[Feynman-Technique#Branch and Merging in Git| Branch and Merging]]

##### Branch and Merging in Git
- Pwede ka gumawa ng mga experimento/possible outcome ng project or bagong features, tapos isasam/*merge* sa main branch ng project pag okay na. 
- Hindi nito na aapektuhan ang main branch ng project ( karaniwang ang *main* o *master*)

---

### SETUPS
#### Initial workflow

|                                                                                               | Desc                                                                                                      |
| :-------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| First is install git. Can look it up online.                                                  |                                                                                                           |
| `git config --global user.name "YourName`<br>`git config --global user.email "yourEmail"`<br> | The initial setup<br>- for it to know kung sino gumagawa ng changes                                       |
| `navigate to your working dir` <br>then do: <br>`git init`                                    | - initialize a git repo sa iyong working dir                                                              |
| `git status`                                                                                  | - this is self explanatory                                                                                |
| add a `.gitignore` file                                                                       | - learn more here [[Feynman-Technique#Bakit isinasama ang .gitignore sa git add. ?\| about `.gitignore`]] |
| `git add . `<br>`git add file1 file2 etc`                                                     | - ang dot `.` = lahat ng files<br>-pwede i specify each ang mga files na may changes lang                 |
| `git commit -m "Your message here" `                                                          | `-m` = ang message or notes mo<br>                                                                        |
| `git push`                                                                                    | to share it sa internet.                                                                                  |

---
#### **Git Command-Marking a dir as safe**
Overview: *usually this is done when a repo is stored in a portable storage, and plugged into different machines.*

```
git config --global --add safe.directory yourDriveLetter:/pathToyourRepo
```

**When to use the command??**
--when using the **`git status`** and lumabas ang message na ito :
```
	`$ git status fatal: detected dubious ownership in repository at 'F:/ObsidianNotes' 
	'F:/ObsidianNotes' is on a file system that does not record ownership To add an exception for this directory, call:
```

	git config --global --add safe.directory F:/ObsidianNotes`

---
#### **Creating a Git repo**
*Ang `github` ay isang platform lamang hindi ito ang git. Maramin pang platform*
1. Pumunta sa Github
2. Click **New Repo**
3. Do not initialize or Do initialize *ReadMe*
4. Then Done
5. You will see instructions like:
	1. `git remote add origin https://github.com/username/project.git`
	2. `git branch -M main`
	3. git push -u origin main


---

### **COMMANDS**

##### **FOR VIEWING AND COMPARING**

| Commands (for Viewing & Comparing)                                                                                                             | Desc                                                                                                        |
| :--------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `git log`                                                                                                                                      | -show commit history                                                                                        |
| `git log --oneline --graph --all`                                                                                                              | -for compact view                                                                                           |
| <br>`git diff`                                                                                                                                 | show diff ng **working dir vs last staged/git add**<br>-may  `-` and `+` sign sa kung ano added and removed |
| `git diff --staged`<br>or<br>`git diff --cached`                                                                                               | -Show diff ng **staged vs last commit**<br>-*usually done before commit*                                    |
| `git diff HEAD`                                                                                                                                | -show diff **working dir + stageing area vs last commit**                                                   |
| `git diff HEAD~1 HEAD`<br>`HEAD~1` - one commit before head<br>`HEAD~2` - two commits before head<br><br>                                      | -compare previous commit to current commit                                                                  |
| `git diff <commit>`<br><br>- for `<commit>` arguments.  Ay pwede ang `commit hash` , `partial hash`,  or `tags` or `main` or `branch name`<br> | -show diff **working dir + stageing area vs specified commit**                                              |
| `git diff commit1 commit2`                                                                                                                     | -compare two commits                                                                                        |
| `git diff --colors-words`                                                                                                                      | -ipakita lang ang pagbabago sa words hindi buong line diff                                                  |

##### LISTING BRANCHES
| Commands          | Description                    |
| :---------------- | ------------------------------ |
| `git branch -r`   | Remote branches sa remote repo |
| `git branch -a`   | All branches (local + remote)  |
| `git show-branch` | For quick view                 |


##### **Undoing Commits**

|                         Commands                          | desc                                                                                                                                                                                         |
| :-------------------------------------------------------: | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                   `git restore <file>`                    | -*undo ang changes sa working dir(di pa commit)*<br>-ibabalik ang file from latest commit to your working dir.<br>-kung may na edit pero gusto tanggalin ito. Ito ang gamitin.               |
|               `git restore --staged <file>`               | -*undo ang mga na git add na*<br>--tanggalin ang file sa staging area **hindi binabago ang working dir**<br>-useful kung na add by accidents                                                 |
|                 `git checkout -- <file>`                  | -undo from staged to working dir<br>-mag rereflect sa working dir ang staged file.                                                                                                           |
|            `git restore --source=HEAD <file>`             | -undo from latest commit to working dir<br>-mag rereflect sa working dir ang file from file na commited.                                                                                     |
| `git restore --source=HEAD --staged --worktree -- <file>` | -*undo changes both working dir and sa staging area*<br>1. kukuha ng version mula sa HEAD commit.<br>2. ilalagay ang source ver sa staging area<br>3. Ilalagay ang source ver sa working dir |
|                                                           |                                                                                                                                                                                              |
|                                                           |                                                                                                                                                                                              |

### **GIT CHECKOUT**
*Overview: * An old way of switching to view commits and perform branching.
```Bash
git checkout <commitID>
```
- mag rereflect sa working dir kung ano laman ng commit na ito.
- ang `HEAD` ay lilipat sa commit na yon
- But ang main pointer is linear lang. 
>[!Example]
```Bash
c1<-----c2<-----c3 (main)
                    ^HEAD   
git checkout c2

c1<-----c2<-----c3 (main)
         ^HEAD
```
	
>[!Question] *Can you do add and commit pag detached HEAD state?*
>Yes, pero not recommended dahil di naka point and branch pointer sa commit na iyon sa parent commit, unless mag perform ng git switch.

>[!Warning] *Example of NOT RECOMMENDED workflow*
>```Bash
>c1<-----c2<-----c3 (main)
>                     ^HEAD
>```
>
>```Bash
>git checkout c2 #DETAACH THE HEAD TO COMMIT 2
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

>[!Note] Correct Way use of git checkout if youre still eager to use it.,
>*The just dont forget to do switch after performing the checkout
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
>commit -m "YourMessge Here"
>```
>


### GIT SWITCH 
*Overview: a command specialize for branch operations*

>[!Example] 
>```Bash
>git switch -c firstbranchName commitStaratingpoint
># -c = create
> # firstBranchName = pangalan ng createBranch
> # commitStaratingpoint = starting point/Parent ng branch (commit id)
>```

|                     Commands                      | Description                                                               |                                                                                   |
| :-----------------------------------------------: | ------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
|                 `git switch main`                 | moves to branch named `main`                                              | it can also use to move from other branches like `git switch experimental-Branch` |
| `git switch -c newBranch Commit2`<br><br><br>`-c` | Create branch named `newbranch` from start from `Commit2`<br><br>= create | use this for experimentation from old commits                                     |
|             `git switch --detach c2`              | Equivalent of `git checkout c2`                                           | More explicit                                                                     |


**`git restore`**
-gamit para i-undo o -revert ang changes sa `working dir or staging area`. 

***Basic Concept***	
- May dalawang lugar kung saan pwede may changes:
	1. Working dir - mga files na nagbago pero di pa na git add.
	2. Staging Area - files na na-add na pero di pa na commit.

**COMMON COMMANDS**

|                                                                    Commands                                                                    | Description                                                                                              |                                       |
| :--------------------------------------------------------------------------------------------------------------------------------------------: | -------------------------------------------------------------------------------------------------------- | ------------------------------------- |
|                                                             `git restore file.txt`                                                             | Restore the file sa pinaka huling commit na version                                                      |                                       |
|                                                        `git restore --staged file.txt`                                                         | - Remove the file in staging area.<br>- But doesnt change the working directory                          | Useful when added a file by accident. |
|                                                      `git restore --source=HEAD file.txt`                                                      | -Restore the file using the specified commit as reference point<br><br>- Directly applies to working dir |                                       |
| `git restore --source=HEAD --staged --worktree file.txt`<br><br><br>*the target file can change to `.` to target all files in current folder.* | - Restore the file to exact state of last commit.<br>- Applies both sa stage and working dir.            |                                       |
|                                                                   FLAGS USE                                                                    |                                                                                                          |                                       |
|                                                               `--source=<tree>`                                                                | - For reference point of which commit you want to get.<br>- The `<tree>` can be comit ID                 |                                       |
|                                                                  `--worktree`                                                                  | Use for applying the changes to the working directory                                                    |                                       |
|                                                                   `--staged`                                                                   | Use for applying the changes to staging area only                                                        |                                       |


---

##### Bakit isinasama ang .gitignore sa git add. ? 
-  Ang **`.gitignore`** ay isang _configuration file_ na nagsasabi kay Git kung aling mga files/folders ang hindi dapat i-track.
- Ang **`.gitignore`** ay hindi lang basta lagayan ng mga ignored files for **`git add.`** . Ito rin ang mag sisilbing automatic rules para sa mga collaborators. To avoid accidentally commit ng build files or secrets 
- Ang **`.gitignore`** file ay: 
	- Hindi personal preferences
	- Hindi system level config
	- Bahagi ito ng strucutre ng project  


#### Difference of `main`, `HEAD`, `pointer`

**MAIN**
Sample Model:
```Commits
A<---B<---C<---D
```
The  `main` is only a branch name.
Example: 
```
main -->
```
Meaning:
- The branch `main` is currently points to commit `D`
- When you commit again it automatically moves.
- ```Commits
	  A<---B<---C<---D<---E 
                       ^main
  ```

**HEAD**
*This is where you are currently.*
Usually pointing to branch not directly to commits

**POINTER**
*A general concept only*
- Branches are pointers
- `Head` is also a pointer
- Its because both of them points to commits or branches