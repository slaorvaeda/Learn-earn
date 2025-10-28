# 12 - Log Management

## Introduction to Log Management

Log management is the process of collecting, storing, analyzing, and monitoring log files to maintain system security, troubleshoot issues, and ensure compliance. Effective log management is crucial for system administration and security.

**Think of it as:**
- **Logs** = Records of what happened on your system
- **Log Management** = Organizing and analyzing these records
- **Log Analysis** = Finding patterns and problems in the records
- **Log Rotation** = Keeping logs manageable by archiving old ones
- **Log Monitoring** = Watching logs in real-time for issues

## Understanding Logs

### What are Logs?

Logs are chronological records of events that occur on a system. They provide:
- **Audit Trail**: Record of system activities
- **Debugging Information**: Help troubleshoot problems
- **Security Evidence**: Track security events
- **Performance Data**: Monitor system performance
- **Compliance Records**: Meet regulatory requirements

### Types of Logs

**System Logs:**
- **Kernel Logs**: System kernel messages
- **Boot Logs**: System startup messages
- **Hardware Logs**: Hardware-related events
- **Service Logs**: System service messages

**Application Logs:**
- **Web Server Logs**: HTTP requests and responses
- **Database Logs**: Database operations and errors
- **Mail Server Logs**: Email sending and receiving
- **Custom Application Logs**: Application-specific events

**Security Logs:**
- **Authentication Logs**: Login attempts and failures
- **Authorization Logs**: Permission changes and access
- **Audit Logs**: Security-related events
- **Firewall Logs**: Network security events

**Network Logs:**
- **Connection Logs**: Network connections
- **Traffic Logs**: Network traffic patterns
- **DNS Logs**: Domain name resolution
- **VPN Logs**: Virtual private network events

## Linux Logging System

### Systemd Journal

**Journal Overview:**
- **Centralized Logging**: All system logs in one place
- **Structured Logging**: Logs with metadata and fields
- **Real-time Access**: Live log monitoring
- **Persistent Storage**: Logs survive reboots
- **Search and Filter**: Powerful query capabilities

**Journal Commands:**
```bash
# View all logs
journalctl

# View logs in real-time
journalctl -f

# View logs for specific service
journalctl -u apache2

# View logs with specific priority
journalctl -p err

# View logs for specific time range
journalctl --since "2023-12-25 10:00:00" --until "2023-12-25 11:00:00"

# View logs for specific user
journalctl _UID=1000

# View logs with specific field
journalctl _COMM=sshd

# View logs in reverse order
journalctl -r

# View logs with no pager
journalctl --no-pager
```

**Journal Configuration:**
```bash
# Edit journal configuration
sudo nano /etc/systemd/journald.conf

# Set log retention
SystemMaxUse=1G
SystemMaxFileSize=100M
SystemMaxFiles=10

# Set log compression
Compress=yes

# Set log forwarding
ForwardToSyslog=yes
```

### Traditional Syslog

**Syslog Configuration:**
```bash
# Edit syslog configuration
sudo nano /etc/rsyslog.conf

# Set log facilities and priorities
*.info;mail.none;authpriv.none;cron.none    /var/log/messages
authpriv.*                                    /var/log/secure
mail.*                                        -/var/log/maillog
cron.*                                        /var/log/cron
*.emerg                                       *
```

**Syslog Facilities:**
- **kern**: Kernel messages
- **user**: User-level messages
- **mail**: Mail system messages
- **daemon**: System daemon messages
- **auth**: Security/authorization messages
- **syslog**: Syslog internal messages
- **lpr**: Line printer subsystem
- **news**: Network news subsystem
- **uucp**: UUCP subsystem
- **cron**: Cron daemon messages
- **authpriv**: Security/authorization messages
- **ftp**: FTP daemon messages
- **local0-7**: Locally used facilities

**Syslog Priorities:**
- **emerg**: Emergency (0)
- **alert**: Action must be taken immediately (1)
- **crit**: Critical conditions (2)
- **err**: Error conditions (3)
- **warning**: Warning conditions (4)
- **notice**: Normal but significant conditions (5)
- **info**: Informational messages (6)
- **debug**: Debug-level messages (7)

## Log Locations

### System Logs

**Common Log Locations:**
```bash
# System logs
/var/log/syslog          # General system messages
/var/log/messages        # System messages (RHEL/CentOS)
/var/log/kern.log        # Kernel messages
/var/log/auth.log        # Authentication messages
/var/log/secure          # Security messages (RHEL/CentOS)
/var/log/cron.log        # Cron job messages
/var/log/daemon.log      # Daemon messages
/var/log/mail.log        # Mail system messages
/var/log/debug           # Debug messages
/var/log/wtmp            # Login records
/var/log/btmp            # Failed login records
/var/log/lastlog         # Last login records
```

