# 06 - User Management

## Introduction to User Management

User management is a fundamental aspect of Linux system administration. It involves creating, modifying, and managing user accounts, groups, and permissions to ensure proper access control and security.

**Think of it as:**
- **Users** = Individual people who can access the system
- **Groups** = Collections of users with similar permissions
- **Permissions** = What users and groups can do
- **Authentication** = Verifying who you are
- **Authorization** = What you're allowed to do

## Understanding Users and Groups

### User Types

**Root User:**
- **UID**: 0 (User ID)
- **GID**: 0 (Group ID)
- **Home Directory**: /root
- **Shell**: Usually /bin/bash
- **Permissions**: Full system access
- **Purpose**: System administration

**System Users:**
- **UID**: 1-999 (varies by distribution)
- **Purpose**: Run system services
- **Home Directory**: Usually /var or /srv
- **Shell**: Usually /bin/false or /usr/sbin/nologin
- **Examples**: www-data, mysql, postgres

**Regular Users:**
- **UID**: 1000+ (varies by distribution)
- **Purpose**: Human users
- **Home Directory**: /home/username
- **Shell**: Usually /bin/bash
- **Permissions**: Limited to their files and directories

### Group Types

**Primary Group:**
- **Definition**: User's default group
- **Purpose**: File ownership and permissions
- **Creation**: Created with user account
- **Name**: Usually same as username

**Secondary Groups:**
- **Definition**: Additional groups user belongs to
- **Purpose**: Access to shared resources
- **Management**: Can be added/removed
- **Examples**: sudo, wheel, adm, users

**System Groups:**
- **Definition**: Groups for system services
- **Purpose**: Service isolation and security
- **Examples**: www-data, mysql, postgres, docker

## User Account Files

### /etc/passwd File

**File Format:**
```
username:x:UID:GID:GECOS:home_directory:shell
```

**Field Descriptions:**
- **username**: Login name
- **x**: Password placeholder (actual password in /etc/shadow)
- **UID**: User ID number
- **GID**: Primary group ID
- **GECOS**: Full name and contact info
- **home_directory**: User's home directory
- **shell**: Login shell

**Example Entry:**
```
john:x:1001:1001:John Doe:/home/john:/bin/bash
```

### /etc/shadow File

**File Format:**
```
username:encrypted_password:last_change:min:max:warn:inactive:expire:reserved
```

**Field Descriptions:**
- **username**: Login name
- **encrypted_password**: Encrypted password hash
- **last_change**: Days since Jan 1, 1970 password was changed
- **min**: Minimum days between password changes
- **max**: Maximum days password is valid
- **warn**: Days to warn before password expires
- **inactive**: Days after password expires before account is disabled
- **expire**: Days since Jan 1, 1970 account expires
- **reserved**: Reserved field

**Example Entry:**
```
john:$6$salt$hash:19000:0:99999:7:::
```

### /etc/group File

**File Format:**
```
group_name:x:GID:user_list
```

**Field Descriptions:**
- **group_name**: Group name
- **x**: Password placeholder
- **GID**: Group ID number
- **user_list**: Comma-separated list of users

**Example Entry:**
```
developers:x:1001:john,jane,bob
```

## User Management Commands

### useradd - Create Users

**Basic User Creation:**
```bash
# Create user with default settings
sudo useradd username

# Create user with specific UID
sudo useradd -u 1001 username

# Create user with specific GID
sudo useradd -g groupname username

# Create user with specific home directory
sudo useradd -d /home/customdir username

# Create user with specific shell
sudo useradd -s /bin/bash username

# Create user with full name
sudo useradd -c "Full Name" username
```

**Advanced User Creation:**
```bash
# Create user with all options
sudo useradd -m -s /bin/bash -c "John Doe" -g users -G sudo,developers john

# Create system user
sudo useradd -r -s /bin/false -d /var/lib/service serviceuser

# Create user with specific UID range
sudo useradd -u 2000 username
```

**User Creation Options:**
- **-m**: Create home directory
- **-s**: Specify shell
- **-c**: Comment (full name)
- **-g**: Primary group
- **-G**: Secondary groups
- **-d**: Home directory
- **-u**: User ID
- **-r**: Create system user
- **-e**: Account expiration date
- **-f**: Password expiration days

### usermod - Modify Users

**Basic Modifications:**
```bash
# Change user's full name
sudo usermod -c "New Full Name" username

# Change user's shell
sudo usermod -s /bin/zsh username

# Change user's home directory
sudo usermod -d /new/home/dir username

# Move home directory contents
sudo usermod -m -d /new/home/dir username

# Change primary group
sudo usermod -g newgroup username

# Add user to secondary group
sudo usermod -a -G groupname username

# Remove user from secondary group
sudo usermod -G group1,group2 username
```

**Advanced Modifications:**
```bash
# Lock user account
sudo usermod -L username

# Unlock user account
sudo usermod -U username

# Set account expiration
sudo usermod -e 2024-12-31 username

# Set password expiration
sudo usermod -f 30 username

# Change user ID
sudo usermod -u 2000 username
```

