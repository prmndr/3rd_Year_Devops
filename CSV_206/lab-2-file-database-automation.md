# Lab 2: File and Database Management Automation

## Objective
In this lab, you will learn how to automate file management tasks and database backups using shell scripts. You'll create scripts to delete old archive files and take regular MySQL backups.

## Prerequisites
- Completion of Lab 1
- Access to a Unix-like environment
- MySQL installed (for database backup tasks)

## Lab Tasks

### 1. Automating File Deletion

a) Create a script to delete files older than 2 days:

```bash
#!/bin/bash

# Directory to clean
DIRECTORY="/path/to/archive"

# Find and delete files older than 2 days
find "$DIRECTORY" -type f -mtime +2 -delete

echo "Deleted files older than 2 days in $DIRECTORY"
```
Save this as `cleanup_old_files.sh`.

b) Make the script executable:
```bash
chmod +x cleanup_old_files.sh
```

c) Test the script (be careful to use a test directory):
```bash
./cleanup_old_files.sh
```

### 2. Scheduling File Cleanup with Cron

a) Open the crontab file:
```bash
crontab -e
```

b) Add a line to run the cleanup script daily at midnight:
```
0 0 * * * /path/to/cleanup_old_files.sh
```

### 3. MySQL Backup Script

a) Create a script for MySQL backups:

```bash
#!/bin/bash

# MySQL credentials
MYSQL_USER="your_username"
MYSQL_PASSWORD="your_password"

# Backup directory
BACKUP_DIR="/path/to/backup/directory"

# Create timestamp
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

# Create backup
mysqldump -u "$MYSQL_USER" -p"$MYSQL_PASSWORD" --all-databases > "$BACKUP_DIR/backup_$TIMESTAMP.sql"

echo "MySQL backup created: $BACKUP_DIR/backup_$TIMESTAMP.sql"
```
Save this as `mysql_backup.sh`.

b) Make the script executable:
```bash
chmod +x mysql_backup.sh
```

### 4. Scheduling MySQL Backups

a) Open the crontab file again:
```bash
crontab -e
```

b) Add a line to run the MySQL backup script every 12 hours:
```
0 */12 * * * /path/to/mysql_backup.sh
```

### 5. Managing Backup Files

a) Create a script to move backups to a specific directory and maintain only the last 7 days of backups:

```bash
#!/bin/bash

BACKUP_SOURCE="/path/to/backup/directory"
BACKUP_DEST="/path/to/archive/directory"

# Move backups to archive directory
mv "$BACKUP_SOURCE"/*.sql "$BACKUP_DEST/"

# Delete backups older than 7 days
find "$BACKUP_DEST" -name "*.sql" -type f -mtime +7 -delete

echo "Backups moved and old backups cleaned up"
```
Save this as `manage_backups.sh`.

b) Make the script executable:
```bash
chmod +x manage_backups.sh
```

c) Schedule this script to run daily:
```
0 1 * * * /path/to/manage_backups.sh
```

### 6. Practical Exercise: Comprehensive Backup Solution

Combine the MySQL backup and backup management into a single script:

```bash
#!/bin/bash

# MySQL credentials
MYSQL_USER="your_username"
MYSQL_PASSWORD="your_password"

# Directories
BACKUP_DIR="/path/to/backup/directory"
ARCHIVE_DIR="/path/to/archive/directory"

# Create timestamp
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

# Create MySQL backup
mysqldump -u "$MYSQL_USER" -p"$MYSQL_PASSWORD" --all-databases > "$BACKUP_DIR/backup_$TIMESTAMP.sql"

echo "MySQL backup created: $BACKUP_DIR/backup_$TIMESTAMP.sql"

# Move backups to archive directory
mv "$BACKUP_DIR"/*.sql "$ARCHIVE_DIR/"

# Delete backups older than 7 days
find "$ARCHIVE_DIR" -name "*.sql" -type f -mtime +7 -delete

echo "Backups archived and old backups cleaned up"
```
Save this as `comprehensive_backup.sh`.

## Conclusion

In this lab, you've learned how to automate file management tasks and database backups. You've created scripts to delete old files, perform MySQL backups, and manage backup files. These automation scripts can significantly reduce manual effort in maintaining systems and data.

## Additional Exercises

1. Modify the backup script to compress the SQL files to save space.
2. Create a log file for the backup process and append the status of each backup.
3. Implement error handling in the scripts to deal with potential issues (e.g., insufficient disk space).

## Resources

- MySQL Backup Documentation: https://dev.mysql.com/doc/refman/8.0/en/backup-methods.html
- Cron Tutorial: https://www.ostechnix.com/a-beginners-guide-to-cron-jobs/
- Bash Scripting Cheatsheet: https://devhints.io/bash
