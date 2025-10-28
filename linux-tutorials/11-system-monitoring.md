# 11 - System Monitoring

## Introduction to System Monitoring

System monitoring is the practice of observing and measuring system performance, resource usage, and health metrics to ensure optimal operation and prevent issues before they become critical.

**Think of it as:**
- **Monitoring** = Keeping watch over your system's health
- **Metrics** = Measurements that tell you how your system is performing
- **Alerts** = Notifications when something goes wrong
- **Dashboards** = Visual displays of system information
- **Logs** = Records of what happened on your system

## Monitoring Fundamentals

### Why Monitor Systems?

**Proactive Management:**
- **Prevent Issues**: Identify problems before they cause downtime
- **Performance Optimization**: Find bottlenecks and optimize resources
- **Capacity Planning**: Plan for future growth and resource needs
- **Security**: Detect suspicious activities and potential threats

**Business Benefits:**
- **Reduced Downtime**: Minimize service interruptions
- **Better Performance**: Ensure optimal user experience
- **Cost Savings**: Optimize resource usage and reduce waste
- **Compliance**: Meet regulatory and audit requirements

### Monitoring Categories

**System Resources:**
- **CPU Usage**: Processor utilization and load
- **Memory Usage**: RAM consumption and availability
- **Disk Usage**: Storage space and I/O performance
- **Network Usage**: Bandwidth and connection metrics

**Application Performance:**
- **Response Times**: How fast applications respond
- **Throughput**: Number of requests processed
- **Error Rates**: Frequency of application errors
- **Availability**: Uptime and service availability

**Infrastructure Health:**
- **Service Status**: Whether services are running
- **Log Analysis**: Error patterns and trends
- **Security Events**: Failed logins, suspicious activities
- **Hardware Health**: Temperature, fan speed, power usage

## System Resource Monitoring

### CPU Monitoring

**CPU Metrics:**
```bash
# Show CPU information
cat /proc/cpuinfo

# Show CPU usage
top
htop

# Show CPU usage with specific format
ps -eo pid,cmd,%cpu --sort=-%cpu | head -10

# Show CPU load average
uptime
cat /proc/loadavg

# Show CPU statistics
cat /proc/stat
```

**CPU Load Average:**
```bash
# Load average interpretation
# 1.0 = 100% CPU utilization
# < 1.0 = System has spare capacity
# > 1.0 = System is overloaded
# 3 values = 1, 5, and 15 minute averages

# Check load average
uptime
w
cat /proc/loadavg
```

**CPU Performance Tools:**
```bash
# Install monitoring tools
sudo apt install htop iotop nethogs

# Real-time CPU monitoring
htop

# CPU usage by process
ps aux --sort=-%cpu | head -10

# CPU usage over time
sar -u 1 10  # 10 samples, 1 second apart
```

### Memory Monitoring

**Memory Metrics:**
```bash
# Show memory usage
free -h

# Show memory usage in MB
free -m

# Show memory usage in GB
free -g

# Show memory usage continuously
watch -n 1 free -h

# Show detailed memory information
cat /proc/meminfo
```

**Memory Types:**
- **Total**: Total physical memory
- **Used**: Memory currently in use
- **Free**: Memory not in use
- **Available**: Memory available for new processes
- **Shared**: Memory shared between processes
- **Buffers**: Memory used for buffers
- **Cached**: Memory used for cache

**Memory Analysis:**
```bash
# Show memory usage by process
ps aux --sort=-%mem | head -10

# Show memory usage by user
ps -eo user,pid,cmd,%mem --sort=-%mem | head -10

# Show memory usage by command
ps -eo cmd,%mem --sort=-%mem | head -10

# Show memory usage over time
sar -r 1 10  # 10 samples, 1 second apart
```

### Disk Monitoring

**Disk Usage:**
```bash
# Show disk usage
df -h

# Show disk usage for specific filesystem
df -h /

# Show disk usage in human-readable format
du -h

# Show disk usage summary
du -sh

# Show disk usage with depth limit
du -h --max-depth=2

# Show disk usage sorted by size
du -h | sort -hr
```

**Disk I/O Monitoring:**
```bash
# Show disk I/O statistics
iostat -x 1

# Show disk I/O by process
iotop

# Show disk I/O statistics
cat /proc/diskstats

# Show disk I/O over time
sar -d 1 10  # 10 samples, 1 second apart
```

