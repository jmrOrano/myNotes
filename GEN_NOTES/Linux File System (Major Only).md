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
>
###
*Templates for now*

- `/home` → your playground, user files
- `/etc` → configs (break something here, congrats, you learned something)
- `/var` → logs, caches, changing data
- `/usr` → installed programs (not “user” in the way you think)
- `/bin` & `/sbin` → essential commands
- `/tmp` → temporary junkyard
 2. Understand the **logic**, not the list

Linux filesystem isn’t random. It follows a philosophy (FHS - Filesystem Hierarchy Standard):

- Static vs dynamic (`/usr` vs `/var`)
- User vs system (`/home` vs `/etc`)
- Essential vs non-essential (`/bin` vs `/usr/bin`)
 3. Learn the rest **on demand**

Directories like:

- `/opt`
- `/mnt`
- `/media`
- `/srv`

 4. For your actual goal (cybersecurity / exploits)

Now here’s where I’ll push back a bit.

If you want to get into **security**, then:

- You **do need deeper familiarity**, especially:
    - `/etc` (configs = attack surface)
    - `/var/log` (logs = evidence)
    - `/proc` and `/sys` (system internals)
    - `/dev` (devices = fun chaos)

Not memorization, but **comfort navigating blind**.

 Commands commonly use

 - tail
 - less
 - grep
 - find
 - cat
 - cd

### CRITICAL ZONES
Overview: *This are the ones that youll actually touch in real life most of the time.*

#### /home 
It is `dynamic`

**Map Everything with:**
`ls -la` -  list all with permissions
`tree` - install this, to better understand the structure.
>[!Note] the dotfiles 
>- `bashrc` - read [[#The .Bashcr file in home dir]] for more info
>- `.profile` - read [[#The ~/.profile / ~/.xprofile]] for more info 
>- `.config/`
>
>They control your shell behavior.

##### The .Bashcr file in home dir
*Ito ang user setting config for the terminal.*

Location : *`~/.bashrc or /home/username/.bashrc)

Dito nilalalgay ang [[Whatis#The `env` (environment variables)| env variables]] for users
For customizing prompt
And also mga commands na want i-automate.

##### The ~/.profile  /  ~/.xprofile
*Dito nakalagay ang mga script that ***runs at log in*** 
- Any commands na ilagay ko dito ay executed automatically when logged in