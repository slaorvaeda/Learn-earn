# 15 - Troubleshooting

## Introduction to Linux Troubleshooting

Troubleshooting is the process of identifying, diagnosing, and resolving problems in Linux systems. It requires systematic thinking, knowledge of system components, and effective use of diagnostic tools.

**Think of it as:**
- **Troubleshooting** = Finding and fixing problems
- **Diagnosis** = Understanding what's wrong
- **Root Cause Analysis** = Finding the real cause of problems
- **Systematic Approach** = Following a logical process
- **Prevention** = Avoiding problems in the future

## Troubleshooting Methodology

### Systematic Approach

**1. Identify the Problem:**
- **What**: What exactly is the problem?
- **When**: When did it start happening?
- **Where**: Where is the problem occurring?
- **Who**: Who is affected by the problem?
- **Impact**: What is the business impact?

**2. Gather Information:**
- **System Logs**: Check relevant log files
- **User Reports**: Get detailed user descriptions
- **System Status**: Check system health and performance
- **Recent Changes**: Identify recent system changes
- **Error Messages**: Collect all error messages

**3. Formulate Hypotheses:**
- **Possible Causes**: List potential causes
- **Probability**: Assess likelihood of each cause
- **Testability**: Determine how to test each hypothesis
- **Impact**: Consider impact of each potential cause

**4. Test and Verify:**
- **Test Hypotheses**: Test each hypothesis systematically
- **Gather Evidence**: Collect evidence for or against each hypothesis
- **Narrow Down**: Eliminate unlikely causes
- **Confirm Root Cause**: Identify the actual root cause

**5. Implement Solution:**
- **Plan Solution**: Develop a solution plan
- **Test Solution**: Test the solution in a safe environment
- **Implement**: Apply the solution carefully
- **Verify**: Confirm the problem is resolved
- **Document**: Document the solution and lessons learned

### Common Troubleshooting Scenarios

**Boot Problems:**
- **System won't boot**: Hardware issues, corrupted bootloader
- **Slow boot**: Too many services, disk issues
- **Boot errors**: Configuration problems, missing files
- **Kernel panics**: Hardware failures, driver issues

**Performance Issues:**
- **Slow system**: High CPU usage, memory issues
- **Slow disk I/O**: Disk failures, I/O bottlenecks
- **Network problems**: Connectivity issues, configuration errors
- **Application crashes**: Memory leaks, configuration issues

**Service Problems:**
- **Service won't start**: Configuration errors, missing dependencies
- **Service crashes**: Resource issues, configuration problems
- **Service slow**: Performance bottlenecks, resource constraints
- **Service unavailable**: Network issues, firewall problems

## Diagnostic Tools

### System Information Tools

**System Information:**
```bash
# System information
uname -a
hostnamectl
lscpu
lsmem
lsblk
lspci
lsusb

# System status
systemctl status
systemctl list-units --failed
systemctl list-timers
```

**Hardware Information:**
```bash
# CPU information
cat /proc/cpuinfo
lscpu
nproc

# Memory information
cat /proc/meminfo
free -h
vmstat

# Disk information
lsblk
fdisk -l
df -h
du -h

# Network information
ip addr show
ip route show
ss -tuln
```

### Log Analysis Tools

**System Logs:**
```bash
# System logs
journalctl
journalctl -f
journalctl -u service-name
journalctl --since "2023-12-25 10:00:00"
journalctl --until "2023-12-25 11:00:00"

# Traditional logs
tail -f /var/log/syslog
tail -f /var/log/messages
tail -f /var/log/kern.log
tail -f /var/log/auth.log
```

**Log Analysis:**
```bash
# Search logs
grep -i error /var/log/syslog
grep -i "failed" /var/log/auth.log
grep -E "(error|warning|critical)" /var/log/syslog

# Count log entries
grep -c "error" /var/log/syslog
grep -c "failed" /var/log/auth.log

# Show log entries with context
grep -A 5 -B 5 "error" /var/log/syslog
```

### Performance Monitoring Tools

**System Performance:**
```bash
# CPU usage
top
htop
ps aux --sort=-%cpu
sar -u 1 10

# Memory usage
free -h
ps aux --sort=-%mem
sar -r 1 10

# Disk usage
df -h
du -h
iostat -x 1
sar -d 1 10

# Network usage
iftop
nethogs
ss -tuln
netstat -i
```

**Process Analysis:**
```bash
# Process information
ps aux
ps auxf
pstree
pgrep -f process-name

# Process resources
top -p PID
htop -p PID
ps -o pid,ppid,cmd,%cpu,%mem PID

# Process files
lsof -p PID
lsof -i :port
lsof /path/to/file
```

## Common Problems and Solutions

### Boot Problems

**System Won't Boot:**
```bash
# Check boot logs
journalctl -b
dmesg | grep -i error

# Check filesystem
fsck /dev/sda1

# Check bootloader
grub-install /dev/sda
update-grub

# Boot from recovery mode
# Select recovery mode from GRUB menu
# Run fsck and other recovery commands
```