**Disk Health:**
```bash
# Check disk health (for SATA drives)
sudo smartctl -a /dev/sda

# Check disk health for all drives
sudo smartctl --scan

# Check disk errors
dmesg | grep -i error

# Check disk space alerts
df -h | awk '$5 > 80 {print $0}'
```

### Network Monitoring

**Network Usage:**
```bash
# Show network interfaces
ip addr show

# Show network statistics
cat /proc/net/dev

# Show network connections
netstat -i

# Show network usage by interface
iftop

# Show network usage by process
nethogs
```

**Network Performance:**
```bash
# Test network connectivity
ping google.com

# Test network speed
speedtest-cli

# Test network latency
traceroute google.com

# Monitor network traffic
tcpdump -i eth0

# Monitor network connections
ss -tuln
```

## Process Monitoring

### Process Information

**Process Status:**
```bash
# Show running processes
ps aux

# Show processes in tree format
ps auxf

# Show processes for specific user
ps -u username

# Show processes with specific command
ps aux | grep firefox

# Show process hierarchy
pstree
```

**Process Resources:**
```bash
# Show process resource usage
top

# Show process resource usage with custom format
ps -eo pid,cmd,%cpu,%mem --sort=-%cpu | head -10

# Show process memory usage
ps -eo pid,cmd,rss,vsz --sort=-rss | head -10

# Show process file usage
lsof | head -10
```

### Process Management

**Process Control:**
```bash
# Kill process by PID
kill 1234

# Kill process by name
killall firefox

# Kill process with specific signal
kill -9 1234

# Change process priority
renice 10 1234

# Suspend process
kill -STOP 1234

# Resume process
kill -CONT 1234
```

**Process Monitoring:**
```bash
# Monitor process in real-time
top -p 1234

# Monitor process with custom interval
top -d 1 -p 1234

# Monitor process with specific user
top -u username

# Monitor process with specific command
top -c
```

## Log Monitoring

### System Logs

**Log Locations:**
```bash
# System logs
/var/log/syslog

# Kernel logs
/var/log/kern.log

# Authentication logs
/var/log/auth.log

# Application logs
/var/log/apache2/
/var/log/nginx/
/var/log/mysql/

# Systemd logs
journalctl
```

**Log Analysis:**
```bash
# View recent logs
tail -f /var/log/syslog

# View logs with timestamps
journalctl -f

# View logs for specific service
journalctl -u apache2

# View logs with specific priority
journalctl -p err

# View logs for specific time range
journalctl --since "2023-12-25 10:00:00" --until "2023-12-25 11:00:00"
```

**Log Monitoring Tools:**
```bash
# Install log monitoring tools
sudo apt install logwatch logcheck

# Monitor logs for errors
grep -i error /var/log/syslog

# Monitor logs for specific pattern
grep -i "failed" /var/log/auth.log

# Monitor logs with multiple patterns
grep -E "(error|warning|critical)" /var/log/syslog
```

### Application Logs

**Web Server Logs:**
```bash
# Apache access logs
tail -f /var/log/apache2/access.log

# Apache error logs
tail -f /var/log/apache2/error.log

# Nginx access logs
tail -f /var/log/nginx/access.log

# Nginx error logs
tail -f /var/log/nginx/error.log
```

**Database Logs:**
```bash
# MySQL logs
tail -f /var/log/mysql/error.log

# PostgreSQL logs
tail -f /var/log/postgresql/postgresql-*.log

# MongoDB logs
tail -f /var/log/mongodb/mongod.log
```

## Performance Monitoring Tools

### System Monitoring Tools

**htop - Enhanced Process Monitor:**
```bash
# Install htop
sudo apt install htop

# Run htop
htop

# htop features:
# - Color-coded process information
# - Interactive process management
# - Real-time system metrics
# - Tree view of processes
# - Search and filter capabilities
```

**iotop - I/O Monitor:**
```bash
# Install iotop
sudo apt install iotop

# Run iotop
sudo iotop

# iotop features:
# - Real-time I/O usage by process
# - Read and write statistics
# - I/O priority information
# - Interactive process management
```

**nethogs - Network Monitor:**
```bash
# Install nethogs
sudo apt install nethogs

# Run nethogs
sudo nethogs

# nethogs features:
# - Real-time network usage by process
# - Bandwidth monitoring
# - Process identification
# - Interactive interface
```

### System Information Tools

