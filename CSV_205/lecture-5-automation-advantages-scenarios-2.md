# Lecture 5: Advantages of Automation and Automation Scenarios (Part 2)

## 1. Ensuring Web Server Uptime (15 minutes) 🌐

### 1.1 Problem:
Web server downtime can lead to lost business and poor user experience.

### 1.2 Automation Solution:

```bash
#!/bin/bash
# Web server uptime check and restart script

SERVER="localhost"
PORT=80

if ! nc -z $SERVER $PORT; then
    echo "Web server is down. Attempting to restart..."
    systemctl restart apache2
    
    # Check if restart was successful
    sleep 10
    if nc -z $SERVER $PORT; then
        echo "Web server successfully restarted."
    else
        echo "Failed to restart web server. Manual intervention required."
        # Send alert email
        mail -s "Web Server Down Alert" admin@example.com << EOF
Web server on $(hostname) is down and could not be automatically restarted.
Please investigate immediately.
EOF
    fi
else
    echo "Web server is running."
fi
```

### 1.3 Benefits:
- Proactive monitoring of web server status
- Automatic recovery from simple failures
- Immediate notification for critical issues

## 2. User Command Validation (20 minutes) 🔐

### 2.1 Problem:
Users may accidentally run dangerous commands or access unauthorized resources.

### 2.2 Automation Solution:

```bash
#!/bin/bash
# User command validation script

validate_command() {
    local cmd=$1
    local user=$2
    
    # List of forbidden commands
    forbidden=("rm -rf /" "shutdown" "reboot")
    
    # Check if command is in the forbidden list
    for fcmd in "${forbidden[@]}"; do
        if [[ $cmd == *"$fcmd"* ]]; then
            echo "Command '$cmd' is forbidden. Access denied."
            logger -p auth.warning "$user attempted to run forbidden command: $cmd"
            return 1
        fi
    done
    
    # Additional checks can be added here (e.g., checking user permissions)
    
    echo "Command '$cmd' is allowed."
    return 0
}

# Example usage
user=$(whoami)
read -p "Enter command to validate: " command

if validate_command "$command" "$user"; then
    echo "Executing command: $command"
    eval $command
else
    echo "Command not executed due to policy violation."
fi
```

### 2.3 Benefits:
- Prevents accidental execution of dangerous commands
- Enhances system security
- Provides audit trail of command attempts

## 3. Disk Usage Alarm (15 minutes) 💽

### 3.1 Problem:
Unexpectedly running out of disk space can cause system failures.

### 3.2 Automation Solution:

```bash
#!/bin/bash
# Disk usage monitoring and alert script

THRESHOLD=90
EMAIL="admin@example.com"

# Check disk usage
USAGE=$(df -h | awk '$NF=="/"{printf "%d", $5}')

if [ $USAGE -gt $THRESHOLD ]; then
    echo "ALERT: Disk usage is above $THRESHOLD%. Current usage: $USAGE%" | 
    mail -s "High Disk Usage Alert on $(hostname)" $EMAIL
    
    # Log the alert
    logger -p user.warning "High disk usage: $USAGE%"
else
    echo "Disk usage is normal: $USAGE%"
fi
```

### 3.3 Benefits:
- Early warning of potential disk space issues
- Prevents unexpected system crashes due to full disks
- Allows proactive management of storage resources

## 4. File Management Automation (20 minutes) 📁

### 4.1 Sending Files to Recycle Bin

#### Problem:
Accidental permanent deletion of important files.

#### Automation Solution:

```bash
#!/bin/bash
# Safe file deletion script (move to recycle bin)

RECYCLE_BIN="$HOME/.recycle_bin"

# Ensure recycle bin exists
mkdir -p $RECYCLE_BIN

# Move files to recycle bin
for file in "$@"; do
    if [ -e "$file" ]; then
        mv "$file" $RECYCLE_BIN/
        echo "Moved $file to recycle bin."
    else
        echo "File $file does not exist."
    fi
done
```

#### Benefits:
- Prevents accidental permanent file deletion
- Easy file recovery
- Maintains clean file system

### 4.2 Restoring Files from Recycle Bin

#### Problem:
Difficulty in recovering accidentally deleted files.

#### Automation Solution:

```bash
#!/bin/bash
# File restoration script

RECYCLE_BIN="$HOME/.recycle_bin"

# List files in recycle bin
echo "Files in recycle bin:"
ls -1 $RECYCLE_BIN

# Prompt user for file to restore
read -p "Enter filename to restore: " filename

if [ -e "$RECYCLE_BIN/$filename" ]; then
    mv "$RECYCLE_BIN/$filename" ./
    echo "Restored $filename to current directory."
else
    echo "File $filename not found in recycle bin."
fi
```

#### Benefits:
- Easy recovery of deleted files
- Reduces data loss incidents
- Improves user confidence in file management

## 5. File Formatter (15 minutes) 📄

### 5.1 Problem:
Inconsistent file formats can cause issues in data processing pipelines.

### 5.2 Automation Solution:

```bash
#!/bin/bash
# File formatter script

format_file() {
    local file=$1
    local extension="${file##*.}"
    
    case $extension in
        txt|csv)
            # Remove trailing whitespaces and empty lines
            sed -i 's/[[:space:]]*$//' "$file"
            sed -i '/^$/d' "$file"
            ;;
        json)
            # Format JSON file
            python -m json.tool "$file" > "${file}.tmp" && mv "${file}.tmp" "$file"
            ;;
        xml)
            # Format XML file
            xmllint --format "$file" > "${file}.tmp" && mv "${file}.tmp" "$file"
            ;;
        *)
            echo "Unsupported file format: $extension"
            return 1
            ;;
    esac
    
    echo "Formatted $file"
}

# Format all files in current directory
for file in *; do
    if [ -f "$file" ]; then
        format_file "$file"
    fi
done
```

### 5.3 Benefits:
- Ensures consistent file formatting
- Improves readability of data files
- Reduces errors in data processing pipelines

## Conclusion and Looking Ahead (5 minutes)

In this lecture, we've explored more advanced automation scenarios, including ensuring web server uptime, user command validation, disk usage monitoring, and file management tasks. These examples demonstrate the power of automation in improving system reliability, security, and efficiency.

In our final lecture, we'll focus on automating Linux tasks using cron jobs, which will allow us to schedule and automate the execution of these scripts and many others on a regular basis.

## Additional Resources

- Book: "Linux Shell Scripting Cookbook" by Shantanu Tushar
- Article: "10 Useful Commands for Linux System Administrators" on Linux.com
- Video: "Bash Scripting Full Course" on freeCodeCamp's YouTube channel
- Online Course: "Linux Automation and Scripting" on Pluralsight

