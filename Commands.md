use h4
#### Some useful command RANDOM

|                   Commands                   | Desc                                                                                                                    |
| :------------------------------------------: | ----------------------------------------------------------------------------------------------------------------------- |
|             the **`inxi`** tool              | *to show complete info ng system in readable format*                                                                    |
|                **`inxi -F`**                 | Display Full system info                                                                                                |
|     **`inxi -Fxz`**<br>**`inxi -Fxxxz`**     | Full + extra details<br>`-F` - *full output*<br>`-x` - *extra details*<br>`-z` - *hide sensitive info (IP, MAC Address* |
|                **`inxi -G`**                 | Display:<br>`-GPU Model`<br>`-Driver in use`<br>`-Res`<br>`-Refresh Rate`                                               |
|                    `lshw`                    | list hardware                                                                                                           |
|         `dpkg -l \| grep <appname>`          | check if app is installed                                                                                               |
| or  `apt list --installed \| grep <appname>` |                                                                                                                         |
|              `which <appname>`               | check if the executable is in path. Mostly useful for checking path configuration.                                      |

|   **NETWORKING COMMANDS**    |                                                                                                                                    |
| :--------------------------: | ---------------------------------------------------------------------------------------------------------------------------------- |
|             `ss`             | Socket statistics - a faster and more detailed replacement for netstat. Shows active connection and listening ports                |
|           `ss -ol`           | Display `established` and `listening`                                                                                              |
|       `mtr google.com`       | combination of `ping` and `traceroute` that provides continuous, real time and report of network response and connectivity to host |
|       `ip route show`        | Display the kernel IP routing table.                                                                                               |
|       `dig google.com`       | query DNS name servers and retrieving detaled information about DNS record                                                         |
|        `lshw -C net`         |                                                                                                                                    |
|           ifconfig           | display ip, mac addr info etc                                                                                                      |
| `iwconfig yourinterfacename` | display info about sa networ interface.                                                                                            |
	
use H4 for titles 
#### The *`lsblk`*  command
Sample output: 
`*sdc      8:32   1   3.8G  0 disk `*
*`└─sdc1   8:33   1   3.7G  0 part /media/linuxuser/F4FD-93EE`*

|                             | Desc                           |
| :-------------------------: | ------------------------------ |
|   sdc 8:32 1 3.8G 0 disk    | - Ito yung mismong USB Drive   |
| \|-sdc1 8:833 1 3.7G 0 part | - ito yung partition           |
|      /media/linuxuser/      | -Dito naka mount ang partition |
|          F4FD-93EE          | -Ito yung name ng USB          |

**PANO SAFELY EJECT AND USB DRIVE? **

*unmount muna ang partition*
**`sudo umount /dev/sdc1`**

*then power off the device*
**`sudo udisksctl power-off -b /dev/sdc`**

|                                            Flags / Options                                             | Desc                                         |
| :----------------------------------------------------------------------------------------------------: | -------------------------------------------- |
|                                                   -b                                                   | -- block-device : ang ibibigay mong argument |
| sa linux, lahat ng hardware devices ay ***nirerepresent bilang files*** sa loob ng directory na : /dev |                                              |
|                                                                                                        |                                              |


----



#### Git Command-Marking a dir as safe
`git config --global --add safe.directory yourDriveLetter:/pathToyourRepo`

**WHEN TO USE??**
--when using the **`git status`** and lumabas ang message na ito :

	`$ git status fatal: detected dubious ownership in repository at 'F:/ObsidianNotes' 
	'F:/ObsidianNotes' is on a file system that does not record ownership To add an exception for this directory, call:
	    
	git config --global --add safe.directory F:/ObsidianNotes`

---

#### Some Basic Git command or Workflow

**COMMON WORKFLOW**

| Command (for initial workflow)                                                                | Desc                                                                                                      |
| :-------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `git config --global user.name "YourName`<br>`git config --global user.email "yourEmail"`<br> | The initial setup<br>- for it to know kung sino gumagawa ng changes                                       |
| `navigate to your working dir` <br>then<br>`git init`                                         | - initialize a git repo sa iyong working dir                                                              |
| `git status`                                                                                  | - this is self explanatory                                                                                |
| add a `.gitignore` file                                                                       | - learn more here [[Feynman-Technique#Bakit isinasama ang .gitignore sa git add. ?\| about `.gitignore`]] |
| `git add . `<br>`git add file1 file2 etc`                                                     | - ang dot `.` = lahat ng files<br>-pwede i specify each ang mga files na may changes lang                 |
| `git commit -m "Your message here" `                                                          | `-m` = ang message or notes mo<br>                                                                        |
| `git push`                                                                                    | to share it sa internet.                                                                                  |

**GIT DIFF**

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
**CREATING GITHUB REPO**
*Ang `github` ay isang platform lamang hindi ito ang git. Maramin pang platform*
1. Pumunta sa Github
2. Click **New Repo**
3. Do not initialize or Do initialize *ReadMe*
4. Then Done
5. You will see instructions like:
	1. `git remote add origin https://github.com/username/project.git`
	2. `git branch -M main`
	3. git push -u origin main

#### GIT UNDOING COMMITS

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
*Commands*

|                         Commands                          | desc                                                                                                                                                                                         |
| :-------------------------------------------------------: | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                   `git restore <file>`                    | -*undo ang changes sa working dir(di pa commit)*<br>-ibabalik ang file from latest commit to your working dir.<br>-kung may na edit pero gusto tanggalin ito. Ito ang gamitin.               |
|               `git restore --staged <file>`               | -*undo ang mga na git add na*<br>--tanggalin ang file sa staging area **hindi binabago ang working dir**<br>-useful kung na add by accidents                                                 |
|                 `git checkout -- <file>`                  | -undo from staged to working dir<br>-mag rereflect sa working dir ang staged file.                                                                                                           |
|            `git restore --source=HEAD <file>`             | -undo from latest commit to working dir<br>-mag rereflect sa working dir ang file from file na commited.                                                                                     |
| `git restore --source=HEAD --staged --worktree -- <file>` | -*undo changes both working dir and sa staging area*<br>1. kukuha ng version mula sa HEAD commit.<br>2. ilalagay ang source ver sa staging area<br>3. Ilalagay ang source ver sa working dir |
|                                                           |                                                                                                                                                                                              |

---

#### About the *`cp and mv`* command
*This is one the essential commands. So taking note of this is i think necessary*

**Reminders**
- **`mv`** and **`cp`** works both for files and directory.
- if same path, it just `renames` or `updates`. No dups or extra space consumed.
	- example : `you're in /home/username/Desktop`
		- `mv bigFolder [space] /home/username/Desktop`
- if the path is diff file system. It behaves recursively .
- in using ***`cp`*** to copy dir to another. using the `-r --recursively` is a must
	- example: `you're in /home/username/Desktop`
		- `cp -r movethisDir/ [space] tothisDir/`
			- or you can set the destination/target first 
		- `cp -r -t tothisDir/ [space] movethisDir/`

Running the `--help` is good, but can be overwhelming. So, i'll list some commonly use flags

- `-v --verbose` : ipapakita sa terminal kung ano ginagawa ng mv
- `-i --interactive` : for yes or no option kung may something come up
- `-n -- no-clobber` : cancel ang mv kung may exisiting folder sa dest. 
- `-t --setTheDESTDir first` : 

---


#### Tips in *`cut`* command
The **`cut`** command outputs in new line if the text has multiple lines.

*What if you want to put each delimiter in new line. But the text is only a single-line?*

--Example : in normal method
**`echo $ PATH | -d":" -f1`**
will only output a single entry dahil ang **`echo $PATH`** is a single line lang.

--**Answer** : use ***`tr`*** command
**`echo $PATH | tr ':' '\n'

--**Explanation**
with `translate tr` command. It basically just replace the colon ***`:`*** with the new line ***`\n`***
`[TR USAGE]` : **`tr [space] ["replaceThis"] [space] ["withThis"]

---

#### Tip in *`find`* command
Pattern : **`find /inthisPath -options or Flags "whatYourelookingFor"`**

**Options or Flags**

|        Options        | Desc                                                                      |
| :-------------------: | ------------------------------------------------------------------------- |
| `. -name "yungname*"` | - kung di alam ang file extension<br>- ang dot `.` represents curr dir    |
|   `. -name "*log"`    | -hanapin lahat ng files na may `log` ang name<br>- ang `*` represents all |
|  `. -name "*.conf"`   | -lahat ng `.conf` na files<br>                                            |
|       .  -iname       | - `case-INsensitive`<br>kung di sure sa uppercase or lower                |
|         .  -f         | Kung files lang                                                           |
|         .  -d         | kung dir lang                                                             |


---

#### THE `grep` Command
Pinaka useful for searching patterns.
**find vs grep**
 - **`find`** for files
 - **`grep`** is for pattern sa loob ng text
 
 **How to use**

|          Command          | Desc                                                                                            |
| :-----------------------: | ----------------------------------------------------------------------------------------------- |
|  `grep "word" file.txt`   | -common pang search                                                                             |
| `grep -i "word" file.txt` | - for *`ignore case sensitive`*                                                                 |
| `grep -n "word" file.txt` | - display line number                                                                           |
| `grep -r "word" /youDir`  | -searches all `recursively` in the target dir.<br>- this includes inside the files in a subDir. |
| `grep -w "word" file.txt` | - hanapin ang exact word                                                                        |
|   `grep "^a" file.tx `    | -lahat ng nagsisimula sa 'a'                                                                    |
|    `ls \| grep "word"`    | -filter ang output ns ls using pipe                                                             |
| `ps aux \| grep firefox`  | - hanaping ang running process                                                                  |

**When to Use?**
- Variable sa code
- Hanapin error sa logs
- Hanapin ang output ng ls
- Hanapin ang process


---

#### User Management Basic Command
1. **`useradd`** :  `sudo useradd bob`
		--*Flow*
			1. root is required since may babaguhin sa system files tulad ng 
				1./etc/passwd/
				2. /etc/shadow
				3. /etc/group
			2. add ang user sa `/etc/passwd`
				`bob:x:1001:1001:Bob User:/home/bob:/bin/bash`
			3. add ng entry sa `/etc/shadow`
				bob:$6$ab23kd...hashedpassword...:19800:0:99999:7:::
			4. add ng entry sa `/etc/group`
				bob:x:1001:
			5. gagawa ng homedir at /home/dir
			
2. **`userdel`** : 
		`sudo userdel bob` : may not delete the home dir so use the `-r` flag
		
3. **`passwd`** : `passwd bob`
	- for changing password
	-  can be done by nomal users but a prompt pass will appear
	- can be done by root without any prompt pass

#### Chmod command
-- a powerful command to change file permissions.
-- There are **two main methods** 

1. **Symbolic Mode** - more readable
	-it uses letter to represent user. 
	-Specify first which permission set you want to change then use a `+` to add permission or `-` to remove it.
	- **`u`** - user
	- **`g`** - group
	- **`o`** - others
	- **`a`** - all : user, group and others

|        Command         | Desc                                                                            |
| :--------------------: | ------------------------------------------------------------------------------- |
| `chmod u+x` myfile.txt | u for user/owner and x for execute. <br>it add execute permission to user/owner |
|      `chmod u-r`       | removes read permission to user                                                 |
|      `chmod ug+w`      | give write permission to user and groups.                                       |
|      `chmod a+r`       | gives read permission to all                                                    |
2. **Numericl (Octal Mode)**
	-Mas powerful and speed compare to symbolic but,
	-You need basic math to do this
	-Sets all the permission at one time for all user , groups and others, with only 3 digit number.
	- **`4`** - read (r)
	- **`2`** - write (w)
	- **`1`** - execute (x)
	
	read about [[[Whatis#The User User Management Files|User Management and Files]] first to understand better at this point

|         Command          | Desc                                                                                     |
| :----------------------: | ---------------------------------------------------------------------------------------- |
| **`chmod 755 file.txt`** | - first digit is `7` represent to user.<br><br>-- its 4+2+1. Represents (rwx) permission |
|                          | - second digit  is 5 for groups<br><br>-- its 4+1. Represents(r-x) permission            |
|                          | - third digit is 5 again. Similar to groups permission                                   |
|                          |                                                                                          |
| **`chmod 760 file.txt`** | - first digit is `7` represent to user.<br><br>-- its 4+2+1. Represents (rwx) permission |
|                          | - second digit  is 6 for groups<br><br>-- its 4+2. Represents(rw-) permission            |
|                          | third is set to 0 for others.<br>- no permission to r,w,x                                |


---

#### The chown command 
**`chown - change owner`**
--to simply *transfer ownership of file to a differenct user*. 
-- useful for user's responsibilities change.
-- need [[[Feynman-Technique#Using sudo vs logging in as root| sudo]] to change owner a file you dont own


|               command                | desc                                                |
| :----------------------------------: | --------------------------------------------------- |
|     `sudo chown user2 file.txt`      | change the owner to user2                           |
|     `sudo chgrp group2 file.txt`     | change the group ownsership to group2               |
| `sudo chown user2:group2 file.txt `  | change both user and group ownership                |
|                                      |                                                     |
| *Can group members edit permission?* | No, only file owner or root can impose permissions. |
|  *Need ba ang user ay nasa group?*   | Nope, both are different.                           |
#### The umask command
-- change the permission sa mga susunod na created file or dir
-- base sa session ng terminal, meaning once run sa terminal its now active.

*Example*

|                                                                         command                                                                         | desc                                                                                                                                                                                  |
| :-----------------------------------------------------------------------------------------------------------------------------------------------------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                          every file has default permission of                                                           | `666` (rw- rw- rw-)                                                                                                                                                                   |
|                                                           every dir has default permission of                                                           | `777` (rwx rwx rwx)                                                                                                                                                                   |
|                                                                           BUT                                                                           | BUT                                                                                                                                                                                   |
|                                                      sa fresh an terminal. <br>run : `umask 0221`                                                       | this command removes <br> 0 value sa user<br> 2 value sa groups<br> 2 values sa others                                                                                                |
| Now the the default for this session in terminal is set to<br>`755` (rwx rw- rw-) for directories<br>`644` for new files to be created in this session. |                                                                                                                                                                                       |
|                                                                                                                                                         | very useful para sa opening accidental wide open permission.<br>Also para sa starting project and auto set ang permission, easy for maintaining permissions and konting tweak na lang |

---

#### The ps command
-- useful for understanding Linux Processes
--though `htop` , `top` , `btop` is real time monitoring, this is static snapshop
--output shows a few key details: 

**PID** : The unique Process ID
**TTY** : The controlling terminal for the process
**STAT** : The current status of the process
**TIME** : The total CPU time the process has used
**CMD** : The command that started the process

|                      Flags                       | desc                                                                                                                                      |
| :----------------------------------------------: | ----------------------------------------------------------------------------------------------------------------------------------------- |
|                     `ps aux`                     | --a -shows the process from all users<br>--u -user-oriented format (may cpu, MEM,etc)<br>--x -include processes without terminal(daemons) |
| `ps axjf`<br>--*this shows in hierrarchy format* | --x include processes that do not have controlling terminal(TTY)<br>--f -full format listing                                              |
|      `ps aux --sort=-%cpu`<br>`--sort=pid`       |                                                                                                                                           |
|                    `-p 1234`                     | - check specific id                                                                                                                       |
|                      `-eL`                       | - shows threads                                                                                                                           |
|                  pstree -p 1234                  |                                                                                                                                           |
|               `pstree -linuxuser`                |                                                                                                                                           |