### userdel - Delete Users

**Basic User Deletion:**
```bash
# Delete user (keep home directory)
sudo userdel username

# Delete user and home directory
sudo userdel -r username

# Force delete user
sudo userdel -f username

# Delete user and remove mail spool
sudo userdel -r -f username
```

### passwd - Password Management

**Password Operations:**
```bash
# Change own password
passwd

# Change another user's password
sudo passwd username

# Lock user password
sudo passwd -l username

# Unlock user password
sudo passwd -u username

# Force password change on next login
sudo passwd -e username

# Set password expiration
sudo passwd -x 90 username

# Set minimum days between password changes
sudo passwd -n 7 username
```

**Password Policy:**
```bash
# Check password status
sudo passwd -S username

# Show password aging information
sudo chage -l username

# Set password aging
sudo chage -M 90 username  # Maximum days
sudo chage -m 7 username   # Minimum days
sudo chage -W 7 username   # Warning days
sudo chage -I 30 username  # Inactive days
sudo chage -E 2024-12-31 username  # Expiration date
```

## Group Management Commands

### groupadd - Create Groups

**Basic Group Creation:**
```bash
# Create group
sudo groupadd groupname

# Create group with specific GID
sudo groupadd -g 1001 groupname

# Create system group
sudo groupadd -r groupname
```

### groupmod - Modify Groups

**Group Modifications:**
```bash
# Change group name
sudo groupmod -n newname oldname

# Change group ID
sudo groupmod -g 2000 groupname
```

### groupdel - Delete Groups

**Group Deletion:**
```bash
# Delete group
sudo groupdel groupname

# Force delete group
sudo groupdel -f groupname
```

### gpasswd - Group Password Management

**Group Password Operations:**
```bash
# Set group password
sudo gpasswd groupname

# Remove group password
sudo gpasswd -r groupname

# Add user to group
sudo gpasswd -a username groupname

# Remove user from group
sudo gpasswd -d username groupname

# Set group administrators
sudo gpasswd -A username groupname
```

## User Information Commands

### id - User and Group Information

**Basic Usage:**
```bash
# Show current user information
id

# Show specific user information
id username

# Show only user ID
id -u username

# Show only group ID
id -g username

# Show all groups
id -G username

# Show group names
id -Gn username
```

### whoami - Current User

**Usage:**
```bash
# Show current username
whoami

# Show current user ID
id -un
```

### who - Logged in Users

**Usage:**
```bash
# Show logged in users
who

# Show detailed information
who -a

# Show last login
who -b

# Show system boot time
who -r
```

### w - Detailed User Information

**Usage:**
```bash
# Show detailed user information
w

# Show specific user
w username

# Show without header
w -h

# Show with short format
w -s
```

### last - Login History

**Usage:**
```bash
# Show login history
last

# Show specific user
last username

# Show last 10 logins
last -n 10

# Show since specific date
last -s 2023-01-01
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

**chmod - Change Permissions:**
```bash
# Add execute permission for owner
chmod u+x filename

# Remove write permission for group
chmod g-w filename

# Set permissions using octal notation
chmod 755 filename

# Set permissions for all groups
chmod a+x filename

# Recursively change permissions
chmod -R 755 directory/
```

**chown - Change Ownership:**
```bash
# Change file owner
sudo chown newowner filename

# Change owner and group
sudo chown newowner:newgroup filename

# Change only group
sudo chown :newgroup filename

# Recursively change ownership
sudo chown -R newowner:newgroup directory/
```

**chgrp - Change Group:**
```bash
# Change file group
sudo chgrp newgroup filename

# Recursively change group
sudo chgrp -R newgroup directory/
```

## Sudo and Root Access

### Understanding Sudo

**Sudo (Super User Do):**
- **Purpose**: Execute commands as another user (usually root)
- **Security**: More secure than direct root login
- **Auditing**: Commands are logged
- **Granular**: Fine-grained permissions

**Sudo Configuration:**
- **File**: /etc/sudoers
- **Editor**: visudo (recommended)
- **Syntax**: username ALL=(ALL) ALL
- **Groups**: %groupname ALL=(ALL) ALL

### Sudo Commands

**Basic Sudo Usage:**
```bash
# Execute command as root
sudo command

# Execute command as another user
sudo -u username command

# Execute command with specific group
sudo -g groupname command

# Execute command in specific directory
sudo -C /path/to/directory command

# Execute command with specific environment
sudo -E command
```

**Sudo Options:**
```bash
# List allowed commands
sudo -l

# Show sudo configuration
sudo -V

# Execute command with password prompt
sudo -S command

# Execute command without password prompt
sudo -N command

# Execute command with specific timeout
sudo -T 300 command
```

### Sudoers Configuration

**Basic Sudoers Entries:**
```bash
# Allow user to run all commands as root
username ALL=(ALL) ALL