**Application Logs:**
```bash
# Web server logs
/var/log/apache2/        # Apache logs
/var/log/nginx/          # Nginx logs
/var/log/httpd/           # Apache logs (RHEL/CentOS)

# Database logs
/var/log/mysql/          # MySQL logs
/var/log/postgresql/     # PostgreSQL logs
/var/log/mongodb/        # MongoDB logs

# Mail server logs
/var/log/mail.log        # Mail system logs
/var/log/postfix/        # Postfix logs
/var/log/dovecot/        # Dovecot logs

# System service logs
/var/log/cups/           # CUPS printing logs
/var/log/lightdm/        # LightDM display manager
/var/log/gdm/            # GDM display manager
```

### Log File Permissions

**Setting Log Permissions:**
```bash
# Set log file permissions
sudo chmod 640 /var/log/syslog
sudo chown root:adm /var/log/syslog

# Set log directory permissions
sudo chmod 755 /var/log
sudo chown root:root /var/log

# Set log rotation permissions
sudo chmod 644 /var/log/syslog.*
sudo chown root:root /var/log/syslog.*
```

## Log Rotation

### Logrotate Configuration

**Logrotate Overview:**
- **Automatic Rotation**: Rotate logs based on size, time, or both
- **Compression**: Compress old log files
- **Retention**: Keep specified number of old logs
- **Post-rotation Actions**: Execute commands after rotation

**Logrotate Configuration:**
```bash
# Edit logrotate configuration
sudo nano /etc/logrotate.conf

# Global settings
weekly
rotate 4
create
dateext
compress
delaycompress
missingok
notifempty
include /etc/logrotate.d
```

**Logrotate Rules:**
```bash
# Create custom logrotate rule
sudo nano /etc/logrotate.d/custom

# Example rule
/var/log/custom.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 644 root root
    postrotate
        /bin/kill -HUP `cat /var/run/custom.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```

**Logrotate Commands:**
```bash
# Test logrotate configuration
sudo logrotate -d /etc/logrotate.conf

# Force logrotate
sudo logrotate -f /etc/logrotate.conf

# Test specific logrotate rule
sudo logrotate -d /etc/logrotate.d/apache2

# Force specific logrotate rule
sudo logrotate -f /etc/logrotate.d/apache2
```

### Manual Log Rotation

**Manual Rotation Script:**
```bash
#!/bin/bash
# Manual Log Rotation Script

LOG_FILE="/var/log/custom.log"
BACKUP_DIR="/var/log/backups"
MAX_FILES=7

# Create backup directory
mkdir -p $BACKUP_DIR

# Rotate log file
if [ -f $LOG_FILE ]; then
    # Create timestamp
    TIMESTAMP=$(date +%Y%m%d_%H%M%S)
    
    # Move current log to backup
    mv $LOG_FILE $BACKUP_DIR/custom_$TIMESTAMP.log
    
    # Compress backup
    gzip $BACKUP_DIR/custom_$TIMESTAMP.log
    
    # Remove old backups
    find $BACKUP_DIR -name "custom_*.log.gz" -mtime +$MAX_FILES -delete
    
    # Create new log file
    touch $LOG_FILE
    chmod 644 $LOG_FILE
    chown root:root $LOG_FILE
    
    # Send HUP signal to application
    kill -HUP $(cat /var/run/custom.pid)
fi
```

## Log Analysis

### Basic Log Analysis

**Text Processing Tools:**
```bash
# View log files
cat /var/log/syslog
less /var/log/syslog
tail -f /var/log/syslog

# Search logs
grep "error" /var/log/syslog
grep -i "failed" /var/log/auth.log
grep -E "(error|warning|critical)" /var/log/syslog

# Count log entries
grep -c "error" /var/log/syslog
grep -c "failed" /var/log/auth.log

# Show unique log entries
grep "error" /var/log/syslog | sort | uniq

# Show log entries with context
grep -A 5 -B 5 "error" /var/log/syslog
```

**Advanced Log Analysis:**
```bash
# Show log entries by date
grep "Dec 25" /var/log/syslog

# Show log entries by time
grep "10:30" /var/log/syslog

# Show log entries by user
grep "user john" /var/log/auth.log

# Show log entries by process
grep "apache2" /var/log/syslog

