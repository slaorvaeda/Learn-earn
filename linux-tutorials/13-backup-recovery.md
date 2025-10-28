# 13 - Backup and Recovery

## Introduction to Backup and Recovery

Backup and recovery are critical processes for protecting data and ensuring business continuity. They involve creating copies of data and systems that can be restored in case of data loss, system failure, or disasters.

**Think of it as:**
- **Backup** = Creating copies of your important data
- **Recovery** = Restoring data from backups when needed
- **Disaster Recovery** = Recovering from major system failures
- **Business Continuity** = Keeping operations running during disruptions
- **Data Protection** = Safeguarding against data loss

## Backup Fundamentals

### Why Backup is Important

**Data Protection:**
- **Hardware Failures**: Disk crashes, server failures
- **Human Errors**: Accidental deletion, configuration mistakes
- **Software Issues**: Corrupted files, system crashes
- **Security Incidents**: Ransomware, data breaches
- **Natural Disasters**: Fires, floods, earthquakes

**Business Benefits:**
- **Minimize Downtime**: Quick recovery from failures
- **Data Integrity**: Ensure data consistency and accuracy
- **Compliance**: Meet regulatory requirements
- **Peace of Mind**: Confidence in data protection
- **Cost Savings**: Avoid expensive data recovery services

### Types of Backups

**Full Backup:**
- **Definition**: Complete copy of all data
- **Advantages**: Complete data protection, simple restoration
- **Disadvantages**: Time-consuming, requires large storage
- **Use Cases**: Initial backup, periodic complete backup

**Incremental Backup:**
- **Definition**: Only changed data since last backup
- **Advantages**: Fast, requires less storage
- **Disadvantages**: Complex restoration, dependency chain
- **Use Cases**: Daily backups, frequent changes

**Differential Backup:**
- **Definition**: Changed data since last full backup
- **Advantages**: Faster restoration than incremental
- **Disadvantages**: Requires more storage than incremental
- **Use Cases**: Weekly backups, moderate changes

**Snapshot Backup:**
- **Definition**: Point-in-time copy of data
- **Advantages**: Fast, consistent state
- **Disadvantages**: Requires snapshot-capable storage
- **Use Cases**: Database backups, virtual machines

## Backup Strategies

### 3-2-1 Backup Rule

**Three Copies:**
- **Original Data**: Primary working copy
- **Local Backup**: Local backup copy
- **Offsite Backup**: Remote backup copy

**Two Different Media:**
- **Different Storage Types**: Hard drives, tapes, cloud
- **Different Locations**: Local and remote
- **Different Technologies**: Different backup systems

**One Offsite:**
- **Geographic Separation**: Different physical location
- **Disaster Protection**: Protection from local disasters
- **Accessibility**: Available when local systems fail

### Backup Scheduling

**Daily Backups:**
- **Critical Data**: Important business data
- **User Files**: Documents, emails, personal data
- **Configuration Files**: System configurations
- **Database Changes**: Transaction logs, incremental data

**Weekly Backups:**
- **System Files**: Operating system files
- **Application Data**: Application configurations
- **Full System**: Complete system backup
- **Archive Data**: Long-term storage

**Monthly Backups:**
- **Complete System**: Full system backup
- **Archive Storage**: Long-term data retention
- **Compliance**: Regulatory requirements
- **Disaster Recovery**: Complete system recovery

## Backup Tools and Methods

### tar - Tape Archive

**Basic tar Usage:**
```bash
# Create tar archive
tar -cvf backup.tar /home/user/

# Create compressed tar archive
tar -czvf backup.tar.gz /home/user/

# Create bzip2 compressed archive
tar -cjvf backup.tar.bz2 /home/user/

# Extract tar archive
tar -xvf backup.tar

# Extract compressed archive
tar -xzvf backup.tar.gz

# List archive contents
tar -tvf backup.tar
```

**Advanced tar Options:**
```bash
# Exclude files and directories
tar -czvf backup.tar.gz --exclude='*.tmp' --exclude='cache' /home/user/

# Create incremental backup
tar -czvf backup_$(date +%Y%m%d).tar.gz --newer-mtime='1 day ago' /home/user/

# Create backup with verification
tar -czvf backup.tar.gz /home/user/ && tar -tzvf backup.tar.gz

# Create backup with progress
tar -czvf backup.tar.gz /home/user/ | pv -l > /dev/null
```

### rsync - Remote Sync

**Basic rsync Usage:**
```bash
# Sync directories
rsync -av /source/ /destination/

# Sync with remote server
rsync -av /local/ user@remote:/remote/

# Sync with compression
rsync -avz /source/ user@remote:/destination/

# Sync with progress
rsync -avz --progress /source/ /destination/

# Sync with deletion
rsync -avz --delete /source/ /destination/
```

