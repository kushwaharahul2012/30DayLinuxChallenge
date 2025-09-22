# DevOps Learning - Day 1 Tasks (Step by Step)

## TASK 1: Create Your First Directories
**What you'll learn**: How to create folders in Linux

**Steps**:
1. Open terminal
2. Type: `mkdir -p /home/$USER/devops-learning/day1/logs`
3. Press Enter
4. Type: `mkdir -p /home/$USER/devops-learning/day1/config`  
5. Press Enter
6. Type: `ls -la /home/$USER/devops-learning/day1/`
7. Press Enter




**Tell me**: Did you see two folders (logs and config)? Copy-paste what you saw.
**Result** vagrant@ubuntu-jammy:~$ mkdir -p /home/vagrant/devops-learning/day1/logs
vagrant@ubuntu-jammy:~$ mkdir -p /home/vagrant/devops-learning/day1/config
vagrant@ubuntu-jammy:~$ ls -la /home/vagrant/devops-learning/day1/
total 16
drwxrwxr-x 2 vagrant vagrant 4096 Sep 18 06:12 config
drwxrwxr-x 2 vagrant vagrant 4096 Sep 18 06:12 logs
 **and i create also in azure vm**
 drwxrwxr-x 4 hp hp 4096 Sep 18 14:09 .
drwxrwxr-x 3 hp hp 4096 Sep 18 14:08 ..
drwxrwxr-x 2 hp hp 4096 Sep 18 14:09 config
drwxrwxr-x 2 hp hp 4096 Sep 18 14:08 logs




---

## TASK 2: Create Empty Files
**What you'll learn**: How to create files quickly

**Steps**:
1. Type: `cd /home/$USER/devops-learning/day1/logs`
2. Press Enter
3. Type: `touch app.log error.log access.log debug.log system.log`
4. Press Enter  
5. Type: `ls -la`
6. Press Enter

**Tell me**: How many files do you see? Do they all end with .log?
**Result**
drwxrwxr-x 2 hp hp 4096 Sep 18 14:13 .
drwxrwxr-x 4 hp hp 4096 Sep 18 14:09 ..
-rw-rw-r-- 1 hp hp    0 Sep 18 14:13 access.log
-rw-rw-r-- 1 hp hp    0 Sep 18 14:13 app.log
-rw-rw-r-- 1 hp hp    0 Sep 18 14:13 debug.log
-rw-rw-r-- 1 hp hp    0 Sep 18 14:13 error.log
-rw-rw-r-- 1 hp hp    0 Sep 18 14:13 system.log


## TASK 3: Find Files on Your Computer
**What you'll learn**: How to search for files

**Steps**:
1. Type: `find /home/$USER -name "*.log" 2>/dev/null`
2. Press Enter
3. Wait for results

**Tell me**: How many .log files did it find? (Just count them)
**Result**
/home/hp/devops-learning/day1/logs/system.log
/home/hp/devops-learning/day1/logs/access.log
/home/hp/devops-learning/day1/logs/app.log
/home/hp/devops-learning/day1/logs/error.log
/home/hp/devops-learning/day1/logs/debug.log
---

## TASK 4: Look at System Information  
**What you'll learn**: How to check what your system is doing

**Steps**:
1. Type: `sudo journalctl -n 5`
2. Press Enter
3. Enter your password if asked

