# 14 - Security Hardening

## Introduction to Security Hardening

Security hardening is the process of securing a system by reducing its attack surface and implementing security best practices. It involves configuring the system to be more secure by default and protecting against common threats.

**Think of it as:**
- **Hardening** = Making your system more secure
- **Attack Surface** = Areas where attackers can exploit vulnerabilities
- **Defense in Depth** = Multiple layers of security
- **Least Privilege** = Giving users only the minimum access they need
- **Security by Design** = Building security into the system from the start

## Security Fundamentals

### Security Principles

**Defense in Depth:**
- **Multiple Layers**: Implement security at multiple levels
- **Redundancy**: Have backup security measures
- **Diversity**: Use different security tools and techniques
- **Fail-Safe**: System fails to secure state

**Least Privilege:**
- **Minimum Access**: Give users only necessary permissions
- **Principle of Need-to-Know**: Access only what's needed for work
- **Regular Review**: Periodically review and adjust permissions
- **Just-in-Time**: Grant access only when needed

**Zero Trust:**
- **Never Trust**: Don't trust any user or device by default
- **Always Verify**: Verify every access request
- **Least Access**: Grant minimum necessary access
- **Continuous Monitoring**: Monitor all activities

### Threat Landscape

**Common Threats:**
- **Malware**: Viruses, trojans, ransomware
- **Phishing**: Social engineering attacks
- **Brute Force**: Password guessing attacks
- **SQL Injection**: Database attacks
- **Cross-Site Scripting**: Web application attacks
- **DDoS**: Denial of service attacks

**Attack Vectors:**
- **Network**: Network-based attacks
- **Physical**: Physical access to systems
- **Social**: Social engineering attacks
- **Insider**: Attacks from within organization
- **Supply Chain**: Attacks through third-party vendors

## System Hardening

### User Account Security

**Strong Password Policy:**
```bash
# Install password quality module
sudo apt install libpam-pwquality

# Configure password policy
sudo nano /etc/security/pwquality.conf

# Password requirements
minlen = 12
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
difok = 8
minclass = 4
maxrepeat = 2
maxclassrepeat = 2
```

**Account Lockout Policy:**
```bash
# Configure account lockout
sudo nano /etc/security/faillock.conf

# Lockout settings
deny = 5
fail_interval = 900
unlock_time = 600
```

**User Account Management:**
```bash
# Disable root login
sudo passwd -l root

# Create sudo user
sudo useradd -m -s /bin/bash admin
sudo usermod -aG sudo admin

# Set password expiration
sudo chage -M 90 admin
sudo chage -W 7 admin

# Check password status
sudo chage -l admin
```

### File System Security

**File Permissions:**
```bash
# Set secure file permissions
sudo chmod 644 /etc/passwd
sudo chmod 600 /etc/shadow
sudo chmod 644 /etc/group
sudo chmod 600 /etc/gshadow

# Set secure directory permissions
sudo chmod 755 /home
sudo chmod 700 /home/username

# Set secure log permissions
sudo chmod 640 /var/log/syslog
sudo chown root:adm /var/log/syslog
```

**File System Hardening:**
```bash
# Mount filesystems with security options
echo "/dev/sda1 / ext4 defaults,noexec,nosuid,nodev 0 1" >> /etc/fstab

# Set sticky bit on world-writable directories
sudo chmod +t /tmp
sudo chmod +t /var/tmp

# Remove world-writable files
find / -type f -perm -002 -exec chmod o-w {} \;

# Remove SUID/SGID files
find / -type f \( -perm -4000 -o -perm -2000 \) -exec ls -l {} \;
```

### Network Security

**Firewall Configuration:**
```bash
# Enable UFW firewall
sudo ufw enable

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH
sudo ufw allow ssh

# Allow HTTP/HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Deny specific ports
sudo ufw deny 23/tcp  # Telnet
sudo ufw deny 21/tcp  # FTP
sudo ufw deny 25/tcp  # SMTP

# Show firewall status
sudo ufw status verbose
```