**Advanced rsync Options:**
```bash
# Sync with exclusions
rsync -avz --exclude='*.tmp' --exclude='cache' /source/ /destination/

# Sync with bandwidth limit
rsync -avz --bwlimit=1000 /source/ /destination/

# Sync with checksums
rsync -avz --checksum /source/ /destination/

# Sync with dry run
rsync -avz --dry-run /source/ /destination/

# Sync with log file
rsync -avz --log-file=backup.log /source/ /destination/
```

### dd - Disk Dump

**Basic dd Usage:**
```bash
# Create disk image
sudo dd if=/dev/sda of=/backup/disk_image.img

# Create compressed disk image
sudo dd if=/dev/sda | gzip > /backup/disk_image.img.gz

# Restore disk image
sudo dd if=/backup/disk_image.img of=/dev/sda

# Create disk image with progress
sudo dd if=/dev/sda of=/backup/disk_image.img status=progress

# Create disk image with verification
sudo dd if=/dev/sda of=/backup/disk_image.img && sudo dd if=/backup/disk_image.img of=/dev/null
```

**Advanced dd Options:**
```bash
# Create disk image with specific size
sudo dd if=/dev/sda of=/backup/disk_image.img bs=1M count=1000

# Create disk image with specific block size
sudo dd if=/dev/sda of=/backup/disk_image.img bs=4M

# Create disk image with error handling
sudo dd if=/dev/sda of=/backup/disk_image.img conv=noerror,sync

# Create disk image with verification
sudo dd if=/dev/sda of=/backup/disk_image.img && sudo dd if=/backup/disk_image.img of=/dev/null
```

### Clonezilla - Disk Cloning

**Clonezilla Installation:**
```bash
# Download Clonezilla
wget https://clonezilla.org/downloads/stable/alternative/clonezilla-live-2.7.3-22-amd64.iso

# Create bootable USB
sudo dd if=clonezilla-live-2.7.3-22-amd64.iso of=/dev/sdb bs=4M status=progress
```

**Clonezilla Usage:**
- **Boot from Clonezilla**: Boot from USB or CD
- **Select Language**: Choose your language
- **Select Mode**: Choose device-to-device or device-to-image
- **Select Source**: Choose source disk or partition
- **Select Destination**: Choose destination disk or image file
- **Configure Options**: Set compression, verification, etc.
- **Start Cloning**: Begin the cloning process

## Database Backup

### MySQL Backup

**MySQL Dump:**
```bash
# Backup single database
mysqldump -u username -p database_name > backup.sql

# Backup all databases
mysqldump -u username -p --all-databases > all_databases.sql

# Backup with compression
mysqldump -u username -p database_name | gzip > backup.sql.gz

# Backup with specific options
mysqldump -u username -p --single-transaction --routines --triggers database_name > backup.sql

# Restore database
mysql -u username -p database_name < backup.sql
```

**MySQL Hot Backup:**
```bash
# Create hot backup
mysqlhotcopy -u username -p database_name /backup/mysql/

# Create hot backup with compression
mysqlhotcopy -u username -p --addtodest database_name /backup/mysql/
```

### PostgreSQL Backup

**PostgreSQL Dump:**
```bash
# Backup single database
pg_dump -U username database_name > backup.sql

# Backup all databases
pg_dumpall -U username > all_databases.sql

# Backup with compression
pg_dump -U username -Fc database_name > backup.dump

# Backup with specific options
pg_dump -U username -h localhost -p 5432 -W database_name > backup.sql

# Restore database
psql -U username database_name < backup.sql
```

**PostgreSQL Continuous Archiving:**
```bash
# Configure continuous archiving
echo "wal_level = replica" >> /etc/postgresql/13/main/postgresql.conf
echo "archive_mode = on" >> /etc/postgresql/13/main/postgresql.conf
echo "archive_command = 'cp %p /backup/postgresql/wal/%f'" >> /etc/postgresql/13/main/postgresql.conf

# Restart PostgreSQL
sudo systemctl restart postgresql
```

## Cloud Backup Solutions

### AWS S3 Backup

**AWS CLI Installation:**
```bash
# Install AWS CLI
sudo apt install awscli

# Configure AWS CLI
aws configure
```

**S3 Backup Script:**
```bash
#!/bin/bash
# AWS S3 Backup Script

BUCKET_NAME="my-backup-bucket"
BACKUP_DIR="/backup"
S3_PATH="s3://$BUCKET_NAME/backups/"

# Create backup
tar -czf $BACKUP_DIR/backup_$(date +%Y%m%d_%H%M%S).tar.gz /home/user/

# Upload to S3
aws s3 cp $BACKUP_DIR/backup_$(date +%Y%m%d_%H%M%S).tar.gz $S3_PATH

# List S3 backups
aws s3 ls $S3_PATH

# Download from S3
aws s3 cp $S3_PATH/backup_20231225_120000.tar.gz $BACKUP_DIR/
```

