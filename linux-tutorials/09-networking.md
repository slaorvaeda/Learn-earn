# 09 - Networking

## Introduction to Linux Networking

Networking is a fundamental aspect of Linux system administration. It involves configuring, monitoring, and troubleshooting network connections, protocols, and services.

**Think of it as:**
- **Network Interface** = Your computer's connection to the network
- **IP Address** = Your computer's unique identifier on the network
- **Gateway** = The device that connects your network to other networks
- **DNS** = The system that translates domain names to IP addresses
- **Firewall** = The security system that controls network traffic

## Network Fundamentals

### OSI Model

**Layer 7 - Application:**
- **Purpose**: User interfaces and network services
- **Examples**: HTTP, FTP, SSH, SMTP
- **Linux Tools**: curl, wget, ssh, telnet

**Layer 6 - Presentation:**
- **Purpose**: Data encryption, compression, formatting
- **Examples**: SSL/TLS, JPEG, MPEG
- **Linux Tools**: openssl, gzip

**Layer 5 - Session:**
- **Purpose**: Managing communication sessions
- **Examples**: RPC, NetBIOS
- **Linux Tools**: rpcbind, netstat

**Layer 4 - Transport:**
- **Purpose**: End-to-end communication
- **Examples**: TCP, UDP
- **Linux Tools**: netstat, ss, tcpdump

**Layer 3 - Network:**
- **Purpose**: Routing and addressing
- **Examples**: IP, ICMP, ARP
- **Linux Tools**: ip, ping, traceroute

**Layer 2 - Data Link:**
- **Purpose**: Frame transmission
- **Examples**: Ethernet, WiFi
- **Linux Tools**: ethtool, iwconfig

**Layer 1 - Physical:**
- **Purpose**: Physical transmission
- **Examples**: Cables, wireless signals
- **Linux Tools**: dmesg, lspci

### TCP/IP Model

**Application Layer:**
- **Purpose**: User applications and services
- **Examples**: HTTP, FTP, SSH, DNS
- **Ports**: 80 (HTTP), 21 (FTP), 22 (SSH), 53 (DNS)

**Transport Layer:**
- **Purpose**: Reliable data delivery
- **Protocols**: TCP (reliable), UDP (fast)
- **Features**: Flow control, error checking, sequencing

**Internet Layer:**
- **Purpose**: Routing and addressing
- **Protocols**: IP, ICMP, ARP
- **Addressing**: IPv4, IPv6

**Network Access Layer:**
- **Purpose**: Physical transmission
- **Protocols**: Ethernet, WiFi, PPP
- **Hardware**: Network cards, cables, wireless

## Network Configuration

### Network Interfaces

**Interface Types:**
- **Ethernet**: Wired network connections
- **WiFi**: Wireless network connections
- **Loopback**: Local system communication
- **Virtual**: Software-defined interfaces

**Interface Naming:**
- **eth0**: First Ethernet interface
- **wlan0**: First wireless interface
- **lo**: Loopback interface
- **enp0s3**: Predictable naming (Ubuntu 18.04+)

### IP Addressing

**IPv4 Addresses:**
- **Format**: 192.168.1.100
- **Range**: 0.0.0.0 to 255.255.255.255
- **Classes**: A, B, C, D, E
- **Private Ranges**: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

**IPv6 Addresses:**
- **Format**: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
- **Shortened**: 2001:db8:85a3::8a2e:370:7334
- **Types**: Global, Link-local, Unique local

**Subnet Masks:**
- **Purpose**: Define network portion of IP address
- **Format**: 255.255.255.0 (/24)
- **CIDR**: Classless Inter-Domain Routing notation

### Network Configuration Files

