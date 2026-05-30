*Install tree `sudo apt install tree` for navigation*

>[!Note]- Commands User throughout this notes
>
>`ls -la` - for listing contents
>	`tree` - to understand struction. DONT DO `TREE`
>

>[!Contents]
>**CRITICAL ZONES**
>[[#/home]]
>- [[#The .Bashcr file in home dir]]


Understand about Dynamic and Static 

### **CRITICAL ZONES**
Overview: *This are the ones that youll actually touch in real life most of the time.*

#### **/home** 
It is `dynamic` - meaning things changes constantly
- To seperate user stuff


**Map Everything with the commands:**
```Bash
ls -lah #ls -long -all -human readable 
tree #for strucutred 
```

>[!Note] the dotfiles 
>- `.bash_history` - self explanatory
>- `bashrc` - read [[#The .Bashcr file in home dir]] for more info
>- `.profile` - read [[#The ~/.profile / ~/.xprofile]] for more info 
>- `.config/` - user level configs
>
>They control your shell behavior.
##### **The .Bashcr file in home dir**
*Overview: Ito ang user setting config for the terminal.*
- **Location :** *`~/.bashrc or /home/username/.bashrc)
- Dito nilalalgay ang [[Whatis#The `env` (environment variables)| env variables]] for users
- For customizing prompt such as `alias` 
- And also mga commands na want i-automate.
- *Dont put `gnome-terminal` inside. Its a terminal fork bomb*

##### **The ~/.profile  /  ~/.xprofile**
*Overview:Dito nakalagay ang mga script that ***runs at log in*** 
- Any commands na ilagay ko dito ay executed automatically when logged in


##### **The ~/.config dir**
*Overview: A user-level configs *
- Located inside the users home directory
- Applies **only** to specific user
- No root needed to modify
- Safe to mess with (mostly)
>[!Question] How is it different to configs inside the `/etc` directory ?
>- Applies to all users
>- Requires root to modify
>- Much higher risk




---

#### **/etc**
It is static. System brain. All configuration files live here — SSH, networking, users, services. If something behaves wrong, check here first.

##### Whats inside?

**User and login data**
- `/etc/passd` - user accounts (names, IDs, shells)
- `/etc/shadow` - password hashes (locked down hard)
- `/etc/group` - groups

**System configuration**
- `/etc/hostname` - machine name
- `/etc/hosts` - manual DNS overrides
- `/etc/fstab` - disk mounting rules

**Services(the stuff running in background)**
- `/etc/systemd/` - system service config
- `/etc/init.d/` - older service script

**Network settings**
- `/etc/resolv.conf` - DNS servers
- `/etc/netplan/`  - ubuntu network config

**App configs**
- `/etc/ssh/sshd_config`
- many more
---

#### **/var**
Overview: *Stands for variable -  meaning its contents are always growing,sharinking, or changing as the system runs. *

**Whats inside of it ?**

###### **var/log**
*Probably the most important one for learning.*
This is where the *"What the hell happened?* gets answered
It contains the ff: 
- system logs
- service logs
- crash info
- auth attempts

### **COMMANDS**
##### **For Navigation**
```
cd
	- `cd..` move back
	- `cd~`  move to home dir
	- `cd -` move to previous dir
	  
pwd - display current where you are right now.
```

###### *ls command*
| Command&Flags | Description                                         | Comment                                                   |
| :-----------: | --------------------------------------------------- | --------------------------------------------------------- |
|     `-l`      | long format, shows, permissions, owner,size, date   | instead of just file names, see the full ID card for each |
|     `-a`      | Show all, including the hidden once                 |                                                           |
|     `-h`      | Human readable sizes(KB,MB,GB Instead or raw bytes) |                                                           |
|     `-t`      | sort by time modified-newest first                  |                                                           |
|     `-r`      | reverse sort order                                  | oldest first                                              |
|     `-S`      | sort by file size-largest first                     |                                                           |
|     `-R`      | recurisve, list all subfolders too                  | very messy , can be useful with pipe(i guess)             |
|     `-1`      | One file per line (easier to read in scripts)       |                                                           |
|               |                                                     |                                                           |
|               |                                                     |                                                           |

###### `tree`
| Command or Flags | Desc                                     | Comment                                         |
| :--------------: | ---------------------------------------- | ----------------------------------------------- |
|      `-L 2`      | Limit depth                              | Useful for large projects. Commonly used        |
|       `-d`       | Directories only-hide files              | for clean map                                   |
|       `-a`       | Show hidden files too                    |                                                 |
|       `-f`       | Show full path for each file             | use when needed the complete path to copy-paste |
|       `-h`       | show file sizes in human-readable format |                                                 |
|       `-I`       | Ignore files/folders matching pattern    |                                                 |
|  `--dirsfirst`   | List folders before files at each level  |                                                 |
##### **For Reading Files**

###### cat command
Usage : `cat [flags] filename.txt`

|  Command or Flags   | Desc                                        |                                                           |
| :-----------------: | ------------------------------------------- | --------------------------------------------------------- |
|        `-n`         | show with line numbers                      |                                                           |
|        `-A`         | show hidden characters (tabs, line endings) | useful for debuggin spacing issues                        |
| `cat file A file B` | print multiple files one after another      | this is actually where the name comes from -- conCATenate |
###### less command
Usage : `less [flags] file`
Useful when file is larage and want to explore it without flooding the screen

| Commands or Flags | Desc                                         |
| :---------------: | -------------------------------------------- |
|       `-N`        | Show line numbers while scrolling            |
|       `+G`        | Open and jump straight to the end            |
| **KEYBOARD KEYS** | **KEYBOARD KEYS**                            |
|    `space key`    | next page                                    |
|      `b key`      | back a page                                  |
|      `/word`      |                                              |
|        `n`        | next match, used when searching with `/word` |
###### head command
Usage: `head [flags] file`
Shows the 10 lines by deafult

| Commands or Flags | Desc                     |                                |
| :---------------: | ------------------------ | ------------------------------ |
|      `-n 20`      | Shows the first 20 lines |                                |
|      `-n 1`       | first line only          | great for checking CSV headers |
###### tail command
Usage: `tail [flags] file`
Mostly use for a live update of log file. Last 10 lines at default
Mostly use in system awareness as well

| Command or Flags | Desc                                      |                                 |
| :--------------: | ----------------------------------------- | ------------------------------- |
|     `-n 50`      | Show last 50 lines                        |                                 |
|       `-f`       | Follow mode. Real time                    | Most powerful use for live view |
|    `-f -n 50`    | start from last 50 lines then follow live |                                 |

##### **For Searching**

###### `grep` Command
Global Regular Expression. Pinaka useful for searching patterns.

**How to use**
`grep [flags] "what to find" filename`

| Command or flags | Desc                                                              | Example                     |
| :--------------: | ----------------------------------------------------------------- | --------------------------- |
|       `-i`       | case-insentitive- matches `ERROR` or `error`                      | `grep -i "error" log.txt`   |
|       `-r`       | recursive- search inside all the files in a folder and subfolders | `grep -r "TODO" ./src`      |
|       `-n`       | Show line numbers  next to each match                             | `grep -n "error" app.log`   |
|       `-l`       | List ONLY THE FILENAME that contain a match, not the lines        | `grep -rl "passowrd"`       |
|       `-c`       | Count how many lines matched-not the lines themselves.            | `grep -c "error" log.txt`   |
|       `-v`       | Invert-show lines that DO NOT match                               | `grep -v "debug" log.txt`   |
|      ` -w`       | whole word only - exact match                                     |                             |
|      `-A 3`      | Show 3 lines after each match. useful for context                 | `grep -A 3 "error" log.txt` |
|      `-B 3`      | Show 3 lines before each match.                                   |                             |
|      `-C 3`      | Show 3 lines around match (before and After the match)            |                             |

Use with [[Feynman-Technique#Regex-Regular Expression|Regex]]

**When to Use?**
- Variable sa code
- Hanapin error sa logs
- Hanapin ang output ng ls
- Hanapin ang process


###### The *`find`* command
Usage : **`find [whereToSearch] [-flags] "[whatToLooK] [action]"`**

`[whatToLook]` - can be words like `"*.txt"` , `"config*"` etc
`[action]` -  can be `-print`, `-delete`, `-exec`

- The companion of [[#`grep` Command|grep]] 
- Search recursively, no need for recursive flag

|   Command or Flags   | Desc                                                                   |                                    |
| :------------------: | ---------------------------------------------------------------------- | ---------------------------------- |
|     **BY NAME**      | **BY NAME**                                                            |                                    |
|   `-name "*.txt"`    | Find files matching a name pattern - case sensitive                    |                                    |
|   `-iname "*log"`    | -Same but case-insensitive                                             | (README.md = readme.md)            |
|   `-name "config*`   | -wildcard - find anything that **starts** with `config`                | `config.txt`, `config123`,         |
|  `-name "*config"`   | - wildcard - find anything that **ends** with `config`                 | `myconfig`, `appconfig`, `.config` |
|                      |                                                                        |                                    |
|     **BY TYPE**      | **BY TYPE**                                                            |                                    |
|      `-type f`       | files only                                                             |                                    |
|      `-type d`       | dir only                                                               |                                    |
|      `-type l`       | for [[Feynman-Technique#ANO ANG SYMBOLIC LINKS?\|symbolic links]] only |                                    |
|                      |                                                                        |                                    |
|     **BY SIZE**      | **BY SIZE**                                                            |                                    |
|     `-size 000c`     | c- bytes<br>k-kilobytes<br>M-megabytes<br>G-Gigabyes                   |                                    |
|    `-size +000M`     | larger than 100Megabytes                                               | can be use to `c, k, G`            |
|    `-size -100M`     | smaller than 100Megabytes                                              |                                    |
|       `-empty`       | empty files or folders                                                 |                                    |
|                      |                                                                        |                                    |
|     **BY TIME**      | **BY TIME**                                                            |                                    |
|     `-mtime -7`      | Modified within last 7 days                                            | `-` means less than N days ago     |
|     `-mtime +30`     | Modified more than 30 days ago                                         | `+` older than N days              |
|  `-newer file.txt`   | find for a fles or dir modified more recently than the `file.txt`      |                                    |
|                      |                                                                        |                                    |
| **DEPT AND ACTIONS** | **DEPT AND ACTIONS**                                                   |                                    |
|    `-maxdepth 2`     | dont go deeper than 2 folders level                                    | avoid searching forever            |
|    `-mindepth 1`     | skip the starting folder itself                                        | only looks inside of it            |
|      `-delete`       | delete every file found                                                | use with extreme caution           |
|   `-exec cmd {} \`   | run a command on each result                                           |                                    |





##### **For System Awareness**

###### the `df` command
how full are my drives? 

|  Command  | Desc                        |                   |
| :-------: | --------------------------- | ----------------- |
|  `df -h`  | Human readable size (GB/MB) |                   |
| `df -h /` | Check ony the root\         |                   |
| `df -hT ` | shows filessytem types      | ext4, tmpfs, etc. |
|           |                             |                   |

###### the `du` command
Tells you which folder is occupies the most

|             Commands              | Desc                                            |
| :-------------------------------: | ----------------------------------------------- |
|               `du`                | recursively, good luck reading this             |
|            `du -sh *`             | Size of every item in current folder, summarize |
|           `du -sh .* *`           | includes the hidden files                       |
|       `du -h --max-depth=1`       | One level deep- avoid overwhelming output       |
| `du -h --max-depth=1 \| sort -rh` | sort  in reverse.                               |

###### the `ps` command
`Process Snapshop` - shows whats running

|          Command          | Desc                                          |
| :-----------------------: | --------------------------------------------- |
|         `ps aux`          | shows all process from all Users with details |
| `ps aux \| grep "python"` | Filter to find a specific running program     |
| `ps aux \| grep -v grep`  | same as above but hides the grep process      |


###### the `free` command
if [[Linux File System (Major Only)#the `df` command|df command]] watches hard drive spaces. This one watches RAM
```Bash
fre -h  #shows RAM and swap usage in readable sizes
```

###### the `top` command
[[Linux File System (Major Only)#the `ps` command|ps command]] is only a frozen moment. This one updates every seconds
```Bash
top
btop
top -u username # shows process only for a specified user

#keyboard bindings
M - sort by memory usage
P - sort by CPU usage
k - kill a process (enter PID)
q - quit
```

---

##### A dashed file `-` 
A `cat` command wont normally work on files named `-` because the interpret the dash as stdin(standard input), not a filename.

The fix to read it are:
```bash
cat ./-
#The ./ tells the shell to specified the file named - in the current dir

cat -- -
#The -- tells the command "everything after this is a filename nor a flag"


```