### Google Cloud Storage

**Google Cloud CLI Installation:**
```bash
# Install Google Cloud CLI
curl https://sdk.cloud.google.com | bash
exec -l $SHELL

# Initialize Google Cloud CLI
gcloud init
```

**GCS Backup Script:**
```bash
#!/bin/bash
# Google Cloud Storage Backup Script

BUCKET_NAME="my-backup-bucket"
BACKUP_DIR="/backup"
GCS_PATH="gs://$BUCKET_NAME/backups/"

# Create backup
tar -czf $BACKUP_DIR/backup_$(date +%Y%m%d_%H%M%S).tar.gz /home/user/

# Upload to GCS
gsutil cp $BACKUP_DIR/backup_$(date +%Y%m%d_%H%M%S).tar.gz $GCS_PATH

# List GCS backups
gsutil ls $GCS_PATH

# Download from GCS
gsutil cp $GCS_PATH/backup_20231225_120000.tar.gz $BACKUP_DIR/
```

## Backup Automation

### Cron Jobs

**Cron Configuration:**
```bash
# Edit crontab
crontab -e

# Daily backup at 2 AM
0 2 * * * /usr/local/bin/backup_script.sh

# Weekly backup on Sunday at 3 AM
0 3 * * 0 /usr/local/bin/weekly_backup.sh

# Monthly backup on 1st at 4 AM
0 4 1 * * /usr/local/bin/monthly_backup.sh
```

**Backup Script Example:**
```bash
#!/bin/bash
# Automated Backup Script

BACKUP_DIR="/backup"
SOURCE_DIR="/home/user"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$DATE.tar.gz"
LOG_FILE="/var/log/backup.log"

# Create backup directory
mkdir -p $BACKUP_DIR

# Create backup
tar -czf $BACKUP_DIR/$BACKUP_FILE $SOURCE_DIR

# Check if backup was successful
if [ $? -eq 0 ]; then
    echo "$(date): Backup created successfully: $BACKUP_FILE" >> $LOG_FILE
else
    echo "$(date): Backup failed!" >> $LOG_FILE
    exit 1
fi

# Remove old backups (keep last 7 days)
find $BACKUP_DIR -name "backup_*.tar.gz" -mtime +7 -delete

# Upload to cloud storage
aws s3 cp $BACKUP_DIR/$BACKUP_FILE s3://my-backup-bucket/backups/
```

### Systemd Timers

**Systemd Timer Configuration:**
```bash
# Create backup service
sudo nano /etc/systemd/system/backup.service

[Unit]
Description=Daily Backup Service
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup_script.sh
User=backup
Group=backup

# Create backup timer
sudo nano /etc/systemd/system/backup.timer

[Unit]
Description=Daily Backup Timer
Requires=backup.service

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target

# Enable and start timer
sudo systemctl enable backup.timer
sudo systemctl start backup.timer
```

## Recovery Procedures

### File Recovery

**Basic File Recovery:**
```bash
# Restore from tar backup
tar -xvf backup.tar

# Restore from compressed backup
tar -xzvf backup.tar.gz

# Restore specific files
tar -xvf backup.tar path/to/file

# Restore with verification
tar -xzvf backup.tar.gz && tar -tzvf backup.tar.gz
```

**Advanced File Recovery:**
```bash
# Restore with rsync
rsync -av /backup/source/ /destination/

# Restore with progress
rsync -avz --progress /backup/source/ /destination/

# Restore with verification
rsync -avz --checksum /backup/source/ /destination/
```

### System Recovery

**Full System Recovery:**
```bash
# Boot from recovery media
# Mount root filesystem
mount /dev/sda1 /mnt

# Restore system files
tar -xzvf /backup/system_backup.tar.gz -C /mnt

# Restore bootloader
chroot /mnt
grub-install /dev/sda
update-grub
exit

# Reboot system
reboot
```

**Database Recovery:**
```bash
# MySQL recovery
mysql -u username -p database_name < backup.sql

# PostgreSQL recovery
psql -U username database_name < backup.sql

# PostgreSQL recovery from dump
pg_restore -U username -d database_name backup.dump
```

## Disaster Recovery

### Disaster Recovery Planning

**Recovery Time Objectives (RTO):**
- **Critical Systems**: 1-4 hours
- **Important Systems**: 4-24 hours
- **Standard Systems**: 24-72 hours
- **Non-Critical Systems**: 72+ hours

**Recovery Point Objectives (RPO):**
- **Critical Data**: 0-1 hour
- **Important Data**: 1-24 hours
- **Standard Data**: 24-72 hours
- **Archive Data**: 72+ hours

### Disaster Recovery Procedures

