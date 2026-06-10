
*The practice of tracking system activity through log files and monitoring tools to detect errors, performance issues, and security events in Linux environment*



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