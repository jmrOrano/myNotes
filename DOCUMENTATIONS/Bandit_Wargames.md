
*June 26, 2026*
*The plan here to put only the 'worth things to note about'. If this things gets messy then i dont know how it happened*

## Level 4 to 5 
---
*Level Goal: The password for the next level is stored in the only human-readable file in the **inhere** directory. Tip: if your terminal is messed up, try the “reset” command.*

**What I did and it worked**
```Bash
cat ./* | strings
```

>Not ideal because
>1. It lose file-level context
>	Not longer knows which file contained the pasword and where it came from
>2. It mix unrelated files
>	It combines everthing into one steam. Its messy and non-diagnostic
>	```
>	file 1 + 2 + 3 -> single blob
>	```

**Better Approach**

>Option1: Per-file inspection (best beginner habit)
```
strings ./*
```
*Similar output to what i did but it does not display all the gibberish texts*

>Option2: Identify readable files first
```
file ./*
```

---

## Level 8 to 9
---
*Level Goal : The password for the next level is stored in the file data.txt and is the only line of text that occurs only once* 

Reading the content of the file outputs a total 1001 lines of texts.
```
cat data.txt -n
```
>*I had the idea of using the `uniq` command and `pipe` 
```bash
cat data.txt | uniq
```
>but, no idea how to execute it  to extract the password  so i had to rely and asked an A.I*


**The Approach**

>Sort it first 
```bash
sort data.txt
```
*Doing this outputs the content in A-Z from top to bottom*

>Then use it together with uniq command
```bash
sort data.txt | uniq -u
```
`-u`  flag is to print only the lines that appears exactly once.

>*I want to re-create this as an Obfuscation lab exercise*

---
## Level 13 to 14
---
*June 27, 2026*
*Level Goal: The password for the next level is stored in **/etc/bandit_pass/bandit14 and can only be read by user bandit14**. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Look at the commands that logged you into previous bandit levels, and find out how to use the key for this level.  
If you need help with this level: a hint file can be found in the home directory.  
Make sure to read the error messages as they are informative.*

I have an experience of using an `scp` command to backup a data. 
I also have an experience in setting up an SSH key but, this level flipped the things what i already know. 

>**In normal setup (what i knew as of writing this)**
```
The client-machine generates a key pair
The private key stays in client machine
The public key gets copied to the server
The client SSH in using its private key
```

>**What this level doing (flipped)**
```
Bandit SERVER has the private key
I need to DOWNLOAD that private key to my local machine
Then use it to SSH into the next level
```

*I never realized at this point that i already have done this before at  trying to back a minecraft data using rsync method with ssh. Where i need to use the host machine's ssh key stored at `C:/Users/username/.ssh/` and copy it and paste it to the WSL filesystem *

>**The approach**

>Identify where the private key is located inside the server

>Copy the private key to local machine. But do logout first
```bash
logout
scp -P 2220 bandit13@bandti.labs.overthewire.org:[key_path] [~/.ssh]
```

>Change the permission of the file to `600`
```bash
chmod 600 <fileName>
```
Or else you will get an error like this when using it to ssh:
```mesage
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0640 for '/home/linuxuser/bandit/sshkey.private' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/home/linuxuser/bandit/sshkey.private": bad permissions

```

>SSH into the next level using private key
```bash
ssh -P 2200 -i ~/.ssh/[filename] <user>@<host_addr>
```

## Level 14 to 15
---
*June 27, 2026*
*Level Goal: The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.*

