# Day 3: Process Management & System Monitoring

## What You'll Learn Today
- How to manage running processes (start, stop, monitor)
- System resource monitoring (CPU, memory, disk)
- Background process management
- Creating monitoring scripts
- System health checks

---

## TASK 1: Start and Manage Background Processes
**What you'll learn**: How to run processes in background (essential for servers!)

**Steps**:
1. Start a long-running process in background:
   ```bash
   sleep 300 &
   ```
2. Check background jobs:
   ```bash
   jobs
   ```
3. Start another background process:
   ```bash
   sleep 600 &
   ```
4. List all background jobs again:
   ```bash
   jobs
   ```
5. Bring first job to foreground:
   ```bash
   fg %1
   ```
6. Press Ctrl+Z to pause it, then put it back in background:
   ```bash
   bg %1
   ```

**Tell me**: 
- How many background jobs did you see?
2261 and 2384 
- What happened when you brought job to foreground?

🎯 Process Came to Foreground
The sleep 300 process moved from background to foreground
Terminal showed sleep 300 (indicating it's now the active process)
⏸️ Your Prompt Disappeared
No more user@cloudshell:~ $ prompt
Terminal was "busy" running the sleep command
You couldn't type new commands
🔒 Terminal Became "Blocked"
The terminal was waiting for sleep 300 to finish
If you tried typing, nothing would happen
You were "stuck" until the process finished (or you interrupted it)

✋ You Used Ctrl+Z to Regain Control
Ctrl+Z paused the sleep process

---

## TASK 2: Monitor System Processes
**What you'll learn**: How to see what's running on your system

**Steps**:
1. See all processes with resource usage:
   ```bash
   ps aux
   ```
2. Find processes using most CPU:
   ```bash
   ps aux --sort=-%cpu | head -10
   ```
3. Find processes using most memory:
   ```bash
   ps aux --sort=-%mem | head -10
   ```
4. Count total number of processes:
   ```bash
   ps aux | wc -l
   ```
  =47  
5. Find all processes containing "sleep":
   ```bash
   ps aux | grep sleep
   ```
= root           1  0.0  0.0   4324  3200 ?        Ss   17:57   0:00 /bin/bash /google/scripts/onrun.sh sleep infinity
root         448  0.0  0.0   2696  1408 ?        S    17:57   0:00 sleep infinity
kingwc2+     676  0.0  0.0   7340  3712 pts/3    S<s+ 17:57   0:00 bash -c echo -en "\033]0;Gemini CLI\a"; echo "Starting Gemini CLI"; tmux has-session -t geminicli 2> /dev/null; if [[ $? -eq 1 ]]; then  tmux new-session -A -D -d -n "cloudshell" -s geminicli;   sleep 2;   tmux send-keys -t geminicli "gemini; exit" Enter; fi; sleep 2; tmux attach -t geminicli;sleep 3; 
kingwc2+    4513  0.0  0.0   2696  1408 ?        S    18:29   0:00 sleep 1
kingwc2+    4515  0.0  0.0   6548  2304 pts/5    S+   18:29   0:00 grep --color=auto sleep

**Tell me**:
- How many total processes are running?
=47
- Which process uses most CPU?

user    5654 12.1  9.7 34043164 793608 ?     Sl   18:36   0:34 /google/devshell/editor/code-oss-for-cloud-shell/node --dns-result-order=ipv4first /google/devshell/editor/code-oss-for-cloud-shell/out/bootstrap-fork --type=extensionHost --transformURIs --useHostProxy=false


- Did you find your sleep processes?
root           1  0.0  0.0   4324  3200 ?        Ss   17:57   0:00 /bin/bash /google/scripts/onrun.sh sleep infinity
root         448  0.0  0.0   2696  1408 ?        S    17:57   0:00 sleep infinity
kingwc2+     676  0.0  0.0   7340  3712 pts/3    S<s+ 17:57   0:00 bash -c echo -en "\033]0;Gemini CLI\a"; echo "Starting Gemini CLI"; tmux has-session -t geminicli 2> /dev/null; if [[ $? -eq 1 ]]; then  tmux new-session -A -D -d -n "cloudshell" -s geminicli;   sleep 2;   tmux send-keys -t geminicli "gemini; exit" Enter; fi; sleep 2; tmux attach -t geminicli;sleep 3; 
kingwc2+    4513  0.0  0.0   2696  1408 ?        S    18:29   0:00 sleep 1
kingwc2+    4515  0.0  0.0   6548  2304 pts/5    S+   18:29   0:00 grep --color=auto sleep
---

## TASK 3: Real-time Process Monitoring
**What you'll learn**: Monitor system resources in real-time

**Steps**:
1. Open real-time process monitor (run for 30 seconds):
   ```bash
   top
   ```
   (Press 'q' to quit)
2. Check system load average:
   ```bash
   uptime
   `
   =18:45:46 up  2:38,  0 user,  load average: 0.01, 0.12, 0.20
3. Monitor memory usage:
   ```bash
   free -h
   ```
   =total        used        free      shared  buff/cache   available
Mem:           7.8Gi       2.4Gi       4.1Gi       1.1Mi       1.5Gi       5.3Gi
Swap:             0B          0B          0B
4. Check disk I/O statistics:
   ```bash
   iostat 2 3
   ```
   (If iostat not available, try: `vmstat 2 3`)
   =procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 3  0      0 4275376 193652 1393856    0    0   254   208  555    5  2  1 97  0  0  0
 0  0      0 4275124 193656 1393908    0    0     2   202 1984 3769  7  5 89  0  0  0
 0  0      0 4275124 193664 1393920    0    0     0    28 1001 1353  2  2 96  0  0  0

**Tell me**:
- What's your system load average?
= 18:45:46 up  2:38,  0 user,  load average:    0.01, 0.12, 0.20
- How much memory is free?
=Mem:4.1Gi
- What did you see in the top output?
=top - 18:45:31 up  2:38,  0 user,  load average: 0.01, 0.13, 0.20
Tasks:  48 total,   1 running,  42 sleeping,   5 stopped,   0 zombie
%Cpu(s):  3.9 us,  2.7 sy,  0.0 ni, 93.1 id,  0.3 wa,  0.0 hi,  0.0 si
MiB Mem :   7947.4 total,   4202.2 free,   2493.5 used,   1547.8 buff/
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   5453.8 avail

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM 
    246 root      20   0 1728200  28488  16000 S   0.3   0.4 
    248 root      20   0 1231508   8236   4096 S   0.3   0.1 
   1191 kingwc2+  20   0   11.3g 109468  48896 S   0.3   1.3 
---

## TASK 4: Kill Processes (Process Control)
**What you'll learn**: How to stop problematic processes

**Steps**:
1. Find your sleep processes:
   ```bash
   ps aux | grep sleep
   ```
   =root           1  0.0  0.0   4324  2560 ?        Ss   17:57   0:00 /bin/bash /google/scripts/onrun.sh sleep infinity
root         448  0.0  0.0   2696  1152 ?        S    17:57   0:00 sleep infinity
kingwc2+     676  0.0  0.0   7340  2560 pts/3    S<s+ 17:57   0:00 bash -c echo -en "\033]0;Gemini CLI\a"; echo "Starting Gemini CLI"; tmux has-session -t geminicli 2> /dev/null; if [[ $? -eq 1 ]]; then  tmux new-session -A -D -d -n "cloudshell" -s geminicli;   sleep 2;   tmux send-keys -t geminicli "gemini; exit" Enter; fi; sleep 2; tmux attach -t geminicli;sleep 3; 
kingwc2+    8980  0.0  0.0   6548  2304 pts/6    S+   18:53   0:00 grep --color=auto sleep

2. Kill one process using PID (replace XXXX with actual PID):
   ```bash
   kill XXXX
   ```
   kill 676
3. Check if it's gone:
   ```bash
   ps aux | grep sleep
   ```
   root           1  0.0  0.0   4324  2560 ?        Ss   17:57   0:00 /bin/bash /google/scripts/onrun.sh sleep infinity
root         448  0.0  0.0   2696  1152 ?        S    17:57   0:00 sleep infinity
kingwc2+    9435  0.0  0.0   6548  2304 pts/6    S+   18:55   0:00 grep --color=auto sleep
4. Force kill the remaining process:
   ```bash
   kill -9 YYYY
   ```
   (Replace YYYY with the PID)
5. Verify all sleep processes are gone:
   ```bash
   jobs
   ps aux | grep sleep
   ```

**Tell me**:
- Were you able to kill the processes? yes 
- What's the difference between `kill` and `kill -9`?
kill - Is normal Process else kill -9 - is terminated process force 


---

## TASK 5: Create a System Monitoring Script
**What you'll learn**: Automate system monitoring

**Steps**:
1. Create a new monitoring script:
   ```bash
   nano /home/$USER/devops-learning/day3/monitor.sh
   ```
2. Add this content:
   ```bash
   #!/bin/bash
   # System Monitor Script - Day 3
   
   echo "=== SYSTEM MONITOR REPORT ==="
   echo "Generated: $(date)"
   echo ""
   
   echo "=== SYSTEM LOAD ==="
   uptime
   echo ""
   
   echo "=== MEMORY USAGE ==="
   free -h
   echo ""
   
   echo "=== DISK USAGE ==="
   df -h | head -5
   echo ""
   
   echo "=== TOP 5 CPU PROCESSES ==="
   ps aux --sort=-%cpu | head -6
   echo ""
   
   echo "=== ACTIVE CONNECTIONS ==="
   ss -tuln | head -10
   echo ""
   
   echo "=== MONITORING COMPLETE ==="
   ```
3. Make it executable:
   ```bash
   chmod +x /home/$USER/devops-learning/day3/monitor.sh
   ```
4. Run your monitoring script:
   ```bash
   /home/$USER/devops-learning/day3/monitor.sh
   ```

**Tell me**:
- Did the script run successfully?
-yes
- What's your current system load?
-13:43:33 up 18:09,  0 user,  load average: 0.00, 0.00, 0.06
- Which process is using most CPU?
code edditor -code-oss-for-cloud-shell

---

## TASK 6: Set Up a Process Health Check
**What you'll learn**: Monitor specific services

**Steps**:
1. Create a service check script:
   ```bash
   nano /home/$USER/devops-learning/day3/service_check.sh
   ```
2. Add this content:
   ```bash
   #!/bin/bash
   # Service Health Check Script
   
   SERVICE="sshd"  # SSH service
   
   echo "=== SERVICE HEALTH CHECK ==="
   echo "Checking service: $SERVICE"
   echo "Time: $(date)"
   echo ""
   
   # Check if service is running
   if pgrep "$SERVICE" > /dev/null; then
       echo "✅ $SERVICE is RUNNING"
       echo "PIDs: $(pgrep $SERVICE | tr '\n' ' ')"
   else
       echo "❌ $SERVICE is NOT RUNNING"
   fi
   
   echo ""
   echo "=== SYSTEM RESOURCE CHECK ==="
   echo "Load Average: $(uptime | awk '{print $10,$11,$12}')"
   echo "Memory Usage: $(free | grep Mem | awk '{printf "%.1f%%", $3/$2 * 100}')"
   echo "Disk Usage: $(df / | tail -1 | awk '{print $5}')"
   echo ""
   ```
3. Make executable and run:
   ```bash
   chmod +x /home/$USER/devops-learning/day3/service_check.sh
   /home/$USER/devops-learning/day3/service_check.sh
   ```

**Tell me**:
- Is the SSH service running?
✅ sshd is RUNNING
PIDs: 70 498 500 502 503 511 726 
- What are your current resource percentages?
=== SYSTEM RESOURCE CHECK ===
Load Average: 0.04  
Memory Usage: 15.9%
Disk Usage: 55%

---

## TASK 7: Network Connection Monitoring
**What you'll learn**: Monitor network activity

**Steps**:
1. Check all network connections:
   ```bash
   ss -tuln
   ```
2. Find processes listening on ports:
   ```bash
   ss -tulnp
   ```
3. Check specific port (like SSH port 22):
   ```bash
   ss -tuln | grep :22
   ```
4. Monitor network statistics:
   ```bash
   netstat -i
   ```

**Tell me**:
- How many services are listening on ports?
- Is port 22 (SSH) open?
-There are 2 port 22 entries, but they represent the same SSH service running on both IPv4 and IPv6!
- What network interfaces do you have?
-1. eth0 (Primary Network Interface)
-2. lo (Loopback Interface)
-3. docker0 (Docker Bridge)

---

## TASK 8: Create a Performance Alert System
**What you'll learn**: Set up basic alerting

**Steps**:
1. Create an alert script:
   ```bash
   nano /home/$USER/devops-learning/day3/alert_check.sh
   ```
2. Add this content:
   ```bash
   #!/bin/bash
   # Performance Alert Script
   
   echo "=== PERFORMANCE ALERTS ==="
   echo "$(date)"
   echo ""
   
   # Check CPU Load
   LOAD=$(uptime | awk '{print $10}' | sed 's/,//')
   if (( $(echo "$LOAD > 1.0" | bc -l) )); then
       echo "🚨 HIGH CPU LOAD: $LOAD"
   else
       echo "✅ CPU Load Normal: $LOAD"
   fi
   
   # Check Memory Usage
   MEM_PERCENT=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100}')
   if [ $MEM_PERCENT -gt 80 ]; then
       echo "🚨 HIGH MEMORY USAGE: ${MEM_PERCENT}%"
   else
       echo "✅ Memory Usage Normal: ${MEM_PERCENT}%"
   fi
   
   # Check Disk Usage
   DISK_PERCENT=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')
   if [ $DISK_PERCENT -gt 85 ]; then
       echo "🚨 HIGH DISK USAGE: ${DISK_PERCENT}%"
   else
       echo "✅ Disk Usage Normal: ${DISK_PERCENT}%"
   fi
   
   echo ""
   echo "=== ALERT CHECK COMPLETE ==="
   ```
3. Make executable and run:
   ```bash
   chmod +x /home/$USER/devops-learning/day3/alert_check.sh
   /home/$USER/devops-learning/day3/alert_check.sh
   ```

**Tell me**:
- Are any of your system resources in alert status?

All good Green.
- What are your current CPU, memory, and disk percentages?
-CPU Load: 0.01 (1% of maximum load)
Memory Usage: 16% (using 16% of total RAM)
Disk Usage: 55% (using 55% of disk space)
---

## What You're Learning Today:

### Real DevOps Skills:
1. **Process Management** - Starting, stopping, monitoring processes
2. **Resource Monitoring** - CPU, memory, disk, network usage
3. **Automation** - Creating monitoring scripts
4. **Alerting** - Setting up basic performance alerts
5. **Troubleshooting** - Finding and managing problematic processes

### Why This Matters:
- **Production Systems**: Servers run hundreds of processes
- **Performance Issues**: You need to find resource-hungry processes
- **System Stability**: Monitoring prevents crashes
- **Automation**: Scripts reduce manual monitoring work

---

## Ready to Master Process Management?

Complete these 8 tasks and you'll understand how DevOps engineers monitor and manage production systems!

**Take your time, follow each step, and report back with your results!** 🚀