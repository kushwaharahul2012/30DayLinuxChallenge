# Day 10: Backup & Disaster Recovery

## Learning Objectives

- Implement automated backup strategies
- Create disaster recovery plans
- Practice restore procedures
- Set up monitoring for backup systems
- Configure off-site backup solutions

## Prerequisites

- Days 1-9 completed
- Understanding of file systems
- Basic scripting knowledge

---

## TASK 1: Full System Backup Strategy

### Create Backup Structure:

```bash
mkdir -p ~/devops-learning/day10/{backups,scripts,logs}
cd ~/devops-learning/day10
```

### Implement Full System Backup:

```bash
cat > scripts/full_backup.sh << 'EOF'
#!/bin/bash
# Full System Backup Script

BACKUP_DIR="$HOME/devops-learning/day10/backups"
DATE=$(date +%Y%m%d_%H%M%S)
LOG="$HOME/devops-learning/day10/logs/backup_$DATE.log"

echo "Starting backup at $(date)" | tee -a $LOG

# System files
sudo tar -czf $BACKUP_DIR/system_$DATE.tar.gz \
  --exclude=/proc --exclude=/sys --exclude=/dev \
  --exclude=/tmp /etc /var/log 2>>$LOG

# User data
tar -czf $BACKUP_DIR/home_$DATE.tar.gz $HOME/devops-learning 2>>$LOG

# Database
sudo mysqldump --all-databases | gzip > $BACKUP_DIR/db_$DATE.sql.gz 2>>$LOG

echo "Backup completed at $(date)" | tee -a $LOG
echo "Total size: $(du -sh $BACKUP_DIR | cut -f1)" | tee -a $LOG
EOF

chmod +x scripts/full_backup.sh
./scripts/full_backup.sh
```

**Verify:** `ls -lh backups/`

---

## TASK 2: Incremental Backup System

### Setup Incremental Backups:

```bash
cat > scripts/incremental_backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="$HOME/devops-learning/day10/backups/incremental"
DATE=$(date +%Y%m%d)
SNAPSHOT_FILE="$BACKUP_DIR/.snapshot"

mkdir -p $BACKUP_DIR

# Incremental backup using rsync
rsync -av --delete --link-dest=$BACKUP_DIR/latest \
  $HOME/devops-learning/ \
  $BACKUP_DIR/backup_$DATE/

# Update latest symlink
ln -snf $BACKUP_DIR/backup_$DATE $BACKUP_DIR/latest

echo "Incremental backup completed: backup_$DATE"
EOF

chmod +x scripts/incremental_backup.sh
```

---

## TASK 3: Automated Restore Procedures

### Create Restore Script:

```bash
cat > scripts/restore.sh << 'EOF'
#!/bin/bash
echo "=== RESTORE WIZARD ==="
echo "1. Restore system files"
echo "2. Restore user data"
echo "3. Restore database"
echo "4. Full restore"

read -p "Choose (1-4): " choice

case $choice in
  1)
    read -p "Backup file: " file
    sudo tar -xzf backups/$file -C /
    ;;
  2)
    read -p "Backup file: " file
    tar -xzf backups/$file
    ;;
  3)
    read -p "SQL file: " file
    gunzip < backups/$file | sudo mysql
    ;;
  4)
    echo "Restoring all systems..."
    # Add full restore logic
    ;;
esac

echo "Restore completed"
EOF

chmod +x scripts/restore.sh
```

---

## TASK 4: Backup Validation & Testing

### Verify Backup Integrity:

```bash
cat > scripts/verify_backup.sh << 'EOF'
#!/bin/bash
echo "=== BACKUP VERIFICATION ==="

for backup in backups/*.tar.gz; do
  echo "Testing: $backup"
  if tar -tzf $backup > /dev/null 2>&1; then
    echo "  ✅ Valid"
  else
    echo "  ❌ Corrupted"
  fi
done

# Check backup sizes
echo ""
echo "Backup sizes:"
du -sh backups/*
EOF

chmod +x scripts/verify_backup.sh
./scripts/verify_backup.sh
```

---

## TASK 5: Off-Site Backup (Cloud/Remote)

### Setup Remote Backup:

```bash
cat > scripts/remote_backup.sh << 'EOF'
#!/bin/bash
# Remote/Cloud Backup

REMOTE_SERVER="user@remote-server.com"
BACKUP_DIR="$HOME/devops-learning/day10/backups"

# Sync to remote server
rsync -avz --delete \
  $BACKUP_DIR/ \
  $REMOTE_SERVER:/backup/$(hostname)/ \
  --log-file=logs/remote_backup.log

echo "Remote backup synced: $(date)"
EOF

chmod +x scripts/remote_backup.sh
```

---

## TASK 6: Disaster Recovery Plan

### Create DR Documentation:

```bash
cat > DR_PLAN.md << 'EOF'
# Disaster Recovery Plan

## Recovery Time Objective (RTO)
- Critical systems: 4 hours
- Non-critical systems: 24 hours

## Recovery Point Objective (RPO)
- Database: 1 hour (hourly backups)
- Files: 24 hours (daily backups)

## Backup Locations
1. Local: ~/devops-learning/day10/backups
2. Remote: backup-server:/backups
3. Cloud: (configure as needed)

## Recovery Procedures
1. Assess damage
2. Locate latest valid backup
3. Run restore.sh script
4. Verify system integrity
5. Resume operations

## Emergency Contacts
- DevOps Lead: [contact]
- System Admin: [contact]
EOF
```

