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
>
>**OTHERS**
>[[#Bakit isinasama ang .gitignore sa git add. ?]]
>
>
>
>
>

### **OVERVIEW**
##### Version Control
----Napaka useful method/system para ma revert ang state ng isang project kung sakaling may mistake or undesireable changes.

**Practical Example of How it works | But no Git, full manual**
----Imagine mo may project ka na nasa thousands ang bilang ng files na nasa multiple directories. Then as you progress, marami ka nang nabago then biglang may mga error/mistake ka na nagawa and hindi mo na alam ayusin. 
---Buti na nalang may nag create ka ng copy ng buong folder ng project mo so you can restore it. *But this can be stressful and impractical kung ang laki ng size ng project mo*
----**Buti na lang may [[Feynman-Technique#Version Control System But with Git| Version Control System]] ka.

##### Version Control System :  But with Git
----Sa pag gamit ng tamang tools for Version Control System like ***Git***. Mas efficient dahil : 
- Hindi mo na need mag create ng multiple copies ng project. 
- With git, nag lo-log lang ito ng mga pag babago and differences sa files
- Not only this allows to have multiple versions throughout the progress ng project mo. Ma aallow pa nito ang collaborations.
- At also may dagdag feature na [[Feynman-Technique#Branch and Merging in Git| Branch and Merging]]

##### Branch and Merging in Git
----pwede ka gumawa ng mga experimento/possible outcome ng project or bagong features, tapos isasam/*merge* sa main branch ng project pag okay na. 
-----Hindi nito na aapektuhan ang main branch ng project ( karaniwang ang *main* o *master*)

---

### SETUPS
##### Initial workflow

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
##### **Git Command-Marking a dir as safe**
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
##### **Creating a Git repo**
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

|                                                       Commands (for Viewing & Comparing)                                                       | Desc                                                                                                        |
| :--------------------------------------------------------------------------------------------------------------------------------------------: | ----------------------------------------------------------------------------------------------------------- |
|                                                                   `git log`                                                                    | -show commit history                                                                                        |
|                                                        `git log --oneline --graph -all`                                                        | -for compact view                                                                                           |
|                                                                   `git diff`                                                                   | show diff ng **working dir vs last staged/git add**<br>-may  `-` and `+` sign sa kung ano added and removed |
|                                                `git diff --staged`<br>or<br>`git diff --cached`                                                | -Show diff ng **staged vs last commit**<br>-*usually done before commit*                                    |
|                                                                `git diff HEAD`                                                                 | -show diff **working dir + stageing area vs last commit**                                                   |
|                   `git diff HEAD~1 HEAD`<br>`HEAD~1` - one commit before head<br>`HEAD~2` - two commits before head<br><br>                    | -compare previous commit to current commit                                                                  |
| `git diff <commit>`<br><br>- for `<commit>` arguments.  Ay pwede ang `commit hash` , `partial hash`,  or `tags` or `main` or `branch name`<br> | -show diff **working dir + stageing area vs specified commit**                                              |
|                                                           `git diff commit1 commit2`                                                           | -compare two commits                                                                                        |
|                                                           `git diff --colors-words`                                                            | -ipakita lang ang pagbabago sa words hindi buong line diff                                                  |

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

**`git checkout <commitID>`**
--mag rereflect sa working dir kung ano laman ng commit na ito.
--ang `HEAD` ay lilipat sa commit na yon
--But ang main pointer is linear lang. 
*Example:*
		`c1<-----c2<-----c3<Pointer & HEADpointer`
		`git checkout c2`
		`c1<-----c2[head]<-----c3<Pointer`
		
*Can you do add and commit pag detached HEAD state?*
--Yes, pero not recommended dahil di ito technically connected sa parent commit, unless mag perform ng branching. 
*Example workflow*
	`c1<-----c2<-----c3<Pointer & HEADpointer`
	`git checkout c2`
	`c1<-----c2[head]<-----c3<Pointer`
	`git add . `
	`git commit -m "new branch"`
	`git switch -c firstBranch`
*Modern workflow*
	`c1<-----c2<-----c3<Pointer & HEADpointer`
	`git switch -c <branchName> <targetCommitParent>`
	`git add`
	`git commit -m "message" `
. Usually `branching` ay ginagawa kung mag pperform ng experimental thing to seperate sa main.

**`git restore`**
-gamit para i-undo o -revert ang changes sa `working dir or staging area`. 
1. *Basic Concept*
	-May dalawang lugar kung saan pwede may changes:
		1. Working dir - mga files na nagbago pero di pa na git add.
		2. Staging Area - files na na-add na pero di pa na commit.




---

##### Bakit isinasama ang .gitignore sa git add. ? 
-  Ang **`.gitignore`** ay isang _configuration file_ na nagsasabi kay Git kung aling mga files/folders ang hindi dapat i-track.
- Ang **`.gitignore`** ay hindi lang basta lagayan ng mga ignored files for **`git add.`** . Ito rin ang mag sisilbing automatic rules para sa mga collaborators. To avoid accidentally commit ng build files or secrets 
- Ang **`.gitignore`** file ay: 
	- Hindi personal preferences
	- Hindi system level config
	- Bahagi ito ng strucutre ng project  