**Network Hardening:**
```bash
# Disable IP forwarding
echo "net.ipv4.ip_forward = 0" >> /etc/sysctl.conf

# Disable source routing
echo "net.ipv4.conf.all.accept_source_route = 0" >> /etc/sysctl.conf

# Disable ICMP redirects
echo "net.ipv4.conf.all.accept_redirects = 0" >> /etc/sysctl.conf

# Enable SYN flood protection
echo "net.ipv4.tcp_syncookies = 1" >> /etc/sysctl.conf

# Apply changes
sudo sysctl -p
```

### Service Security

**Disable Unnecessary Services:**
```bash
# List all services
systemctl list-unit-files --type=service

# Disable unnecessary services
sudo systemctl disable bluetooth
sudo systemctl disable cups
sudo systemctl disable avahi-daemon

# Stop running services
sudo systemctl stop bluetooth
sudo systemctl stop cups
sudo systemctl stop avahi-daemon
```

**Service Hardening:**
```bash
# Configure SSH security
sudo nano /etc/ssh/sshd_config

# SSH security settings
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
X11Forwarding no
AllowUsers admin

# Restart SSH service
sudo systemctl restart ssh
```

## Application Security

### Web Server Security

**Apache Security:**
```bash
# Install Apache
sudo apt install apache2

# Configure Apache security
sudo nano /etc/apache2/conf-available/security.conf

# Security settings
ServerTokens Prod
ServerSignature Off
TraceEnable Off
Header always set X-Content-Type-Options nosniff
Header always set X-Frame-Options DENY
Header always set X-XSS-Protection "1; mode=block"

# Enable security module
sudo a2enmod security
sudo systemctl restart apache2
```

**Nginx Security:**
```bash
# Install Nginx
sudo apt install nginx

# Configure Nginx security
sudo nano /etc/nginx/nginx.conf

# Security settings
server_tokens off;
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";

# Restart Nginx
sudo systemctl restart nginx
```

### Database Security

**MySQL Security:**
```bash
# Install MySQL
sudo apt install mysql-server

# Run security script
sudo mysql_secure_installation

# Configure MySQL security
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

# Security settings
bind-address = 127.0.0.1
skip-networking
local-infile = 0
```

**PostgreSQL Security:**
```bash
# Install PostgreSQL
sudo apt install postgresql

# Configure PostgreSQL security
sudo nano /etc/postgresql/13/main/postgresql.conf

# Security settings
listen_addresses = 'localhost'
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'
```

## Security Monitoring

### Intrusion Detection

**AIDE - Advanced Intrusion Detection Environment:**
```bash
# Install AIDE
sudo apt install aide

# Initialize AIDE database
sudo aideinit

# Move database to proper location
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Run AIDE check
sudo aide --check

# Update AIDE database
sudo aide --update
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```

**Fail2ban - Intrusion Prevention:**
```bash
# Install Fail2ban
sudo apt install fail2ban

# Configure Fail2ban
sudo nano /etc/fail2ban/jail.local

# SSH protection
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600

# Start Fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Security Scanning

**Lynis - Security Auditing:**
```bash
# Install Lynis
sudo apt install lynis

# Run security audit
sudo lynis audit system

# Run specific audit
sudo lynis audit system --profile /etc/lynis/default.prf

# Generate report
sudo lynis audit system --report-file /tmp/lynis-report.txt
```

**Chkrootkit - Rootkit Detection:**
```bash
# Install Chkrootkit
sudo apt install chkrootkit

# Run rootkit scan
sudo chkrootkit

# Run with verbose output
sudo chkrootkit -v
```

## Encryption and Key Management

### Disk Encryption

**LUKS Disk Encryption:**
```bash
# Install encryption tools
sudo apt install cryptsetup

# Create encrypted partition
sudo cryptsetup luksFormat /dev/sdb1

