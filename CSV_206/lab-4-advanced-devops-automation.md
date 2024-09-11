# Lab 4: Advanced DevOps Automation and Monitoring

## Objective
In this final lab, you will apply the concepts learned in previous labs to create a comprehensive DevOps automation solution. You'll also explore advanced monitoring techniques and tools.

## Prerequisites
- Completion of Labs 1, 2, and 3
- Access to a Unix-like environment
- Basic knowledge of Python (for some advanced scripts)

## Lab Tasks

### 1. Comprehensive System Health Check Script

Create a script that performs a full system health check:

```bash
#!/bin/bash

OUTPUT_FILE="/tmp/system_health_$(date +%Y%m%d_%H%M%S).log"

# Function to check disk usage
check_disk_usage() {
    echo "Disk Usage:" >> "$OUTPUT_FILE"
    df -h >> "$OUTPUT_FILE"
}

# Function to check memory usage
check_memory_usage() {
    echo "Memory Usage:" >> "$OUTPUT_FILE"
    free -m >> "$OUTPUT_FILE"
}

# Function to check CPU load
check_cpu_load() {
    echo "CPU Load:" >> "$OUTPUT_FILE"
    uptime >> "$OUTPUT_FILE"
}

# Function to check running processes
check_processes() {
    echo "Top 5 CPU-consuming processes:" >> "$OUTPUT_FILE"
    ps aux --sort=-%cpu | head -n 6 >> "$OUTPUT_FILE"
}

# Function to check network statistics
check_network() {
    echo "Network Statistics:" >> "$OUTPUT_FILE"
    netstat -tuln >> "$OUTPUT_FILE"
}

# Main execution
echo "System Health Check - $(date)" > "$OUTPUT_FILE"
echo "===============================" >> "$OUTPUT_FILE"

check_disk_usage
check_memory_usage
check_cpu_load
check_processes
check_network

echo "System health check completed. Log saved to $OUTPUT_FILE"
```

Save this as `system_health_check.sh` and make it executable.

### 2. Automated Log Rotation and Compression

Create a script to rotate and compress log files:

```bash
#!/bin/bash

LOG_DIR="/var/log"
ARCHIVE_DIR="/var/log/archives"
MAX_AGE_DAYS=30

# Create archive directory if it doesn't exist
mkdir -p "$ARCHIVE_DIR"

# Find and compress log files older than 7 days
find "$LOG_DIR" -name "*.log" -type f -mtime +7 | while read -r file; do
    gzip "$file"
    mv "${file}.gz" "$ARCHIVE_DIR/"
done

# Remove archives older than MAX_AGE_DAYS
find "$ARCHIVE_DIR" -name "*.gz" -type f -mtime +"$MAX_AGE_DAYS" -delete

echo "Log rotation and compression completed."
```

Save this as `log_rotate_compress.sh` and make it executable.

### 3. Advanced Web Application Monitoring (Using Python)

Create a Python script to monitor a web application and send alerts:

```python
#!/usr/bin/env python3

import requests
import smtplib
from email.mime.text import MIMEText
import time

def check_website(url):
    try:
        response = requests.get(url, timeout=10)
        return response.status_code == 200
    except requests.RequestException:
        return False

def send_alert(subject, body):
    sender = "your_email@example.com"
    recipient = "admin@example.com"
    password = "your_email_password"

    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = sender
    msg['To'] = recipient

    with smtplib.SMTP_SSL('smtp.gmail.com', 465) as server:
        server.login(sender, password)
        server.sendmail(sender, recipient, msg.as_string())

def monitor_website(url, check_interval=60):
    while True:
        if not check_website(url):
            alert_subject = f"Website Down: {url}"
            alert_body = f"The website {url} is currently unreachable."
            send_alert(alert_subject, alert_body)
            print(f"Alert sent: {url} is down")
        else:
            print(f"{url} is up")
        time.sleep(check_interval)

if __name__ == "__main__":
    website_url = "https://example.com"
    monitor_website(website_url)
```

Save this as `web_monitor.py` and make it executable.

### 4. Docker Container Monitoring

Create a script to monitor Docker containers:

```bash
#!/bin/bash

# Function to check Docker service status
check_docker_service() {
    if systemctl is-active --quiet docker; then
        echo "Docker service is running."
    else
        echo "Docker service is not running. Attempting to start..."
        sudo systemctl start docker
        if systemctl is-active --quiet docker; then
            echo "Docker service started successfully."
        else
            echo "Failed to start Docker service. Manual intervention required."
            exit 1
        fi
    fi
}

# Function to list running containers
list_running_containers() {
    echo "Running Containers:"
    docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Image}}"
}

# Function to check container resource usage
check_container_resources() {
    echo "Container Resource Usage:"
    docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.BlockIO}}"
}

# Main execution
check_docker_service
list_running_containers
check_container_resources
```

Save this as `monitor_docker.sh` and make it executable.

### 5. Integrated Monitoring Dashboard (Using Python and Flask)

Create a simple web-based monitoring dashboard:

```python
#!/usr/bin/env python3

from flask import Flask, render_template
import subprocess
import psutil

app = Flask(__name__)

@app.route('/')
def dashboard():
    # System info
    cpu_percent = psutil.cpu_percent()
    mem_percent = psutil.virtual_memory().percent
    disk_percent = psutil.disk_usage('/').percent

    # Docker info
    docker_output = subprocess.check_output(['docker', 'ps', '--format', '{{.Names}} {{.Status}}']).decode('utf-8')
    docker_containers = [line.split() for line in docker_output.split('\n') if line]

    return render_template('dashboard.html', 
                           cpu_percent=cpu_percent, 
                           mem_percent=mem_percent, 
                           disk_percent=disk_percent,
                           docker_containers=docker_containers)

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

Create a `templates/dashboard.html` file:

```html
<!DOCTYPE html>
<html>
<head>
    <title>System Dashboard</title>
</head>
<body>
    <h1>System Dashboard</h1>
    <h2>System Resources</h2>
    <p>CPU Usage: {{ cpu_percent }}%</p>
    <p>Memory Usage: {{ mem_percent }}%</p>
    <p>Disk Usage: {{ disk_percent }}%</p>

    <h2>Docker Containers</h2>
    <ul>
    {% for container in docker_containers %}
        <li>{{ container[0] }} - {{ container[1] }}</li>
    {% endfor %}
    </ul>
</body>
</html>
```

Save these as `dashboard.py` and `templates/dashboard.html` respectively.

## Conclusion

In this final lab, you've created advanced automation and monitoring solutions that integrate various aspects of system administration and DevOps practices. These scripts and applications provide a foundation for building robust, automated monitoring and management systems.

## Additional Exercises

1. Extend the monitoring dashboard to include historical data using a database.
2. Implement alerting thresholds for the system health check script.
3. Create a central logging solution that aggregates logs from multiple servers.
4. Implement automated scaling for Docker containers based on resource usage.

## Resources

- Python psutil documentation: https://psutil.readthedocs.io/en/latest/
- Flask documentation: https://flask.palletsprojects.com/
- Docker CLI documentation: https://docs.docker.com/engine/reference/commandline/cli/
- Grafana for advanced dashboards: https://grafana.com/