**Ubuntu/Debian:**
- **Netplan**: /etc/netplan/*.yaml
- **NetworkManager**: /etc/NetworkManager/
- **Interfaces**: /etc/network/interfaces

**Red Hat/CentOS:**
- **NetworkManager**: /etc/NetworkManager/
- **Network Scripts**: /etc/sysconfig/network-scripts/
- **Hostname**: /etc/hostname

**Arch Linux:**
- **NetworkManager**: /etc/NetworkManager/
- **Systemd Network**: /etc/systemd/network/
- **Hostname**: /etc/hostname

## Network Commands

### Basic Network Commands

**ip - Modern Network Configuration:**
```bash
# Show network interfaces
ip addr show
ip a

# Show routing table
ip route show
ip r

# Show network statistics
ip -s link show

# Configure IP address
sudo ip addr add 192.168.1.100/24 dev eth0

# Remove IP address
sudo ip addr del 192.168.1.100/24 dev eth0

# Bring interface up
sudo ip link set eth0 up

# Bring interface down
sudo ip link set eth0 down

# Add route
sudo ip route add 192.168.2.0/24 via 192.168.1.1

# Delete route
sudo ip route del 192.168.2.0/24
```

**ifconfig - Legacy Network Configuration:**
```bash
# Show network interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Configure IP address
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0

# Bring interface up
sudo ifconfig eth0 up

# Bring interface down
sudo ifconfig eth0 down

# Configure broadcast address
sudo ifconfig eth0 broadcast 192.168.1.255
```

**route - Routing Table Management:**
```bash
# Show routing table
route -n

# Add default gateway
sudo route add default gw 192.168.1.1

# Add specific route
sudo route add -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.1.1

# Delete route
sudo route del -net 192.168.2.0 netmask 255.255.255.0
```

### Network Testing Commands

**ping - Test Connectivity:**
```bash
# Ping a host
ping google.com

# Ping with specific count
ping -c 4 google.com

# Ping with specific interval
ping -i 2 google.com

# Ping with specific timeout
ping -W 5 google.com

# Ping IPv6 address
ping6 2001:4860:4860::8888
```

**traceroute - Trace Network Path:**
```bash
# Trace route to host
traceroute google.com

# Trace route with specific protocol
traceroute -T google.com

# Trace route with specific port
traceroute -p 80 google.com

# Trace route with IPv6
traceroute6 google.com
```

**mtr - Network Diagnostic Tool:**
```bash
# Install mtr
sudo apt install mtr

# Run mtr
mtr google.com

# Run mtr with specific options
mtr -r -c 10 google.com
```

### Network Information Commands

**netstat - Network Statistics:**
```bash
# Show listening ports
netstat -tlnp

# Show all connections
netstat -an

# Show routing table
netstat -rn

# Show network interfaces
netstat -i

# Show statistics by protocol
netstat -s
```

**ss - Socket Statistics:**
```bash
# Show listening sockets
ss -tlnp

# Show all sockets
ss -an

# Show TCP sockets
ss -t

# Show UDP sockets
ss -u

# Show socket statistics
ss -s
```

**lsof - List Open Files:**
```bash
# Show network connections
lsof -i

# Show connections on specific port
lsof -i :80

# Show connections for specific user
lsof -i -u username

# Show TCP connections
lsof -i tcp
```

## DNS Configuration

### DNS Resolution

**DNS Configuration Files:**
- **/etc/resolv.conf**: DNS servers
- **/etc/hosts**: Local hostname resolution
- **/etc/nsswitch.conf**: Name service switch configuration

**DNS Commands:**
```bash
# Query DNS
nslookup google.com

# Query DNS with specific server
nslookup google.com 8.8.8.8

# Query DNS with dig
dig google.com

# Query specific record type
dig google.com MX

# Query with specific server
dig @8.8.8.8 google.com

# Reverse DNS lookup
dig -x 8.8.8.8
```

**DNS Configuration:**
```bash
# Edit resolv.conf
sudo nano /etc/resolv.conf

# Add DNS servers
nameserver 8.8.8.8
nameserver 8.8.4.4

# Edit hosts file
sudo nano /etc/hosts

# Add hostname mapping
127.0.0.1 localhost
192.168.1.100 myserver
```

