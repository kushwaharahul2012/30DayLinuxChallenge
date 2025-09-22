# DevOps Learning – Day 2 Tasks (Step by Step)

## Day 2: Text Processing & Log Analysis
**Learning Goal**: Practice text manipulation and log analysis skills in Linux.

---

## TASK 1: Get Your Log File Ready
**What you'll learn**: How to create or download a sample log file  

**Steps**:
1. Open your terminal  
2. Type: `mkdir -p /home/$USER/devops-learning/day2`  
3. Press Enter  
4. Move into the folder: `cd /home/$USER/devops-learning/day2`  
5. Create a log file with at least 100 lines (either generate mock logs or copy an existing one into `access.log`)  

**Tell me**: Did you get a file called `access.log` with at least 100 lines? Paste the first 10 lines. 
1758286558 127.0.0.184 GET /index.html 404
1758286558 127.0.0.72 GET /index.html 301
1758286558 127.0.0.92 GET /index.html 302
1758286558 127.0.0.167 GET /index.html 302
1758286558 127.0.0.161 GET /index.html 200
1758286558 127.0.0.30 GET /index.html 301
1758286558 127.0.0.211 GET /index.html 502
1758286558 127.0.0.132 GET /index.html 403
1758286558 127.0.0.130 GET /index.html 200
1758286558 127.0.0.88 GET /index.html 404


## TASK 2: Count Total Number of Lines
**What you'll learn**: How to count lines in a file  

**Steps**:
1. Ensure you are inside `/home/$USER/devops-learning/day2`  
2. Type: `wc -l access.log`  
3. Press Enter  

**Tell me**: How many lines did it show? 
100

## TASK 3: Extract Unique IP Addresses
**What you'll learn**: How to filter and deduplicate data  

**Steps**:
1. Look at your log and check which column has the IP address (usually the first or second)  
2. Use a command to print only that column  
3. Pipe the output through a sorting command  
4. Pipe it again through a duplicate-removal command  

**Tell me**: Paste the first 10 unique IPs and the total count.
127.0.0.106
127.0.0.107
127.0.0.108
127.0.0.109
127.0.0.115
127.0.0.120
127.0.0.123
127.0.0.124
127.0.0.125
127.0.0.127
Totel Count - 85



## TASK 4: Count Each HTTP Status Code
**What you'll learn**: How to group and count items  

**Steps**:
1. Identify which column contains the HTTP status code (200, 404, etc.)  
2. Extract that column from the log  
3. Sort the values  
4. Count how many times each appears  

**Tell me**: Paste the full list of status codes with counts.  
     23 200
     13 400
     13 301
     11 404
     11 302
      8 403
      6 503
      6 502
      5 401
      4 500
---

## TASK 5: Find Log Entries from the Last Hour
**What you'll learn**: How to filter entries based on time  

**Steps**:
1. Check the timestamp format in your log (epoch seconds or human-readable time)  
2. Get the current system time in seconds (`date +%s`)  
3. Subtract 3600 (for one hour ago)  
4. Print only lines where the log timestamp is greater than or equal to that value  

**Tell me**: How many entries did you find from the last hour?  
100 
---

## TASK 6: Extract Timestamp and Status Code with `awk`
**What you'll learn**: How to print only specific columns  

**Steps**:
1. Look at your log format and identify the column numbers for timestamp and status code  
2. Use a command to print only those two columns  
3. Redirect the output to a new file `ts_status.txt`  

**Tell me**: Paste the first 10 lines of `ts_status.txt`.  
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
2025-09-19 /index.html
---

## TASK 7: Monitor Logs in Real Time
**What you'll learn**: How to watch a file for new entries  

**Steps**:
1. Create a new file called `monitor.sh`  
2. Write a script that follows (`tail -f`) the log file in real time  
3. Make it check for either the word `ERROR` or status codes starting with `5`  
4. Print an alert whenever it sees one  
5. Save and run the script  

**Tell me**: Did your script print an alert when you added a fake `500` log line? Paste the alert line.  
echo "$(date '+%Y-%m-%d %H:%M:%S') 127.0.0.1 GET /fail 500" >> access.log
---

## TASK 8: Sort Log Entries by Timestamp
**What you'll learn**: How to sort data by a specific field  

**Steps**:
1. Use the sorting command on the timestamp column of your log  
2. Redirect the result into a new file `sorted.log`  

**Tell me**: Paste the first 10 lines of `sorted.log`.  
2025-09-19 13:33:13 127.0.0.101 GET /index.html 200
2025-09-19 13:33:13 127.0.0.103 GET /index.html 401
2025-09-19 13:33:13 127.0.0.10 GET /index.html 503
2025-09-19 13:33:13 127.0.0.128 GET /index.html 404
2025-09-19 13:33:13 127.0.0.131 GET /index.html 200
2025-09-19 13:33:13 127.0.0.133 GET /index.html 200
2025-09-19 13:33:13 127.0.0.134 GET /index.html 401
2025-09-19 13:33:13 127.0.0.135 GET /index.html 200
2025-09-19 13:33:13 127.0.0.139 GET /index.html 503
2025-09-19 13:33:13 127.0.0.140 GET /index.html 400
---

## TASK 9: Find the Top 10 Most Accessed URLs
**What you'll learn**: How to rank items by frequency  

**Steps**:
1. Identify the column in the log that contains the URL  
2. Extract only that column  
3. Count how many times each URL appears  
4. Sort them by frequency (highest first)  
5. Show only the top 10  

**Tell me**: Paste the top 10 URLs with their counts.  
      35 /index.html
     31 /contact
     29 /login
     28 /home
     21 /products
     21 /cart
     18 /signup
     17 /about
---

## TASK 10: Create a Summary Report
**What you'll learn**: How to combine multiple commands into one report  

**Steps**:
1. Count total number of requests (lines)  
2. Count unique IP addresses  
3. Count how many requests are errors (4xx and 5xx)  
4. Calculate the error rate (errors ÷ total × 100)  
5. Collect the “Top 10 URLs” from Task 9  
6. Save all this information into `report.txt`  

**Tell me**: Paste the full contents of `report.txt`.  
Total Requests: 200
Unique IPs: 1
Error Count (4xx + 5xx): 115
Error Rate (%): 0.00