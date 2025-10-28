# 03 - Linux Basics

## Introduction to Linux Command Line

The command line interface (CLI) is the heart of Linux. While graphical interfaces are user-friendly, the command line provides powerful tools for system administration, automation, and advanced operations.

**Think of it as:**
- **Command Line** = Your direct conversation with the computer
- **Terminal** = The window where you type commands
- **Shell** = The program that interprets your commands
- **Commands** = Instructions you give to the system

## Understanding the Terminal

### What is a Terminal?

A terminal is a text-based interface that allows you to interact with your Linux system using commands. It's the primary way system administrators and power users control Linux systems.

**Terminal vs GUI:**
- **Terminal**: Text-based, powerful, efficient
- **GUI**: Graphical, user-friendly, limited
- **Both**: Can be used together for maximum productivity

### Opening a Terminal

**Different Methods:**
- **Keyboard Shortcut**: Ctrl+Alt+T (most distributions)
- **Applications Menu**: Look for "Terminal" or "Konsole"
- **Right-click Menu**: "Open Terminal Here" in file manager
- **Search**: Type "terminal" in application launcher

### Terminal Anatomy

**Terminal Components:**
- **Prompt**: Shows current user, hostname, and directory
- **Command Line**: Where you type commands
- **Cursor**: Shows your current position
- **Scrollback**: History of previous commands and output

**Example Prompt:**
```bash
user@hostname:~$ 
```
- **user**: Current username
- **@**: Separator
- **hostname**: Computer name
- **~**: Current directory (home directory)
- **$**: Prompt symbol (regular user)
- **#**: Root user prompt

## Essential Commands

### Navigation Commands

**pwd - Print Working Directory**
```bash
# Show current directory
pwd
# Output: /home/username
```

**ls - List Directory Contents**
```bash
# List files and directories
ls

# List with details
ls -l

# List all files (including hidden)
ls -a

# List with human-readable sizes
ls -lh

# List with details and all files
ls -la
```

**cd - Change Directory**
```bash
# Go to home directory
cd
cd ~

# Go to parent directory
cd ..

# Go to root directory
cd /

# Go to specific directory
cd /home/username/Documents

# Go to previous directory
cd -
```

**mkdir - Make Directory**
```bash
# Create single directory
mkdir newfolder

# Create multiple directories
mkdir folder1 folder2 folder3

# Create directory with parents
mkdir -p path/to/new/directory

# Create directory with permissions
mkdir -m 755 newfolder
```

**rmdir - Remove Directory**
```bash
# Remove empty directory
rmdir emptyfolder

# Remove multiple empty directories
rmdir folder1 folder2 folder3
```

### File Operations

**touch - Create Empty File**
```bash
# Create single file
touch filename.txt

# Create multiple files
touch file1.txt file2.txt file3.txt

# Create file with specific timestamp
touch -t 202312251200 filename.txt
```

**cp - Copy Files and Directories**
```bash
# Copy file
cp source.txt destination.txt

# Copy file to directory
cp file.txt /path/to/directory/

# Copy directory recursively
cp -r sourcedir destinationdir

# Copy with preservation of attributes
cp -p source.txt destination.txt

# Copy with verbose output
cp -v source.txt destination.txt
```

**mv - Move/Rename Files and Directories**
```bash
# Rename file
mv oldname.txt newname.txt

# Move file to directory
mv file.txt /path/to/directory/

# Move directory
mv olddir newdir

# Move multiple files
mv file1.txt file2.txt /destination/directory/
```

**rm - Remove Files and Directories**
```bash
# Remove file
rm filename.txt

# Remove multiple files
rm file1.txt file2.txt file3.txt

# Remove directory recursively
rm -r directoryname

# Remove with confirmation
rm -i filename.txt

# Remove with verbose output
rm -v filename.txt

# Remove force (dangerous!)
rm -f filename.txt
```

### File Viewing Commands

**cat - Display File Contents**
```bash
# Display entire file
cat filename.txt

# Display multiple files
cat file1.txt file2.txt

# Display with line numbers
cat -n filename.txt

# Display non-printing characters
cat -A filename.txt
```

**less - View File Page by Page**
```bash
# View file with pagination
less filename.txt

# Navigation in less:
# Space: Next page
# b: Previous page
# q: Quit
# /search: Search for text
# n: Next search result
# g: Go to beginning
# G: Go to end
```

