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

### CRITICAL ZONES
Overview: *This are the ones that youll actually touch in real life most of the time.*

#### **/home** 
It is `dynamic` - things changes
- To seperate user stuff
- 

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
##### The .Bashcr file in home dir
*Overview: Ito ang user setting config for the terminal.*
- **Location :** *`~/.bashrc or /home/username/.bashrc)
- Dito nilalalgay ang [[Whatis#The `env` (environment variables)| env variables]] for users
- For customizing prompt such as `alias` 
- And also mga commands na want i-automate.
- *Dont put `gnome-terminal` inside. Its a terminal fork bomb*

##### The ~/.profile  /  ~/.xprofile
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
#### **COMMANDS**
##### For Navigation
```
cd
pwd
ls -lah
tree
```

##### For Reading Files
```
cat file
less file
head file
tail file
```

##### For Searching
```
grep "text" file
find / -name filename
```
##### For System Awareness
```
df -h
du -sh *
ps aux
```

|  du- disk usage command   | Desc                                                                                                  |
| :-----------------------: | ----------------------------------------------------------------------------------------------------- |
|           `du`            | - recursively<br>-good luck reading this                                                              |
|          `du -h`          | Human readable.<br>But still messy                                                                    |
|        `du -sh *`         | -summarize -human readable<br>-* means all files and folders in current dir.<br>-exclude hidden files |
|       `du -sh .* *`       | -includes the hidden files                                                                            |
| `du -sh .* * \| sort -hr` | -pipe and sort<br>-r means reverse<br>                                                                |
|    `du --max-depth=1`     | -much cleaner<br>-                                                                                    |