---

## TASK 7: Monitoring & Alerting

### Backup Monitoring Script:

```bash
cat > scripts/monitor_backups.sh << 'EOF'
#!/bin/bash
echo "=== BACKUP MONITORING ==="

# Check last backup age
LAST_BACKUP=$(ls -t backups/*.tar.gz | head -1)
AGE=$(($(date +%s) - $(stat -c %Y $LAST_BACKUP)))
HOURS=$((AGE / 3600))

echo "Last backup: $HOURS hours ago"

if [ $HOURS -gt 25 ]; then
  echo "⚠️  WARNING: Backup overdue!"
  # Send alert (configure email/slack)
else
  echo "✅ Backup status: OK"
fi

# Check backup space
USAGE=$(df -h backups | tail -1 | awk '{print $5}' | sed 's/%//')
echo "Disk usage: ${USAGE}%"

if [ $USAGE -gt 80 ]; then
  echo "⚠️  WARNING: Low disk space!"
fi
EOF

chmod +x scripts/monitor_backups.sh
```

---

## TASK 8: Automated Backup Schedule

### Setup Cron Jobs:

```bash
# Add to crontab
(crontab -l 2>/dev/null; cat << 'CRON'
# Backup Schedule
0 2 * * * ~/devops-learning/day10/scripts/full_backup.sh
0 */6 * * * ~/devops-learning/day10/scripts/incremental_backup.sh
0 3 * * 0 ~/devops-learning/day10/scripts/remote_backup.sh
0 */4 * * * ~/devops-learning/day10/scripts/monitor_backups.sh
CRON
) | crontab -

echo "Backup schedule configured"
crontab -l
```

---

## TASK 9: Backup Rotation & Cleanup

### Implement Retention Policy:

```bash
cat > scripts/cleanup_old_backups.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="$HOME/devops-learning/day10/backups"

# Keep daily backups for 7 days
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete

# Keep weekly backups for 30 days
find $BACKUP_DIR/weekly -name "*.tar.gz" -mtime +30 -delete

# Keep monthly backups for 365 days
find $BACKUP_DIR/monthly -name "*.tar.gz" -mtime +365 -delete

echo "Cleanup completed"
du -sh $BACKUP_DIR
EOF

chmod +x scripts/cleanup_old_backups.sh
```

---

## Day 10 Final Challenge

### Complete Backup Infrastructure:

```bash
cat > scripts/complete_backup_system.sh << 'EOF'
#!/bin/bash
echo "=== COMPLETE BACKUP SYSTEM TEST ==="

# 1. Full backup
echo "1. Running full backup..."
./scripts/full_backup.sh

# 2. Verify backup
echo "2. Verifying backup..."
./scripts/verify_backup.sh

# 3. Test restore (to temp location)
echo "3. Testing restore..."
mkdir -p test_restore
tar -xzf backups/home_*.tar.gz -C test_restore/

# 4. Cleanup test
rm -rf test_restore/

# 5. Generate report
echo "4. Generating report..."
cat > backup_report.txt << REPORT
Backup System Test Report
Date: $(date)

Backup Status: PASSED
Files backed up: $(ls -1 backups/*.tar.gz | wc -l)
Total size: $(du -sh backups | cut -f1)
Last backup: $(ls -t backups/*.tar.gz | head -1)

All systems operational.
REPORT

cat backup_report.txt
echo "✅ Complete backup system test passed"
EOF

chmod +x scripts/complete_backup_system.sh
./scripts/complete_backup_system.sh
```

---

## Summary

### Completed:

- ✅ Full system backup automation
- ✅ Incremental backup strategy
- ✅ Restore procedures tested
- ✅ Backup validation implemented
- ✅ Remote/off-site backup configured
- ✅ Disaster recovery plan documented
- ✅ Monitoring and alerting setup
- ✅ Automated scheduling with cron
- ✅ Retention policies implemented

### Key Commands:

```bash
# Backup
tar -czf backup.tar.gz /path/to/data

# Restore
tar -xzf backup.tar.gz -C /restore/location

# Verify
tar -tzf backup.tar.gz

# Remote sync
rsync -avz source/ user@remote:/dest/

# Schedule
crontab -e
```

### Best Practices:

1. **3-2-1 Rule**: 3 copies, 2 different media, 1 off-site
2. **Test restores** regularly
3. **Monitor backup age** and size
4. **Automate everything** possible
5. **Document procedures** clearly
6. **Encrypt sensitive** backups
7. **Verify integrity** after backup

### Production Checklist:

- [ ] Automated daily backups
- [ ] Tested restore procedures
- [ ] Off-site backup configured
- [ ] Monitoring alerts active
- [ ] DR plan documented
- [ ] Retention policy implemented
- [ ] Backup verification automated
- [ ] Team trained on procedures

**Next (Day 11):** Performance tuning and optimization

---

**Time to complete:** 2-3 hours
**Difficulty:** Intermediate
**Key skill:** Disaster preparedness