**head - Display First Lines**
```bash
# Display first 10 lines
head filename.txt

# Display first 20 lines
head -n 20 filename.txt

# Display first 5 lines
head -5 filename.txt
```

**tail - Display Last Lines**
```bash
# Display last 10 lines
tail filename.txt

# Display last 20 lines
tail -n 20 filename.txt

# Follow file changes (useful for logs)
tail -f /var/log/syslog
```

### File Search Commands

**find - Search for Files**
```bash
# Find files by name
find /home -name "*.txt"

# Find files by type
find /home -type f

# Find directories
find /home -type d

# Find files by size
find /home -size +100M

# Find files by modification time
find /home -mtime -7

# Find files by permissions
find /home -perm 644

# Execute command on found files
find /home -name "*.txt" -exec ls -l {} \;
```

**locate - Quick File Search**
```bash
# Search for files
locate filename

# Search with pattern
locate "*.txt"

# Update locate database
sudo updatedb

# Case-insensitive search
locate -i filename
```

**grep - Search Text in Files**
```bash
# Search for text in file
grep "search term" filename.txt

# Search in multiple files
grep "search term" *.txt

# Case-insensitive search
grep -i "search term" filename.txt

# Search recursively in directories
grep -r "search term" /path/to/directory/

# Show line numbers
grep -n "search term" filename.txt

# Show context lines
grep -C 3 "search term" filename.txt
```

## File Permissions and Ownership

### Understanding Permissions

**Permission Types:**
- **Read (r)**: Permission to read file contents
- **Write (w)**: Permission to modify file contents
- **Execute (x)**: Permission to execute file as program

**Permission Groups:**
- **Owner (u)**: File owner permissions
- **Group (g)**: Group member permissions
- **Others (o)**: Other users permissions

**Permission Examples:**
- **755**: rwxr-xr-x (Owner: read/write/execute, Group: read/execute, Others: read/execute)
- **644**: rw-r--r-- (Owner: read/write, Group: read, Others: read)
- **777**: rwxrwxrwx (Everyone: read/write/execute)
- **600**: rw------- (Owner: read/write, Group: none, Others: none)

### Permission Commands

**ls -l - View Permissions**
```bash
# Display detailed file information
ls -l

# Output example:
# -rw-r--r-- 1 user group 1024 Dec 25 12:00 filename.txt
# |---------| | | |    |      |  |  |  |  |  |
# |         |  | | |    |      |  |  |  |  |  +-- File name
# |         |  | | |    |      |  |  |  |  +----- Time
# |         |  | | |    |      |  |  |  +-------- Day
# |         |  | | |    |      |  |  +----------- Month
# |         |  | | |    |      |  +-------------- Year
# |         |  | | |    |      +----------------- Size
# |         |  | | |    +------------------------ Group
# |         |  | | +----------------------------- Owner
# |         |  | +------------------------------- Links
# |         |  +---------------------------------- Type
# |         +------------------------------------- Permissions
```

**chmod - Change Permissions**
```bash
# Add execute permission for owner
chmod u+x filename.txt

# Remove write permission for group
chmod g-w filename.txt

# Set permissions using octal notation
chmod 755 filename.txt

# Set permissions for all groups
chmod a+x filename.txt

# Recursively change permissions
chmod -R 755 directory/
```

**chown - Change Ownership**
```bash
# Change file owner
sudo chown newowner filename.txt

# Change owner and group
sudo chown newowner:newgroup filename.txt

# Change only group
sudo chown :newgroup filename.txt

# Recursively change ownership
sudo chown -R newowner:newgroup directory/
```

**chgrp - Change Group**
```bash
# Change file group
sudo chgrp newgroup filename.txt

# Recursively change group
sudo chgrp -R newgroup directory/
```

## Text Processing Commands

### Basic Text Processing

**wc - Word Count**
```bash
# Count lines, words, and characters
wc filename.txt

# Count only lines
wc -l filename.txt

# Count only words
wc -w filename.txt

# Count only characters
wc -c filename.txt
```

**sort - Sort Lines**
```bash
# Sort lines alphabetically
sort filename.txt

# Sort numerically
sort -n filename.txt

# Sort in reverse order
sort -r filename.txt

# Sort by specific field
sort -k 2 filename.txt
```