# Open encrypted partition
sudo cryptsetup luksOpen /dev/sdb1 encrypted_partition

# Create filesystem
sudo mkfs.ext4 /dev/mapper/encrypted_partition

# Mount encrypted partition
sudo mount /dev/mapper/encrypted_partition /mnt/encrypted

# Close encrypted partition
sudo umount /mnt/encrypted
sudo cryptsetup luksClose encrypted_partition
```

**File Encryption:**
```bash
# Encrypt file with GPG
gpg --symmetric --cipher-algo AES256 file.txt

# Decrypt file
gpg --decrypt file.txt.gpg

# Encrypt directory
tar -czf - directory/ | gpg --symmetric --cipher-algo AES256 > directory.tar.gz.gpg
```

### SSL/TLS Configuration

**OpenSSL Configuration:**
```bash
# Generate private key
openssl genrsa -out server.key 2048

# Generate certificate signing request
openssl req -new -key server.key -out server.csr

# Generate self-signed certificate
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

# Verify certificate
openssl x509 -in server.crt -text -noout
```

**Let's Encrypt SSL:**
```bash
# Install Certbot
sudo apt install certbot

# Obtain SSL certificate
sudo certbot --apache -d example.com

# Auto-renewal
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet
```

## Security Policies and Compliance

### Security Policies

**Password Policy:**
```bash
# Create password policy
sudo nano /etc/security/pwquality.conf

# Password requirements
minlen = 12
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
difok = 8
minclass = 4
maxrepeat = 2
maxclassrepeat = 2
```

**Access Control Policy:**
```bash
# Create access control list
sudo nano /etc/security/access.conf

# Deny access for specific users
-:ALL EXCEPT root:ALL
-:user1:ALL
-:user2:ALL
```

### Compliance Frameworks

**CIS Benchmarks:**
- **Center for Internet Security**: Security configuration guidelines
- **CIS Controls**: 20 critical security controls
- **CIS Hardening**: System hardening guidelines
- **CIS Assessment**: Security assessment tools

**NIST Framework:**
- **Identify**: Asset management and risk assessment
- **Protect**: Access control and data security
- **Detect**: Monitoring and anomaly detection
- **Respond**: Incident response procedures
- **Recover**: Recovery and improvement

## Security Automation

### Automated Security Scripts

**Security Hardening Script:**
```bash
#!/bin/bash
# Security Hardening Script

# Update system
sudo apt update && sudo apt upgrade -y

# Install security tools
sudo apt install -y ufw fail2ban aide chkrootkit lynis

# Configure firewall
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh

# Configure Fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Configure AIDE
sudo aideinit
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Set secure file permissions
sudo chmod 644 /etc/passwd
sudo chmod 600 /etc/shadow
sudo chmod 644 /etc/group
sudo chmod 600 /etc/gshadow

# Disable unnecessary services
sudo systemctl disable bluetooth
sudo systemctl disable cups
sudo systemctl disable avahi-daemon

echo "Security hardening completed"
```

**Security Monitoring Script:**
```bash
#!/bin/bash
# Security Monitoring Script

LOG_FILE="/var/log/security_monitor.log"
ALERT_EMAIL="admin@example.com"

# Function to log and alert
log_and_alert() {
    local message="$1"
    echo "$(date): $message" >> $LOG_FILE
    echo "$message" | mail -s "Security Alert" $ALERT_EMAIL
}

# Check for failed login attempts
failed_logins=$(grep "Failed password" /var/log/auth.log | wc -l)
if [ $failed_logins -gt 10 ]; then
    log_and_alert "High number of failed login attempts: $failed_logins"
fi

# Check for root login attempts
root_logins=$(grep "root" /var/log/auth.log | wc -l)
if [ $root_logins -gt 0 ]; then
    log_and_alert "Root login attempts detected: $root_logins"
fi