### NetworkManager

**NetworkManager Commands:**
```bash
# Show network status
nmcli general status

# Show network interfaces
nmcli device status

# Show connections
nmcli connection show

# Show active connections
nmcli connection show --active

# Connect to network
nmcli connection up "Connection Name"

# Disconnect from network
nmcli connection down "Connection Name"

# Add new connection
nmcli connection add type ethernet con-name "My Connection" ifname eth0

# Modify connection
nmcli connection modify "My Connection" ipv4.addresses 192.168.1.100/24

# Delete connection
nmcli connection delete "My Connection"
```

## Firewall Configuration

### UFW (Uncomplicated Firewall)

**UFW Commands:**
```bash
# Enable UFW
sudo ufw enable

# Disable UFW
sudo ufw disable

# Show status
sudo ufw status

# Show verbose status
sudo ufw status verbose

# Allow port
sudo ufw allow 80

# Allow port with protocol
sudo ufw allow 80/tcp

# Allow port range
sudo ufw allow 8000:9000/tcp

# Allow from specific IP
sudo ufw allow from 192.168.1.100

# Deny port
sudo ufw deny 22

# Delete rule
sudo ufw delete allow 80

# Reset UFW
sudo ufw --force reset
```

### iptables

**iptables Commands:**
```bash
# Show rules
sudo iptables -L

# Show rules with line numbers
sudo iptables -L -n --line-numbers

# Allow incoming SSH
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow incoming HTTP
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Allow incoming HTTPS
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Drop all other incoming
sudo iptables -A INPUT -j DROP

# Save rules
sudo iptables-save > /etc/iptables/rules.v4

# Restore rules
sudo iptables-restore < /etc/iptables/rules.v4
```

## Network Troubleshooting

### Common Network Issues

**Connectivity Problems:**
```bash
# Check network interface status
ip link show

# Check IP configuration
ip addr show

# Check routing table
ip route show

# Test connectivity
ping google.com

# Check DNS resolution
nslookup google.com

# Check network statistics
ip -s link show
```

**Performance Issues:**
```bash
# Check network utilization
iftop

# Check network connections
netstat -an

# Check network errors
dmesg | grep -i network

# Check interface statistics
cat /proc/net/dev
```

**Security Issues:**
```bash
# Check listening ports
netstat -tlnp

# Check firewall status
sudo ufw status

# Check network connections
ss -tuln

# Check for suspicious connections
lsof -i
```

### Network Monitoring

**Real-time Monitoring:**
```bash
# Monitor network traffic
iftop

# Monitor network connections
netstat -c

# Monitor network statistics
watch -n 1 'cat /proc/net/dev'

# Monitor network errors
dmesg -w | grep -i network
```

**Log Analysis:**
```bash
# Check system logs
journalctl -u NetworkManager

# Check network logs
tail -f /var/log/syslog | grep -i network

# Check firewall logs
tail -f /var/log/ufw.log
```

## Wireless Networking

### WiFi Configuration

**WiFi Commands:**
```bash
# Scan for networks
iwlist scan

# Show WiFi interfaces
iwconfig

# Connect to WiFi
nmcli device wifi connect "SSID" password "password"

# Show WiFi status
nmcli device wifi list

# Show WiFi connection info
nmcli device wifi show

# Disconnect from WiFi
nmcli device disconnect wlan0
```

**WiFi Troubleshooting:**
```bash
# Check WiFi driver
lspci | grep -i wireless

# Check WiFi interface
iwconfig

# Check WiFi power management
iwconfig wlan0 power off

# Check WiFi signal strength
iwconfig wlan0 | grep -i signal

# Check WiFi errors
dmesg | grep -i wifi
```

## Network Services

### SSH (Secure Shell)