**uniq - Remove Duplicate Lines**
```bash
# Remove duplicate lines
uniq filename.txt

# Count duplicate lines
uniq -c filename.txt

# Show only unique lines
uniq -u filename.txt

# Show only duplicate lines
uniq -d filename.txt
```

**cut - Extract Columns**
```bash
# Extract first column
cut -d',' -f1 filename.csv

# Extract multiple columns
cut -d',' -f1,3,5 filename.csv

# Extract by character position
cut -c1-10 filename.txt
```

**awk - Text Processing**
```bash
# Print first column
awk '{print $1}' filename.txt

# Print lines with specific condition
awk '$3 > 100 {print}' filename.txt

# Print formatted output
awk '{printf "Name: %s, Age: %s\n", $1, $2}' filename.txt
```

**sed - Stream Editor**
```bash
# Replace text
sed 's/old/new/g' filename.txt

# Delete lines
sed '5d' filename.txt

# Print specific lines
sed -n '10,20p' filename.txt

# Edit file in place
sed -i 's/old/new/g' filename.txt
```

## System Information Commands

### System Information

**uname - System Information**
```bash
# Show system information
uname -a

# Show kernel name
uname -s

# Show kernel version
uname -r

# Show machine hardware
uname -m
```

**whoami - Current User**
```bash
# Show current username
whoami
```

**id - User and Group IDs**
```bash
# Show user and group information
id

# Show specific user information
id username
```

**date - Date and Time**
```bash
# Show current date and time
date

# Show date in specific format
date "+%Y-%m-%d %H:%M:%S"

# Set system date (requires root)
sudo date -s "2023-12-25 12:00:00"
```

**uptime - System Uptime**
```bash
# Show system uptime
uptime

# Show uptime in seconds
cat /proc/uptime
```

### Process Information

**ps - Process Status**
```bash
# Show running processes
ps

# Show all processes
ps aux

# Show processes in tree format
ps auxf

# Show processes for specific user
ps -u username
```

**top - Process Monitor**
```bash
# Show running processes interactively
top

# Show processes for specific user
top -u username

# Show processes sorted by CPU usage
top -o %CPU
```

**htop - Enhanced Process Monitor**
```bash
# Install htop
sudo apt install htop

# Run htop
htop
```

## Network Commands

### Basic Network Commands

**ping - Test Network Connectivity**
```bash
# Ping a host
ping google.com

# Ping with specific count
ping -c 4 google.com

# Ping with specific interval
ping -i 2 google.com
```

**ifconfig - Network Interface Configuration**
```bash
# Show network interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Configure interface (requires root)
sudo ifconfig eth0 192.168.1.100
```

**ip - Modern Network Configuration**
```bash
# Show network interfaces
ip addr show

# Show routing table
ip route show

# Show network statistics
ip -s link show
```

**netstat - Network Statistics**
```bash
# Show listening ports
netstat -tlnp

# Show all connections
netstat -an

# Show routing table
netstat -rn
```

## Package Management

### Ubuntu/Debian Package Management

**apt - Advanced Package Tool**
```bash
# Update package lists
sudo apt update

# Upgrade installed packages
sudo apt upgrade

# Install package
sudo apt install package-name

# Remove package
sudo apt remove package-name

# Remove package and configuration files
sudo apt purge package-name

# Search for packages
apt search package-name

# Show package information
apt show package-name

# List installed packages
apt list --installed
```

**dpkg - Debian Package Manager**
```bash
# Install .deb package
sudo dpkg -i package.deb

# Remove package
sudo dpkg -r package-name

# Show package information
dpkg -l package-name

# List installed packages
dpkg -l
```

### Red Hat/CentOS Package Management

**yum - Yellowdog Updater Modified**
```bash
# Update packages
sudo yum update

# Install package
sudo yum install package-name

# Remove package
sudo yum remove package-name

# Search for packages
yum search package-name

# Show package information
yum info package-name
```

**dnf - Dandified YUM**
```bash
# Update packages
sudo dnf update

# Install package
sudo dnf install package-name

# Remove package
sudo dnf remove package-name

# Search for packages
dnf search package-name
```

