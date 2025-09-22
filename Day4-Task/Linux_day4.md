# Day 4: Network Management & Security Fundamentals

## What You'll Learn Today
- Network connectivity testing and troubleshooting
- Security configuration (SSH, firewall basics)
- Network monitoring and analysis
- Basic security hardening
- Network service management

---

## TASK 1: Network Connectivity Testing
**What you'll learn**: How to test and troubleshoot network connections

**Steps**:
1. Test basic connectivity to Google:
   ```bash
   ping -c 4 google.com
   ```
2. Test connectivity to a specific server:
   ```bash
   ping -c 4 8.8.8.8
   ```
3. Trace the network path to Google:
   ```bash
   traceroute google.com
   ```
   (If traceroute not available, try: `tracepath google.com`)
4. Test DNS resolution:
   ```bash
   nslookup google.com
   ```
5. Test with dig (more detailed DNS info):
   ```bash
   dig google.com
   ```

**Tell me**: 
- Did all ping tests succeed?
-yes
-4 packets transmitted, 4 received, 0% packet loss, time 3055ms
- How many hops (routers) to reach Google?
-"traceroute not available" in GCP
- What IP address did Google resolve to?
-74.125.68.101
 74.125.68.100

---

## TASK 2: Port Connectivity Testing
**What you'll learn**: Test if specific ports are open and accessible

**Steps**:
1. Test SSH port on your local machine:
   ```bash
   nc -zv localhost 22
   if nc not availble use telnet 
   telnet localhost 22
   ```
2. Test if Google's web server is accessible:
   ```bash
   nc -zv google.com 80
   nc -zv google.com 443
   ```
3. Test multiple ports at once:
   ```bash
   nmap -p 22,80,443 google.com
   ```
4. Scan your own listening ports:
   ```bash
   nmap -p 1-1000 localhost
   ```
5. Check what's listening on specific ports:
   ```bash
   lsof -i :22
   lsof -i :3000
   ```

**Tell me**:
- Which ports are open on Google? 
-PORT 443 IS OPEN 
- What process is listening on port 22?
-OpenSSH_9.6p1 - Your SSH server
- How many ports are open on your localhost?
-11
---

## TASK 3: SSH Security Configuration
**What you'll learn**: Configure secure remote access

**Steps**:
1. Check current SSH configuration:
   ```bash
   sudo cat /etc/ssh/sshd_config | grep -E "Port|PermitRootLogin|PasswordAuthentication"
   ```
2. Check SSH service status:
   ```bash
   sudo systemctl status ssh
   ```
   (If that fails, try: `sudo systemctl status sshd`)
3. View SSH connection attempts:
   ```bash
   sudo journalctl -u ssh | tail -10
   ```
4. Check currently connected SSH sessions:
   ```bash
   who
   w
   ```
