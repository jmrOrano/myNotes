*June 07, 2026*

# RANDOM SHJT FOR NOW

# For unzipping

## **Gunzip** 
*June 07, 2026*
*For gzip* — not for `.zip` or `.tar`

### **Usage for Decompressing**
```
gunzip filename.gz
```

>Commands and Flags

| Usage               | Desc                                                                                  |     |
| :------------------ | ------------------------------------------------------------------------------------- | --- |
| `gunzip -k file.gz` | Keep the original `.gz` file                                                          |     |
| `gunzip -l file.gz` | Just view the info(without extracting)<br>`compressed size, uncompressed side, ratio` |     |
| `gunzip -f file.gz` | Force **Decompress**                                                                  |     |
| `gunzip -c file.gz` | Shows the content without extracting (just print)                                     |     |

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
*June 07, 2026*
*Slower then [[#**Gunzip**]] but compresses smaller.*
> Similarly — it also compress files and not directories

| Flags | Desc                         |                                                   |
| :---: | ---------------------------- | ------------------------------------------------- |
| `-d`  | Use in bzip2<br>`decompress` | `bzip2 -d file`                                   |
| `-k`  | keeps the `.bz2` file        |                                                   |
| `-f`  | `Force overwrite`            | Overwrite the exisitng output file without asking |
| `-v`  | `verbose`                    | Shows compression progress                        |


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
bzip2 file.txt
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
# **CRON**

## systemctl


# FOR DISK-RELATED TOOLS

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


# File Transfer Tools

### Rsync

**With SSH**
```bash
rysync -avz --progress -e "ssh -p [portNum]" username@ipaddress:[source] [destinationToLocalMachine] 
```