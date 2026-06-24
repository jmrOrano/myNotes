
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

