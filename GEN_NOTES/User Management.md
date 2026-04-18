
*This section covers the essential concepts and commands for managing users in Linux. It includes creating and deleting user accounts, modifying user information, managing groups, and setting permissions.*


*TEMPLETE FOR NOW*
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
chown
chmod
understanding rwx

### SPECIAL PERMISSIONS
suid
sgid
stickt bit


### CONFIGURATION FILES
#### /etc/passwd
/etc/shadow
/etc/group





>[!Tip]- Some usefull commands for all of these
>
>`getent` - short for get entries