# Check for suspicious activities
suspicious=$(grep -E "(sudo|su|su -)" /var/log/auth.log | wc -l)
if [ $suspicious -gt 5 ]; then
    log_and_alert "Suspicious activities detected: $suspicious"
fi

# Check disk space
disk_usage=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
if [ $disk_usage -gt 80 ]; then
    log_and_alert "High disk usage: ${disk_usage}%"
fi
```

## Practice Exercises

### Exercise 1: Basic Security Hardening
**Objective**: Practice basic security hardening

**Tasks**:
1. Configure strong password policy
2. Set up firewall rules
3. Disable unnecessary services
4. Set secure file permissions
5. Test security configuration

**Commands to Practice**:
```bash
# Configure password policy
sudo nano /etc/security/pwquality.conf

# Set up firewall
sudo ufw enable
sudo ufw allow ssh
sudo ufw deny 23

# Disable services
sudo systemctl disable bluetooth
sudo systemctl disable cups

# Set file permissions
sudo chmod 644 /etc/passwd
sudo chmod 600 /etc/shadow
```

### Exercise 2: Intrusion Detection Setup
**Objective**: Set up intrusion detection system

**Tasks**:
1. Install and configure AIDE
2. Install and configure Fail2ban
3. Set up security monitoring
4. Test intrusion detection
5. Configure alerts

**Commands to Practice**:
```bash
# Install AIDE
sudo apt install aide

# Initialize AIDE
sudo aideinit
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Install Fail2ban
sudo apt install fail2ban

# Configure Fail2ban
sudo nano /etc/fail2ban/jail.local

# Start services
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Exercise 3: Security Audit
**Objective**: Perform security audit

**Tasks**:
1. Run security audit with Lynis
2. Check for rootkits with Chkrootkit
3. Analyze security findings
4. Implement security recommendations
5. Document security status

**Commands to Practice**:
```bash
# Install audit tools
sudo apt install lynis chkrootkit

# Run security audit
sudo lynis audit system

# Check for rootkits
sudo chkrootkit

# Analyze results
sudo lynis audit system --report-file /tmp/audit-report.txt
```

### Exercise 4: Security Monitoring Script
**Objective**: Create security monitoring script

**Tasks**:
1. Create security monitoring script
2. Add log analysis functions
3. Implement alerting system
4. Test monitoring script
5. Set up automated monitoring

**Script Example**:
```bash
#!/bin/bash
# Security Monitoring Script

LOG_FILE="/var/log/security_monitor.log"
ALERT_EMAIL="admin@example.com"

# Function to check failed logins
check_failed_logins() {
    local failed_logins=$(grep "Failed password" /var/log/auth.log | wc -l)
    if [ $failed_logins -gt 10 ]; then
        echo "$(date): High number of failed logins: $failed_logins" >> $LOG_FILE
        echo "High number of failed logins: $failed_logins" | mail -s "Security Alert" $ALERT_EMAIL
    fi
}

# Function to check disk space
check_disk_space() {
    local disk_usage=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
    if [ $disk_usage -gt 80 ]; then
        echo "$(date): High disk usage: ${disk_usage}%" >> $LOG_FILE
        echo "High disk usage: ${disk_usage}%" | mail -s "Disk Alert" $ALERT_EMAIL
    fi
}

# Main monitoring function
main() {
    check_failed_logins
    check_disk_space
}

# Run monitoring
main
```

## Important Reminders

⚠️ **Remember these key points about security hardening:**

- **Defense in Depth**: Implement multiple layers of security
- **Least Privilege**: Give users only necessary permissions
- **Regular Updates**: Keep systems and software updated
- **Monitor Continuously**: Monitor for security threats and anomalies
- **Test Regularly**: Test security measures and procedures
- **Document Everything**: Keep detailed security documentation
- **Stay Informed**: Keep up with security threats and best practices

**Security hardening is an ongoing process that requires continuous attention and improvement. Proper security measures help protect systems and data from various threats.** 🐧✨
