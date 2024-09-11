# Lab 3: Server Monitoring and Management

## Objective
In this lab, you will learn how to monitor web server activity, generate daily reports, and create a script to ensure continuous operation of a web server.

## Prerequisites
- Completion of Labs 1 and 2
- Access to a Unix-like environment
- Apache or Nginx web server installed
- Postfix or another MTA (Mail Transfer Agent) configured for sending emails

## Lab Tasks

### 1. Analyzing Web Server Logs

a) Create a script to analyze Apache/Nginx access logs:

```bash
#!/bin/bash

LOG_FILE="/var/log/apache2/access.log"  # or "/var/log/nginx/access.log" for Nginx
OUTPUT_FILE="/tmp/web_summary.txt"

# Get total requests
TOTAL_REQUESTS=$(wc -l < "$LOG_FILE")

# Get unique IP addresses
UNIQUE_IPS=$(awk '{print $1}' "$LOG_FILE" | sort -u | wc -l)

# Get most common HTTP status codes
HTTP_CODES=$(awk '{print $9}' "$LOG_FILE" | sort | uniq -c | sort -rn | head -5)

# Create summary
echo "Web Server Daily Summary" > "$OUTPUT_FILE"
echo "------------------------" >> "$OUTPUT_FILE"
echo "Total Requests: $TOTAL_REQUESTS" >> "$OUTPUT_FILE"
echo "Unique IP Addresses: $UNIQUE_IPS" >> "$OUTPUT_FILE"
echo "Top 5 HTTP Status Codes:" >> "$OUTPUT_FILE"
echo "$HTTP_CODES" >> "$OUTPUT_FILE"

echo "Web server log analysis complete. Summary saved to $OUTPUT_FILE"
```
Save this as `analyze_web_logs.sh` and make it executable.

### 2. Emailing the Web Server Summary

a) Create a script to email the web server summary:

```bash
#!/bin/bash

RECIPIENT="your_email@example.com"
SUBJECT="Daily Web Server Summary"
BODY_FILE="/tmp/web_summary.txt"

# First, run the log analysis
./analyze_web_logs.sh

# Now email the summary
if [ -f "$BODY_FILE" ]; then
    mail -s "$SUBJECT" "$RECIPIENT" < "$BODY_FILE"
    echo "Summary email sent to $RECIPIENT"
else
    echo "Error: Summary file not found"
fi
```
Save this as `email_web_summary.sh` and make it executable.

### 3. Scheduling Daily Summary Emails

a) Open the crontab file:
```bash
crontab -e
```

b) Add a line to run the email script daily at 23:59:
```
59 23 * * * /path/to/email_web_summary.sh
```

### 4. Monitoring and Restarting Web Server

a) Create a script to check if the web server is running and restart it if necessary:

```bash
#!/bin/bash

SERVICE_NAME="apache2"  # or "nginx" for Nginx

# Function to check if a service is running
is_service_running() {
    systemctl is-active --quiet "$SERVICE_NAME"
}

# Function to restart a service
restart_service() {
    echo "Restarting $SERVICE_NAME..."
    sudo systemctl restart "$SERVICE_NAME"
}

# Main logic
if is_service_running; then
    echo "$SERVICE_NAME is running."
else
    echo "$SERVICE_NAME is not running. Attempting to restart..."
    restart_service
    
    # Check if the restart was successful
    if is_service_running; then
        echo "$SERVICE_NAME has been successfully restarted."
    else
        echo "Failed to restart $SERVICE_NAME. Manual intervention may be required."
        # Here you could add code to send an alert email to the admin
    fi
fi
```
Save this as `monitor_webserver.sh` and make it executable.

### 5. Continuous Monitoring Setup

a) To run this script every 5 minutes, add the following to your crontab:
```
*/5 * * * * /path/to/monitor_webserver.sh
```

### 6. Practical Exercise: Comprehensive Web Server Monitoring

Combine log analysis, email reporting, and server monitoring into a single script:

```bash
#!/bin/bash

LOG_FILE="/var/log/apache2/access.log"
OUTPUT_FILE="/tmp/web_summary.txt"
RECIPIENT="your_email@example.com"
SUBJECT="Web Server Daily Report"
SERVICE_NAME="apache2"

# Analyze logs
analyze_logs() {
    TOTAL_REQUESTS=$(wc -l < "$LOG_FILE")
    UNIQUE_IPS=$(awk '{print $1}' "$LOG_FILE" | sort -u | wc -l)
    HTTP_CODES=$(awk '{print $9}' "$LOG_FILE" | sort | uniq -c | sort -rn | head -5)

    echo "Web Server Daily Summary" > "$OUTPUT_FILE"
    echo "------------------------" >> "$OUTPUT_FILE"
    echo "Total Requests: $TOTAL_REQUESTS" >> "$OUTPUT_FILE"
    echo "Unique IP Addresses: $UNIQUE_IPS" >> "$OUTPUT_FILE"
    echo "Top 5 HTTP Status Codes:" >> "$OUTPUT_FILE"
    echo "$HTTP_CODES" >> "$OUTPUT_FILE"
}

# Check server status
check_server() {
    if systemctl is-active --quiet "$SERVICE_NAME"; then
        echo "Server Status: Running" >> "$OUTPUT_FILE"
    else
        echo "Server Status: Not Running - Attempting Restart" >> "$OUTPUT_FILE"
        sudo systemctl restart "$SERVICE_NAME"
        if systemctl is-active --quiet "$SERVICE_NAME"; then
            echo "Server successfully restarted" >> "$OUTPUT_FILE"
        else
            echo "Failed to restart server" >> "$OUTPUT_FILE"
        fi
    fi
}

# Main execution
analyze_logs
check_server
mail -s "$SUBJECT" "$RECIPIENT" < "$OUTPUT_FILE"

echo "Daily report sent and server status checked"
```
Save this as `comprehensive_web_monitor.sh`, make it executable, and schedule it to run daily.

## Conclusion

In this lab, you've learned how to monitor web server activity, generate and email daily reports, and create a script to ensure continuous operation of a web server. These skills are crucial for maintaining and managing web servers effectively in a DevOps environment.

## Additional Exercises

1. Extend the log analysis to include top 10 requested URLs.
2. Implement a mechanism to alert (e.g., via SMS) if the web server fails to restart.
3. Create a weekly summary report that aggregates daily data.

## Resources

- Apache Log Files: https://httpd.apache.org/docs/2.4/logs.html
- Nginx Log Files: https://docs.nginx.com/nginx/admin-guide/monitoring/logging/
- Linux Service Management with systemd: https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units
