# General

## Shebang

Include at the top of any bash script file.

```bash
#!/bin/bash
```

## General Navigation / Interaction with Bash Shell

```bash
history  # bash command history
history -c # clear bash command history
cat  # print file (all contents)
less  # print file (scrollable)
	/keyword  # search within less result
tail  # print last several lines of a file
watch  # watch file contents change in real time
echo [text]  # print specified text
printf [text]  # print specified text, highly customizable
pwd  # print full path of current working directory
```

## Deleting

```bash
### WARNING ### - be very careful with this
# Delete directory (includes all files contained within):
rm -rf
	# -r = recursive, -f = force
```

### Shell-Safe RM

[https://github.com/kaelzhang/shell-safe-rm](https://github.com/kaelzhang/shell-safe-rm)

## Environment Variables

```bash
env       # print all environment variables
export    # see all environment variables running
```

***

## Variables

```bash
x=3    # No spaces between equal sign.
```

## Timing

```bash
sleep 5	# Delay 5 seconds.
date +%s%3N # Get system time in milliseconds.
```

## Printing

### echo

```bash
# echo Command:
x="   hello   world   "
echo $x		# This automatically trims whitespaces from variable.
echo "$x"	# This prints the exact true string (incl. whitespaces), but no leading spaces.
echo $'\n'	# Print newline character.
echo -e "\n"	# Print newline character. -e enables interpretation of backslash escapes.
n=$'\n'; echo $n	# Store newline character to variable and use later.
```

### printf

```bash
# Printing:
echo hello; echo "hello" # Same result.
printf %s "hello"
printf '\r'%s "hello"	# Carriage return, goes back to beginning of same line and overwrites.
printf "\033[5A"	# Jump up 5 lines in the print screen.
printf "\033[5,4A"	# Jump up 5 lines and over 4 columns in the print screen. (I haven't tested this.)
```

## Reading from Standard Input

```bash
# Read from standard input (prompt for user input):
read
echo $REPLY; echo ${REPLY}	# Equivalent. Print what was last read in.
read -r	# -r = ignore backslash escapes
read line	# read user input and store to variable $line
read -u 3	# read from file descriptor 2 instead of standard input.
	# File descriptor must first be created with the following:
	exec 3< file	# Open $file to file descriptor 3. Don't include $ in front of 'file'.
	exec 3>&-	# Close file descriptor 3.
# Read returns 0 until end of file is encountered, it times out, or error occurs.
# IMPORTANT NOTE ABOUT FILE DESCRIPTORS:
#	File descriptors 0, 1 and 2 are for stdin, stdout and stderr respectively.
#	File descriptors 3, 4, .. 9 are for additional files.
```

## Reading from File

### Read Text File Line by Line

#### Method 1

```bash
file=$(cat temp.txt)
for line in $file
do
    echo -e "$line\n"
done
```

#### Method 2

File descriptor is created when file is opened, and stays open until closed.

**IMPORTANT NOTE ABOUT FILE DESCRIPTORS:**

File descriptors `0`, `1` and `2` are for `stdin`, `stdout` and `stderr` respectively.

File descriptors `3`, `4`, .. `9` are for additional files.

```bash
exec 3< file;        # open file file as fd 3 (fd = file descriptor)
echo "hello" > file  # write to file
read -ur 3           # Reads 1 line and stores to $REPLY.
```

Keep running this, and it will read each line sequentially.

* `-u` = reads from file instead of stdin
* `-r` = ignores backslash escapes

```bash
read -ur line        # Reads 1 line and stores to $line.
```

Close file and re-open to start at the beginning again.

```bash
exec 3>&-    # Close file descriptor 3.
```

#### Method 3

```bash
file="temp.txt"
while read -r line
do
    echo -e "$line\n"
done <$file
```

## Writing to a File

```bash
# Write to a file; overwrite everything.
echo "hello" > ./file.txt
# Write to a file; append, don't overwrite.
echo "hello" >> ./file.txt

# Capture stdout to a file.
command_to_run > output_file.txt
# Capture stderr to a file.
command_to_run 2> error_file.txt
# Send stdout to the terminal, and both
# stdout and stderr to a file.
command_to_run | tee output_file.txt
# Send stdout to the terminal, and
# only stderr to a file.
command_to_run 2>&1 | tee output_file.txt
```

***

## Bash Configuration

```bash
# Add commands that execute every time Bash shell opens.
echo "[commands]" >> ~/.bashrc
```

### Aliases

#### Set an Alias

Calling the alias will call another command or series of commands.

```bash
# ~/.bashrc
alias update="sudo apt update && sudo apt upgrade -y"
```

#### Override an Alias

If an alias overloads an existing command name, there are ways to override the alias.

```bash
# ~/.bashrc
alias ls="ls -la"
```

```bash
\ls            # Option 1
command ls     # Option 2
/bin/ls        # Option 3, use full path to command
/usr/bin/ls
```

## Source Command

```bash
# Run file, and all variables created by it will be added to the current context.
source ./script.sh
# Add this path to the current shell's context.
# Allows you to execute files from this directory by name
# without providing the complete path.
source ~/andrew/robotics/scripts/
```

## Process Management

```bash
# Start/stop a process.
sudo systemctl start [process]
sudo systemctl stop [process]
```

```bash
top  # see processes currently running
htop  # newer, more advanced version of 'top'
ps faux  # simple list of processes currently running
ps faux | grep "xxx"  # filter processes; grep itself will always show up as a process in the resulting list
Ctrl+C  # sends SIGINT signal to program, shuts down program, only works on foreground processes
Ctrl+Z  # sends SIGSTOP signal, suspsneds process, sends foreground process to background, only works on foreground processes. Processes in background won't receive Ctrl+C or 'kill' commands.
bg  # resume execution of a suspended/background process, bring it back to foreground. It will immediately receive previous Ctrl+C and 'kill' signals that were sent when the process was in the background.
kill <PID>  # kill a process by PID, get from 'ps faux' list. Process must be executing in foreground to kill it
./script.sh &  # starts process as a background process

# View of system performance information (like task manager):
glances

# System performance monitors
sysbench
Geekbench
```

## Grep

### Search for Invisible Control Characters

Note - control characters might not render at all, or they might render inconsistently.

```bash
grep -a $'\xff' # search for character '\xff'
```

Create a dummy test file with control characters to make sure search works.

```bash
echo $'\xff' > test.txt
```