**Slow Boot:**
```bash
# Check boot time
systemd-analyze
systemd-analyze blame
systemd-analyze critical-chain

# Disable unnecessary services
systemctl disable service-name
systemctl mask service-name

# Check for slow services
systemctl list-unit-files --type=service
systemctl status service-name
```

**Boot Errors:**
```bash
# Check GRUB configuration
cat /etc/default/grub
update-grub

# Check kernel parameters
cat /proc/cmdline

# Check initramfs
lsinitramfs /boot/initrd.img-$(uname -r)
update-initramfs -u
```

### Performance Issues

**High CPU Usage:**
```bash
# Identify high CPU processes
top
htop
ps aux --sort=-%cpu | head -10

# Check system load
uptime
cat /proc/loadavg

# Check for runaway processes
ps aux | grep -v grep | awk '{if($3>50) print $0}'

# Kill problematic processes
kill -9 PID
killall process-name
```

**High Memory Usage:**
```bash
# Check memory usage
free -h
ps aux --sort=-%mem | head -10

# Check memory leaks
ps aux | awk '{sum+=$6} END {print sum/1024 " MB"}'

# Check swap usage
swapon -s
cat /proc/swaps

# Clear memory cache
echo 3 > /proc/sys/vm/drop_caches
```

**Slow Disk I/O:**
```bash
# Check disk usage
df -h
du -h

# Check disk I/O
iostat -x 1
iotop

# Check for disk errors
dmesg | grep -i error
smartctl -a /dev/sda

# Check filesystem
fsck /dev/sda1
```

### Network Problems

**Network Connectivity:**
```bash
# Check network interfaces
ip addr show
ifconfig

# Test connectivity
ping google.com
ping -c 4 8.8.8.8

# Check routing
ip route show
traceroute google.com

# Check DNS
nslookup google.com
dig google.com
```

**Network Configuration:**
```bash
# Check network configuration
cat /etc/network/interfaces
cat /etc/netplan/*.yaml

# Restart network
sudo systemctl restart networking
sudo netplan apply

# Check network services
systemctl status NetworkManager
systemctl status networking
```

**Firewall Issues:**
```bash
# Check firewall status
sudo ufw status
sudo iptables -L

# Check blocked connections
sudo ufw status verbose
sudo iptables -L -n

# Test specific ports
telnet hostname port
nc -zv hostname port
```

### Service Problems

**Service Won't Start:**
```bash
# Check service status
systemctl status service-name
journalctl -u service-name

# Check service configuration
systemctl cat service-name
cat /etc/systemd/system/service-name.service

# Check dependencies
systemctl list-dependencies service-name

# Restart service
sudo systemctl restart service-name
```

**Service Crashes:**
```bash
# Check service logs
journalctl -u service-name -f
tail -f /var/log/service-name.log

# Check resource usage
ps aux | grep service-name
lsof -p PID

# Check for errors
grep -i error /var/log/service-name.log
```

**Service Performance:**
```bash
# Check service resources
systemctl show service-name
ps aux | grep service-name

# Check service connections
ss -tuln | grep port
netstat -tuln | grep port

# Monitor service performance
htop -p PID
iotop -p PID
```

## Advanced Troubleshooting

### Kernel Debugging

**Kernel Messages:**
```bash
# Check kernel messages
dmesg
dmesg | grep -i error
dmesg | grep -i warning

# Check kernel modules
lsmod
modinfo module-name
modprobe module-name

# Check kernel parameters
cat /proc/cmdline
sysctl -a
```

**Kernel Panics:**
```bash
# Check kernel panic logs
dmesg | grep -i panic
journalctl -k

# Check hardware
lspci
lsusb
lscpu
lsmem

# Check drivers
lsmod | grep driver-name
modinfo driver-name
```

### Hardware Troubleshooting

**Hardware Detection:**
```bash
# Check hardware
lspci
lsusb
lscpu
lsmem
lsblk

# Check hardware errors
dmesg | grep -i error
journalctl -k

# Check hardware temperature
sensors
cat /sys/class/thermal/thermal_zone*/temp
```

**Hardware Testing:**
```bash
# Test memory
memtest86+
memtester 1G

# Test disk
badblocks -v /dev/sda
smartctl -t short /dev/sda

# Test network
ping -c 1000 8.8.8.8
iperf3 -c server-ip
```

### Application Debugging

**Application Logs:**
```bash
# Check application logs
tail -f /var/log/application.log
journalctl -u application

# Check application configuration
cat /etc/application/application.conf
application --config-check
```

**Application Performance:**
```bash
# Profile application
strace -p PID
ltrace -p PID
perf top -p PID

# Check application resources
ps aux | grep application
lsof -p PID
```

## Troubleshooting Scripts

### System Health Check Script