*I struggle here and took me half-day. I can use AI to know how to do this but that is just so shjt. So instead i asked AI what are the things necessary knowledge i should to understand whats going on in this level — And I ended up understanding things about: *
- [[Tools#Netcat (nc)]]
- [[RANDOM FOR NOW#SOCKETS]]
- [[PROXY#SOCKS PROXY (SOCK4 & SOCKS5)|SOCKS]] *I made a mistake and confused it to sockets so i took a lot of time here which is not related*
- [[How_host_do_speaks_on_the_internet#Layer Placement for Real world|TCP/IP Stack in Practical Visualization]]

## Level 16 to 17
---
July 03, 2026
*Level Goal: The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.*

I tried the following:
```
ss -l
ss -tulnp
```
Dont know how to use it properly. I use `nc` nectcat command
```
nc localhost -zv locahost 31000-32000
```
It outputs list of `Connection Refused`. But, i noticed some `succeded` but still messy so i tried to dump the `connection refused` in `/dev/null`.
```
nc localhost -zv localhost 31000-32000 2>/dev/null
```
But, even the succeeded connection gets dumped as well. Also, even i filtered it out i wont find out which of them speaks SSL/TLS `Connection to localhost (127.0.0.1) 31960 port [tcp/*] succeeded!
`

So i tried `nmap`
```
bandit16@bandit:~$ nmap localhost 

Starting Nmap 7.98 ( https://nmap.org ) at 2026-07-03 08:38 +0000
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000093s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 993 closed tcp ports (conn-refused)
PORT      STATE SERVICE
22/tcp    open  ssh
1111/tcp  open  lmsocialserver
1840/tcp  open  netopia-vo2
4321/tcp  open  rwhois
8000/tcp  open  http-alt
30000/tcp open  ndmps
50001/tcp open  unknown
```
I found out that it didnt display ports from `31000-32000`. So I search for a flag that uses specified port
```
bandit16@bandit:~$ nmap -p 31000-32000 localhost 

Starting Nmap 7.98 ( https://nmap.org ) at 2026-07-03 08:40 +0000
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00013s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown
```
It did find the ports. Im confused why not specifying the flag in the first run didnt find it. 
Now, for identifying if one is using `SSL/TLS` and which dont. The level recommended use are 
```
ssh, telnet, nc, ncat, socat, openssl, s_client, nmap, netstat, ss
```
Doing the `openssl` in each port is tedious, so i gave up.  And did a search for better way.

##### The Approach
- Use the `-p` flag. Because without it, the nmap only searches for the 1000 most common ports
- Then for the searching which uses TLS/SSL — add a flags `-sV` that tells the service/version
>[!Message]- 
>``` 
bandit16@bandit:~$ nmap -p 31000-32000 -sV localhost 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-07-03 09:00 +0000
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00015s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.98%T=SSL%I=7%D=7/3%Time=6A477A23%P=x86_64-pc-linux-gn
SF:u%r(GenericLines,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cur
SF:rent\x20password\.\n")%r(GetRequest,32,"Wrong!\x20Please\x20enter\x20th
SF:e\x20correct\x20current\x20password\.\n")%r(HTTPOptions,32,"Wrong!\x20P
SF:lease\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(RTSPReq
SF:uest,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20pass
SF:word\.\n")%r(Help,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\.\n")%r(FourOhFourRequest,32,"Wrong!\x20Please\x20ent
SF:er\x20the\x20correct\x20current\x20password\.\n")%r(LPDString,32,"Wrong
SF:!\x20Please\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(S
SF:IPOptions,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x2
SF:0password\.\n");
>
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 123.54 seconds
>
>```

The password of level 16 has letter `k` to it 
```
kS0Hf0u5HiXFwKMKFqXvPdOTNGGa0X8V
```
And according to [manual](https://docs.openssl.org/3.0/man1/openssl-s_client/#options) of openssl client in the section of CONNECTED COMMANDS
```Quote
If a connection is established with an SSL server then any data received from the server is displayed and any key presses will be sent to the server. If end of file is reached then the connection will be closed down. 

When used interactively (which means neither **-quiet** nor **-ign_eof** have been given), then certain commands are also recognized which perform special operations. These commands are a letter which must appear at the start of a line. They are listed below.

- **Q**
    
    End the current SSL connection and exit.
    
- **R**
    
    Renegotiate the SSL session (TLSv1.2 and below only).
    
- **k**
    
    Send a key update message to the server (TLSv1.3 only)
    
- **K**
    
    Send a key update message to the server and request one back (TLSv1.3 only)
```
This means that if the password gets copied and display `KEYUPDATE` — its most likely in **interactive mode** unless added the flag `-quiet` during the connection
```
openss s_client -quiet localhost 31790
```


---
## Level 17 to 18
---
*July 11, 2026*
```
## Level Goal
There are 2 files in the homedirectory: **passwords.old and passwords.new**. The password for the next level is in **passwords.new** and is the only line that has been changed between **passwords.old and passwords.new**

**NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19**

## Commands you may need to solve this level
cat, grep, ls, diff
```

##### My mental approach
---
is : Use the [[Commands#sort command|sort]] + [[Commands#uniq command|uniq]] combo command 
```
ls
passwords.new paswords.old
sort passwords.new passwords.old | uniq -u
```
The output :
```
OQxXZjELndr90zuhOTDYBEomI0SZITXI
qOg5pVOjPx9x9VccyYBADiT4xxyoUB8D
```
Im impressed. I can manually try those two password when logging in to next level. But, according to level goal. 
`The password is in password.new` . 

I dont know which one of them is is from the `new` or from the `old`. 


---
##### The Approach
---
Instead of using the `sort + uniq -u` approach.

Use `diff` command.
```Bash
diff passwords.new passwords.old
```
*I have made a notes about the command which can be read here:* [[Commands#diff command|diff]]

---

## Level 18 to 19
*July 12, 2026*
```
## Level Goal

The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.

## Commands you may need to solve this level

ssh, ls, cat
```

- This level is related to level 17 to 18. 
- Logging in to level 18 just auto logs it out with message of `Byebye !`

##### My Mental Approach
---
- According to the clue, someone modified the `.bashrc`. I have a note about this file before which can be read at : [[Linux_File_System__Major_Only_#The .bashrc File| .bashrc]] 
- I have the notes, but i dont know the practicality of how it is related to ssh session. So i did a bit of search which now i added a diagram in the [[SSH_Setup#**Uncomplicated Architecture**]]
- Moving on.  I tried reading the `/bashrch` file during the ssh:
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org "cat ~/.bashrc"
```
- Found out that at the end of the line, there is an added command of:
```Bash
echo 'Byebye !'
exit 0
```
- I searched about it. It says that this will cause the terminal window or SSH session to **instantly close** every single time when trying to open it.
- Online search also states that reading the `.bashrc` can be bypass directly since its just a `Bash Configuration`
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org "sed -i 'exit 0/d' ~/.bashrc"
```
- But it only result to:  (i dont know what `sed` command is) 
```Bash
sed: couldn't open temporary file /home/bandit18/sedUyiWg9: Permission denied
```
- So i tried to  rename the file hoping it the `.profile` would not read it. but: it did not work as well:
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org "mv ~/.bashrc ~/.bashrc_backup"
mv: cannot move '/home/bandit18/.bashrc' to '/home/bandit18/.bashrc_backup': Permission denied
```
- I did expect though that this wont work because the game is strict in permissions.
- Another method i found is using a different shell
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org -t "sh"
```
- **It worked.**

##### The breakdown
---
- Using different shell like `sh` did worked because it doesnt read the `.bashrc` file. 
- The `.bashrc` is specific for Bash only.
- During the ssh login attempt, the `.bashrc` file gets read last therefore executing the `exit 0`. 
- By changing the shell to `sh` it bypasses (indirectly) the file

##### The Proper Approach
---
- After some thinking.  I realized i can just directly read the `readme` file in the home directory without an interactive shell
```Bash
cat readme
```
- I literally forgot about that. But it is, what it is.
- I also realized that you the `-t` flag is "for interactive terminal" – which is already the default behavior of standard SSH login attempt, thus explicitly stating the flag like: 
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org -t 
```
- Is the same as :
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org
```
- The one that manages to solve it is the command `"sh"`: 
```Bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org "sh"
```
- Though without the `-t` flag, the terminal will look like plain, with no symbol `$` .

## Level 19 to 20
---
*Augus 08, 2026*

Level goal:
```
To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.
```

###### **My mental approach**
---

Upon logging in, i run the ff commands:
```
ls
stat bandit20-do
```
It shows the ff:
```bash
  File: bandit20-do
  size: 14880     	Blocks: 32         IO Block: 4096   regular file
Device: 259,1	Inode: 300215      Links: 1
Access: (4750/-rwsr-x---)  Uid: (11020/bandit20)   Gid: (11019/bandit19)
Access: 2026-08-07 17:27:05.229489493 +0000
Modify: 2026-06-24 14:59:09.608243504 +0000
Change: 2026-06-24 14:59:09.613243539 +0000
 Birth: 2026-06-24 14:59:09.608243504 +0000
```

>The way i understand this is level is :
- The file `bandit20-do` is owned by user `bandit20`
- The file has a special permission of [[User Management#**SUID**|SUID]] 
- The file belongs to the group `bandit19`, The user of this current session — meaning, i can execute it.

**Executing the file**
*I tried the ff:*
```bash
bash bandit20-do
===========bandit20-do: bandit20-do: cannot execute binary file

bandit20-do
===========bandit20-do: command not found
```
Both failed, i  had to google.

##### **The proper approach**
---
Same principles to beginning. DO:
```
./bandit20-do
```

Outputs:
```c
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
```
Its a clue, meaning i can run a single line command when executing the file.
```c
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
```
The password is:
```
4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA
```

**How things works in this level**
- Normally,  a program runs as the user who executes it.
- With SUID, the program runs as the file's owner regardless of who actually runs it.
- In this level the file is a tool that temporarily grants the user `bandit19` the identity of user `bandit20`– just enough to read the password.



---

## Level 20 to 21
---
*August 08, 2026*

Level goal:
```
There is a setuid binary in the homedirectory that does the following: 
---it makes a connection to localhost on the port you specify as a commandline argument. 
---It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

**NOTE:** Try connecting to your own network daemon to see if it works as you think

## Commands you may need to solve this level
ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)
```

##### **My mental approach**
---
This is like same principle to [[#Level 19 to 20]] but with complexity.
I checked the binary file:
```c
bandit20@bandit:~$ stat suconnect
  File: suconnect
  size: 15604           Blocks: 32         IO Block: 4096   regular file
Device: 259,1   Inode: 300217      Links: 1
Access: (4750/-rwsr-x---)  Uid: (11021/bandit21)   Gid: (11020/bandit20)
Access: 2026-08-07 16:57:01.129159582 +0000
Modify: 2026-06-24 14:59:10.943252643 +0000
Change: 2026-06-24 14:59:10.949252684 +0000
Birth: 2026-06-24 14:59:10.943252643 +0000
```

When i run the file, a message appears
```c
bandit20@bandit:~$ ./suconnect                                                       
Usage: ./suconnect <portnumber>                                                      
This program will connect to the given port on localhost using TCP. If it receives th
e correct password from the other side, the next password is transmitted back.  
```

I dont know what port to use so i run nmap targeted to localhost:
```
bandit20@bandit:~$ nmap localhost
Starting Nmap 7.98 ( https://nmap.org ) at 2026-08-08 14:12 +0000
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00011s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 992 closed tcp ports (conn-refused)
PORT      STATE SERVICE
22/tcp    open  ssh
1111/tcp  open  lmsocialserver
1234/tcp  open  hotline
1840/tcp  open  netopia-vo2
4321/tcp  open  rwhois
8000/tcp  open  http-alt
30000/tcp open  ndmps
50001/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

I tested each port, most of them does nothing except `port 4321`
```
bandit20@bandit:~$ ./suconnect 4321
Read: 100 phrack search daemon, type help for more information
ERROR: This doesn't match the current password!
```

i tried pasting the password, for level 20 but didnt work:
```
bandit20@bandit:~$ ./suconnect 4321 4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA
Read: 100 phrack search daemon, type help for more information
ERROR: This doesn't match the current password!
```

A bit of reiew about the level:
`**NOTE:** Try connecting to your own network daemon to see if it works as you think`
So i try connecting to localhost using [[Tools#Netcat (nc)]]
```
bandit20@bandit:~$ nc 127.0.0.1 4321
100 phrack search daemon, type help for more information
4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA
```
But it didnt do anything.

---

##### **The proper approach**
---
I misunderstood the instruction. 
There should be two terminal running

1st terminal within the ssh session:
```
echo "bW9kBv5WC3P4yoDyf12LSdGuNz5ka6hY" | nc -l 9999
```

2nd terminal within the ssh session as well:
```
./suconnect 9999
```

The break down:
- `Terminal 1` – the one who will listen and will wait for those who will connect
- `Terminal 2` – the one who will connect using the `suconnect` binary file.
- `suconnect` – the binary file that Terminal 2 will use.
- Once terminal 2 runs the file, it will connect to terminal 1 and will echo the password.
- The Terminal 2 will receive the echoed password and if its correct, it will send the next password to terminal 1

---

## Level 21 to 22
---
*August 14, 2026*

Level Goal:
```
A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.
```

##### **My Approach**
---
Upon logging in I immediately checked the location
```bash
cd /etc/cron.d && ls

===OUTPUT===
behemoth4_cleanup  clean_tmp  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  e2scrub_all  leviathan5_cleanup  manpage3_resetpw_job  otw-tmp-dir
```

>I dont know where to start so i first searched about cron jobs and took a note of it at:
[[Tools#**Cron**]]

I tried to output all whats inside the directory:
```bash
cat *
====OUTPUT=====
bandit21@bandit:/etc/cron.d$ cat *
*/30 * * * * root find /tmp -amin +60 -type f,l -delete &> /dev/null && find /tmp -amin +5 -type d -empty -delete &> /dev/null
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
30 3 * * 0 root test -e /run/systemd/system || SERVICE_MODE=1 /usr/libexec/e2fsprogs/e2scrub_all_cron
10 3 * * * root test -e /run/systemd/system || SERVICE_MODE=1 /sbin/e2scrub_all -A -r
cat: behemoth4_cleanup: Permission denied
cat: leviathan5_cleanup: Permission denied
cat: manpage3_resetpw_job: Permission denied
cat: otw-tmp-dir: Permission denied
```
I know how to read them as i made a searching first before proceeding. But still, i dont know what to do with them.  Im aware there are cron jobs running every minute executing a file.

>I tired listing cron jobs, but didnt work due to permission as expected:
```bash
bandit21@bandit:/etc/cron.d$ cron jobs

===OUTPUT===
cron: can't open or create /var/run/crond.pid: Permission denied

bandit21@bandit:/etc/cron.d$ crontab -l
===OUTPUT===
crontabs/bandit21/: fopen: Permission denied

```

>Next, i tried navigating where the script is located. I first check the `/usr/bin/cronjob_bandit22.sh `
```bash
cd /usr/bin/
cat cronjob_bandit22.sh

===OUTPUT===
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
My translation. 
- the [[User Management#chmod|chmod]] changes the permission for the `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv` to have a read access to `others`
- it then redirects `>` the password  for `/etc/bandit_pass/bandit22` using cat command into the temp file. Giving me –`bandit21` a read access

>I can now have the content of the file without actually having a read access to `/etc/bandit_pass/bandit22 `
```bash
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
====OUTPUT=========
RYVux2rHEm9tiXHmLFzuR7Vhx6AZQMEz
```

>**Additional Information**

The script file is actually owned by `bandit22`. But the group owner is `bandit21`
```bash
bandit21@bandit:/usr/bin$ stat  /usr/bin/cronjob_bandit22.sh 
====OUTPUT====
  File: /usr/bin/cronjob_bandit22.sh
  size: 130             Blocks: 8          IO Block: 4096   regular file
Device: 259,1   Inode: 26504       Links: 1
Access: (0750/-rwxr-x---)  Uid: (11022/bandit22)   Gid: (11021/bandit21)
Access: 2026-08-13 15:12:01.612051668 +0000
Modify: 2026-06-24 14:59:12.219261377 +0000
Change: 2026-06-24 14:59:12.226261425 +0000
 Birth: 2026-06-24 14:59:12.219261377 +0000
```
The permission for the group is set by `5` having a `read` and `execute` permission.  Allowing me to read it.

---

## Level 22 to 23
---
*August 18, 2026*
Level Goal:
```
A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE:** Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.
```

##### **My approach**
---
>Upon logging in i checked for the crontab related to bandit23:
```
====COMMAND===
ls -lah

===OUTPUT====
-rw-r--r--   1 root root  122 Jun 24 14:59 cronjob_bandit23
```

>It has read access to others. So i read it:
```
===COMMAND===
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23

===OUTPUT===
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```
The script being run by user `bandit23` is located at ` /usr/bin`. Similar to previous level.

>Inside the `/usr/bin`. I first checked for the permission of the script.
```
====COMMAND====
bandit22@bandit:/usr/bin$ ls -lah | grep "cronjob_bandit23.sh"

===OUTPUT===
-rwxr-x---       1 bandit23 bandit22   211 Jun 24 14:59 cronjob_bandit23.sh
```
- It doesnt have any permission to `others`
- But it has execute permission for group owner. 
- The group owner of this file is `bandit22` — meaning as bandit 22, i can execute this.

>I check the content of the script next.
```
===COMMAND===
bandit22@bandit:/usr/bin$ cat cronjob_bandit23.sh

===OUTPUT===
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```
The way i understand how this work is:
- The cron job set for user `bandit23` is executing the script ` /usr/bin/cronjob_bandit23.sh`
- Inside the script it has line `myname=$(whoami)`. Its a `command substitution` mentioned at my notes about [[Linux_File_System__Major_Only_#mktemp|mktemp]]
- The value for the `myname` variable will be `bandit23` since. Its the username set to execute the command at crontab. 
- My problem is, i dont understand the `md5sum` command is used for at the line : `mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)`
- I did google about `md5sum` : All i understand for now is that it creates a unique digital signature called `checksum` — which im not aware yet as of this writing. But i read what i think is important:
  `Changes completely if even one letter in the file changes.` I have a hunch that its random but its a fixed output depending on input.

> I tried checking the contents inside `/tmp`
```
===COMMAND===
cd /tmp
bandit22@bandit:/etc/cron.d$ cd /tmp/

===OUTPUT===
ls: cannot open directory '.': Permission denied
```
Got denied.

>Attempt workaround.
- Since the i have the line of command for the variable `mytarget`. Maybe i can recreate it.
```
===COMMAND===
bandit22@bandit:/tmp$ mytarget=$(echo I am user bandit23 | md5sum | cut -d ' ' -f1)
bandit22@bandit:/tmp$ echo $mytarget

===OUTPUT===
8ca319486bfbbc3663ea0fbe81326349

```

>Now attemping to read.
```
===COMMAND===
bandit22@bandit:/tmp$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349

===OUTPUT====
gKXDTAXnIz3OBxiPjRZ2uqutUlPZrBsw
```
**IT WORKED**

---

## Level 23 - 24
---
*August 19, 2026*

Level Goal:
```
A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE:** This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

**NOTE 2:** Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…
```

##### **My Approach**
---
Check what's inside the `/etc/cron.d`:
```
====COMMAND====
cd /etc/cron.d && ls

====OUTPUT====
behemoth4_cleanup  cronjob_bandit22  cronjob_bandit24  leviathan5_cleanup    otw-tmp-dir
clean_tmp          cronjob_bandit23  e2scrub_all       manpage3_resetpw_job
```

Reading the crontab for `bandit24`:
```
====COMMAND====
cat cronjob_bandit24

====OUTPUT====
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```