**Tell me**: Did you see some log entries with dates/times? (Don't paste sensitive info)
**Result**
Sep 18 14:13:32 Mydevopsvm sshd[8181]: error: kex_exchange_identification: read: Connection reset by peer
Sep 18 14:13:32 Mydevopsvm sshd[8181]: Connection reset by 117.156.96.5 port 36112
Sep 18 14:15:01 Mydevopsvm CRON[8183]: pam_unix(cron:session): session opened for user root(uid=0) by (uid=0)
Sep 18 14:15:01 Mydevopsvm CRON[8184]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
Sep 18 14:15:01 Mydevopsvm CRON[8183]: pam_unix(cron:session): session closed for user root
---

## TASK 5: Check Your Disk Space
**What you'll learn**: How to see how much space you have

**Steps**:
1. Type: `df -h`
2. Press Enter
3. Type: `du -sh /home/$USER/devops-learning`  
4. Press Enter

**Tell me**: What percentage of your disk is used? How much space does your devops-learning folder take?
**Result**
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  2.2G   27G   8% /
tmpfs           425M     0  425M   0% /dev/shm
tmpfs           170M  992K  169M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
efivarfs        128K   39K   85K  32% /sys/firmware/efi/efivars
/dev/sda15      105M  6.1M   99M   6% /boot/efi
/dev/sdb1       3.9G   28K  3.7G   1% /mnt
tmpfs            85M  4.0K   85M   1% /run/user/1000


16K     /home/hp/devops-learning
---
________________________________________________________________________________________________________________________

# Advanced Day 1 Tasks - DevOps Security & Automation

## ADVANCED TASK 1: File Permissions & Security
**What you'll learn**: How to secure files and directories (critical in production!)

**The Problem**: Your log files are readable by everyone on the system. In production, log files often contain sensitive information.

**Steps**:
1. Check current permissions: `ls -la /home/$USER/devops-learning/day1/logs/`
2. Make logs directory secure (only owner can access): `chmod 700 /home/$USER/devops-learning/day1/logs`
3. Make log files readable only by owner: `chmod 600 /home/$USER/devops-learning/day1/logs/*.log`
4. Verify changes: `ls -la /home/$USER/devops-learning/day1/logs/`
5. Check parent directory: `ls -la /home/$USER/devops-learning/day1/`

**Tell me**: 
- What permissions did the files have before? (like -rw-rw-r--)
- What permissions do they have after? 
- Can you explain the difference?

**Result**
-before permission -rw-rw-r--1  access.log
-after -rw --------1 access.log

i think after chmod user can't get easily or check this logs files if you have authorize or permission you can check these logs files that is major diffrences security and userbased logs.
---

## ADVANCED TASK 2: Process Monitoring & System Analysis
**What you'll learn**: How to monitor what's running on your system

**The Problem**: DevOps engineers need to know what processes are consuming resources.

**Steps**:
1. See all running processes: `ps aux`
2. Find processes using most CPU: `ps aux --sort=-%cpu | head -10`
3. Find processes using most memory: `ps aux --sort=-%mem | head -10`
4. See real-time process activity: `top` (press 'q' to quit after 10 seconds)
5. Count total processes: `ps aux | wc -l`
6. Find processes related to SSH: `ps aux | grep ssh`

**Tell me**: 
- How many total processes are running?
-107
- Which process is using the most CPU?
root           1  0.0  1.5 167768 13144 ?        Ss   13:32   0:03 /sbin/init
- Which process is using the most memory?
root        2868  0.0  4.2 1700716 36708 ?       Ssl  13:54   0:00 /usr/lib/snapd/snapd
- Are there any SSH processes running?
i'm not found any ssh process maybe i don;t know how to check
---

## ADVANCED TASK 3: Create Your First Automation Script
**What you'll learn**: Write a script that automates system checks

**The Problem**: Manual system checking is time-consuming. DevOps engineers automate repetitive tasks.

**Steps**:
1. Create script directory: `mkdir -p /home/$USER/devops-learning/day1/scripts`
2. Create the script: `nano /home/$USER/devops-learning/day1/scripts/system_check.sh`
3. Copy this script content:
```bash
#!/bin/bash
# System Health Check Script
# Created by: [Your Name]

echo "=== SYSTEM HEALTH CHECK ==="
echo "Date: $(date)"
echo ""

echo "=== DISK USAGE ==="
df -h | grep -E "(Filesystem|/dev/)"
echo ""

echo "=== MEMORY USAGE ==="
free -h
echo ""

echo "=== CPU LOAD ==="
uptime
echo ""

echo "=== TOP 5 CPU PROCESSES ==="
ps aux --sort=-%cpu | head -6
echo ""

echo "=== LOG FILES COUNT ==="
find /home/$USER -name "*.log" 2>/dev/null | wc -l
echo "Log files found in home directory"
echo ""

echo "=== SYSTEM CHECK COMPLETE ==="
```
4. Save and exit nano (Ctrl+X, then Y, then Enter)
5. Make script executable: `chmod +x /home/$USER/devops-learning/day1/scripts/system_check.sh`
6. Run your script: `/home/$USER/devops-learning/day1/scripts/system_check.sh`

**Tell me**: 
- Did the script run successfully?
after - `/home/$USER/devops-learning/day1/scripts/system_check.sh` my system not do anything just like stuck even i trying to give commands like pwd ls -la not responces came -
hp@Mydevopsvm:~/devops-learning/day1/logs$ chmod +x /home/hp/devops-learning/day1/scripts/system_check.sh
hp@Mydevopsvm:~/devops-learning/day1/logs$ /home/hp/devops-learning/day1/scripts/system_check.sh
hp@Mydevopsvm:~/devops-learning/day1/logs$ 
hp@Mydevopsvm:~/devops-learning/day1/logs$ cd ..
hp@Mydevopsvm:~/devops-learning/day1$ cd ..
hp@Mydevopsvm:~/devops-learning$ cd ..
hp@Mydevopsvm:~$ /home/hp/devops-learning/day1/scripts/system_check.sh
hp@Mydevopsvm:~$ ls -la
hp@Mydevopsvm:~$ pwd

others answers i don't know
- What's your system's memory usage percentage?
- What's your system uptime?
- Copy-paste the "TOP 5 CPU PROCESSES" section

---

## Why These Advanced Tasks Matter:

### Task 1 - Security:
- **Production Reality**: Log files contain sensitive data (user info, errors, system details)
- **Security Best Practice**: Restrict file permissions to prevent unauthorized access
- **DevOps Skill**: Understanding Linux permissions is fundamental

### Task 2 - Monitoring:
- **Production Reality**: You need to identify resource-hungry processes
- **Troubleshooting**: High CPU/memory usage causes performance issues  
- **DevOps Skill**: Proactive monitoring prevents system crashes

### Task 3 - Automation:
- **Production Reality**: Manual checks don't scale to hundreds of servers
- **Efficiency**: One script replaces 10 manual commands
- **DevOps Skill**: Scripting is the foundation of automation

---

## Bonus Challenge (Optional):
If you complete all 3 tasks easily, try this:
- Modify the script to email you the results (we'll work on this together)
- Set up the script to run automatically every hour using cron

**Ready to become an advanced DevOps practitioner?** 💪

Go tackle these 3 tasks and show me your results!