**SSH Configuration:**
```bash
# Install SSH server
sudo apt install openssh-server

# Start SSH service
sudo systemctl start ssh

# Enable SSH service
sudo systemctl enable ssh

# Check SSH status
sudo systemctl status ssh

# Edit SSH configuration
sudo nano /etc/ssh/sshd_config
```

**SSH Commands:**
```bash
# Connect to remote host
ssh username@hostname

# Connect with specific port
ssh -p 2222 username@hostname

# Connect with key file
ssh -i ~/.ssh/id_rsa username@hostname

# Copy files with SCP
scp file.txt username@hostname:/path/to/destination

# Copy files with RSYNC
rsync -avz file.txt username@hostname:/path/to/destination
```

### Web Server (Apache/Nginx)

**Apache Configuration:**
```bash
# Install Apache
sudo apt install apache2

# Start Apache
sudo systemctl start apache2

# Enable Apache
sudo systemctl enable apache2

# Check Apache status
sudo systemctl status apache2

# Edit Apache configuration
sudo nano /etc/apache2/apache2.conf
```

**Nginx Configuration:**
```bash
# Install Nginx
sudo apt install nginx

# Start Nginx
sudo systemctl start nginx

# Enable Nginx
sudo systemctl enable nginx

# Check Nginx status
sudo systemctl status nginx

# Edit Nginx configuration
sudo nano /etc/nginx/nginx.conf
```

## Practice Exercises

### Exercise 1: Basic Network Configuration
**Objective**: Practice basic network configuration

**Tasks**:
1. Check current network configuration
2. Configure static IP address
3. Test network connectivity
4. Configure DNS servers
5. Test DNS resolution

**Commands to Practice**:
```bash
# Check network configuration
ip addr show
ip route show

# Configure static IP
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip route add default via 192.168.1.1

# Test connectivity
ping google.com

# Configure DNS
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# Test DNS
nslookup google.com
```

### Exercise 2: Network Troubleshooting
**Objective**: Practice network troubleshooting

**Tasks**:
1. Identify network problems
2. Check network interfaces
3. Test connectivity
4. Check DNS resolution
5. Apply fixes

**Commands to Practice**:
```bash
# Check network status
ip link show
ip addr show

# Test connectivity
ping -c 4 google.com

# Check DNS
nslookup google.com

# Check routing
traceroute google.com

# Check network statistics
ip -s link show
```

### Exercise 3: Firewall Configuration
**Objective**: Practice firewall configuration

**Tasks**:
1. Check firewall status
2. Configure basic firewall rules
3. Test firewall rules
4. Monitor firewall logs
5. Troubleshoot firewall issues

**Commands to Practice**:
```bash
# Check UFW status
sudo ufw status

# Enable UFW
sudo ufw enable

# Allow SSH
sudo ufw allow 22

# Allow HTTP
sudo ufw allow 80

# Check firewall logs
sudo tail -f /var/log/ufw.log
```

### Exercise 4: Network Services
**Objective**: Practice configuring network services

**Tasks**:
1. Install and configure SSH
2. Install and configure web server
3. Test service connectivity
4. Monitor service logs
5. Troubleshoot service issues

**Commands to Practice**:
```bash
# Install SSH
sudo apt install openssh-server

# Start SSH
sudo systemctl start ssh

# Test SSH
ssh localhost

# Install Apache
sudo apt install apache2

# Start Apache
sudo systemctl start apache2

# Test web server
curl localhost
```

## Important Reminders

⚠️ **Remember these key points about Linux networking:**

- **Test Connectivity**: Always test network connections after configuration
- **Check Logs**: Monitor network logs for troubleshooting
- **Security First**: Configure firewalls and secure network services
- **Document Changes**: Keep records of network configuration changes
- **Backup Configurations**: Backup network configuration files
- **Monitor Performance**: Keep an eye on network performance and utilization
- **Stay Updated**: Keep network drivers and services updated

**Networking is essential for modern Linux systems. Understanding how to configure, monitor, and troubleshoot network connections will make you a more effective Linux administrator.** 🐧✨
