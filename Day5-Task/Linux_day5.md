# Day 5: Web Server Management & Service Configuration

## What You'll Learn Today
- Install and configure web servers (Apache/Nginx)
- Manage web server configurations
- Set up virtual hosts and SSL basics
- Monitor web server performance
- Create automated deployment scripts
- Understand reverse proxy concepts

---

## TASK 1: Web Server Installation & Basic Setup
**What you'll learn**: Install and start your first web server

**Steps**:
1. Update package repositories:
   ```bash
   sudo apt update
   ```
2. Install Nginx web server:
   ```bash
   sudo apt install nginx -y
   ```
3. Check if Nginx is running:
   ```bash
   sudo systemctl status nginx
   ```
   (If systemctl not available, try: `sudo service nginx status`)
4. Test the web server:
   ```bash
   curl http://localhost
   curl http://localhost:80
   ```
5. Check which process is using port 80:
   ```bash
   sudo lsof -i :80
   ```
6. View Nginx configuration:
   ```bash
   sudo nginx -t
   cat /etc/nginx/nginx.conf | head -20
   ```

**Tell me**: 
- Did Nginx install successfully?
-Yes
- Is it responding on port 80? yes confird via ss
- What does the default page show?
-“Welcome to Nginx”

---

## TASK 2: Create Custom Web Content
**What you'll learn**: Deploy custom content to your web server

**Steps**:
1. Find the web root directory:
   ```bash
   ls -la /var/www/html/
   ```
2. Backup the default page:
   ```bash
   sudo cp /var/www/html/index.nginx-debian.html /var/www/html/index.backup.html
   ```
3. Create your custom index page:
   ```bash
   sudo tee /var/www/html/index.html << 'EOF'
   <!DOCTYPE html>
   <html>
   <head>
       <title>DevOps Learning - Day 5</title>
       <style>
           body { font-family: Arial, sans-serif; margin: 40px; background: #f0f0f0; }
           .container { background: white; padding: 30px; border-radius: 10px; }
           h1 { color: #2c3e50; }
           .info { background: #ecf0f1; padding: 15px; border-radius: 5px; margin: 10px 0; }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>🚀 Welcome to My DevOps Web Server!</h1>
           <div class="info">
               <strong>Server:</strong> Nginx<br>
               <strong>Date:</strong> <script>document.write(new Date().toLocaleDateString());</script><br>
               <strong>Status:</strong> ✅ Online and Running<br>
               <strong>DevOps Engineer:</strong> [Your Name]
           </div>
           <h2>System Information</h2>
           <p>This web server is running on a Linux system managed through DevOps practices.</p>
           <h2>Services Status</h2>
           <ul>
               <li>✅ Nginx Web Server</li>
               <li>✅ SSH Remote Access</li>
               <li>✅ System Monitoring</li>
           </ul>
       </div>
   </body>
   </html>
   EOF
   ```
4. Test your custom page:
   ```bash
   curl http://localhost | head -10
   ```
5. Check file permissions:
   ```bash
   ls -la /var/www/html/index.html
   ```

**Tell me**: 
- Did your custom page load successfully?
-Yes — confirmed by curl http://localhost | head -10 showing your custom HTML (<title>DevOps Learning - Day 5</title>).
- What content does curl show?
-it shows updated page
- Are the file permissions correct?
- yes owner is root --rw-r--r-- 1 root root 1140 ...

---

## TASK 3: Configure Virtual Hosts
**What you'll learn**: Host multiple websites on one server

**Steps**:
1. Create directory for a new site:
   ```bash
   sudo mkdir -p /var/www/devops-site
   ```
2. Create content for the new site:
   ```bash
   sudo tee /var/www/devops-site/index.html << 'EOF'
   <!DOCTYPE html>
   <html>
   <head><title>DevOps Site</title></head>
   <body>
       <h1>🛠️ DevOps Tools Site</h1>
       <p>This is a separate virtual host!</p>
       <ul>
           <li>Docker</li>
           <li>Kubernetes</li>
           <li>Jenkins</li>
           <li>Monitoring Tools</li>
       </ul>
   </body>
   </html>
   EOF
   ```
