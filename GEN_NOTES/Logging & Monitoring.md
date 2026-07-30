#
*The practice of tracking system activity through log files and monitoring tools to detect errors, performance issues, and security events in Linux environment*

## What are logs / Why they matter and exist

The services, [[Whatis#**What is a kernel?**|kernel]], and [[Whatis#Deamons|daemon]] on your system are constantly active. This activity is recorded and saved on your system in files called logs, creating a human-readable journal of all important system events.

System logs are essential for 
- Monitoring system health,
- Troubleshooting problems and;
-  Auditing security.

>This data is typically stored in the [[Linux_File_System__Major_Only_#/var|/var]] directory, which is designated for variable data like logs.
## Types of Logs
### General/System Logs
### Kernel Logs
### Authentication Logs
### Application Logs

## Logging Systems
### syslog / rsyslog


### journald

## Common Log Locations
### /var/log
### syslog
### auth.log
### kern.log

## Reading Logs
tail, grep, less, journalctl

## Log Levels
## Log Rotation
## Monitoring Basics
## Practical Exercises
## Questions



*TEMPLATE NUMBER 1 FOR NOW*
**CORE COMMANDS**
	`journalctl`
		`--since`
		`--until`
		`--verify` `#good for security check if logs are tampered`
	`dmseg`'
	`tail -f`
	`less`

**COMMON USE CASE**
	`check boot logs`
	`debug service failure`
	`monitor live logs`

**KEY PATHS**
	`/var/logs/syslog`
	`/var/log/auth.log`
	`etc...`



**PERSONAL NOTES /PATTERNS**
- concept of `logrotate` - for prevention of logs eating the disk space

*TEMPLATE NUMBER 2 FOR NOW*

{List of Subtopics}
	Overview of each subtopic
			Syntax
			Common flags
			Examples
			Tips
Add additional if necessary


# JOURNALCTL

>To clean journalctl at `/var/log`

|                            Commands                             | Desc                          |
| :-------------------------------------------------------------: | ----------------------------- |
|                    `journalctl --disk-usage`                    | Check for current usage first |
|                                                                 |                               |
|                      OPTION A: TIME BASED                       | OPTION A                      |
|               `sudo journalctl --vacuum-time=7d`                | Keeps only last 7 days.       |
|                                                                 |                               |
|                      OPTION B: SIZE-BASED                       |                               |
|               `sudo journalctl --vacuum-size=100`               |                               |
|                                                                 |                               |
|                     FOR PERFECT CONTROL<br>                     | FOR PERFECT CONTROL           |
|     edit config at : `sudo nano /etc/systemd/journald.conf`     |                               |
| SystemMaxUse=200M<br>SystemKeepFree=1G<br>SystemMaxFileSize=50M |                               |
|            `sudo systemctl restart systemd-journald`            |                               |

#  HOW TO

### How to read parse a json log file

Install the package named `jq`
```bash
apt install jq
```
Verify if  the file you will read is a JSON.
```bash
file file.log
# or
head file.log
```
If its verified and confirmed:
```bash
jq . file.log #the dot . is necessary and is a filter expression that means the output is unchange but formatted. 
```
Depending on the log file, certain key can be use to replace the dot `.`
```bash
'.user'
'.message'
#or multiple
'{time, user, method, url, message}'
```

---

>Filtering by values:  Use the `select()` fucntion
```bash
jq '.[] | select(.key == "value")' file                              #single value
jq '.[] | select(.key1 == "value" and .key2 == "value")' file        #mroe than 1 key and value using the AND operator
jq '.[] | select(.key1 == "value" or .key2 == "value2)' file         #same as above but using the OR operator
```
Note: remove the `.[]` if the log file is not enclose in an `[]-array`. 

---

>Filtering a dynamic values of certain key. — Pattern matching. Use `contains()` or `test()` function.
```bash
jq 'select(.key | contains("matching pattern"))' file             #common pattern
jq  'select(.key | test("matching pattern") and .level == 2)'     # adding another key. The parenthesis matters!
jq 'select((.key | contains("matching pattern)) and .key == value) | {value, message, etc}'  # if you want to incluse some other key as well, but dont forget to add the exisiting filter key

#USE TEST() FUNCTION FOR CASE INSENSITIVE
jq 'select((.key | test("matching case"; "i")) and .key == value) | {key1, key2, key3}' file
```


---


# Log files

### Abstraction level of how log works

|       Layer        | Kernel Side                                              | Userspace Side                                                       |
| :----------------: | -------------------------------------------------------- | -------------------------------------------------------------------- |
|      Producer      | Kernel                                                   | Apps (sshd, dockerd, cron, etc)                                      |
|    Temp holding    | Ring buffer (inside RAM)                                 | Sockets (for transport only)                                         |
|     Collector      | journald(via /dev/kmsg)<br>reads kernel-messages         | journald(via /dev/log) or native socket<br>receives app messages     |
|       Viewer       | `dmesg` (direct from ring buffer)<br>`journalctl -k`     | `journalctl` ( for processed journal)<br>`journalctl -u ssh`         |
| Persistent storage | `/var/log/journal` (binary)<br>or<br>`/var/log/kern.log` | `/var/log/journal`(binary)<br>or<br>`/var/log/auth.log` and etc.<br> |

### 2 main log producers
```
               PRODUCERS

        Kernel            User Space
           │                  │
           │                  │
           ▼                  ▼
     Kernel Messages    Application Messages
           │                  │
           └──────────┬───────┘
                      │
              systemd-journald
                      │
               Journal Storage
                      │
             ┌────────┴─────────┐
             ▼                  ▼
        journalctl         rsyslog (optional)
                               │
                               ▼
                        /var/log/*.log
```

---
### Persistent Log

|        Types        | Examples                                                 |     |
| :-----------------: | -------------------------------------------------------- | --- |
| Authentication Logs | - `/var/log/auth.log`<br>- `sshd`<br>-`sudo`<br>-`PAM`   |     |
|                     |                                                          |     |
|     System logs     | -`/var/log/syslog`<br>-General System events             |     |
|                     |                                                          |     |
|     Kernel Logs     | - `/var/log/kern.log`<br>Hardware, Driver, Kernel Events |     |
|    Service Logs     | `Docker`<br>`Fail2ban`<br>`Nginx`<br>etc                 |     |

### kernel log
*/var/log/kern.log*

It logs the ff events: 
- Hardware detection 
- Driver events
- Network interface
- Disk and filesystem events
- and Kernel warning/errors


## Commands

### dmesg 
*Diagnostic message*

- Reads the messages from the [[Whatis#What is a kernel ring buffer?|kernel ring buffer]]
- A temporary log

For a modern linux system:
```
                    Kernel
                       │
          ┌────────────┴─────────────┐
          │                          │
          ▼                          ▼
 Kernel Ring Buffer          systemd-journald
          │                          │
          │                          ▼
       dmesg                 Journal Database
                                     │
                                     ▼
                               journalctl
                                     │
                        (-k filters kernel messages)
```

## journaling
---
July 28, 2026
- Reading persistent log files at  `/var/log/*` – is the traditional file-based logging
- Reading logs via `journald` – a query tool for `systemd journal`, a structured logging system

### Common commands used
---
- `--since`:  – options are : `today`, `"yyyy-mm-dd"`, `"N minutes ago"` ``
- `--until`
- `--verify`

---
### How to check journaling current usage
---
- Just like regular log files, a journal is stored as a binary journal files.
- In a sense it looks like continuous, But, its not infinite. It has a retention policy:
	- `SystemMaxUse=`         – Max disk usage
	- `SystemMaxFileSize` – Max individual file size
	- `MaxRetentionSec=`   – Retention time
The location of binary journal files is at:
```
/var/log/journal/
└── machine-id/
    ├── system@0005a3.journal
    ├── system@0005a4.journal
    └── system@0005a5.journal
```

>Check current usage:
```bash
journalctl --disk-usage
```

---
### How to journal ssh
---
```bash
sudo journalctl -u ssh
journalctl -u ssh --since "2026-07-20" --until "2026-07-28"
```

---