## Environment Variables

### Understanding Environment Variables

Environment variables are dynamic values that affect the behavior of processes and programs. They provide a way to pass configuration information to programs.

**Common Environment Variables:**
- **PATH**: Directories to search for executables
- **HOME**: User's home directory
- **USER**: Current username
- **SHELL**: Current shell
- **PWD**: Present working directory
- **LANG**: Language and locale settings

### Environment Variable Commands

**env - Show Environment Variables**
```bash
# Show all environment variables
env

# Show specific variable
env | grep PATH
```

**echo - Display Variable Value**
```bash
# Show variable value
echo $HOME
echo $PATH
echo $USER
```

**export - Set Environment Variable**
```bash
# Set variable for current session
export MY_VAR="value"

# Set variable permanently (add to ~/.bashrc)
echo 'export MY_VAR="value"' >> ~/.bashrc
source ~/.bashrc
```

**unset - Remove Environment Variable**
```bash
# Remove variable
unset MY_VAR
```

## Command History and Shortcuts

### Command History

**history - Command History**
```bash
# Show command history
history

# Show last 10 commands
history 10

# Search history
history | grep "command"
```

**History Shortcuts:**
- **!!**: Execute last command
- **!n**: Execute command number n
- **!string**: Execute last command starting with string
- **Ctrl+R**: Search history interactively

### Keyboard Shortcuts

**Essential Shortcuts:**
- **Ctrl+C**: Cancel current command
- **Ctrl+D**: Exit shell or end input
- **Ctrl+L**: Clear screen
- **Ctrl+A**: Move cursor to beginning of line
- **Ctrl+E**: Move cursor to end of line
- **Ctrl+U**: Delete from cursor to beginning of line
- **Ctrl+K**: Delete from cursor to end of line
- **Ctrl+W**: Delete previous word
- **Alt+B**: Move cursor back one word
- **Alt+F**: Move cursor forward one word
- **Tab**: Auto-complete commands and filenames

## Practice Exercises

### Exercise 1: File System Navigation
**Objective**: Practice basic file system navigation

**Tasks**:
1. Navigate to your home directory
2. Create a new directory called "practice"
3. Create several files in the practice directory
4. List the contents of the practice directory
5. Navigate to different directories and back

**Commands to Practice**:
```bash
cd ~
mkdir practice
cd practice
touch file1.txt file2.txt file3.txt
ls -la
cd ..
pwd
cd practice
```

### Exercise 2: File Operations
**Objective**: Practice file operations

**Tasks**:
1. Create a text file with some content
2. Copy the file to a new location
3. Move the file to another directory
4. Create a backup of the file
5. Delete the original file

**Commands to Practice**:
```bash
echo "Hello World" > test.txt
cp test.txt test_copy.txt
mkdir backup
mv test.txt backup/
cp test_copy.txt backup/test_backup.txt
rm test_copy.txt
```

### Exercise 3: Text Processing
**Objective**: Practice text processing commands

**Tasks**:
1. Create a file with multiple lines of text
2. Count the lines, words, and characters
3. Search for specific text in the file
4. Sort the lines alphabetically
5. Remove duplicate lines

**Commands to Practice**:
```bash
cat > sample.txt << EOF
apple
banana
cherry
apple
date
banana
EOF

wc sample.txt
grep "apple" sample.txt
sort sample.txt
sort sample.txt | uniq
```

### Exercise 4: System Information
**Objective**: Practice system information commands

**Tasks**:
1. Display system information
2. Show current user and groups
3. Display system uptime
4. List running processes
5. Show network interfaces

**Commands to Practice**:
```bash
uname -a
whoami
id
uptime
ps aux
ifconfig
```

## Important Reminders

⚠️ **Remember these key points as you learn Linux basics:**

- **Practice Regularly**: Use the command line daily to build familiarity
- **Read Error Messages**: They often contain helpful information
- **Use Tab Completion**: It saves time and prevents typos
- **Check Man Pages**: Use `man command` for detailed help
- **Be Careful with rm**: It can permanently delete files
- **Use Absolute Paths**: When in doubt, use full paths
- **Backup Important Data**: Always backup before making changes

**You're now ready to explore the powerful world of Linux commands! These basics will serve as the foundation for all your Linux learning.** 🐧✨