# Show log entries by IP address
grep "192.168.1.100" /var/log/auth.log
```

### Log Analysis Tools

**awk for Log Analysis:**
```bash
# Extract specific fields
awk '{print $1, $2, $3}' /var/log/syslog

# Count log entries by field
awk '{print $5}' /var/log/syslog | sort | uniq -c

# Filter log entries
awk '/error/ {print $0}' /var/log/syslog

# Calculate log statistics
awk '{count++} END {print "Total entries:", count}' /var/log/syslog
```

**sed for Log Processing:**
```bash
# Replace text in logs
sed 's/error/ERROR/g' /var/log/syslog

# Extract specific lines
sed -n '10,20p' /var/log/syslog

# Delete specific lines
sed '/error/d' /var/log/syslog

# Extract specific patterns
sed -n '/error/p' /var/log/syslog
```

**grep for Log Searching:**
```bash
# Search with multiple patterns
grep -E "(error|warning|critical)" /var/log/syslog

# Search with case insensitive
grep -i "error" /var/log/syslog

# Search with line numbers
grep -n "error" /var/log/syslog

# Search with context
grep -C 3 "error" /var/log/syslog

# Search with inverted match
grep -v "info" /var/log/syslog
```

## Log Monitoring

### Real-time Log Monitoring

**Basic Monitoring:**
```bash
# Monitor logs in real-time
tail -f /var/log/syslog

# Monitor multiple logs
tail -f /var/log/syslog /var/log/auth.log

# Monitor logs with timestamps
tail -f /var/log/syslog | while read line; do
    echo "$(date): $line"
done
```

**Advanced Monitoring:**
```bash
# Monitor logs with filtering
tail -f /var/log/syslog | grep -i error

# Monitor logs with alerting
tail -f /var/log/syslog | while read line; do
    if echo "$line" | grep -q "error"; then
        echo "ALERT: $line" | mail -s "Log Alert" admin@example.com
    fi
done
```

### Log Monitoring Tools

**multitail - Multiple Log Monitoring:**
```bash
# Install multitail
sudo apt install multitail

# Monitor multiple logs
multitail /var/log/syslog /var/log/auth.log

# Monitor logs with colors
multitail -c /var/log/syslog -c /var/log/auth.log

# Monitor logs with filters
multitail -f /var/log/syslog -f /var/log/auth.log
```

**logwatch - Log Analysis and Reporting:**
```bash
# Install logwatch
sudo apt install logwatch

# Run logwatch
sudo logwatch

# Run logwatch with specific options
sudo logwatch --range yesterday --detail high

# Configure logwatch
sudo nano /etc/logwatch/conf/logwatch.conf
```

**logcheck - Log Monitoring and Alerting:**
```bash
# Install logcheck
sudo apt install logcheck

# Configure logcheck
sudo nano /etc/logcheck/logcheck.conf

# Run logcheck
sudo logcheck

# Set up logcheck cron job
echo "0 6 * * * /usr/sbin/logcheck" | sudo crontab -
```

## Log Security

### Log Protection

**Securing Log Files:**
```bash
# Set proper permissions
sudo chmod 640 /var/log/syslog
sudo chown root:adm /var/log/syslog

# Set immutable flag
sudo chattr +i /var/log/syslog

# Remove immutable flag
sudo chattr -i /var/log/syslog

# Set append-only flag
sudo chattr +a /var/log/syslog

# Remove append-only flag
sudo chattr -a /var/log/syslog
```

**Log Integrity:**
```bash
# Create log checksums
md5sum /var/log/syslog > /var/log/syslog.md5

# Verify log integrity
md5sum -c /var/log/syslog.md5

# Create log signatures
gpg --sign /var/log/syslog

# Verify log signatures
gpg --verify /var/log/syslog.gpg
```

### Log Forensics

**Log Analysis for Security:**
```bash
# Check for failed login attempts
grep "Failed password" /var/log/auth.log

# Check for successful logins
grep "Accepted password" /var/log/auth.log

# Check for sudo usage
grep "sudo:" /var/log/auth.log

# Check for privilege escalation
grep "su:" /var/log/auth.log

# Check for suspicious activities
grep -E "(root|admin|password)" /var/log/auth.log
```

**Log Correlation:**
```bash
# Correlate events by time
grep "Dec 25 10:30" /var/log/syslog

# Correlate events by user
grep "user john" /var/log/auth.log

# Correlate events by IP
grep "192.168.1.100" /var/log/auth.log