3. Create Nginx virtual host configuration:
   ```bash
   sudo tee /etc/nginx/sites-available/devops-site << 'EOF'
   server {
       listen 8080;
       server_name localhost;
       root /var/www/devops-site;
       index index.html;
       
       location / {
           try_files $uri $uri/ =404;
       }
       
       access_log /var/log/nginx/devops-site.access.log;
       error_log /var/log/nginx/devops-site.error.log;
   }
   EOF
   ```
4. Enable the virtual host:
   ```bash
   sudo ln -s /etc/nginx/sites-available/devops-site /etc/nginx/sites-enabled/
   ```
5. Test Nginx configuration:
   ```bash
   sudo nginx -t
   ```
6. Reload Nginx:
   ```bash
   sudo systemctl reload nginx
   ```
   (Or: `sudo service nginx reload`)
7. Test your new virtual host:
   ```bash
   curl http://localhost:8080
   ```

**Tell me**: 
- Did the virtual host configuration test pass?
-(syntax is ok
configuration file /etc/nginx/nginx.conf test is successful)
- Can you access the site on port 8080?
(Yes — curl http://localhost:8080 returned your custom HTML.)
- What content appears on the new virtual host?
(<h1>🛠️ DevOps Tools Site</h1>
<p>This is a separate virtual host!</p>
<ul>
    <li>Docker</li>
    <li>Kubernetes</li>
    <li>Jenkins</li>
    <li>Monitoring Tools</li>
</ul>)

---

## TASK 4: Web Server Log Analysis
**What you'll learn**: Monitor and analyze web server logs

**Steps**:
1. View Nginx access logs:
   ```bash
   sudo tail -10 /var/log/nginx/access.log
   ```
2. View Nginx error logs:
   ```bash
   sudo tail -10 /var/log/nginx/error.log
   ```
3. Generate some web traffic:
   ```bash
   for i in {1..5}; do curl -s http://localhost > /dev/null; done
   for i in {1..3}; do curl -s http://localhost:8080 > /dev/null; done
   ```
4. Check the new log entries:
   ```bash
   sudo tail -5 /var/log/nginx/access.log
   ```
5. Create a log analysis script:
   ```bash
   nano ~/devops-learning/day5/nginx_logs.sh
   ```
   Add this content:
   ```bash
   #!/bin/bash
   # Nginx Log Analysis Script
   
   echo "=== NGINX LOG ANALYSIS ==="
   echo "Generated: $(date)"
   echo ""
   
   echo "=== ACCESS LOG SUMMARY ==="
   echo "Total requests today:"
   sudo grep "$(date +%d/%b/%Y)" /var/log/nginx/access.log 2>/dev/null | wc -l
   
   echo "Recent requests (last 5):"
   sudo tail -5 /var/log/nginx/access.log
   echo ""
   
   echo "=== ERROR LOG CHECK ==="
   echo "Recent errors (last 3):"
   sudo tail -3 /var/log/nginx/error.log
   echo ""
   
   echo "=== STATUS CODE ANALYSIS ==="
   echo "HTTP 200 responses:"
   sudo grep " 200 " /var/log/nginx/access.log | wc -l
   echo "HTTP 404 responses:"
   sudo grep " 404 " /var/log/nginx/access.log | wc -l
   echo ""
   
   echo "=== TOP REQUESTED PAGES ==="
   sudo awk '{print $7}' /var/log/nginx/access.log 2>/dev/null | sort | uniq -c | sort -nr | head -5
   
   echo ""
   echo "=== LOG ANALYSIS COMPLETE ==="
   ```
6. Make executable and run:
   ```bash
   chmod +x ~/devops-learning/day5/nginx_logs.sh
   ~/devops-learning/day5/nginx_logs.sh
   ```

**Tell me**: 
- How many requests were logged today?
(15 Request)
- Are there any errors in the error log?
(No critical errors. The only entry is a notice:)
- What are the most requested pages?
(Top pages are:
/ → requested 14 times (your main site root).
/developmentserver/metadatauploader → requested 1 time.)

---

## TASK 5: SSL/HTTPS Setup (Self-Signed Certificate)
**What you'll learn**: Configure basic HTTPS encryption

**Steps**:
1. Create SSL certificate directory:
   ```bash
   sudo mkdir -p /etc/nginx/ssl
   ```
2. Generate self-signed SSL certificate:
   ```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
       -keyout /etc/nginx/ssl/nginx.key \
       -out /etc/nginx/ssl/nginx.crt \
       -subj "/C=US/ST=State/L=City/O=DevOps/CN=localhost"
   ```
3. Create HTTPS virtual host:
   ```bash
   sudo tee /etc/nginx/sites-available/https-site << 'EOF'
   server {
       listen 8443 ssl;
       server_name localhost;
       
       ssl_certificate /etc/nginx/ssl/nginx.crt;
       ssl_certificate_key /etc/nginx/ssl/nginx.key;
       
       root /var/www/html;
       index index.html;
       
       location / {
           try_files $uri $uri/ =404;
       }
   }
   EOF
   ```
4. Enable HTTPS site:
   ```bash
   sudo ln -s /etc/nginx/sites-available/https-site /etc/nginx/sites-enabled/
   ```
5. Test and reload:
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```
6. Test HTTPS (ignore certificate warning):
   ```bash
   curl -k https://localhost:8443 | head -5
   ```

**Tell me**: 
- Did the SSL certificate generate successfully?
(yes)
- Can you access the HTTPS site on port 8443?
(yes)
- What certificate information is shown?
(subject=C=US, ST=State, L=City, O=DevOps, CN=localhost
notBefore=Sep 23 14:46:18 2025 GMT
notAfter=Sep 23 14:46:18 2026 GMT)

## TASK 6: Reverse Proxy Configuration
**What you'll learn**: Set up Nginx as a reverse proxy

**Steps**:
1. Start a simple backend service (Python HTTP server):
   ```bash
   cd ~/devops-learning/day5
   echo "<h1>Backend Service</h1><p>This is served by Python on port 8000</p>" > backend.html
   python3 -m http.server 8000 &
   PYTHON_PID=$!
   ```
   (echo "<h1>Backend Service</h1><p>This is served by Python on port 8000</p>" > backend.html
   python3 -m http.server 8000 &
   PYTHON_PID=$!
[1] 2650)
2. Test the backend directly:
   ```bash
   curl http://localhost:8000/backend.html
   ```
   (curl http://localhost:8000/backend.html
127.0.0.1 - - [23/Sep/2025 15:42:01] "GET /backend.html HTTP/1.1" 200 -
<h1>Backend Service</h1><p>This is served by Python on port 8000</p>)

3. Create reverse proxy configuration:
   ```bash
   sudo tee /etc/nginx/sites-available/reverse-proxy << 'EOF'
   server {
       listen 9000;
       server_name localhost;
       
       location / {
           proxy_pass http://localhost:8000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   EOF
   ```
4. Enable reverse proxy:
   ```bash
   sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl reload nginx
   ```
   (sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful)
5. Test reverse proxy:
   ```bash
   curl http://localhost:9000/backend.html
   ```
   (curl http://localhost:9000/backend.html
127.0.0.1 - - [23/Sep/2025 15:45:49] "GET /backend.html HTTP/1.0" 200 -
<h1>Backend Service</h1><p>This is served by Python on port 8000</p>)
6. Stop the Python server:
   ```bash
   kill $PYTHON_PID 2>/dev/null || pkill -f "python3 -m http.server"
   ```

**Tell me**: 
- Did the reverse proxy work correctly?
(❌ No — the Python backend on port 8000 terminated right away, so the reverse proxy had nothing to forward traffic to.)
- Could you access the backend through port 9000?
(❌ No — since port 8000 wasn’t serving content, Nginx on 9000 also failed.)
- What's the difference between direct and proxied access?
(Direct access (:8000) means the client talks straight to the Python HTTP server.
Proxied access (:9000) means the client talks to Nginx, which relays the request to the Python)

---

## TASK 7: Web Server Performance Monitoring
**What you'll learn**: Monitor web server performance and resources

**Steps**:
1. Create performance monitoring script:
   ```bash
   nano ~/devops-learning/day5/nginx_performance.sh
   ```
   Add this content:
   ```bash
   #!/bin/bash
   # Nginx Performance Monitor
   
   echo "=== NGINX PERFORMANCE MONITOR ==="
   echo "Timestamp: $(date)"
   echo ""
   
   echo "=== NGINX PROCESS STATUS ==="
   ps aux | grep nginx | grep -v grep
   echo ""
   
   echo "=== LISTENING PORTS ==="
   echo "HTTP services:"
   ss -tln | grep -E ":80|:8080|:8443|:9000"
   echo ""
   
   echo "=== CONNECTION STATISTICS ==="
   echo "Active connections to port 80:"
   ss -t | grep ":80" | wc -l
   echo "Active connections to port 8080:"
   ss -t | grep ":8080" | wc -l
   echo ""
   
   echo "=== NGINX STATUS ==="
   systemctl is-active nginx 2>/dev/null || service nginx status | head -1
   echo ""
   
   echo "=== RESOURCE USAGE ==="
   echo "Nginx memory usage:"
   ps aux | grep nginx | grep -v grep | awk '{sum+=$6} END {printf "%.2f MB\n", sum/1024}'
   echo ""
   
   echo "=== RECENT ACCESS LOG ==="
   echo "Last 3 requests:"
   sudo tail -3 /var/log/nginx/access.log 2>/dev/null || echo "No access log available"
   echo ""
   
   echo "=== CONFIGURATION TEST ==="
   sudo nginx -t 2>&1
   echo ""
   
   echo "=== PERFORMANCE MONITORING COMPLETE ==="
   ```
2. Make executable and run:
   ```bash
   chmod +x ~/devops-learning/day5/nginx_performance.sh
   ~/devops-learning/day5/nginx_performance.sh
   ```

**Tell me**: 
- How much memory is Nginx using?
(21.60 MB)
- How many ports is Nginx listening on?
(4 ports → 80, 8080, 8443, and 9000)
- Is the Nginx configuration valid?
(Yes — nginx -t reports syntax is OK)

---

## TASK 8: Automated Deployment Script
**What you'll learn**: Create automated web deployment

**Steps**:
1. Create deployment script:
   ```bash
   nano ~/devops-learning/day5/web_deploy.sh
   ```
   Add this content:
   ```bash
   #!/bin/bash
   # Web Deployment Automation Script
   
   TIMESTAMP=$(date '+%Y-%m-%d_%H-%M-%S')
   BACKUP_DIR="/var/www/backups"
   WEB_ROOT="/var/www/html"
   
   echo "=== WEB DEPLOYMENT SCRIPT ==="
   echo "Starting deployment at: $(date)"
   echo ""
   
   # Create backup directory
   sudo mkdir -p $BACKUP_DIR
   
   # Backup current website
   echo "📦 Creating backup..."
   sudo tar -czf $BACKUP_DIR/website_backup_$TIMESTAMP.tar.gz -C $WEB_ROOT .
   echo "✅ Backup created: website_backup_$TIMESTAMP.tar.gz"
   echo ""
   
   # Deploy new content
   echo "🚀 Deploying new content..."
   sudo tee $WEB_ROOT/index.html << EOF
   <!DOCTYPE html>
   <html>
   <head>
       <title>Deployed Site - $TIMESTAMP</title>
       <style>
           body { font-family: Arial, sans-serif; margin: 40px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; }
           .container { background: rgba(255,255,255,0.1); padding: 30px; border-radius: 15px; backdrop-filter: blur(10px); }
           h1 { color: #fff; text-shadow: 2px 2px 4px rgba(0,0,0,0.3); }
           .info { background: rgba(255,255,255,0.2); padding: 15px; border-radius: 10px; margin: 15px 0; }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>🚀 Automated Deployment Successful!</h1>
           <div class="info">
               <strong>Deployment Time:</strong> $TIMESTAMP<br>
               <strong>Server:</strong> Nginx<br>
               <strong>Status:</strong> ✅ Deployment Complete<br>
               <strong>Backup:</strong> Created successfully
           </div>
           <h2>Deployment Features</h2>
           <ul>
               <li>✅ Automated backup creation</li>
               <li>✅ Zero-downtime deployment</li>
               <li>✅ Configuration validation</li>
               <li>✅ Rollback capability</li>
           </ul>
           <p><em>This page was deployed automatically using DevOps practices!</em></p>
       </div>
   </body>
   </html>
   EOF
   
   # Test nginx configuration
   echo "🔧 Testing Nginx configuration..."
   if sudo nginx -t; then
       echo "✅ Configuration is valid"
   else
       echo "❌ Configuration error - rolling back..."
       sudo tar -xzf $BACKUP_DIR/website_backup_$TIMESTAMP.tar.gz -C $WEB_ROOT
       exit 1
   fi
   
   # Reload nginx
   echo "🔄 Reloading Nginx..."
   sudo systemctl reload nginx || sudo service nginx reload
   
   # Test deployment
   echo "🧪 Testing deployment..."
   if curl -s http://localhost | grep -q "Deployment Successful"; then
       echo "✅ Deployment test passed!"
   else
       echo "⚠️ Deployment test failed"
   fi
   
   echo ""
   echo "🎉 DEPLOYMENT COMPLETE!"
   echo "📊 Deployment Summary:"
   echo "   - Backup: $BACKUP_DIR/website_backup_$TIMESTAMP.tar.gz"
   echo "   - Status: $(curl -s -o /dev/null -w "%{http_code}" http://localhost)"
   echo "   - Time: $(date)"
   ```
2. Make executable and run:
   ```bash
   chmod +x ~/devops-learning/day5/web_deploy.sh
   ~/devops-learning/day5/web_deploy.sh
   ```
3. Test the deployed site:
   ```bash
   curl http://localhost | head -10
   ```

**Tell me**: 
- Did the automated deployment complete successfully?
(Yes — deployment finished, config validated, and page served.)
- Was a backup created?
(website_backup_2025-09-23_15-58-52.tar.gz)
- Does the new deployed page load correctly?
(Yes — curl showed Automated Deployment Successful!, and HTTP status was 200)

---

## What You're Learning Today:

### Real DevOps Web Server Skills:
1. **Web Server Management** - Installing, configuring, managing Nginx
2. **Virtual Hosts** - Hosting multiple sites on one server
3. **SSL/HTTPS** - Basic encryption and certificate management
4. **Reverse Proxy** - Load balancing and backend service integration
5. **Log Analysis** - Monitoring and troubleshooting web traffic
6. **Performance Monitoring** - Resource usage and optimization
7. **Automated Deployment** - Zero-downtime deployment practices
8. **Backup & Recovery** - Disaster recovery procedures

### Why This Matters:
- **Production Web Services**: Most applications are web-based
- **High Availability**: Web servers must stay online 24/7
- **Security**: HTTPS and proper configuration are essential
- **Scalability**: Reverse proxies enable load distribution
- **Monitoring**: Web server metrics are critical for performance
- **Automation**: Manual deployments don't scale

---

## Ready to Master Web Server Management?

Complete these 8 tasks to understand how DevOps engineers manage production web infrastructure!

**This is the foundation of modern web application deployment!** 🌐🚀