# Allow user to run specific commands
username ALL=(ALL) /usr/bin/apt, /usr/bin/dpkg

# Allow user to run commands without password
username ALL=(ALL) NOPASSWD: ALL

# Allow group to run all commands
%groupname ALL=(ALL) ALL

# Allow user to run commands as specific user
username ALL=(username) ALL
```

**Advanced Sudoers Configuration:**
```bash
# Allow user to run commands with specific options
username ALL=(ALL) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade

# Allow user to run commands with specific environment
username ALL=(ALL) SETENV: /usr/bin/docker

# Allow user to run commands with specific working directory
username ALL=(ALL) CWD=/home/username /usr/bin/git

# Allow user to run commands with specific timeout
username ALL=(ALL) TIMEOUT=300 /usr/bin/longrunningcommand
```

## User Environment

### Environment Variables

**Common Environment Variables:**
- **HOME**: User's home directory
- **USER**: Current username
- **SHELL**: Current shell
- **PATH**: Command search path
- **PWD**: Present working directory
- **LANG**: Language and locale

**Setting Environment Variables:**
```bash
# Set for current session
export VARIABLE_NAME="value"

# Set for current user (add to ~/.bashrc)
echo 'export VARIABLE_NAME="value"' >> ~/.bashrc

# Set for all users (add to /etc/environment)
echo 'VARIABLE_NAME="value"' >> /etc/environment
```

### Shell Configuration

**User Shell Files:**
- **~/.bashrc**: Bash configuration for interactive shells
- **~/.bash_profile**: Bash configuration for login shells
- **~/.profile**: Shell-agnostic configuration
- **~/.bash_logout**: Commands to run on logout

**System Shell Files:**
- **/etc/bash.bashrc**: System-wide bash configuration
- **/etc/profile**: System-wide shell configuration
- **/etc/environment**: System-wide environment variables

### Home Directory Structure

**Standard Home Directory:**
```
/home/username/
├── Desktop/          # Desktop files
├── Documents/        # Document files
├── Downloads/        # Downloaded files
├── Music/           # Music files
├── Pictures/        # Picture files
├── Videos/          # Video files
├── .bashrc          # Bash configuration
├── .bash_profile    # Bash login configuration
├── .profile         # Shell configuration
├── .ssh/            # SSH keys and configuration
└── .local/          # Local user data
```

## Practice Exercises

### Exercise 1: User Creation and Management
**Objective**: Practice creating and managing users

**Tasks**:
1. Create a new user with home directory
2. Set a password for the user
3. Add the user to additional groups
4. Modify user information
5. Test user login

**Commands to Practice**:
```bash
# Create user
sudo useradd -m -s /bin/bash -c "Test User" testuser

# Set password
sudo passwd testuser

# Add to groups
sudo usermod -a -G sudo,users testuser

# Modify user
sudo usermod -c "Updated Test User" testuser

# Check user info
id testuser
```

### Exercise 2: Group Management
**Objective**: Practice creating and managing groups

**Tasks**:
1. Create a new group
2. Add users to the group
3. Create files with group ownership
4. Test group permissions
5. Remove users from group

**Commands to Practice**:
```bash
# Create group
sudo groupadd developers

# Add users to group
sudo usermod -a -G developers testuser

# Create file with group ownership
sudo touch /tmp/groupfile
sudo chown :developers /tmp/groupfile
sudo chmod 660 /tmp/groupfile

# Test group access
groups testuser
```

### Exercise 3: Permission Management
**Objective**: Practice managing file permissions

**Tasks**:
1. Create files with different permissions
2. Test access with different users
3. Change file ownership
4. Modify permissions
5. Test permission changes

**Commands to Practice**:
```bash
# Create test files
touch file1.txt file2.txt file3.txt

# Set different permissions
chmod 755 file1.txt
chmod 644 file2.txt
chmod 600 file3.txt

# Change ownership
sudo chown testuser:developers file1.txt

# Test permissions
ls -l file*.txt
```

### Exercise 4: Sudo Configuration
**Objective**: Practice configuring sudo access

**Tasks**:
1. Check current sudo configuration
2. Create a sudoers entry for a user
3. Test sudo access
4. Configure sudo for specific commands
5. Test restricted sudo access

**Commands to Practice**:
```bash
# Check sudo configuration
sudo -l

# Edit sudoers file
sudo visudo

# Add user to sudo group
sudo usermod -a -G sudo testuser

# Test sudo access
sudo whoami
```

## Important Reminders

⚠️ **Remember these key points about user management:**

- **Principle of Least Privilege**: Give users only the permissions they need
- **Regular Audits**: Review user accounts and permissions regularly
- **Strong Passwords**: Enforce strong password policies
- **Account Expiration**: Set expiration dates for temporary accounts
- **Monitor Access**: Keep track of user logins and activities
- **Backup User Data**: Always backup important user data
- **Document Changes**: Keep records of user management changes

**User management is crucial for system security and organization. Proper user management ensures that users have appropriate access while maintaining system security.** 🐧✨