# Correlate events by process
grep "apache2" /var/log/syslog
```

## Log Management Best Practices

### Log Collection

**Centralized Logging:**
- **Collect All Logs**: Gather logs from all systems
- **Standardize Format**: Use consistent log formats
- **Timestamp Everything**: Ensure all logs have timestamps
- **Include Metadata**: Add relevant context information

**Log Storage:**
- **Adequate Space**: Ensure sufficient storage capacity
- **Fast Access**: Use fast storage for active logs
- **Backup Strategy**: Implement log backup procedures
- **Retention Policy**: Define log retention periods

### Log Analysis

**Regular Analysis:**
- **Daily Review**: Check logs daily for issues
- **Trend Analysis**: Identify patterns and trends
- **Anomaly Detection**: Look for unusual activities
- **Performance Monitoring**: Track system performance

**Automated Analysis:**
- **Log Parsing**: Automate log parsing and analysis
- **Alert Generation**: Set up automated alerts
- **Report Generation**: Create automated reports
- **Dashboard Creation**: Build monitoring dashboards

## Practice Exercises

### Exercise 1: Basic Log Analysis
**Objective**: Practice basic log analysis techniques

**Tasks**:
1. View system logs
2. Search for specific patterns
3. Count log entries
4. Extract specific information
5. Create log analysis report

**Commands to Practice**:
```bash
# View system logs
cat /var/log/syslog
tail -f /var/log/syslog

# Search for errors
grep -i error /var/log/syslog
grep -i "failed" /var/log/auth.log

# Count log entries
grep -c "error" /var/log/syslog
grep -c "failed" /var/log/auth.log

# Extract specific information
awk '{print $1, $2, $3}' /var/log/syslog
grep "Dec 25" /var/log/syslog
```

### Exercise 2: Log Rotation Setup
**Objective**: Practice setting up log rotation

**Tasks**:
1. Configure logrotate
2. Create custom logrotate rules
3. Test logrotate configuration
4. Monitor log rotation
5. Verify log retention

**Commands to Practice**:
```bash
# Edit logrotate configuration
sudo nano /etc/logrotate.conf

# Create custom rule
sudo nano /etc/logrotate.d/custom

# Test configuration
sudo logrotate -d /etc/logrotate.conf

# Force rotation
sudo logrotate -f /etc/logrotate.conf

# Check rotated logs
ls -la /var/log/syslog*
```

### Exercise 3: Log Monitoring Script
**Objective**: Create a log monitoring script

**Tasks**:
1. Create script to monitor logs
2. Add filtering and alerting
3. Set up log analysis
4. Test the script
5. Document the monitoring process

**Script Example**:
```bash
#!/bin/bash
# Log Monitoring Script

LOG_FILE="/var/log/syslog"
ALERT_EMAIL="admin@example.com"
ERROR_PATTERNS=("error" "failed" "critical" "fatal")

# Function to send alerts
send_alert() {
    local message="$1"
    echo "$message" | mail -s "Log Alert" $ALERT_EMAIL
}

# Function to monitor logs
monitor_logs() {
    tail -f $LOG_FILE | while read line; do
        for pattern in "${ERROR_PATTERNS[@]}"; do
            if echo "$line" | grep -qi "$pattern"; then
                echo "ALERT: $line"
                send_alert "Log Alert: $line"
            fi
        done
    done
}

# Start monitoring
monitor_logs
```

### Exercise 4: Log Security Analysis
**Objective**: Practice log security analysis

**Tasks**:
1. Analyze authentication logs
2. Check for security events
3. Identify suspicious activities
4. Create security report
5. Implement log protection

**Commands to Practice**:
```bash
# Check failed logins
grep "Failed password" /var/log/auth.log

# Check successful logins
grep "Accepted password" /var/log/auth.log

# Check sudo usage
grep "sudo:" /var/log/auth.log

# Check for suspicious activities
grep -E "(root|admin|password)" /var/log/auth.log

# Set log permissions
sudo chmod 640 /var/log/syslog
sudo chown root:adm /var/log/syslog
```

## Important Reminders

⚠️ **Remember these key points about log management:**

- **Regular Monitoring**: Check logs regularly for issues and security events
- **Proper Rotation**: Implement log rotation to manage disk space
- **Secure Storage**: Protect log files from unauthorized access
- **Backup Strategy**: Implement log backup and retention policies
- **Analysis Tools**: Use appropriate tools for log analysis
- **Documentation**: Document log management procedures
- **Compliance**: Ensure log management meets regulatory requirements

**Effective log management is essential for system security, troubleshooting, and compliance. Proper log management helps maintain system health and provides valuable insights into system behavior.** 🐧✨