**lscpu - CPU Information:**
```bash
# Show CPU information
lscpu

# Show CPU information in specific format
lscpu -e

# Show CPU information with specific fields
lscpu -p
```

**lsmem - Memory Information:**
```bash
# Show memory information
lsmem

# Show memory information in specific format
lsmem -o RANGE,SIZE,STATE
```

**lsblk - Block Device Information:**
```bash
# Show block device information
lsblk

# Show block device information with specific format
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT
```

## Monitoring Scripts

### Basic Monitoring Script

```bash
#!/bin/bash
# Basic System Monitoring Script

echo "=== System Monitoring Report ==="
echo "Date: $(date)"
echo "Hostname: $(hostname)"
echo "Uptime: $(uptime -p)"
echo ""

echo "=== CPU Information ==="
echo "Load Average: $(cat /proc/loadavg)"
echo "CPU Usage: $(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | awk -F'%' '{print $1}')"
echo ""

echo "=== Memory Information ==="
free -h
echo ""

echo "=== Disk Usage ==="
df -h
echo ""

echo "=== Top Processes by CPU ==="
ps aux --sort=-%cpu | head -5
echo ""

echo "=== Top Processes by Memory ==="
ps aux --sort=-%mem | head -5
echo ""

echo "=== Network Interfaces ==="
ip addr show | grep -E "(inet |UP|DOWN)"
echo ""

echo "=== Recent Log Entries ==="
tail -5 /var/log/syslog
```

### Advanced Monitoring Script

```bash
#!/bin/bash
# Advanced System Monitoring Script

# Configuration
LOG_FILE="/var/log/system_monitor.log"
ALERT_THRESHOLD_CPU=80
ALERT_THRESHOLD_MEMORY=80
ALERT_THRESHOLD_DISK=80
EMAIL="admin@example.com"

# Function to log messages
log_message() {
    echo "$(date): $1" >> $LOG_FILE
}

# Function to send alerts
send_alert() {
    local subject="$1"
    local message="$2"
    echo "$message" | mail -s "$subject" $EMAIL
    log_message "ALERT: $subject"
}

# Check CPU usage
check_cpu() {
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | awk -F'%' '{print $1}')
    if (( $(echo "$cpu_usage > $ALERT_THRESHOLD_CPU" | bc -l) )); then
        send_alert "High CPU Usage" "CPU usage is ${cpu_usage}%"
    fi
}

# Check memory usage
check_memory() {
    local memory_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
    if [ $memory_usage -gt $ALERT_THRESHOLD_MEMORY ]; then
        send_alert "High Memory Usage" "Memory usage is ${memory_usage}%"
    fi
}

# Check disk usage
check_disk() {
    local disk_usage=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
    if [ $disk_usage -gt $ALERT_THRESHOLD_DISK ]; then
        send_alert "High Disk Usage" "Disk usage is ${disk_usage}%"
    fi
}

# Check service status
check_services() {
    local services=("apache2" "mysql" "nginx")
    for service in "${services[@]}"; do
        if ! systemctl is-active --quiet $service; then
            send_alert "Service Down" "Service $service is not running"
        fi
    done
}

# Main monitoring function
main() {
    log_message "Starting system monitoring"
    
    check_cpu
    check_memory
    check_disk
    check_services
    
    log_message "System monitoring completed"
}

# Run main function
main
```

## Monitoring Best Practices

### Monitoring Strategy

**What to Monitor:**
- **Critical Services**: Essential system services
- **Resource Usage**: CPU, memory, disk, network
- **Application Performance**: Response times, error rates
- **Security Events**: Failed logins, suspicious activities
- **Business Metrics**: User activity, transaction volumes

**Monitoring Frequency:**
- **Real-time**: Critical systems and services
- **Every minute**: System resources and performance
- **Every 5 minutes**: Application metrics
- **Every hour**: Log analysis and trends
- **Daily**: Capacity planning and reporting

### Alert Management

**Alert Levels:**
- **Critical**: Immediate attention required
- **Warning**: Attention needed within hours
- **Info**: Informational only
- **Debug**: Detailed information for troubleshooting

**Alert Best Practices:**
- **Set Appropriate Thresholds**: Avoid alert fatigue
- **Use Escalation**: Escalate unresolved alerts
- **Document Procedures**: Have response procedures
- **Test Alerts**: Regularly test alert systems
- **Review and Tune**: Continuously improve alerting

### Performance Optimization

