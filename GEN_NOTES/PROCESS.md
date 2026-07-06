*June 30, 2026*

## Why study about processes? 
```
Every attack that lands on linux system eventually has to 'become' a process — a reverse shell, a cryptominer, a privilege escaltion exploit, all of it runs as a a PID with a parent, a state, and a set of permissions. 

Understanding how processes are created, monitored, and killed isn't just  sysadmin trivia — its the foundation for: 

- Detecting Intrusions: Spotting a suspicious parent-child relationship (e.g a web server spawning a shell) relies on understanding fork/exec
- Persistence Techniques: Attackers abuse orphaned processes, init scripts, and process masquerading to survive reboots and evade detection
- Privilege Escalation: Many Exploits hinge on how the kernel manages process ownership, signlas, and the `/proc` filesystem
- Incident Response: Reading `ps`, `/proc`, and process states quickly is often the first step in figuring out what a compromised box is actually doing right now
- Evasion awareness: Knowing how zombies, signals, and job control work helps understand recognize when an attacker is using them to hide or persist.
  
In short: We cant defend (or attack) what you dont understand. This section builds the OS-level fluency that later security topics — like process injection, rootkits, and live forensics — depends on.
```

## What is a Linux Process
---
*June 30,2026*
*A process are/is a running instance of a program in a machine*

Program is just an inert code stored in a disk. The moment you run it, the kernel creates a process: It allocates memory, loads the code into that memory, gives it a unique ID number (PID), and starts executing it. **At the moment The program becomes a process.**

