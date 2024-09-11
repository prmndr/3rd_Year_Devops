# Lab 1: Scripting Fundamentals and Basic Automation

## Objective
In this lab, you will learn the basics of shell scripting, including conditional statements and loops. You'll also create automation scripts to perform common tasks.

## Prerequisites
- Access to a Unix-like environment (Linux, macOS, or Windows Subsystem for Linux)
- Basic command-line knowledge

## Lab Tasks

### 1. Introduction to Shell Scripting

a) Create your first shell script:
```bash
#!/bin/bash
echo "Hello, DevOps!"
```
Save this as `hello.sh` and make it executable with `chmod +x hello.sh`.

b) Run your script:
```bash
./hello.sh
```

### 2. Working with Variables

a) Create a script that uses variables:
```bash
#!/bin/bash
NAME="DevOps Engineer"
echo "Welcome, $NAME!"
```
Save as `welcome.sh` and run it.

### 3. Conditional Statements

a) Create a script that uses an if-else statement:
```bash
#!/bin/bash
FILE="/etc/hosts"
if [ -f "$FILE" ]; then
    echo "$FILE exists."
else
    echo "$FILE does not exist."
fi
```
Save as `check_file.sh` and run it.

### 4. Loops

a) Create a script that uses a for loop:
```bash
#!/bin/bash
for i in {1..5}
do
   echo "Iteration $i"
done
```
Save as `loop.sh` and run it.

### 5. Basic Automation Script

a) Create a script that automates the process of updating your system:
```bash
#!/bin/bash
echo "Updating system..."
sudo apt update
sudo apt upgrade -y
echo "System update complete."
```
Save as `update_system.sh` and run it.

### 6. Practical Exercise: Log Analyzer

Create a script that analyzes a log file and reports the number of occurrences of a specific event:

```bash
#!/bin/bash

LOG_FILE="/var/log/syslog"
SEARCH_TERM="error"

if [ ! -f "$LOG_FILE" ]; then
    echo "Log file not found: $LOG_FILE"
    exit 1
fi

COUNT=$(grep -c "$SEARCH_TERM" "$LOG_FILE")

echo "Number of occurrences of '$SEARCH_TERM' in $LOG_FILE: $COUNT"
```
Save this as `log_analyzer.sh` and run it.

## Conclusion

In this lab, you've learned the basics of shell scripting, including working with variables, conditional statements, and loops. You've also created simple automation scripts that can save time and effort in DevOps tasks.

## Additional Exercises

1. Modify the log analyzer script to accept the log file path and search term as command-line arguments.
2. Create a script that checks the disk usage of the system and sends an alert if it's above 80%.
3. Write a script that backs up a specified directory to a timestamped archive.

## Resources

- Bash Scripting Tutorial: https://linuxconfig.org/bash-scripting-tutorial-for-beginners
- Shell Scripting for Beginners: https://www.freecodecamp.org/news/shell-scripting-crash-course-how-to-write-bash-scripts-in-linux/