5. Create SSH key pair (if you don't have one):
   ```bash
   ssh-keygen -t rsa -b 4096 -f ~/.ssh/devops_key
   ```

**Tell me**:
- What port is SSH running on?
-port 22
- Is root login permitted?
-Yes
- How many active SSH sessions do you see?
-0 traditional SSH sessions (web-based shell environment)
---

## TASK 4: Basic Firewall Management
**What you'll learn**: Configure basic firewall rules for security

**Steps**:
1. Check if firewall is active:
   ```bash
   sudo ufw status
   ```
2. If UFW isn't available, check iptables:
   ```bash
   sudo iptables -L
   ```
3. Enable UFW (if available):
   ```bash
   sudo ufw enable
   ```
4. Add basic rules (if UFW available):
   ```bash
   sudo ufw allow ssh
   sudo ufw allow 3000
   sudo ufw status numbered
   ```
5. Check system firewall logs:
   ```bash
   sudo journalctl -f | grep -i firewall &
   ```
   Press Ctrl+C after 10 seconds

**Tell me**:
- Is a firewall currently active?
-Yes - iptables is active ✅
- Which firewall system is your system using?
-iptables with Docker integration 🐳
- What rules are currently configured?
-Permissive policies (ACCEPT all)
Docker networking rules (container isolation)
No restrictive rules (cloud-managed security)
---

## TASK 5: Network Traffic Monitoring
**What you'll learn**: Monitor network activity and connections

**Steps**:
1. Monitor active connections in real-time:
   ```bash
   ss -tuln
   ```
2. Show network statistics:
   ```bash
   netstat -s
   ```
3. Monitor network interface traffic:
   ```bash
   watch -n 2 'cat /proc/net/dev'
   ```
   (Press Ctrl+C after 10 seconds)
4. Check routing table:
   ```bash
   ip route show
   ```
5. Monitor DNS queries (if available):
   ```bash
   dig +trace google.com | head -20
   ```

**Tell me**:
- How many total TCP connections are established?
-24 connections established
- What's your default gateway IP?
-10.88.0.1
- Did you see network traffic changing in real-time?
-Yes - the watch command would show interface statistics updating 

---

## TASK 6: Create Network Security Monitor Script
**What you'll learn**: Automate network security monitoring

**Steps**:
1. Create network monitoring script:
   ```bash
   nano ~/devops-learning/day4/network_monitor.sh
   ```
2. Add this content:
   ```bash
   #!/bin/bash
   # Network Security Monitor Script
   
   echo "=== NETWORK SECURITY MONITOR ==="
   echo "Generated: $(date)"
   echo ""
   
   echo "=== ACTIVE CONNECTIONS ==="
   echo "Total established connections: $(ss -t | grep ESTAB | wc -l)"
   echo "Listening services: $(ss -tln | grep LISTEN | wc -l)"
   echo ""
   
   echo "=== SSH SECURITY CHECK ==="
   echo "SSH service status:"
   systemctl is-active ssh 2>/dev/null || systemctl is-active sshd 2>/dev/null || echo "SSH status unknown"
   echo "Active SSH sessions: $(who | wc -l)"
   echo ""
   
   echo "=== PORT SCAN DETECTION ==="
   echo "Recent connection attempts to SSH:"
   sudo journalctl -u ssh --since "5 minutes ago" | grep -i "connection\|failed\|invalid" | tail -3 2>/dev/null || echo "No recent SSH logs found"
   echo ""
   
   echo "=== NETWORK INTERFACES ==="
   echo "Active interfaces:"
   ip link show | grep "state UP" | awk '{print $2}' | sed 's/://'
   echo ""
   
   echo "=== FIREWALL STATUS ==="
   if command -v ufw >/dev/null; then
       echo "UFW Status: $(sudo ufw status | head -1)"
   elif command -v iptables >/dev/null; then
       echo "IPTables rules: $(sudo iptables -L | grep -c Chain) chains configured"
   else
       echo "No standard firewall detected"
   fi
   echo ""
   
   echo "=== SECURITY MONITORING COMPLETE ==="
   ```
3. Make executable and run:
   ```bash
   chmod +x ~/devops-learning/day4/network_monitor.sh
   ~/devops-learning/day4/network_monitor.sh
   ```

**Tell me**:
- How many established connections do you have?
-22
- Is SSH service running properly?
-So in your current environment: SSH is not running at all, and that’s expected.
- What firewall status did it detect?
-IPTables rules: 10 chains configured

---

## TASK 7: DNS and Domain Analysis
**What you'll learn**: Analyze domain names and DNS records

**Steps**:
1. Get comprehensive DNS info for a domain:
   ```bash
   dig ANY google.com
   ```
2. Check mail servers:
   ```bash
   dig MX google.com
   ```
3. Check name servers:
   ```bash
   dig NS google.com
   ```
4. Reverse DNS lookup:
   ```bash
   dig -x 8.8.8.8
   ```
5. Check DNS response times:
   ```bash
   for i in {1..3}; do time dig google.com > /dev/null; done
   ```

**Tell me**:
- What mail servers does Google use?
-smtp.google.com
- What are Google's name servers?
-ns1.google.com
ns2.google.com
ns3.google.com
ns4.google.com
- What domain name does 8.8.8.8 reverse to?
-Resolves to dns.google
---

## TASK 8: Security Hardening Check
**What you'll learn**: Basic security assessment and hardening

**Steps**:
1. Check for security updates:
   ```bash
   sudo apt list --upgradable 2>/dev/null | head -10
   ```
2. Check system users:
   ```bash
   cat /etc/passwd | grep -E "bash|sh" | cut -d: -f1
   ```
3. Check sudo access:
   ```bash
   sudo cat /etc/sudoers | grep -v "^#" | grep -v "^$"
   ```
4. Check failed login attempts:
   ```bash
   sudo journalctl | grep "Failed password" | tail -5
   ```
5. Create a security report script:
   ```bash
   nano ~/devops-learning/day4/security_check.sh
   ```
   Add this content:
   ```bash
   #!/bin/bash
   # Basic Security Assessment
   
   echo "=== SECURITY ASSESSMENT REPORT ==="
   echo "Date: $(date)"
   echo "System: $(hostname)"
   echo ""
   
   echo "=== USER ACCOUNTS ==="
   echo "Users with shell access:"
   cat /etc/passwd | grep -E "bash|sh" | cut -d: -f1 | sort
   echo ""
   
   echo "=== NETWORK SECURITY ==="
   echo "Open ports (listening):"
   ss -tln | grep LISTEN | awk '{print $4}' | cut -d: -f2 | sort -n | uniq
   echo ""
   
   echo "=== SSH SECURITY ==="
   echo "SSH configuration check:"
   if [ -f /etc/ssh/sshd_config ]; then
       echo "Root login: $(sudo grep "^PermitRootLogin" /etc/ssh/sshd_config || echo "default (usually yes)")"
       echo "Password auth: $(sudo grep "^PasswordAuthentication" /etc/ssh/sshd_config || echo "default (usually yes)")"
   else
       echo "SSH config not accessible"
   fi
   echo ""
   
   echo "=== SYSTEM UPDATES ==="
   echo "Available security updates:"
   apt list --upgradable 2>/dev/null | grep -i security | wc -l
   echo ""
   
   echo "=== SECURITY CHECK COMPLETE ==="
   ```
6. Make executable and run:
   ```bash
   chmod +x ~/devops-learning/day4/security_check.sh
   ~/devops-learning/day4/security_check.sh
   ```

**Tell me**:
- How many users have shell access?
- totel 4 [user]
postgres
root
sshd
- What ports are publicly listening?
22
900
922
970
980
981
3000
8998
41967
45245
- Are there any security updates available?
-Available security updates:
0
---

## What You're Learning Today:

### Real DevOps Network Skills:
1. **Connectivity Testing** - Diagnosing network issues
2. **Port Management** - Understanding service accessibility
3. **SSH Security** - Securing remote access
4. **Firewall Configuration** - Basic network security
5. **Traffic Monitoring** - Watching network activity
6. **DNS Management** - Understanding domain resolution
7. **Security Hardening** - Basic system security
8. **Automation** - Network monitoring scripts

### Why This Matters:
- **Production Security**: Servers need proper network security
- **Troubleshooting**: Network issues are common in DevOps
- **Compliance**: Many regulations require network security controls
- **Monitoring**: Proactive network monitoring prevents outages

---

## Ready to Master Network Security?

Complete these 8 tasks to understand how DevOps engineers secure and manage network infrastructure!

**Take your time and report back with your results for each task!** 🔒🚀