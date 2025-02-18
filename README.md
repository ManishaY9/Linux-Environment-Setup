# Linux-Environment-Setup

## Project Overview
This project involves setting up and managing a secure, monitored, and well-maintained development environment for developers. The tasks include:
1. [Task 1: System Monitoring Setup](#task-1-system-monitoring-setup)
2. [Task 2: User Management and Access Control](#task-2-user-management-and-access-control)
3. [Task 3: Backup Configuration for Web Servers](#task-3-backup-configuration-for-web-servers)
4. [Report and Challenges](#report-and-challenges)
5. [Summary of Implementation](#summary-of-implementation)

## Tasks Breakdown
### Task 1: System Monitoring Setup
**Objective:** Install monitoring tools, track system performance, and log resource usage.
To configure a monitoring system ensuring the development environment’s health, performance, and capacity planning.
#### Implementation:

1. **Installed monitoring tools:**
    - Install `htop` and `nmon` for monitoring.
        ```bash
        sudo apt update  
        sudo apt install htop nmon -y  # Install monitoring tools
        ```

2. **Configured disk usage monitoring:**
    - Use `df` and `du` to check disk usage.
        ```bash
        df -h  # Display disk space usage
        du -sh /var/log  # Check space occupied by log files
        ```

    - Create a script to log disk usage periodically:

        ```bash
        #!/bin/bash
        df -h > /var/log/disk_usage_$(date +%F).log
        du -sh /var/www/html /usr/share/nginx/html > /var/log/dir_usage_$(date +%F).log
        ```

3. **Implemented process monitoring:**
    - Identify resource-intensive processes using `htop`:
        ```bash
        htop
        ```
    - Use `ps aux` for top 10 process information:
        ```bash
        ps aux --sort=-%cpu | head -10 
        ```
    - Create a script to log top 10 resource-intensive processes:
        ```bash
        nano /usr/local/bin/system_monitor.sh
        #!/bin/bash
        LOG_DIR="/var/log/devops-setup"
        LOG_FILE="$LOG_DIR/system_metrics_$(date '+%Y-%m-%d_%H-%M-%S').log"
        # CPU usage (top 10 processes)
        echo "=== CPU Usage at $TIMESTAMP ===" >> "$LOG_FILE"
        ps aux --sort=-%cpu | head -n 10 >> "$LOG_FILE"

        # Disk usage
        echo -e "\n=== Disk Usage ===" >> "$LOG_FILE"
        df -h >> "$LOG_FILE"
        ```

        - Make the script executable

        ```bash
        sudo chmod +x /usr/local/bin/system_monitor.sh
        ```
        Add to cron for hourly execution:
        ```bash
        crontab -e
        0 * * * * /usr/local/bin/system_monitor.sh 
        ```
### Outputs
- Screenshots of `htop`, `nmon`, and disk usage commands.
 ![image](https://github.com/user-attachments/assets/9737a604-9a6f-4125-b6c5-f12585bc8a83)
 ![image](https://github.com/user-attachments/assets/608cdc66-25fb-4068-b182-836844ad5c97)
 ![image](https://github.com/user-attachments/assets/1a4d199d-2c9a-46ad-afbc-c0d414dcf6fe)
 ![image](https://github.com/user-attachments/assets/616352dc-e028-46d7-8e7e-55b5c7ec878c)
 ![image](https://github.com/user-attachments/assets/3b455ae5-2835-47cc-80fa-79f7fda6c6ad)
 ![image](https://github.com/user-attachments/assets/65a1ffe9-c707-4cfa-871f-d5b05813563f)

- Example log file: `monitoring.log`.
 ![image](https://github.com/user-attachments/assets/2bd2d35f-5632-4f2b-9db1-121b122e6248)

### Task 2: User Management and Access Control
**Objective:** Create user accounts, set up secure directories, and enforce password policies.

#### Implementation:
## Step 1: Create User Accounts

- Add users:
    ```bash
    sudo useradd -m Sarah
    sudo useradd -m Mike
    ```

- Set secure passwords:
    ```bash
    sudo passwd Sarah
    sudo passwd Mike
    ```

---

## Step 2: Create Isolated Directories

- Create directories:
    ```bash
    sudo mkdir -p /home/Sarah/workspace /home/Mike/workspace
    ```

- Set ownership and permissions:
    ```bash
    sudo chown Sarah:Sarah /home/Sarah/workspace
    sudo chown Mike:Mike /home/Mike/workspace
    sudo chmod 700 /home/Sarah/workspace
    sudo chmod 700 /home/Mike/workspace
    ```

- Implement Password Policies
    ```bash
    sudo chage -M 30 Sarah
    sudo chage -M 30 Mike
    sudo chage -l Sarah
    sudo chage -l Mike
    ```

# Task 3: Backup Configuration for Web Servers

- Step 1: Create Backup Scripts


    - For Sarah's Apache Server:
        Create a backup script:
        ```bash
        sudo nano /home/Sarah/apache_backup.sh
        ```
        Add the following content:
        ```bash
        #!/bin/bash
        tar -czf /backups/apache_backup_$(date +%F).tar.gz /etc/httpd/ /var/www/html/
        ```
        Make the script executable:
        ```bash
        sudo chmod +x /home/Sarah/apache_backup.sh
        ```

    - For Mike's Nginx Server:
        Create a backup script:
        ```bash
        sudo nano /home/Mike/nginx_backup.sh
        ```

        Add the following content:
        ```bash
        #!/bin/bash
        tar -czf /backups/nginx_backup_$(date +%F).tar.gz /etc/nginx/ /usr/share/nginx/html/
        ```

        Make the script executable:
        ```bash
        sudo chmod +x /home/Mike/nginx_backup.sh
        ```
- Step 2: Schedule Backups with Cron

    - Schedule Sarah's Apache backup:
        1. Open the crontab editor:
        ```bash
        crontab -e
        ```
        2. Add the following line to schedule the backup every Tuesday at 12:00 AM:
        ```bash
        0 0 * * 2 /home/Sarah/apache_backup.sh
        ```

    - Schedule Mike's Nginx backup:
        1. Open the crontab editor:
        ```bash
        crontab -e
        ```
        2. Add the following line to schedule the backup every Tuesday at 12:00 AM:
        ```bash
        0 0 * * 2 /home/Mike/nginx_backup.sh
        ```
- Step 3: Verify Backups:
    ```bash
    ls -lh /backups/
    ```
 ![image](https://github.com/user-attachments/assets/df176f58-7c2e-457c-b676-bfb77acc2b4b)
 ![image](https://github.com/user-attachments/assets/fc9d03c5-f96f-41c0-aaf9-89afed68c7a0)
 ![image](https://github.com/user-attachments/assets/23995cf1-7e2a-427f-b5bb-7a135c8d13c8)
 ![image](https://github.com/user-attachments/assets/57386c24-6e5b-49d5-aa4c-1b28dc5c2ed4)
![image](https://github.com/user-attachments/assets/b002a852-8721-444d-93c3-0f846722f6f1)

### Summary of Implementation:

- **System Monitoring:** Installed `htop` and `nmon`, set up logging, identified resource-intensive processes.
- **User Management:** Created users, configured password policies and permissions.
- **Backup Configuration:** Implemented automated backup for web servers with cron jobs.

### **Challenges Encountered:**

- **Permission Issues:** Setting correct ownership and permissions for user directories and backup files.
- **Cron Job Debugging:** Ensuring that scheduled tasks executed as expected .