```bash
#!/bin/bash
# System Health Check Script

echo "=== System Health Check ==="
echo "Date: $(date)"
echo "Hostname: $(hostname)"
echo ""

# Check system load
echo "=== System Load ==="
uptime
echo ""

# Check memory usage
echo "=== Memory Usage ==="
free -h
echo ""

# Check disk usage
echo "=== Disk Usage ==="
df -h
echo ""

# Check running processes
echo "=== Top Processes by CPU ==="
ps aux --sort=-%cpu | head -5
echo ""

# Check running processes by memory
echo "=== Top Processes by Memory ==="
ps aux --sort=-%mem | head -5
echo ""

# Check network interfaces
echo "=== Network Interfaces ==="
ip addr show | grep -E "(inet |UP|DOWN)"
echo ""

# Check system services
echo "=== Failed Services ==="
systemctl list-units --failed
echo ""

# Check recent log entries
echo "=== Recent Log Entries ==="
journalctl --since "1 hour ago" | tail -10
echo ""

echo "=== Health Check Complete ==="
```

### Performance Monitoring Script

```bash
#!/bin/bash
# Performance Monitoring Script

LOG_FILE="/var/log/performance_monitor.log"
ALERT_EMAIL="admin@example.com"

# Function to log and alert
log_and_alert() {
    local message="$1"
    echo "$(date): $message" >> $LOG_FILE
    echo "$message" | mail -s "Performance Alert" $ALERT_EMAIL
}

# Check CPU usage
check_cpu() {
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | awk -F'%' '{print $1}')
    if (( $(echo "$cpu_usage > 80" | bc -l) )); then
        log_and_alert "High CPU usage: ${cpu_usage}%"
    fi
}

# Check memory usage
check_memory() {
    local memory_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
    if [ $memory_usage -gt 80 ]; then
        log_and_alert "High memory usage: ${memory_usage}%"
    fi
}

# Check disk usage
check_disk() {
    local disk_usage=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
    if [ $disk_usage -gt 80 ]; then
        log_and_alert "High disk usage: ${disk_usage}%"
    fi
}

# Check system load
check_load() {
    local load_avg=$(cat /proc/loadavg | awk '{print $1}')
    local cpu_cores=$(nproc)
    if (( $(echo "$load_avg > $cpu_cores" | bc -l) )); then
        log_and_alert "High system load: ${load_avg} (CPU cores: ${cpu_cores})"
    fi
}

# Main monitoring function
main() {
    check_cpu
    check_memory
    check_disk
    check_load
}

# Run monitoring
main
```

## Practice Exercises

### Exercise 1: Basic Troubleshooting
**Objective**: Practice basic troubleshooting techniques

**Tasks**:
1. Check system status and health
2. Analyze system logs
3. Identify performance issues
4. Check service status
5. Document findings

**Commands to Practice**:
```bash
# Check system status
uptime
free -h
df -h

# Check logs
journalctl -f
tail -f /var/log/syslog

# Check processes
top
ps aux --sort=-%cpu

# Check services
systemctl status
systemctl list-units --failed
```

### Exercise 2: Performance Troubleshooting
**Objective**: Practice performance troubleshooting

**Tasks**:
1. Identify performance bottlenecks
2. Monitor system resources
3. Analyze process behavior
4. Optimize system performance
5. Document performance improvements

**Commands to Practice**:
```bash
# Monitor performance
htop
iotop
iftop

# Check system load
uptime
cat /proc/loadavg

# Check memory usage
free -h
ps aux --sort=-%mem

# Check disk I/O
iostat -x 1
iotop
```

### Exercise 3: Service Troubleshooting
**Objective**: Practice service troubleshooting

**Tasks**:
1. Diagnose service problems
2. Check service configuration
3. Analyze service logs
4. Fix service issues
5. Test service functionality

**Commands to Practice**:
```bash
# Check service status
systemctl status apache2
systemctl status mysql

# Check service logs
journalctl -u apache2
journalctl -u mysql

# Restart services
sudo systemctl restart apache2
sudo systemctl restart mysql

# Check service configuration
systemctl cat apache2
systemctl cat mysql
```

### Exercise 4: Network Troubleshooting
**Objective**: Practice network troubleshooting

**Tasks**:
1. Diagnose network connectivity issues
2. Check network configuration
3. Test network services
4. Fix network problems
5. Verify network functionality

**Commands to Practice**:
```bash
# Check network status
ip addr show
ip route show

# Test connectivity
ping google.com
traceroute google.com

# Check network services
ss -tuln
netstat -tuln

# Check firewall
sudo ufw status
sudo iptables -L
```

## Important Reminders

⚠️ **Remember these key points about troubleshooting:**

- **Systematic Approach**: Follow a logical troubleshooting process
- **Document Everything**: Keep detailed records of problems and solutions
- **Test Changes**: Always test solutions in a safe environment
- **Backup First**: Always backup before making changes
- **Learn from Problems**: Use problems as learning opportunities
- **Prevent Recurrence**: Implement measures to prevent similar problems
- **Stay Calm**: Keep a clear head and think logically

**Effective troubleshooting is a skill that improves with practice. Understanding system components and using the right tools helps identify and resolve problems quickly and efficiently.** 🐧✨
