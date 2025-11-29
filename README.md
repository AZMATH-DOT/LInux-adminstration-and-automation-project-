 Linux Adminstarion and Automation project 

---

# ✅ **1. Project Folder Structure**

Create this structure:

```
Linux-Challenge/
├── README.md
├── notes.txt
├── disk_usage.txt
├── process_list.txt
├── ping_results.txt
├── cpu_mem_usage.txt
├── timestamp.txt
├── user_list.txt
├── scripts/
│   ├── backup.sh
│   ├── cleanup.sh
│   ├── rotate_logs.sh
│   ├── resource_monitor.sh
│   └── bulk_user_add.sh
└── backup/
```

Create folders:

```bash
mkdir -p Linux-Challenge/scripts
mkdir -p Linux-Challenge/backup
```
student3![IMG-20251129-WA0011](https://github.com/user-attachments/assets/ea43a24c-0ff7-476e-ae33-3383d0a3d5f0)
---
![IMG-20251129-WA0011](https://github.com/user-attachments/assets/22f77cda-37c3-46b1-ab6e-df342eafca69)

# ✅ **2. README.md (Paste this)**

```
# Linux Challenge – DevOps Project

This repository contains the full solution for the **Linux Challenge for DevOps Students**, including beginner, intermediate, and advanced Linux administration tasks with automation scripts.

## 📘 Project Structure
- Basic Linux Commands  
- User & Group Management  
- Process Management  
- Networking  
- Shell Scripting  
- Automation  
- Advanced Monitoring & Bulk User Management  
###![IMG-20251129-WA0013](https://github.com/user-attachments/assets/4672b159-1d43-4a6a-a31a-f2678ee70047)
## 🚀 Scripts Overview

### backup.sh  
Compresses the project directory into a tar.gz file and stores it in the `backup/` folder.

![IMG-20251129-WA0014](https://github.com/user-attachments/assets/2a488c1e-f073-46e6-8c91-45ff95a171bf)

 cleanup.sh  
Deletes all backup files older than 7 days (cron-ready).

### rotate_logs.sh  
Rotates `timestamp.txt` if it exceeds 1MB.

### resource_monitor.sh  
Logs CPU, memory, and disk usage every 5 minutes and sends alert if limits are exceeded.

### bulk_user_add.sh  
Creates multiple users with default passwords and adds them to a group.
![IMG-20251129-WA0015](https://github.com/user-attachments/assets/4530f578-9151-48a5-bbc0-5ac7ec2545e6)

## 📦 Cron Job Example

Run cleanup script every midnight:
```

0 0 * * * /path/to/project/scripts/cleanup.sh
```

---

# ✅ **3. All Required Shell Scripts**

## 📌 **scripts/backup.sh**

```bash
#!/bin/bash

PROJECT_DIR="/home/devops/academy/project"
BACKUP_DIR="/home/devops/academy/backup"
BACKUP_FILE="project_backup_$(date +%F_%H-%M-%S).tar.gz"

# Check if project directory exists
if [ ! -d "$PROJECT_DIR" ]; then
    echo "Error: Project directory does not exist! Backup aborted."
    exit 1
fi

# Run backup
tar -czf "$BACKUP_DIR/$BACKUP_FILE" "$PROJECT_DIR"

if [ $? -eq 0 ]; then
    echo "Backup created: $BACKUP_DIR/$BACKUP_FILE"
else
    echo "Backup failed!"
fi
```

---

## 📌 **scripts/cleanup.sh**

```bash
#!/bin/bash

BACKUP_DIR="/home/devops/academy/backup"

find "$BACKUP_DIR" -type f -mtime +7 -exec rm {} \;

echo "Old backup files older than 7 days deleted."
```

---

## 📌 **scripts/rotate_logs.sh**

```bash
#!/bin/bash

LOG_FILE="/home/devops/academy/project/timestamp.txt"
MAX_SIZE=1048576  # 1MB

if [ -f "$LOG_FILE" ]; then
    SIZE=$(stat -c%s "$LOG_FILE")
    
    if [ "$SIZE" -gt "$MAX_SIZE" ]; then
        mv "$LOG_FILE" "${LOG_FILE}_$(date +%F_%H-%M-%S)"
        touch "$LOG_FILE"
        echo "Log rotated."
    fi
fi
```

---

## 📌 **scripts/resource_monitor.sh**

```bash
#!/bin/bash

LOG_FILE="/home/devops/academy/project/resource_usage.log"
CPU_LIMIT=80
DISK_LIMIT=20

while true; do
    CPU=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
    MEM=$(free -m | awk '/Mem:/ {print $3}')
    DISK=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')

    echo "$(date): CPU=${CPU}%, MEM=${MEM}MB, DISK=${DISK}%" >> $LOG_FILE

    # Alerts
    if (( ${CPU%.*} > CPU_LIMIT )); then
        echo "ALERT: CPU Usage Above 80%"
    fi

    if (( DISK < DISK_LIMIT )); then
        echo "ALERT: Disk Space Below 20%"
    fi

    sleep 300  # 5 minutes
done
```

---

## 📌 **scripts/bulk_user_add.sh**

```bash
#!/bin/bash

USER_LIST="/home/devops/academy/project/user_list.txt"
DEFAULT_GROUP="students"
DEFAULT_PASS="Password123"

# Ensure group exists
if ! getent group $DEFAULT_GROUP > /dev/null; then
    groupadd $DEFAULT_GROUP
fi

while read USER; do
    useradd -m -g $DEFAULT_GROUP "$USER"
    echo "$USER:$DEFAULT_PASS" | chpasswd
    echo "Created user: $USER"
done < "$USER_LIST"
```
[IMG-20251129-WA0016](https://github.com/user-attachments/assets/fb1ca817-f137-459d-a138-55340d1738dd)
---

# ✅ **4. Supporting Files**

### **notes.txt**

```
DevOps Linux Challenge
```

### **user_list.txt**

```
student1
student2
student3![IMG-20251129-WA0011](https://github.com/user-attachments/assets/ea43a24c-0ff7-476e-ae33-3383d0a3d5f0)
![IMG-20251129-WA0017](https://github.com/user-attachments/assets/20549e56-f705-4bec-be0d-3520626dee17)
![IMG-20251129-WA0019](https://github.com/user-attachments/assets/2299b94d-157d-4d4b-b1f4-f0a37a539063)

You can add more usernames.

---

# ✅ **5. Background process script for timestamp.txt**

Run this manually:

```bash
while true; do
    date >> timestamp.txt
    sleep 60
done &
```

To kill it:

```bash
ps aux | grep timestamp.txt
kill <PID>

![IMG-20251129-WA0020](https://github.com/user-attachments/assets/d2866850-684b-4b80-8409-41a2675a6647)
![IMG-20251129-WA0021](https://github.com/user-attachments/assets/892a845c-70df-41e2-bc25-6872bc17dbd9)
![IMG-20251129-WA0022](https://github.com/user-attachments/assets/0fb76469-9065-45bf-88a6-fe6ba1b514ad)