### What makes a process?
*the kernel needs to track all of this for every single one*
`PID` — A unique number identifying it (like an employee ID badge)
`Memory Space` — its own private chunk of RAM, isolated from other processes
`Code` — That actual instructions being executed
`State` — What its currently doing(running, waiting, sleeping) — it is cover at [[#Process States]]
`Open Files Resources` — files, network connections, etc. it currently has open
`Parent PID(PPID)` — which process created it (every process except PID1)

### What happens when a process starts?
*When you execute a program, the kernel creates a process and provide it with everything it needs to run*
`Process ID (PID)`
`Memory`
`CPU Time`
`Open files`
`Environment Variables`
`Permissions`
`Scheduling Information`
`Signal Handling Information`

### Every running command becomes a process
Suppose we type:
```
sleep 100
```
Linux creates something like:
```
PID: 2345
Program: sleep
State: Sleeping
Owner: <your user>
Memory: Allocated
```
## The kernel's role in process management
***The kernel is the core of OS***. Everything else — your terminal, your browser, your scripts — are just processes _requesting_ things from it. The kernel is the only software that directly talks to hardware (CPU, RAM, disk).

Think of the kernel as an **operating system dispatcher at an airport.** It doesn't fly the planes — it decides _who takes off, when, which runway, and how long they get._ No plane moves without its say.

It does four main jobs:

### 1. Process Creation and Destruction
When you run a command, the kernel is reponsible for :
- creating processes ([[#fork()]], [[#exec()]]) 
- Assigning PIDs
- Setting up Memory and resources
- Cleaning up when process ends (`exit()`, `cleanup`, `zombie reaping`)

### 2. CPU Scheduling (Time Sharing)
The CPU can only run one thing per core at a time. The kernel rapidly  switches between processes (thousands of times per second) giving each a tiny time slice — so fast it feels like everything runs simultaneously. Its not. This is called **context switching**. 

>The kernel decides:
- which process runs now
- which process waits
- how long each process gets CPU time
This is why multitasking works.

### 3. Process State Management
>The kernel tracks every process state:
- Running ([[#R]])
- Sleeping ([[#S]])
- Uninterruptible Sleep ([[#D]])
- Stopped ([[#T]])
- Zombie ([[#Z]])
>It constantly Updates states based on:
- CPU Availability
- I/O Operations (disk/network)
- signals received
- Parent-child relationships
*Example:*
- A process reading disk                    → goes into `D` state
- A process waiting for input             → `S` state
- A process finished but not cleaned → `Z` state

### 4. Resource Management and Isolation
Each process gets its own private memory. Process `A` cannot read process `B`'s memory directly.*(When malware does memory injection, its specifically exploiting ways around this wall.)*

>The kernel ensures:
- memory separation (one process cannot access another’s memory)
- file descriptor tracking (open files per process)
- permissions enforcement (user/group access)
- CPU limits (via scheduling)
- resource usage tracking

>This is what prevents:
- one program from crashing the entire system
- unauthorized access between users
- memory corruption across programs


---


## Basic ps command
---
July 03, 2026 
`ps` — Process Snapshot.

Only shows processes attached to current terminal sessions. 
```
ps

PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 1285 pts/0    00:00:00 ps
```
`PID` - The process's unique ID 
`TTY` - Which terminal its running in
`TIME` - How much CPU time it has consumed
`CMD` The command that started it

## ps -ef
---
July 03, 2026
Unlike plain `ps`, which is limited, this one shows 
>every process running on the system (not just yours or your terminal)

```bash
ps -ef

`-e` show all processes
`-f` show full format output


UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 11:50 ?        00:00:02 /sbin/init splash
root           2       0  0 11:50 ?        00:00:00 [kthreadd]
root           3       2  0 11:50 ?        00:00:00 [pool_workqueue_release]
root           4       2  0 11:50 ?        00:00:00 [kworker/R-rcu_gp]

```

`UID`    - user who owns the process
`PID`    - process ID
`PPID`  - parent process ID
`C`        -  CPU usage (short-term)
`STIME` - start time
`TTY`      - terminal attached (if any). If its a question mark `?` th
`TIME`    - total CPU time used
`CMD`      - The full command used to start the process

### The important concept of PPID
Every process becomes another process.
This is one of the most important columns for cybersecurity.

it helps detect:

**Suspicious Process Chains**
*Example:  A web server (nginx/apache) spawning a `bash` or `sh`*

**Reverse shells**
*Example : `python -> bash`*

**Orphaned / unusual processes**
If PPID is strange or unexpected, it can indicate:
- persistence
- exploitation
- misconfiguration

## BSD-style ps options
July 04,2026
Linux Supports three different syntax families:
- UNIX style (`-e`, `-f`)
- BSD style (`aux`, `ax`, etc.)
- GNU style (`--sort`, `--forest`, etc.)
--BSD Style is popular because it:
- does **not require hyphens**
- is faster to type
- is widely used in system administration

*Example:* This shows **all processes on the system**, regardless of user or terminal.
```
ps aux

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1  168000  12000 ?        Ss   08:00   0:02 /sbin/init
user      1200  0.0  0.2  250000  21000 ?        Ss   08:10   0:00 sshd
user      1300  0.0  0.1  180000  15000 pts/0    Ss   08:11   0:00 bash
```

`a` — shows processes for all users
`u` — display user oriented format
`x` — include processes without a terminal  (background system stuff)

**Breakdown of each column**
`USER` — owner of process
`PID` — process ID
`%CPU` — CPU Usage percentage
`%MEM` — Memory usage Percentage
`VSZ` — Virtual Memory Size
`RSS` — Actual Physical Memory Used
`TTYP` — Terminal Attached
`STAT` — The [[#Process States]]
`START` — Start time 
`TIME` — Total CPU time
`COMMAND` — Full command

### Common Flags and Patterns Used

|      Flags      | Desc                        |                                   |
| :-------------: | --------------------------- | --------------------------------- |
|       `a`       | show all users process      |                                   |
|       `u`       | User friendly format        | *add the CPU, MEM columns*        |
|       `x`       | includes background process | see things not tied to a terminal |
|       `f`       | Tree view                   | *show parent and child visually*  |
|       `e`       | Every process               | *Same as `a` but Unix Style*      |
|      `-p`       | Target specific PID         |                                   |
|      `-u`       | Filter by username          |                                   |
|      `-o`       | Custom columns              |                                   |
| `--sort=-<CPU>` | Sort the output             |                                   |

| Pattern Command                        | Des                                       |
| :------------------------------------- | ----------------------------------------- |
| `ps aux`                               | quick overview                            |
| `ps -ef`                               | Tracing who spawned what PPID             |
| `ps auxf`                              | Full picture everything + tree view       |
| `ps -eo pid,ppid,user,stat,cmd`        | Clean minimal view                        |
| `ps aux \| grep <name>`                | For hunting specific process              |
| `ps axo pid,user,stat,cmd \| grep "?"` | Show everything with no terminal (hidden) |

*The limit of `ps` though — a sophisticated attacker can manipulate what `ps` sees by tampering with [[#/proc filesystem]] (which is exactly where the `ps` reads from). Thats where the rootkits live.*
## Process States
July 05, 2026
A Process State describe the process's current condition from the kernel's perspective.
**`Kernel's Perspective`** — Because, a process doesn't decide *To sleep*. The kernel's determines its state **based on what its doing and what its waiting for.** 

*Example:*
- A process executing instructions → `R` (Running/Runnable)
- Waiting for keyboard input → `S` (Interruptible Sleep)
- Waiting for a disk read to finish → `D` (Uninterruptible Sleep)
- Paused by a signal → `T` (Stopped)
- Finished execution but waiting for its parent → `Z` (Zombie)
Think of the state as the kernel's current status label for that process.

```
ps aux 
#LOOK AT THE STAT COLUMN
#the first letter is the Primary Process State
#The remaining letters are additional Attributes.
```

### Why does it exist? 
The CPU is a limited resource. Imagine 500 processes all wanting to run. 
The kernel needs to know:
- Who is actually running?
- Who is waiting?
- Who is blocked?
- Who has finished?
- Who is paused?
Process states are how the kernel tracks this information.
Without them, scheduling would be impossible.

### Important Idea
A Process does not stat in one state forever. States change constantly
```
bash
 ↓
Running (R)
 ↓
Waiting for keyboard input
 ↓
Sleeping (S)
 ↓
User types a command
 ↓
Running (R)
 ↓
Waiting again
 ↓
Sleeping (S)
```
This transition happens so quickly that you rarely notice it.

### Common Linux Process States
July 05, 2026
#### R-Running or Runnable
*Not always mean "Actively executing on the CPU.*
- The process is either **currently executing** OR **ready to execute**.

>**Why this distinction exist:**
The CPU is always limited:
- 1 core --> 1 process at a time
- 4 cores --> 4 processes at a time
But the system may have 100+ active processes. So the kernel splits the `R` into 2:
`Running` : currently executing on CPU
`Runnable` : ready, waiting for CPU slot
Both are labeled simply as `R`.

>**When does a Process enter the `R` ?**
- When it is created
- When it wakes up from sleep(`S`)
- When it receives CPU time during scheduling
- It resumes after being paused.

**Why `R` matters in real systems**
> 1.CPU Saturation
>> Too many processes competing for CPU time
>2.Heavy Computation
>>Things like compiling code, cryptographic operations, video rendering, malware(crypto miners)
>3.Poorly Optimized 
>>Process that never sleep or yield CPU.

#### S-Interruptible Sleep
*Not using the CPU*
*One of the most common process states in Linux*
Instead, it is waiting for something like:
- keyboard input
- network data
- timer completion
- file read/write response
- a signal from the kernel or another process

>**Why is is called Interruptible?**
Because while waiting, the process can be:
- woken up immediately when the event happens
- interrupted by signals (like `SIGINT`, `SIGTERM`)
- moved back to `R` state instantly
So the kernel can safely pause and resume it. A perfect example is bash that is in `S` when not typing.

>**What can cause a process enter the `S` state?**
>>- Waiting for input
>>- Waiting for network data (HTTP Req., SSH session idle, socket read)
>>- Waiting for file I/O (reading from a disk, writing logs, database access)
#### D-uninterruptible Sleep
*Waiting for **kernel-level** operation that CANNOT BE INTERRUPTED*
- Cannot be interrupted by signals
- Cannot be killed with `kill-9`
- Cannot be woken up manually
- Will only continue when the kernel operation finishes
> The process is not "paused" — it is blocked inside the kernel
>If a process remains in this state for a long time, it may indicate a problem with hardware or a driver.

>**What could cause the D state**
>> Reading from slow or failing disk, Writing to Disk, File System Operation
>> Network Filesystem (NSF,SMB)
>> Kernel Level Hardware operations: Device Drivers, Storage Controllers, USB Devices in rare cases


>**Why it exist**
>To protect kernel operations
>Disk writes could be incomplete
>Memory mapping could break
>
#### T-Stopped
***Paused**, either by a signal or by job control.*
==It is not running, but it is also not terminated==

>**How a process enter the T State**
>>1.Job Control (Terminal Pause `ctrl + Z`). It sends a `SIGSTP` signal
>>2.Signal-based stopping (`kill -STOP <PID>`). To resume (`kill -CONT <PID>`)

>**What happens Internally**
>>- CPU execution is halted
>>- Process state is saved
>>- Scheduler ignores it
>>- It remains in memory
>>- It waits for resume signal

>**Why it exists**
>>For Job Control in terminals
>>>Pause a long-running commands
>>>Resume later in foreground/background
>>Debugging
>>>Freeze a process before inspection
>>>Attach a debuggers safely
>>System Control
>>>Pause a processes without killing them
#### Z-Zombie
*Finished execution but still has an entry in the process table*
Also called `defunct process`
A process that is NOT RUNNING ANYMORE
A few zombies are normal, but a large number may indicate a bug in the parent application.
It cannot be killed

>**How it happens**
```
1. Child process finishes its job
2. Child sends exit status to parent — "hey I'm done"
3. Parent is supposed to read that exit status (called "reaping")
4. Until the parent reads it — the child lingers as a zombie
5. Once parent acknowledges it — kernel cleans it up, gone forever
```
==In summary: *A child process exists, but the parent does NOT call the `wait()` *==

>**When it becomes a problem?**
>*A zombie lasting a split second is completely normal. The problem is when:*
>>- The parent is **too busy** to collect exit statuses
>>- The parent **has a bug** and never reads them
>>- The parent **was killed** before collecting — leaving orphaned zombies behind

### The extra symbols (Modifiers)
*In the STAT column there are extra letters*:

| Symbols | Meaning                                            |
| :-----: | -------------------------------------------------- |
|    +    | foreground process (attached to terminal)          |
|    s    | session leader                                     |
|    <    | high priority (real time / elevated priority)      |
|    N    | low prio (nice adjusted)                           |
|    l    | multi-threaded processes                           |
|    L    | pages locked in memory - cannot be swapped to disk |

### Process States Practical Syntaxes/Commands
| Commands & Syntaxes | Desc                                       |     |
| :------------------ | ------------------------------------------ | --- |
| `bg`                | Put stopped/paused process into background |     |
| `fg`                | bring to foreground                        |     |
| `ctrl + z`          | Stopped/pause  a process                   |     |
| `<command> &`       | Start the command in background            |     |
### Misconception about process states
>"Running means the program is open.

Not necessarily.
An example is an open terminal and the shell(`bash)` not executing instructions. Most of the time, its waiting for input. So it spends most of its life in `S` state and not `R`.

>"Sleeping = not doing something"

A sleeping process is usually **waiting for an event**.
*Examples:*
- keyboard input
- network packet
- disk read
- timer expiration
While waiting, there's no reason to waste CPU time.



## Process Creation
### fork()
### exec()
### Parent-child relationships
## Process Termination
### Signals
### kill
### SIGTERM
### SIGKILL
## Orphan and Zombies
## Niceness and Priorities
## /proc filesystem
## TTY and Controlling Terminal
## Job Control