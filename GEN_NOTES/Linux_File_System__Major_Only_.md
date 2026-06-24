*Status: In Progress*
*Last Updated: Jun 2026*
*Purpose: Understanding the Linux directory structure — what each folder is, why it exists, and what lives inside.*

>[!Contents]-
>- [[#Overview — The Big Picture]]
>- [[#CRITICAL ZONES]]
>	- [[#/home]]
>		- [[#The .bashrc File]]
>		- [[#The ~/.profile / ~/.xprofile]]
>		- [[#The ~/.config Directory]]
>	- [[#/etc]]
>	- [[#/var]]
>		- [[#/var/log]]
>		- [[#/var/cache]]
>		- [[#/var/spool]]
>		- [[#/var/lib]]
>		- [[#/var/run → /run]]
>	- [[#/usr]]
>		- [[#/usr/bin]]
>		- [[#/usr/sbin]]
>		- [[#/usr/lib]]
>		- [[#/usr/share]]
>		- [[#/usr/local]]
>		- [[#/usr/src]]
>	- [[#/tmp]]
>- [[#LESS TOUCHED]]
>  - [[#Common File System Types]]
>- [[#The Anatomy of Disk]]
>	- [[#Filesystem Structure]]
>- [[#COMMANDS]]
>	- [[#Navigation]]
>	- [[#Reading Files]]
>	- [[#Searching]]
>	- [[#System Awareness]]
>	- [[#Misc]]
>- [[#Other information]]
>	- [[#A file named `-`]]
>	- [[#Swap]]
>	- [[#Block]]
>	- 

---

## Overview — The Big Picture

>[!Info] Dynamic vs Static — Know This First
>Before diving in, understand this split:
>- **Dynamic** — contents change constantly as the system runs (logs grow, temp files appear, user files change)
>- **Static** — contents only change when you install/update software or edit configs
>
>This matters because it tells you *who owns it* and *how risky it is to touch.*

```
/                         ← Root. Everything starts here.
├── home/                 ← DYNAMIC  | Your personal stuff
├── etc/                  ← STATIC   | All system configs
├── var/                  ← DYNAMIC  | Logs, cache, app data
├── usr/                  ← STATIC   | Installed software
├── tmp/                  ← DYNAMIC  | Short-lived temp files
├── bin/  → usr/bin       ← Symlink (legacy)
└── sbin/ → usr/sbin      ← Symlink (legacy)
```

---

## CRITICAL ZONES
*These are the ones you'll actually touch in real life most of the time.*

---

### /home
**What it is:** Your personal space. Every user gets their own folder here.
**Nature:** Dynamic — changes constantly.
**Owner:** You (your user account).

```
/home/
└── yourname/
    ├── Documents/
    ├── Downloads/
    ├── .bashrc          ← dotfile: shell config
    ├── .profile         ← dotfile: login script
    ├── .bash_history    ← dotfile: command history
    └── .config/         ← dotfile: app configs
```

**Explore it:**
```bash
ls -lah ~        # shows everything including dotfiles
tree ~ -L 2      # structured view, 2 levels deep
```

>[!Note] About Dotfiles
>Files starting with `.` are hidden by default — run `ls -a` to see them.
>They control your shell behavior. Don't delete them randomly.

---

#### The .bashrc File
**Location:** `~/.bashrc`
**When it runs:** Every time you open a new terminal.
**What it's for:**
- Setting [[Whatis#The `env` (environment variables)|environment variables]] for your user
- Creating `alias` shortcuts (e.g. `alias ll='ls -lah'`)
- Commands you want auto-executed every session

>[!Warning] Don't put `gnome-terminal` inside `.bashrc` — it causes a terminal fork bomb.

---

#### The ~/.profile / ~/.xprofile
**When it runs:** Once at login — not every terminal open.
**What it's for:** Scripts and [[Whatis#The `env` (environment variables)|env variables]] that only need to run once per session.

*Think of `.bashrc` as "runs every new terminal tab" and `.profile` as "runs once when you first log in."*

---

#### The ~/.config Directory
**What it is:** User-level app configuration folder.
**Safe to explore and modify — no root needed.**

>[!Question] How is this different from `/etc`?
>| | `~/.config` | `/etc` |
>|---|---|---|
>| Applies to | Only your user | All users |
>| Requires root | No | Yes |
>| Risk level | Low | High |

---

### /etc
**What it is:** The system's brain. All configuration files live here.
**Nature:** Static — only changes when you edit configs or install software.
**Owner:** root.

*Rule of thumb: If something is behaving wrong, check here first.*

**Explore it:**
```bash
ls /etc
sudo cat /etc/hostname
sudo cat /etc/hosts
```

**What's inside:**

*User and Login Data*
- `/etc/passwd` — user accounts (names, IDs, shells)
- `/etc/shadow` — password hashes (root only)
- `/etc/group` — group memberships

*System Configuration*
- `/etc/hostname` — machine name
- `/etc/hosts` — manual DNS overrides
- `/etc/fstab` — disk mounting rules on boot ^27d1c1

*Services*
- `/etc/systemd/` — systemd service configs
- `/etc/init.d/` — older service scripts (legacy)

*Network*
- `/etc/resolv.conf` — DNS servers
- `/etc/netplan/` — Ubuntu network config

*App Configs*
- `/etc/ssh/sshd_config` — SSH server settings
- ...one for almost every installed service

---

### /var
**What it is:** Variable data — everything that grows, shrinks, or changes while the system runs.
**Nature:** Dynamic — constantly changing.
**Owner:** root + services.

*This directory is gold for debugging. Because:*
- Attackers leave traces here
- Services reveal their behavior here
- Failed SSH logins, sudo usage, daemon crashes — all recorded here

**Explore it:**
```bash
ls /var
sudo ls /var/log
sudo tail -f /var/log/syslog
```

---

#### /var/log
**What it is:** Where *"What the hell happened?"* gets answered.
*Probably the most important subdirectory for learning.*

Key files:
```
syslog       ← General "what's happening" journal
auth.log     ← Every login attempt (including hackers trying SSH)
kern.log     ← Kernel and hardware messages
dmesg        ← Boot-time hardware detection
journal/     ← Systemd's binary log (read via journalctl, not cat)
```

```bash
sudo tail -f /var/log/auth.log          # watch login attempts live
sudo grep "Failed" /var/log/auth.log    # find failed logins
journalctl -xe                          # read systemd journal with context
```

---

#### /var/cache
**What it is:** Temporary cached data. Apps store reusable stuff here to avoid re-downloading or recalculating.
**Safe to clean.**

Contains: package manager cache, thumbnails, metadata.
```bash
sudo apt clean    # wipes /var/cache/apt/archives — frees space, nothing breaks
```

---

#### /var/spool
**What it is:** Queued tasks waiting to happen.
*Think of it as a waiting room — jobs sit here until processed.*

Things like: print jobs, mail queue, cron tasks.

---

#### /var/lib
**What it is:** Persistent application state.
**This is where apps keep their long-term memory — not configs, but actual operational data.**

Examples:
- `/var/lib/docker` ← Docker images and container data
- `/var/lib/mysql` ← MySQL database files

>[!Warning] Don't manually delete things here unless you know what you're doing. Deleting `/var/lib/docker` wipes all your containers and images.

---

#### /var/run → /run
**What it is:** Runtime information — only relevant while the system is running.
**Note:** Modern Linux moved this. `/var/run` is now just a [[Whatis#**Symbolic Link?**|symlink]] pointing to `/run`.

Contains: PID files, sockets, temporary service state.
*When a service starts, it writes its PID here so other programs can find it.*

---

### /usr
**What it is:** Unix System Resources. Where installed software lives.
**Nature:** Static — only changes on package install/update.
**Owner:** root.

*Not the OS itself — but a huge portion of what `apt` installs ends up here. Think of it as the application shelf.*

```bash
ls /usr
ls /usr/bin | less
```

---

#### /usr/bin
**What it is:** Binaries — executable programs available to all users.
**Almost every command you use daily lives here.**

```bash
ls /usr/bin | less

which grep    # → /usr/bin/grep
type ls       # → ls is /usr/bin/ls
```

>[!Note]- About `/bin` at the root directory
>`/bin` is just a [[Whatis#**Symbolic Link**|symlink]] pointing to `/usr/bin` — they are the same thing.
>Kept for backward compatibility — old scripts still hardcode paths like `/bin/bash`.

---

#### /usr/sbin
**What it is:** System Binaries — same idea as `/usr/bin` but for sysadmin tools.
**Regular users can see the files, but most commands here require `sudo`.**

```
/usr/sbin/
├── useradd / usermod / userdel   ← Manage user accounts
├── sshd                          ← The SSH server daemon
├── nginx                         ← Web server daemon
├── iptables                      ← Firewall rules
├── fdisk                         ← Disk partitioning
└── reboot / shutdown             ← Power control
```

>[!Note]- About `/sbin` at the root directory
>Same story as `/bin` — [[Whatis#**Symbolic Link?**|symlink]] to `/usr/sbin`, kept for backward compatibility.

---

#### /usr/lib
**What it is:** Shared libraries — pre-compiled chunks of code that multiple programs share.
*Think of it as a communal toolbox that programs borrow from instead of each carrying their own copy.*

```
/usr/bin/vim   ←─┐
/usr/bin/git   ←─┼── all need encryption code → /usr/lib/libssl.so
/usr/bin/curl  ←─┘
```

**The `.so` extension = Shared Object** — Linux's version of Windows `.dll` files.

```bash
ldd /usr/bin/curl         # see what libraries curl depends on
which curl | xargs ldd    # same thing, chained
```

---

#### /usr/share
**What it is:** Shared read-only data that programs need to reference but not execute.
*Programs aren't just code — they need supporting data too: docs, icons, fonts, timezones.*

```
/usr/share/
├── doc/          ← Documentation for installed packages
├── man/          ← Man pages (what `man nginx` reads from)
├── icons/        ← System icons
├── fonts/        ← System fonts
├── zoneinfo/     ← Timezone database
└── applications/ ← .desktop files (app launcher entries)
```

```bash
# When you type `man nginx`, Linux reads from:
/usr/share/man/man8/nginx.8.gz

# When your system knows it's UTC+8 in Manila:
/usr/share/zoneinfo/Asia/Manila
```

---

#### /usr/local
**What it is:** Things YOU install manually — not the distro, not `apt`.
*The most important one for homelabbers and admins.*

- Manually compiled software
- Manually installed binaries
- Your own custom scripts and tools

```
/usr/local/
├── bin/     ← Your manually installed executables
├── lib/     ← Their libraries
├── etc/     ← Their configs
└── share/   ← Their data
```

>[!Important] Golden Rule
>Never manually drop files into `/usr/bin`. Always use `/usr/local/bin` for your own stuff — so system updates never wipe your work.

| | `/usr/bin` | `/usr/local/bin` |
|---|---|---|
| Managed by | `apt` / package manager | **You** |
| On system update | Gets overwritten | **Safe** |
| Responsibility | Distro's | Yours |

---

#### /usr/src
**What it is:** Source code — raw human-readable code of system-level software.
*Most commonly holds Linux kernel headers.*

```
/usr/src/
└── linux-headers-6.x.x/    ← Linux kernel headers
```

*Mostly empty on a fresh install — only populated when you explicitly install kernel headers.*

---

### /tmp
**What it is:** Temporary files — short-lived, doesn't survive a reboot.
**Nature:** Dynamic, world-writable, auto-cleared on reboot.
**Owner:** root (but everyone can write inside).

*Instead of every app creating their own temp folder, Linux standardizes it here.*

Programs use `/tmp` to:
- Write something mid-operation then delete it
- Store a file just long enough to hand it to another program
- Unpack something temporarily before installing

**Permissions are unusual here:**
```bash
ls -lhad /tmp
# drwxrwxrwt ← the 't' at the end is the sticky bit
```
Uses a [[User Management#STICKY BIT|Sticky Bit]] — everyone can write, but **you can only delete your own files**.

>[!Note] /tmp vs /var/tmp
>| | `/tmp` | `/var/tmp` |
>|---|---|---|
>| Cleared on reboot | Yes | No |
>| Use when | Data is truly throwaway | Process might survive reboot |

>[!Warning] Security Note
>Because `/tmp` is shared and world-writable, it's a known attack surface:
>- Attackers drop malicious scripts here
>- Insecure scripts writing here can be hijacked (symlink attacks)
>
>*You'll see this come up again in security topics.*



---

## LESS TOUCHED
*Good to know they exist, but you won't be living in these daily.*

|                         | Directory | What it is                                                                                                                                                  |
| ----------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                         | `/boot`   | Kernel and bootloader files. Don't touch.<br>Stores the files required for the system's boot process, including the Linux kernel and the boot loader files. |
|                         | `/sys`    | Virtual filesystem — hardware info exposed by the kernel                                                                                                    |
| System Information      |           |                                                                                                                                                             |
|                         | `/proc`   | A virtual filesystem that provides real-time information about currently running processes and kernel parameters.                                           |
|                         | `/srv`    | Intended for site-specific data served by the system, such as files for a web server.                                                                       |
| Device and Mount Points |           |                                                                                                                                                             |
|                         | `/dev`    | Contains special device files that represent hardware components like hard drives, terminals, and input devices.                                            |
|                         | `/mnt`    | A generic mount point for temporarily mounting filesystems.                                                                                                 |
|                         | `/media`  | A standard mount point for removable media like USB drives, SD cards, and CD-ROMs.                                                                          |
|                         | `/opt`    | Reserved for optional or third-party application software packages.                                                                                         |
|                         | `/root`   | Home directory for the root user (not `/home/root`)                                                                                                         |
|                         | `/run`    | Contains information about the running system since the last boot, such as process IDs (PIDs) and other runtime data.                                       |
|                         | `/bin`    | Symlink → `/usr/bin`                                                                                                                                        |
|                         | `/sbin`   | Symlink → `/usr/sbin`                                                                                                                                       |
|                         | `/lib`    | Symlink → `/usr/lib`                                                                                                                                        |

---

# Common File System Types
---
>**ext4**

^ee9fa8

```
— The default for many distros
— Reliable and standard choice for most use case
– Backward-compatible with its predecessors (ext2/ext3)
— Support very large disk volumes (up to 1Tb), and up to (16Tb File sizes)
```

>**Btrs** 

^a5ed07

```
— "B-tree FS" - modern filesystems with advance features like:
	- Built-in snapshots
	- Incremental backups
	- Improved performance
— While it is now considered stable and is the default in some distributions, it is still under active development.
```

>**XFS**

^8d214b

```
— High performance journaling filesystem
— Excels at handling large files and parallel I/O operations.
— Excellent choice for systems that manage large amount of data such as : Media Servers.
```

>**NFTS and FAT**
```
— Standard on Windows Filesystems. 
— Provides full support for reading and writing to them.
— Which is useful for dual-boot systems.
```

>**HFS+**
```
— The standard on macOS filesystems.
— Linux has only read-only support for it by default
— Additional tools is used for write suppport.
```

# The Anatomy of Disk
---
*June12 2026*

```
Remember this hierarchy:
Disk
 └─ Partition
     └─ Filesystem
         ├─ Inodes
         └─ Data Blocks

```

>A physical disk (e.g. `/dev/sda`) can be divided into multiple **partitions**.


Example:
```
/dev/sda = entire disk (The first storage detected byu the system)
/dev/sda1 = first partition 
/dev/sda2 = second partition

/dev/sdb = The second storage drive detected by the system
```

>Partitions allow different parts of a disk to serve different purposes:
```
- Operating System
- User Data
- Swap Space
- Separate Filesystems
```
>A partition usually contains a filesystem ([[#^ee9fa8|ext4]], [[#^8d214b|xfs]], [[#^a5ed07|btrfs]], etc.), but it can also be used for swap or other special purposes.

## Partition Table
---
>A **partition table** stores information about how a disk is divided.

It tells the OS:
```
- Where partitions start and end
- Which partitions exist
- Which partition is bootable
- How disk space is allocated
```

>Two common partition table types:
### MBR (Master Boot Record)
---
*Legacy partitioning scheme.*

**Characteristics:**
```
- Maximum of 4 primary partitions
- Supports:
    - Primary partitions
    - Extended partitions
    - Logical partitions
- Supports disks up to 2 TB
```

**To create more than 4 partitions:**
- Create an Extended Partition
- Create Logical Partitions inside it
- 
>Example
```
├─ Primary Partition 
├─ Primary Partition 
├─ Primary Partition 
└─ Extended Partition
 ├─ Logical Partition
 ├─ Logical Partition
 └─ Logical Partition
```

---
### GPT (GUID Partition Table)
---
*Modern partitioning standard.*

>Characteristics:
```
- Supports many partitions
- No primary/extended/logical distinction
- Uses unique GUID identifiers
- Works with modern UEFI systems
- Supports very large disks (>2 TB)
```
>GPT is the preferred partition table on modern Linux systems.

---
## Filesystem Structure
*filesystem is an organized collection of files and directories. At its core, it consists of a database to manage files and the files themselves. Let's explore its structure in more detail.*

>**Boot block**
```Quote
— Contains Boot Related Information.

Located in the first few sectors of a filesystem, this block is not used by the filesystem itself. Instead, it contains information used to boot the operating system. Only one boot block is needed per OS. While other partitions may have boot blocks, they often go unused.
```

>**Superblock**

^becce8

```Quote
— Stores filesystems metadata:
	- Filesystem size
	- Block size
	- Inode Information
	- Filesystem Status

This is a single block following the boot block that contains metadata about the filesystem, such as the size of the inode table, the size of logical blocks, and the total size of the filesystem.
```

>**Inode table**
```Quote
— Database of filesystem objects.
— Every file and directory has an Indode
— Stores metadata such as:
	- Ownership
	- Permissions
	- Timestamps
	- File size
	- Data block locations
— The inode stores information ABOUT a file, not the file contents.

This is the database that manages files and directories. Each file or directory has a unique entry in the inode table, which stores various attributes about it. We will cover inodes in a dedicated lesson.
```
*Read here to know about Inodes: [[#Inodes]]*

>**Data Blocks**
```Quote
This is where the actual content of your files and directories is stored.

	- Text inside documents
	- Images
	- Videos
	- Program data
```

---
## Disk Naming
*/dev/sda vs UUID*

In Linux, names like `/dev/sda` and `/dev/sdb` are **temporary device labels** assigned to storage drives (HDDs, SSDs, USB drives) when the system detects them during boot.

>These names are not permanent and should not be relied on for stable configuration.

### Naming Convention

>**Device prefix**
- **sd** — *Serial Disk* or SCSI disk (modern shorthand for SATA, SCSI, and USB storage devices)

>**Drive order**
- `/dev/sda` — first detected disk
- `/dev/sdb` — second detected disk
- `/dev/sdc` — third detected disk, and so on

>**Paritions** — *Numbers represent partitions on a disk*
- `/dev/sda1` — first partition
- `/dev/sda2` — second partition
- `/dev/sdb1` — first partition on second disk

>[!Important] Important Behavior
>Device names like `sda`, `sdb`, etc. are **not fixed**.
>
>They can change depending on:
>
>- Boot order changes in BIOS/UEFI
>- Adding or removing drives
>- Reordering SATA/USB connections
>- System detection timing during boot
>
>This means:
> A disk that was `/dev/sdb` today may become `/dev/sda` after reboot.
>

### Why UUID is Preferred
To avoid unreliable device naming, Linux uses **UUIDs (Universally Unique Identifiers)**.

>A UUID:
```
- Is stored inside the filesystem
- Does not change across reboots
- Remains stable even if device names change
```
Because of this, UUIDs are the recommended method for:
```
- Mounting disks
- Configuring `/etc/fstab`
- Ensuring persistent storage mapping
```

**To View UUIDs**
```Bash
lsblk -f
or
blkid
```

---
# COMMANDS

---

## Navigation

#### cd / pwd
```bash
cd /path/to/dir    # go somewhere
cd ..              # go back one level
cd ~               # go to home directory
cd -               # go back to previous directory
pwd                # where am I right now?
```

---

#### ls
***List directory contents.***

>**Usage**
```Bash
ls [flags] [path]
```

| Flag | Description                                  | Note                  |
| :--: | -------------------------------------------- | --------------------- |
| `-l` | Long format — permissions, owner, size, date | Full ID card per file |
| `-a` | Show all including hidden files (dotfiles)   |                       |
| `-h` | Human readable sizes (KB, MB, GB)            | Use with `-l`         |
| `-t` | Sort by time modified — newest first         |                       |
| `-r` | Reverse sort order                           |                       |
| `-S` | Sort by file size — largest first            |                       |
| `-R` | Recursive — list all subfolders              | Gets messy fast       |
| `-1` | One file per line                            | Cleaner for scripts   |
| `-i` | shows the [[#Inodes]] number                 |                       |

```bash
ls -lah           # the go-to: long + all + human readable
ls -lah /etc      # at a specific path
ls -lt            # see what changed recently
```

---

#### tree
**Show directory structure visually.**

>**Usage**
```Bash 
tree [flags] [path]
```
*Not installed by default — `sudo apt install tree`*

| Flag | Description | Note |
|:---:|---|---|
| `-L 2` | Limit depth to 2 levels | Most commonly used |
| `-d` | Show directories only | Clean map |
| `-a` | Show hidden files too | |
| `-f` | Show full path for each entry | Useful for copy-pasting |
| `-h` | Human-readable file sizes | |
| `--dirsfirst` | List folders before files | |

```bash
tree ~ -L 2           # home dir, 2 levels deep
tree /etc -L 1 -d     # top-level dirs in /etc only
```

---

## Reading Files

#### **cat**
***Print file contents to screen.***

>**Usage**
```Bash
cat [flags] filename
```
*Best for small files. Use `less` for large ones.*

|       Flag        | Description                                 | Note                                |
| :---------------: | ------------------------------------------- | ----------------------------------- |
|       `-n`        | Show line numbers                           |                                     |
|       `-A`        | Show hidden characters (tabs, line endings) | Useful for debugging spacing issues |
| `cat fileA fileB` | Print multiple files one after another      | Name comes from **con**cat**enate** |

---

#### less
***View large files without flooding the screen.***

>**Usage**
```Bash
less [flags] file
```

| Flag / Key | Description |
|:---:|---|
| `-N` | Show line numbers |
| `+G` | Jump straight to the end on open |
| `Space` | Next page |
| `b` | Back a page |
| `/word` | Search for a word |
| `n` | Next match |
| `q` | Quit |

```bash
less /var/log/syslog
ls /usr/bin | less
```

---

#### head
***Show the first lines of a file.***

>**Usage**
```Bash
head [flags] file
```
*Default: first 10 lines.*

| Flag | Description | Note |
|:---:|---|---|
| `-n 20` | Show first 20 lines | |
| `-n 1` | First line only | Great for checking CSV headers |

---

#### tail
***Show the last lines of a file.***

>**Usage**
```Bash
tail [flags] file
```
*Default: last 10 lines. Most powerful use: watching live logs.*

| Flag | Description | Note |
|:---:|---|---|
| `-n 50` | Show last 50 lines | |
| `-f` | Follow mode — real time updates | Most useful flag |
| `-f -n 50` | Start from last 50 lines then follow live | |

```bash
tail -f /var/log/syslog
tail -f -n 50 /var/log/auth.log
```

---

## Searching

#### **grep**
***Search for patterns inside files.***

>**Usage:**
```Bash
grep [flags] "pattern" filename
```
*Reads one line at a time, makes a decision, moves to the next.*

| Flag | Description | Example |
|:---:|---|---|
| `-i` | Case-insensitive | `grep -i "error" log.txt` |
| `-r` | Recursive — search all files in a folder | `grep -r "TODO" ./src` |
| `-n` | Show line numbers next to matches | `grep -n "error" app.log` |
| `-l` | List only filenames that contain a match | `grep -rl "password"` |
| `-c` | Count matched lines | `grep -c "error" log.txt` |
| `-v` | Invert — show lines that do NOT match | `grep -v "debug" log.txt` |
| `-w` | Whole word only | |
| `-A 3` | Show 3 lines after each match | `grep -A 3 "error" log.txt` |
| `-B 3` | Show 3 lines before each match | |
| `-C 3` | Show 3 lines around match | |

```bash
# AND logic — must match both
grep -i "error" file.txt | grep "2026-06-02"

# OR logic — match either
grep -iE "error|warning" file.txt
```

*See also: [[Whatis#Regex-Regular Expression|Regex]] for advanced patterns.*

---

#### find
***Search for files and directories by name, type, size, time, and more.***

>**Usage":**
```Bash
find [where] [flags] [action]
```
*Searches recursively by default. Companion of `grep` — combine with [[Whatis#The `xargs`|xargs]] for powerful results.*
Use for precise filtering. 
Slower compared to [[#locate]] because *find* command scans the live filesystem.

|                Flag                | Description                                              |                                                                                  |                  |
| :--------------------------------: | -------------------------------------------------------- | -------------------------------------------------------------------------------- | ---------------- |
|                                    |                                                          |                                                                                  |                  |
|            **BY NAME**             | **BY NAME**                                              | **BY NAME**                                                                      |                  |
|          `-name "*.txt"`           | Match pattern — case sensitive                           |                                                                                  |                  |
|          `-iname "*.log"`          | Case-insensitive match                                   |                                                                                  |                  |
|         `-name "config*"`          | Anything starting with `config`                          |                                                                                  |                  |
|         `-name "*config"`          | Anything ending with `config`                            |                                                                                  |                  |
|                                    |                                                          |                                                                                  |                  |
|            **BY TYPE**             | **BY TYPE**                                              | **By Type:**                                                                     |                  |
|             `-type f`              | Files only                                               |                                                                                  |                  |
|             `-type d`              | Directories only                                         |                                                                                  |                  |
|             `-type l`              | [[Whatis#**Symbolic Link?**                              | symlink]]                                                                        | For symlink only |
|                                    |                                                          |                                                                                  |                  |
|            **BY SIZE**             | *(units: `c`=bytes `k`=KB `M`=MB `G`=GB)*                |                                                                                  |                  |
|           `-size +100M`            | Larger than 100MB                                        |                                                                                  |                  |
|           `-size -100M`            | Smaller than 100MB                                       |                                                                                  |                  |
|              `-empty`              | Empty files or folders                                   |                                                                                  |                  |
|                                    |                                                          |                                                                                  |                  |
|            **BY TIME**             | **BY TIME**                                              | **BY TIME**                                                                      |                  |
|            `-mtime -7`             | Modified within last 7 days                              | `-` = less than N days ago                                                       |                  |
|            `-mtime +30`            | Modified more than 30 days ago                           | `+` = older than N days                                                          |                  |
|         `-newer file.txt`          | Modified more recently than `file.txt`                   |                                                                                  |                  |
|                                    |                                                          |                                                                                  |                  |
|         DEPTH AND ACTIONS          | DEPTH AND ACTIONS                                        | DEPTH AND ACTIONS                                                                |                  |
|           `-maxdepth 2`            | Don't go deeper than 2 levels                            | Avoid searching forever                                                          |                  |
|           `-mindepth 1`            | Skip the starting folder itself                          |                                                                                  |                  |
|             `-delete`              | Delete every file found                                  | **Use with extreme caution**                                                     |                  |
|         `-exec cmd {} \;`          | Run a command on each result                             |                                                                                  |                  |
|                                    |                                                          |                                                                                  |                  |
|    BY [[#Inodes\|INODE]] NUMBER    |                                                          |                                                                                  |                  |
|   `-inum [inodeNum]` 2>/dev/null   | Use when single inode is link to multiple filename entry | Recommended to use  [[#ls\|ls command]] first to find the inode number of a file |                  |
| `-samefile [filename] 2>/dev/null` | Same as above — but no need to use the inode number      |                                                                                  |                  |

```bash
find /var/log -name "*.log" -mtime -7
find / -type f -size +100M 2>/dev/null
find /etc -name "*.conf" | xargs grep "port"
find . -empty -type f -delete
```

#### locate
*June 11, 2026*
***the fast file finder — the same family as [[#find]], but work very differently.***
Faster than  [[#find]] because the `locate` command searches an indexed database that contains a list of files.
Use for a quick find where a file is. 

>**Usage**
```Bash
locate [flags] [filename]
#OR
locate [flags] [wherePath] [filename]
```

>**Flags**

| flags         | desc                          |                                                                |
| :------------ | ----------------------------- | -------------------------------------------------------------- |
| `-i`          | Case insensitive              |                                                                |
| `-c`          | count how many matches exists | doesnt print paths                                             |
| `-l 10`       | limit the output to 10        | useful for lots outputs<br>at this point filter your searches. |
| `-r "\.log$"` | Uses a regex pattern          | this example, it search files ending in `.log` exactly         |
| `"*.conf`     | A wildcard                    |                                                                |
 

>A service periodically builds a database(for index). 
>**And to manually update it:** 
```Bash
updatedb
```

>[!Tip] Good thing to do
>The database located at /var/lib is update once daily by systemd timer or [[Tools#**CRON**]].  Its good thing to practice doing that as an exercise.



---

## System Awareness

#### df
**How full are my drives?**

|  Command  | Description                | Note              |
| :-------: | -------------------------- | ----------------- |
|  `df -h`  | Human readable sizes       | Use this always   |
| `df -h /` | Check root filesystem only |                   |
| `df -hT`  | Also shows filesystem type | ext4, tmpfs, etc. |
|  `df i`   | Display [[#Inodes]]        |                   |

---

#### du
**Which folder is eating the most space?**

| Command | Description |
|:---:|---|
| `du -sh *` | Size of every item in current folder |
| `du -sh .* *` | Same but includes hidden files |
| `du -h --max-depth=1` | One level deep |
| `du -h --max-depth=1 \| sort -rh` | Sorted largest first |

```bash
du -h --max-depth=1 / | sort -rh | head -20    # top 20 largest dirs

du -h fileName #Just the size of specific file
```

---

#### ps
**Process Snapshot — frozen picture of what's running.**

| Command | Description |
|:---:|---|
| `ps aux` | All processes from all users with details |
| `ps aux \| grep "python"` | Filter for a specific program |
| `ps aux \| grep -v grep` | Same but hides the grep process itself |

*For live view, use `top`.*

---

#### free
**How much RAM is being used?**
```bash
free -h    # RAM and swap in human-readable sizes
```

---

#### top
**Live process monitor — like `ps` but updates every second.**

```bash
top                     # basic live view
btop                    # nicer version (install separately)
top -u username         # only show a specific user's processes
```

| Key | Action |
|:---:|---|
| `M` | Sort by memory |
| `P` | Sort by CPU |
| `k` | Kill a process (enter PID) |
| `q` | Quit |

---

# Misc
---
#### which
**Find where a command lives.**
```bash
which curl    # → /usr/bin/curl
```

#### ldd
**List what shared libraries a binary depends on.**
```bash
ldd /usr/bin/curl
which ls | xargs ldd
```

#### whereis
*Use to show the path related to the command like — binary, config, libraries, manual*

```Bash
whereis ssh
whereis grep
whereis 
```

---
#### mktemp
***for creating temporary files or folders.***
- Guaranteed to have a unique name — so you never accidentally overwrite something that already exist.

>**BASIC USAGE** 
```bash
mktemp
#Creates a new empty file in /tmp with a random unique name and prints the path to it. Every run gives a different name.

#Output — a unique path printed to screen
/tmp/tmp.aB3xK9
```

>**USEFUL FLAGS**

|           flags            | desc                                            |                                                                                                                            |                                                |
| :------------------------: | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
|            `-d`            | temp folder instead of file                     |                                                                                                                            |                                                |
|            `-p`            | specify the directory on where it will generate | - use when the file needs to survive on reboot.<br>                                                                        |                                                |
| `mktemp /tmp/myapp-xxxxxx` | using custom name and prefixes                  | -the `x` are placeholders<br><br>-mktemp replaces the placeholder with random characters.<br><br>-atleast 3`x`'s is must = | *Example:*<br><br>`mktemp -d /tmp/tempDir-xxx` |

>[!Important]
`mktemp` **is mostly used inside scripts, not interactively.** When you're just exploring the terminal, you rarely need it. But once you start writing bash scripts that need to store intermediate data, it becomes essential.

**In script usage**
>*mktemp prints the path to stdout — if you don't save it, you can't use the file. The standard pattern is to capture it immediately into a variable.*
```Bash
#store of the stdout of the mktemp command in a variable
#the $() is called command substitution
temporary_variable=$(mktemp) 

#now it can be use by calling the variable name as its path
echo "some data" > $temporary_variable
grep "data" $temporary_variable

#clean up when done
rm $temporary_variable

```

>[!Warning] Always clean up
> Files in [[#/tmp]] survive until reboot but not forever. 
> In scripts, delete your temp files when done with rm `$TMPFILE` or `rm -rf $TMPDIR`. Good habit to build early.

#### The *`lsblk`*  command

|                                            Flags / Options                                             | Desc                                                        |                                                      |
| :----------------------------------------------------------------------------------------------------: | ----------------------------------------------------------- | ---------------------------------------------------- |
|                                                  `-b`                                                  | -- block-device : ang ibibigay mong argument                |                                                      |
| sa linux, lahat ng hardware devices ay ***nirerepresent bilang files*** sa loob ng directory na : /dev |                                                             |                                                      |
|                                                  `-l`                                                  | Produce a flat list rather than nested tree structure       |                                                      |
|                                                  `-p`                                                  | Print the absolute full path                                |                                                      |
|                                                  `-d`                                                  | Display the physical disk only — hides the child partitions |                                                      |
|                                                  `-f`                                                  | Display info about filesystem.                              | Use for identifying [[#Why UUID is Preferred\|UUID]] |

---
#### strings command
*June 11, 2026*
***extracts and prints sequence of human readable characters from a file***

>**Usage**
```Bash
strings [flags] filename
```

---
# Other information
---
### A file named `-`
`cat` won't work on a file literally named `-` — it interprets the dash as stdin, not a filename.

```bash
cat ./-    # ./ tells the shell: this is a file, not a flag
cat -- -   # -- tells the command: everything after this is a filename
```

### Swap
*June 15,2026*

>[!Quote] 
If you are low on memory, the system uses this partition to "swap" pieces of memory of idle processes to the disk, so you're not bogged down for memory.

>**3 Main Types**

**Swap Partition (Classic)**
*A dedicated disk partition:*
- Created with [[Tools#mkswap]]
	- `mkswap`
	- enabled via `swapon`
	- Persistent via adding a config at /etc/fstab

**Swap File (modern common default)**
*Just a file acting like swap*

- Persistent at `/etc/fstab` . See the line `/swapfile`

**zram swap**
*A unique one.*
Read here for more: [[Feynman-Technique#**The ZRAM technology**]]

***How to see the all the swap devices***
```bash
swapon --show
```

---
### Block
A **fixed-size chunk of storage** — the smallest unit the filesystem reads or writes at a time.

- A disk is divided into millions of equal-sized blocks
- Each block is labeled as `free` or `used`
- Files claim blocks via inode mapping
- Default block size on ext4: **4 KB (4096 bytes)**
- The moment you make a  [[Documentations#Step2 Format partition (make filesystem)|filesystem]]. It predivides the entire partition into blocks upfront. All of them exist immediately — there are just labeled `free` by block bitmap.

```bash
# Check your filesystem's block size
stat -f /
sudo dumpe2fs /dev/sda1 | grep "Block size"
```
#### How a file is stored in blocks

A 10 KB file on ext4 gets sliced into 4 KB pieces:
```
myfile.txt (10 KB)  
│  
├── Block A [████████] 4 KB (full)  
├── Block B [████████] 4 KB (full)  
└── Block C [████ ] 2 KB used / 2 KB WASTED ← slack space

```

The unused space in the last block is called **slack space**.
It cannot be shared with another file — the whole block is reserved.
```

Blocks needed = ceil(file size ÷ block size)  
= ceil(10 ÷ 4)  
= 3 blocks

Space reserved = 3 × 4 KB = 12 KB  
Slack space = 12 - 10 = 2 KB wasted

```

#### Files do not have to be stored in consecutive blocks

On a fresh disk, a file likely lands neatly:
```d
file.txt → Block 100 → Block 101 → Block 102
```

But after time — other files written, some deleted — free blocks
become scattered, and a new file might land like this:
```d

Block 100 [file.txt - part 1]  
Block 101 [file.txt - part 2]  
Block 102 [other file] ← taken  
Block 103 [other file] ← taken  
Block 104 [file.txt - part 3] ← skipped ahead  
Block 105 [file.txt - part 4]

```

This is called **fragmentation** — the file is still one logical file,
but its physical pieces are scattered across the disk.

> The filesystem handles this transparently. You open `file.txt`
> and get the full file — the scattered blocks are stitched back
> together by the inode, which tracks every block that belongs
> to this file in order.

---

> This is where [[#Inodes]] come in — the inode for each file stores
> the ordered list of every block that belongs to it, allowing the
> filesystem to reassemble scattered pieces into one coherent file.


---

### Inodes
*June 19, 2026*
*Index Node*

>**The core idea**
>*A filename is just a **human-friendly label**. The inode is the **actual identity of the file** — it holds everything real about it. The filename is just a sticky note on the outside. The inode is the file's birth certificate.*

>In short
>*An inode (Index Node) is a filesystem metadata structure that describes a file.* 

>**THE CHAIN**
>*When you open a file— the OS goes through three steps every time.*
```
DIRECTORY ENTRY  |     | INODE #2847         |      | DATA BLOCKS
notes.txt        | ->  | permissions, size   | ->   | actual content
->inode #2847    |     | -> block 4,91,205   |      |the real data

```
*The filename (notes.txt) lives in a directory entry — its just a pointer to an inode number. The inode holds the real metadata and points to actual data blocks.*

>**What an Inode actually stores**

| Store            | Desc                                                                           |
| :--------------- | ------------------------------------------------------------------------------ |
| `File type`      | regular file, directory, [[Whatis#**Symbolic Link?**|symlink]], etc. |
| `Permissions`    | rwxr-xr-x — who can read, write, execute                                       |
| `Owner (UID)`    | user ID of the owner                                                           |
| `Group (GID)`    | group ID                                                                       |
| `File size`      | in bytes                                                                       |
| `Link count`     | how many filenames point to this inode                                         |
| `Timestamps`     | atime (last accessed), mtime (last modified), ctime (inode last changed)       |
| `Block pointers` | addresses of the data blocks that hold this file's content                     |

>**COMMANDS**
```
stat [file]   : Shows full inodes info — size 

ls -i         : Shows the inode number

df -i         : Shows inode usage vs total inodes available

```

>**DEMONSTRATION**
```Bash
touch original.txt
ln original.txt copy.txt
ls -li

#OUTPUT
3074151 -rw-r--r-- 2 
3074151 -rw-r--r-- 2
```
*Notice both files will have same node number. Because linux allows multiple names to point to a same node. Because a file name is just an entry to inode.* 

>The `ln original.txt copy.txt` is hard link. An opposite of [[Whatis#**Symbolic Link**|Symlink]]

>**THE CONSEQUENCE OF THIS DESIGN**
- ***Renaming is Instant*** — only the directory entry(the label) changes. The inode and all data blocks stays exactly where they are. No data moves.

- ***Hard linking*** — Having 2 different filename but links to one node. Deleting one doesnt delete the file — other names still points to the same node. [[Whatis#**Symbolic Link**|Read about symlink]]

- ***Deleting doesnt erase data immediately*** — only removes the directory entry and decrements the link count. Only when link count hits 0 are the blocks marked free. The actual data sits untouched until overwritten.

- ***Inode limit*** — The inode table is fixed at file system creation. A disk with millions of tiny files can exhaust all inodes even if there's plenty of block space left.

>IN SEARCHING OF SAME INODE

Unlike with [[Whatis#**Symbolic Link**]] which displays the path name with use `ls -la` — a hard link does not.
But you can refer to [[#find]] for the flags that is used in searching for filename that uses the same inode.