**System Recovery:**
1. **Assess Damage**: Determine extent of damage
2. **Activate DR Site**: Switch to disaster recovery site
3. **Restore Systems**: Restore from backups
4. **Verify Functionality**: Test restored systems
5. **Switch Back**: Return to primary site

**Data Recovery:**
1. **Identify Data Loss**: Determine what data was lost
2. **Select Recovery Point**: Choose appropriate backup
3. **Restore Data**: Restore from selected backup
4. **Verify Integrity**: Check data integrity
5. **Update Systems**: Update affected systems

## Practice Exercises

### Exercise 1: Basic Backup Creation
**Objective**: Practice creating basic backups

**Tasks**:
1. Create a full backup of user data
2. Create an incremental backup
3. Compress the backup
4. Verify the backup integrity
5. Test restoration

**Commands to Practice**:
```bash
# Create full backup
tar -czvf full_backup_$(date +%Y%m%d).tar.gz /home/user/

# Create incremental backup
tar -czvf incremental_backup_$(date +%Y%m%d).tar.gz --newer-mtime='1 day ago' /home/user/

# Verify backup
tar -tzvf full_backup_$(date +%Y%m%d).tar.gz

# Test restoration
mkdir test_restore
tar -xzvf full_backup_$(date +%Y%m%d).tar.gz -C test_restore
```

### Exercise 2: Automated Backup Script
**Objective**: Create an automated backup script

**Tasks**:
1. Create a backup script
2. Add compression and verification
3. Set up log rotation
4. Configure email notifications
5. Test the script

**Script Example**:
```bash
#!/bin/bash
# Automated Backup Script

BACKUP_DIR="/backup"
SOURCE_DIR="/home/user"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$DATE.tar.gz"
LOG_FILE="/var/log/backup.log"
EMAIL="admin@example.com"

# Create backup directory
mkdir -p $BACKUP_DIR

# Create backup
tar -czf $BACKUP_DIR/$BACKUP_FILE $SOURCE_DIR

# Check if backup was successful
if [ $? -eq 0 ]; then
    echo "$(date): Backup created successfully: $BACKUP_FILE" >> $LOG_FILE
    echo "Backup created successfully: $BACKUP_FILE" | mail -s "Backup Success" $EMAIL
else
    echo "$(date): Backup failed!" >> $LOG_FILE
    echo "Backup failed!" | mail -s "Backup Failed" $EMAIL
    exit 1
fi

# Remove old backups (keep last 7 days)
find $BACKUP_DIR -name "backup_*.tar.gz" -mtime +7 -delete
```

### Exercise 3: Database Backup
**Objective**: Practice database backup and recovery

**Tasks**:
1. Create MySQL database backup
2. Create PostgreSQL database backup
3. Test database restoration
4. Set up automated database backups
5. Verify backup integrity

**Commands to Practice**:
```bash
# MySQL backup
mysqldump -u root -p --all-databases > mysql_backup_$(date +%Y%m%d).sql

# PostgreSQL backup
pg_dumpall -U postgres > postgresql_backup_$(date +%Y%m%d).sql

# Test MySQL restoration
mysql -u root -p < mysql_backup_$(date +%Y%m%d).sql

# Test PostgreSQL restoration
psql -U postgres < postgresql_backup_$(date +%Y%m%d).sql
```

### Exercise 4: Cloud Backup Setup
**Objective**: Set up cloud backup solution

**Tasks**:
1. Configure AWS S3 or Google Cloud Storage
2. Create cloud backup script
3. Set up automated cloud backups
4. Test cloud backup and restoration
5. Monitor cloud backup costs

**Script Example**:
```bash
#!/bin/bash
# Cloud Backup Script

BUCKET_NAME="my-backup-bucket"
BACKUP_DIR="/backup"
S3_PATH="s3://$BUCKET_NAME/backups/"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$DATE.tar.gz"

# Create backup
tar -czf $BACKUP_DIR/$BACKUP_FILE /home/user/

# Upload to S3
aws s3 cp $BACKUP_DIR/$BACKUP_FILE $S3_PATH

# Verify upload
aws s3 ls $S3_PATH | grep $BACKUP_FILE

# Clean up local backup
rm $BACKUP_DIR/$BACKUP_FILE
```

## Important Reminders

⚠️ **Remember these key points about backup and recovery:**

- **Test Regularly**: Always test backup and recovery procedures
- **Multiple Copies**: Follow the 3-2-1 backup rule
- **Document Procedures**: Keep detailed recovery procedures
- **Monitor Backups**: Regularly check backup status and integrity
- **Update Plans**: Keep backup and recovery plans current
- **Train Staff**: Ensure team members know recovery procedures
- **Review and Improve**: Continuously improve backup strategies

**Effective backup and recovery are essential for data protection and business continuity. Proper backup strategies help minimize downtime and ensure data availability when needed.** 🐧✨