**Monitoring Overhead:**
- **Minimize Impact**: Use efficient monitoring tools
- **Sample Appropriately**: Don't monitor too frequently
- **Use Lightweight Tools**: Choose efficient monitoring solutions
- **Monitor Monitoring**: Ensure monitoring systems are healthy

**Data Management:**
- **Retention Policies**: Define data retention periods
- **Data Compression**: Compress historical data
- **Data Archival**: Archive old data
- **Data Purging**: Remove unnecessary data

## Practice Exercises

### Exercise 1: Basic System Monitoring
**Objective**: Practice basic system monitoring

**Tasks**:
1. Check system resources (CPU, memory, disk)
2. Identify top resource-consuming processes
3. Monitor system load over time
4. Check disk space and I/O
5. Monitor network usage

**Commands to Practice**:
```bash
# Check system resources
htop
free -h
df -h

# Check top processes
ps aux --sort=-%cpu | head -10
ps aux --sort=-%mem | head -10

# Monitor system load
uptime
cat /proc/loadavg

# Check disk I/O
iostat -x 1

# Check network usage
iftop
```

### Exercise 2: Log Monitoring
**Objective**: Practice log monitoring and analysis

**Tasks**:
1. Monitor system logs in real-time
2. Search for specific error patterns
3. Analyze log trends
4. Set up log monitoring alerts
5. Create log analysis reports

**Commands to Practice**:
```bash
# Monitor system logs
tail -f /var/log/syslog
journalctl -f

# Search for errors
grep -i error /var/log/syslog
grep -i "failed" /var/log/auth.log

# Analyze log trends
grep -i error /var/log/syslog | wc -l
grep -i error /var/log/syslog | tail -10
```

### Exercise 3: Performance Monitoring Script
**Objective**: Create a performance monitoring script

**Tasks**:
1. Create a script that monitors system resources
2. Add alerting for high resource usage
3. Log monitoring data to a file
4. Add email notifications
5. Test the script and alerts

**Script Example**:
```bash
#!/bin/bash
# Performance Monitoring Script

LOG_FILE="/var/log/performance_monitor.log"
ALERT_EMAIL="admin@example.com"

# Function to check CPU usage
check_cpu() {
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | awk -F'%' '{print $1}')
    if (( $(echo "$cpu_usage > 80" | bc -l) )); then
        echo "ALERT: High CPU usage: ${cpu_usage}%" | tee -a $LOG_FILE
        echo "High CPU usage: ${cpu_usage}%" | mail -s "CPU Alert" $ALERT_EMAIL
    fi
}

# Function to check memory usage
check_memory() {
    local memory_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
    if [ $memory_usage -gt 80 ]; then
        echo "ALERT: High memory usage: ${memory_usage}%" | tee -a $LOG_FILE
        echo "High memory usage: ${memory_usage}%" | mail -s "Memory Alert" $ALERT_EMAIL
    fi
}

# Main monitoring function
main() {
    echo "$(date): Starting performance monitoring" >> $LOG_FILE
    check_cpu
    check_memory
    echo "$(date): Performance monitoring completed" >> $LOG_FILE
}

# Run main function
main
```

### Exercise 4: Service Monitoring
**Objective**: Practice monitoring system services

**Tasks**:
1. Check status of critical services
2. Monitor service logs
3. Set up service monitoring alerts
4. Test service failure scenarios
5. Document service monitoring procedures

**Commands to Practice**:
```bash
# Check service status
systemctl status apache2
systemctl status mysql
systemctl status nginx

# Monitor service logs
journalctl -u apache2 -f
journalctl -u mysql -f

# Check service dependencies
systemctl list-dependencies apache2

# Restart services
sudo systemctl restart apache2
sudo systemctl restart mysql
```

## Important Reminders

⚠️ **Remember these key points about system monitoring:**

- **Monitor Proactively**: Set up monitoring before problems occur
- **Set Appropriate Thresholds**: Avoid alert fatigue with proper thresholds
- **Document Procedures**: Have clear response procedures for alerts
- **Regular Review**: Continuously review and improve monitoring
- **Test Alerts**: Regularly test alerting systems
- **Keep It Simple**: Start with basic monitoring and add complexity gradually
- **Monitor the Monitors**: Ensure monitoring systems are healthy

**System monitoring is essential for maintaining healthy and performant Linux systems. Effective monitoring helps prevent issues and ensures optimal system operation.** 🐧✨
