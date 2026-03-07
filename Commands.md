use h4
#### Some useful command RANDOM

|               Commands               | Desc                                                                                                                    |
| :----------------------------------: | ----------------------------------------------------------------------------------------------------------------------- |
|         the **`inxi`** tool          | *to show complete info ng system in readable format*                                                                    |
|            **`inxi -F`**             | Display Full system info                                                                                                |
| **`inxi -Fxz`**<br>**`inxi -Fxxxz`** | Full + extra details<br>`-F` - *full output*<br>`-x` - *extra details*<br>`-z` - *hide sensitive info (IP, MAC Address* |
|            **`inxi -G`**             | Display:<br>`-GPU Model`<br>`-Driver in use`<br>`-Res`<br>`-Refresh Rate`                                               |
|                                      |                                                                                                                         |

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

| Command (for initial workflow)                                                                | Desc                                                                                                      |
| :-------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `git config --global user.name "YourName`<br>`git config --global user.email "yourEmail"`<br> | The initial setup<br>- for it to know kung sino gumagawa ng changes                                       |
| `navigate to your working dir` <br>then<br>`git init`                                         | - initialize a git repo sa iyong working dir                                                              |
| `git status`                                                                                  | - this is self explanatory                                                                                |
| add a `.gitignore` file                                                                       | - learn more here [[Feynman-Technique#Bakit isinasama ang .gitignore sa git add. ?\| about `.gitignore`]] |
| `git add . `<br>`git add file1 file2 etc`                                                     | - ang dot `.` = lahat ng files<br>-pwede i specify each ang mga files na may changes lang                 |
| `git commit -m "Your message here" `                                                          | `-m` = ang message or notes mo<br>                                                                        |
| `git push`                                                                                    | to share it sa internet.                                                                                  |

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

|         Options          | Desc                                                                      |
| :----------------------: | ------------------------------------------------------------------------- |
|  `. -name "yungname*"`   | - kung di alama ang file extension<br>- ang dot `.` represents curr dir   |
|     `. -name "*log"`     | -hanapin lahat ng files na may `log` ang name<br>- ang `*` represents all |
|    `. -name "*.conf"`    | -lahat ng `.conf` na files<br>                                            |
|        .  -iname         | - `case-INsensitive`<br>kung di sure sa uppercase or lower                |
|          .  -f           | Kung files lang                                                           |
|          .  -d           | kung dir lang                                                             |


---

#### THE `grep` Commaand
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
