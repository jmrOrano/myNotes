


>[!Note]- Table of Contents
>[[User Management]]
>- [[User Management#**USER ADD**|useradd]]
>- [[User Management#**ADDUSER**|adduser]]
>- [[User Management#**USER DEL**|userdel]]
>- [[User Management#**USERMOD**|usermod]]
>- [[User Management#**passwd**|passwd]]
>
>[[User Management#GROUPS|Groups]]
>- [[User Management#**groupadd**|groupadd]]
>- [[User Management#**groupdel**|groupdel]]
>- [[User Management#**gpasswd**|gpasswd]]
>- [[User Management#**usermod -aG**|usermod -aG]]
>
>[[User Management#PERMISSIONS|Permissions]]
>- [[User Management#chmod|chmod]]
>- [[User Management#**chown**|Chown]]
>
> [[User Management#SPECIAL PERMISSIONS|Special Permissions]]
> - [[User Management#**SUID**|suid]]
> - [[User Management#**SGID**|sgid]]
> - [[User Management#**STICKY BIT**|sticky bit]]
> 
> **OTHERS**
> - [[#The User User Management Files]]


*This section covers the essential concepts and commands for managing users in Linux. It includes creating and deleting user accounts, modifying user information, managing groups, and setting permissions.*



### USER ACCOUNT 
*Overview : Involves creating, modifying, and deleting user accounts*
**User** : is not just a linux name. Its a :
- A **UID(User ID)** -- the real identity (eg. 1000)
- A **home directory** -- `/home/username`
- A **shell** -- `/bin/bash`, `/bin/zsh`, etc....
- Permissions tied to that UID 

**The system doesnt care about username--it cares about UID**

>[!Important]- Linux Seperates ID's into two general ranges:
>**SYSTEM GROUPS**
>- Usually has *Group Id(GID)* of < 1000
>- These are used by *OS and Services* such as `root, daemon, www-data`
>
>**REGULAR/USER GROUPS**
>- Usually has *GID* of => 1000
>- Created for real users or custom groups like: 
>	- `devs , docker, admins, ....`
>
>>[!Note] Reminder
>>This range change depening on distro. Some systems starts at 500 for older setups. And Containers or Custom configs may ignore this

#### **USER ADD**
*A low-level command to Create a new user account on the system*
What does it do? 
- Register new user in `/etc/passwd` , `/etc/shadow/`, or even in `/etc/group `
- Assigns a UID
- (optionally) creates a home dir
- (optionally) sets a shell

>[!Syntax] 
>`useradd [options] <username>`

>[!Example]
>`useradd bob` 
>`useradd -d /home/bob -c "Regular user" bob`

>[!Info] Common Flags
>`-d` ---set home directory
>`-c` ---comment/description for that user
>`-m` ---create a home directory
>`-s` ---set a login shell

>[!Tip]
>- *the flag `-m` is usually use instead of `-d` to auto create a home at `/home/`*
>- *if `-s` is not specified, the system uses the default shell defined in `/etc/default/useradd` or `/etc/passwd`*
>- *Normal users need a real shell to interact with system. But with Service accounts--should not have shell to prevent credentials access or malicious logins*
>- Check in `/etc/passwd` if user is created 
>- Check userid faster by : `id <username>`


#### **ADDUSER**
*Overview : a high level command which  is different compare to [[#USER ADD]] command. It is basically a friendly wrapper" around lower-level tools like `useradd`.*

>[!Syntax]
>	`adduser <name>`

>[!Example]
>Creating a new user interactively:
>	`sudo adduser <username>`

>[!Info] What makes it different from the [[#USER ADD]] command ? 
> - `adduser` is **interactive and automatic**, while `useradd` is **manual and minimal**
>
>When you run `adduser` directly, it usually: 
>- Creates home dir automatically
>- Sets up user group properly
>- Prompts you to set password immediately
>- Ask for optional info (full,name, Phone, etc)
>
>Internally, it still uses lower-level tools like `useradd`, but handles the setup for you.

>[!Common Behavior(Behind the scene)]
>- Creates `/home/username`
>- Updates `/etc/passwd`
>- Sets password via `passwd`
>- Assigns default shell (usually `bin/bash)`

>[!Tip] 
>Use `adduser` when you want simplicity
>Use `useradd` for automation/server scripting and low level. 


#### **USER DEL**
*Overview: Deletes a user account from the system*

>[!Syntax]
>`userdel [options] <name>`

>[!Example]
>Delete a user:
>`sudo userdel <username>`
>
>Delete a user + remove the home directory
>`sudo userdel -r <username>`

>[!Info] Common Flags
>`-r` ---means recurisve, remove home directory and mail spool
>`-f` ---means force, deletes even if use if logged in or has process

>[!Tip]
>**Always decide first:**
>- "keep files?" - `userdel <username>`
>- "full cleanup?" -`userdel -r <username>
>
>**Check if user exists before and after deleting:**
>- `id username`
>- in `/etc/passwd`
>- in /home/ or use `ls -ld /home/<usernmae> `
>- run `ps -u <username>` to see if it still has process running
>
>**Handling leftover files**
>- even using the `-r` flag will still left some files in other directories.
>- run `sudo find / -user <theID>` to locate and delete them.


#### **USERMOD**
*Overview: Modifies an exisitng user account (change group, shell, home directory and etc.)*

>[!Syntax]
>`usermod [options] <name>`

>[!Example]
>Adding user to the group
>	`sudo usermod -aG sudoGroup <usernaame>`
>
>Changing user's shell:
>	`sudo usermod -s /bin/bash <username>`
>	
>Changing home directory:
>	`sudo usermod -d /home/home -m <username>`
>	

>[!Info] Common Flags
>`-aG` - append user to a group (IMPORTANT: keeps existing group)
>`-G` - set groups (replace all exisiting group)
>`-s` - change login shell
>`-d` - change home directory of user
>`-m` - move contents when changing home directory
>`-l` - change username 

>[!Tip]
> - Always use the `-aG` when adding to a group
>	`usermod -aG group <username>` . Without `-a` it remove the existing groups
>
> - Verify things with command : `id <username>`
> - Re-login if changes doesn't apply immediately
> - When changing home directory, use `-m` to avoid losing files.
>
>

#### **passwd**
*Overview: Use to set, change, or manage a user’s password and account lock status.*

>[!Syntax]  
>`passwd [options] <name>`

>[!Example]  
>Set or change password:
>`sudo passwd username`
>
>Change your own password:
>`passwd`
>
>Lock and Unlock a user account:
>`sudo passwd -l username`
>`sudo passwd -u username`

>[!Info] Common Flags  
`-l` → lock account (disable login)  
`-u` → unlock account  
`-d` → delete password (⚠️ allows passwordless login)  
`-e` → expire password (force change on next login)

>[!Tip]  
>- After `useradd`, always set a password:
>
>- Locking (`-l`) is safer than deleting a user if you want temporary disable
>
>- Verify password status:
>	`sudo passwd -S username`
>	
>- Avoid using `-d` unless you understand the security risk (no password = easier access)
>
>- If password changes don’t apply, ensure user logs out and logs back in





### GROUPS
*Overview: is a way to organize users so you can manage permissions more efficiently. *
Instead of giving permissions to each user one-by-one, you assign them to a **group**, then add users to that group.

**Core Idea:** Users → belong to Groups → Groups → control access


>[!Info]- Types of Group
>
>1. **Primary Group**
>	- Each user has one **one main group**
>	- Usually same as username
>>[!Example]
>>	`username -> group: username`
>>	
>2. **Secondary (Supplementary) Group**
>	- Extra groups a user belongs to
>	- Used for shared access
>>[!Example]
>>	`username -> devs, sales, docker, sudo` 
>
>**How to view groups**
>`id username` or `groups username`
>
>>[!Tip]
>>A primary group is not a meant for isolation. 
>>A `group` is a shared container


#### **groupadd**
*Overview: Creates a new group in the system.*

>[!Syntax]  
>`groupadd [options] <group_name>`

>[!Example]  
Create a new group:
>`sudo groupadd devs`
>
>Create group with specific GID:
>`sudo groupadd -g 1050 devs`

>[!Info] Common Flags  
>`-g` → specify GID (Group ID) manually  
>`-f` → force (no error if group already exists)

>[!Tip]  
>Verify group creation:
>`getent group devs`
>
>Groups are stored in:
>`/etc/group`
>
>After creating a group, you usually:
> -add users → `usermod -aG group username`
> 
>Keep group names simple and meaningful (e.g. `devs`, `admins`, `docker`)

#### **groupdel**
*Overview: deletes a group from the system*

>[!Syntax]  
>`groupdel <group_name>`

>[!Example]  
>Delete a group:
>`sudo groupdel devs`

>[!Info] Notes  
>No major flags commonly used (basic command is usually enough)
>
>The command will fail if:
>
>- the group is a **primary group** of any user

>[!Tip]  
>- Verify group exists before deleting:
>`getent group devs`
>
>- Check if group is used as primary group:
>`grep devs /etc/passwd`
>
>- If it appears there, you must change the user’s primary group first
>
>- Verify deletion:
>`getent group devs`
>- Deleting a group does NOT delete users—only removes the group itself

#### **gpasswd**
*Overview: Manages group memberships and group administrators*

>[!Syntax]  
>`gpasswd [options] <group_name>`

>[!Example]  
>Add user to a group:
>`sudo gpasswd -a <username> <groupname>`
>
>Remove user from a group:
>`sudo gpasswd -d username devs`
>
>Set group administrators:
>`sudo gpasswd -A user1,user2 devs`
>

>[!Info] Common Flags  
>`-a` → add user to group  
>`-d` → remove user from group  
>`-A` → assign group administrator(s)  
>`-M` → set members (⚠️ replaces all existing members)

>[!Tip]  
>- Alternative to `usermod -aG` for adding users:
>`gpasswd -a username group`
>
>- Verify group members:
>`getent group devs`
>
>- Be careful with `-M`:  
> It overwrites all current members (similar to `usermod -G`)
>
>- Use `-a` for safe additions instead of replacing members

#### **usermod -aG**
*Overview:Adds a user to one or more supplementary groups without removing existing group memberships. *

>[!Syntax]  
>`usermod -aG <group> <user>`

>[!Example]  
>Add user to sudo group:
>`sudo usermod -aG sudo username`
>
>Add user to multiple groups:
>`sudo usermod -aG docker,devs username`

> [!Info] Meaning of Flags  
> `-a` → append (VERY IMPORTANT)  
> `-G` → supplementary groups list
>
>Together: **append user to group(s) without overwriting existing ones****
>>[!Warning] 
>>Without `-a`: `usermod -G docker username`
>>- This **replaces ALL existing groups**

>[!Tip]  
>Always prefer `-aG` unless you intentionally want to reset group membership
>
Verify changes:
>`id username`
>
>- User may need to log out and log back in for group changes to apply
>
>Think of it like:
>
> `-G` = overwrite  
> `-aG` = add safely
### PERMISSIONS
*Overview:*  *Permissions control **who can read, write, or execute a file or directory in Linux**.*

>[!Note] **Core Concepts**
>Every file has 3 permission layers: 
>- **User (owner)** - the person who own the file
>- **Group** - Users in the same group
>- **Others** - everyone else
>  
>Permission Types (`rwx`)
>- `r` - read (view file / list directory)
>- `w` - write (modify a file)
>- `x` - execute (run file / access directory)

>[!Note] How to see Permissions
>
>`ls -l`
>
>*Example output*
>```Bash
>drwxr-xr-x 2 linuxuser linuxuser 4096 Apr  2 18:38 Desktop
>```
>>[!Important] Breakdown(Understanding Permission String)
>>
>>The first char : `d`
>>- `d` - directory 
>>- `-` -symbolic link/regular file
>>- `c` - character device etc....
>>
>>The next 9 Characters : `rwxr-xr-x`
>>*Lets divide it into 3 groups* : `rwx | r-x | r-x |`
>>
>>- `rwx` - **Owner** permission
>>- `r-x` - **Groups** permission
>>- `r-x` - **Others** Permission
>
>For files:
>- `r` - read contents
>- `w` - modify contents
>- `x` - execute a file (for scripting)
>
>For directories
>- `r` - list files (ls)
>- `w` - create/delete files
>- `x` - enter directories (cd)

>[!Tip] 
>Think of permissions like layers of access control:
>
>- **Owner** = full control
>- **Group** = shared access
>- **Others** = public access (limited)
>
>
>- Most important habit: always run `ls -l` before changing permissions
>

#### chmod
*Overview:  
Changes the permissions (r, w, x) of a file or directory.*

>[!Note] There are two main methods
>1. [[User Management#Symbolic Mode|Symbolic Mode]]
>2. [[User Management#Numeric Mode|Numeric Mode]]
>

>[!Syntax]
>`chmod [options] <permissions> <file/dir>`
##### Symbolic Mode
*Overview: easier to read, uses letters*

>[!Example] 
>``` Bash
>chmod u+x <file> /add execute to user
> chmod g-w <file> /remove write to group`
>chmod o=r <file> /set others to read only
>```
>
>>[!Important] Characters breakdown
>>`u` - user/owner
>>`g` - group 
>>`o` - others
>>`a` - all
>>
>>Operators
>>`+` -- add permission
>>`-` -- remove permission
>>`=` -- set exact permission
>
>>[!Tip] 
>>- You can modify multiple permission at once
>>	`chmod ug+w <file>`
>
##### Numeric Mode
*Overview: faster, widely use, uses numbers, but has a bit of understanding curve to it*

>[!Example] 
>`chmod 755 <file>`
>
>Meaning:
>- User : 7 (rwx)
>- Group : 5 (r-x)
>- Others : 5 (r-x)
>
>>[!Important] Values Breakdown
>>`r` = `4` 
>>`w` = `2`
>>`x` = `1` 
>>
>> Now add them
>> `rwx` = `7`
>> `rw-` = `6`
>> `r-x` = `5`
>> 
>
>>[!Warning]
>>- the `chmod 777 <file>` gives full access but is unsafe

>[!Tip] Tip
>- You can lock yourself from a file : `chmod 000 file.txt`. But it doesnt require a sudo to bring it back `chmod 640 file.txt`, because the `chmod` command **relies on file ownership, not on file permission itself**
>
> - "*Explicit is better than implicit*" : By default,  script files `.sh` is doesnt have execute permission thats why it only treated as regular file once created. So it better to *"explicitly"* give it a execute permission `chmod +x file.sh`


#### **chown**
*Overview: Changes the **owner (user)** and/or **group** of a file or directory.*

>[!Syntax]
> `chown [options] <user>[:group] <file>`

>[!Example]
>Change `owner` only:
>`sudo chown <username> <file>`
>
>Change `owner and group`
>`sudo chown <username>:<groupname> [file]`
>
>Change `groupname` only
>`sudo chown :<groupname> [file]`

>[!Info] Common Flags
>`-R` -> *recursive* apply to directories and all contents

>[!Tip] 
>Always verify after changing ownership:
>`ls -l`
>
>Use `-R` carefully, it affects all files inside


### SPECIAL PERMISSIONS
*Overview : modify how execution and access behave beyond standard `rwx`.*

>[!Type]
> - **SUID**(Set User ID) value of 4
> - **SGID**(Set Group ID) value of 2
> - **Sticky Bit** value of 1

#### **SUID**
*Overview: **Applies to executables only.** When set, the program runs with the **file owner’s privileges**, not the user who launched it.*

>[!Example]
>`ls -l /usr/bin/passwd`
>Output : `-rwsr-xr-x`. Notice the `s` instead of `x` 
>
>- `/bin/passwd` lets normal users change their password, but it runs with **root privileges** because of SetUID.

>[!How to set]
>`chmod u+s <file>`
>`chmod 4744 <file>`


>[!Tip]
>- Used for programs that need elevated privilages (*changing passwords*)
>- Can be dangerous if misused (privilage escalation risk)

#### **SGID**
*Overview: **
- *On files = runs with group owner privilages.*
- *On directories = files created inside inherit the **parent directory’s group**, not the creator’s group.*
	- Useful for **shared project folders** where all files should belong to the same group.

>[!Example]
>`chmod g+s <dir/file>`
>`chmod 2755 <file/dir>`


#### **STICKY BIT**
Overview: 
- **Applies to directories only**
- Ensure that only the file's owner (or root) can delete or rename files inside, even if others have write access.
- Commonly used in `/tmp` to prevent users from deleting each other's file.

>[!Example] Example and how to Identify
>`ls -ld /tmp` 
>- the `t` in the `others` section of the permission.
>
>`chmod +t <folder>` 
>`chmod 1750 <folder/file>`
>-  Set permission

>[!Warning]
>- SETUID/SETGID can be dangerous if misused. A vuln program with SetUID root can allow attackers to gain full system access.
>> [!Note]
>>- Sticky bit is safer, mainly used for shared directories
>>- Always audit which binaries have SetUID/SetGID set `find / -perm -4000` for SetUID, `-2000` for SetGID.

#### **umask**
Overview : 
*-- change the permission sa mga susunod na created file or dir*
-- base sa session ng terminal, meaning once run sa terminal its now active.
-- *change the [[Whatis#The ~/.profile / ~/.xprofile|`.profile`]] to make it persist*

>[!Syntax]
>`umask <value>`
>
>**Check current umask**
>- `umask`
>-

>[!Info]
>**How it works**
>- Linux starts with default permissions, then subracts umask
>
>**Default values are:**
>-Files - 666 (rw-rw-rw)
>Dir - 777 (rwx-rwx-rwx)

>[!Example]
>**Files**
>`666 - 022 = 644`
>`rw-, r--, r--` 
>
>**Dir**
>`777 - 022 = 755`
>`rwx, r-x, r-x`
>
>


>[!Tip]- Some usefull commands for all of these
>
>`getent` - short for get entries
>`getent group <groupname>` - list those in group
>`id <username>` - for getting user information
>`


>[!Warning]
> - for script files. removing the `x` (`sudo chmod 644`) doesnt fully prevent execution.  
> 	>>[!Note]
> 	>> There are 2 ways to run a file: Direct and Interpreter. 
> 	>> 1. Direct Execution (needs `x` permission)
> 	>> 	`./script.sh`
> 	>> 	kernel ang tatawag
> 	>> 	
> 	>> 2. Interpreter Execution (needs `r` permission)
> 	>> 	`bash script.sh`
> 	>> 	bash interpreter ang mag babasa ng file
> 	>> 	requries read permission
> 	>

___

### The User User Management Files
For commands : [command ]

1.  The `sudoers` file
	- located at /etc/sudoers
	- it lists of the users and groups who can use sudo command
	- must be edited with the `visudo` command as root for safer method.
2. The `passwd` file
	- located at /etc/passwd
	- contains the mapping between usernames and UIDs
		- `root:x:0:0:root:/root:/bin/bash` 
		- `root` - the user
		- `x`        - indicate encrypted pass in the /etc/shadow
		- `*`         - if its asterisk, acc is locked and cannot be used for login
		- `0`          - the `UID` the root is always zero
		- `0`          - the iditentifier of user in a primary group
	- editing is possible but heavily discourage, causing typo can cause system locking.
	- more reliable to use `useradd, usermod, userdel` command util to manage.
3. the `shadow` file
	 - located at `/etc/shadow`
	 - it stores encrypted user password 
		 - `root:!:20495:0:99999:7:::`
			- each user is seperated by colon
4. the `group` file
	- located at `/etc/group` 
	- for managing permissions for multiple users
		- `root:x:0:`
		- each group is seperated by colon
