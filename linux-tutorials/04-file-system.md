# 04 - Linux File System

## Understanding the Linux File System

The Linux file system is a hierarchical structure that organizes files and directories in a tree-like format. Unlike Windows, which uses drive letters, Linux uses a single root directory (/) from which all other directories branch out.

**Think of it as:**
- **Root Directory (/)**: The foundation of the file system tree
- **Directories**: Folders that can contain files and other directories
- **Files**: Data stored on the system
- **Links**: References to files or directories
- **Mount Points**: Locations where other file systems are attached

## File System Hierarchy

### Root Directory Structure

**Essential Directories:**
- **/**: Root directory (top-level)
- **/bin**: Essential binary executables
- **/sbin**: System binary executables
- **/etc**: System configuration files
- **/home**: User home directories
- **/var**: Variable data files
- **/tmp**: Temporary files
- **/usr**: User programs and data
- **/opt**: Optional software packages
- **/dev**: Device files
- **/proc**: Process information
- **/sys**: System information
- **/boot**: Boot loader files
- **/lib**: Essential shared libraries
- **/media**: Removable media mount points
- **/mnt**: Temporary mount points
- **/root**: Root user's home directory
- **/run**: Runtime data
- **/srv**: Service data
- **/lost+found**: Recovered files

### Directory Purposes

**System Directories:**
- **/bin**: Contains essential commands like ls, cp, mv, rm
- **/sbin**: Contains system administration commands like ifconfig, mount
- **/lib**: Contains shared libraries needed by programs
- **/etc**: Contains system configuration files
- **/dev**: Contains device files representing hardware

**User Directories:**
- **/home**: Contains user home directories
- **/usr**: Contains user programs, libraries, and documentation
- **/opt**: Contains optional software packages
- **/tmp**: Contains temporary files (cleared on reboot)

**Variable Directories:**
- **/var**: Contains variable data like logs, mail, spool files
- **/proc**: Contains process and system information
- **/sys**: Contains system information and configuration

## File Types

### Regular Files

**Text Files:**
- **Plain Text**: Configuration files, scripts, documentation
- **Source Code**: Programming language files
- **Log Files**: System and application logs
- **Data Files**: CSV, JSON, XML files

**Binary Files:**
- **Executables**: Compiled programs
- **Libraries**: Shared libraries (.so files)
- **Images**: Pictures, icons, graphics
- **Archives**: Compressed files (.tar, .zip, .gz)

### Directory Files

**Directory Characteristics:**
- **Container**: Can contain files and other directories
- **Permissions**: Has read, write, execute permissions
- **Size**: Shows as 4096 bytes (block size)
- **Links**: Contains entries for files and subdirectories

### Special Files

**Device Files:**
- **Block Devices**: Hard drives, USB drives (/dev/sda, /dev/sdb)
- **Character Devices**: Terminals, serial ports (/dev/tty, /dev/null)
- **Network Devices**: Network interfaces (/dev/eth0)

**Symbolic Links:**
- **Soft Links**: Pointers to other files or directories
- **Broken Links**: Point to non-existent files
- **Relative Links**: Point to files relative to link location
- **Absolute Links**: Point to files with absolute paths

**Named Pipes:**
- **FIFO**: First In, First Out communication
- **Process Communication**: Allows processes to communicate
- **Temporary**: Created and destroyed as needed

## File Permissions

### Permission System

**Permission Types:**
- **Read (r)**: Permission to read file contents
- **Write (w)**: Permission to modify file contents
- **Execute (x)**: Permission to execute file as program

**Permission Groups:**
- **Owner (u)**: File owner permissions
- **Group (g)**: Group member permissions
- **Others (o)**: Other users permissions

**Permission Representation:**
- **Symbolic**: rwxr-xr-x
- **Octal**: 755
- **Binary**: 111 101 101

### Common Permission Examples

**File Permissions:**
- **644**: rw-r--r-- (Owner: read/write, Group: read, Others: read)
- **755**: rwxr-xr-x (Owner: read/write/execute, Group: read/execute, Others: read/execute)
- **600**: rw------- (Owner: read/write, Group: none, Others: none)
- **777**: rwxrwxrwx (Everyone: read/write/execute)

**Directory Permissions:**
- **755**: rwxr-xr-x (Owner: full access, Group: read/execute, Others: read/execute)
- **700**: rwx------ (Owner: full access, Group: none, Others: none)
- **755**: rwxr-xr-x (Standard directory permissions)

### Special Permissions

**Setuid (s):**
- **Purpose**: Execute file with owner's privileges
- **Example**: /usr/bin/passwd
- **Octal**: 4755
- **Symbolic**: rwsr-xr-x

**Setgid (s):**
- **Purpose**: Execute file with group's privileges
- **Example**: /usr/bin/wall
- **Octal**: 2755
- **Symbolic**: rwxr-sr-x

**Sticky Bit (t):**
- **Purpose**: Only owner can delete files in directory
- **Example**: /tmp directory
- **Octal**: 1777
- **Symbolic**: rwxrwxrwt

## File Ownership

### Ownership Concepts

**File Owner:**
- **Creator**: User who created the file
- **Rights**: Can change permissions and ownership
- **Identification**: Shown in ls -l output

**File Group:**
- **Membership**: Users who belong to the group
- **Rights**: Can have group-specific permissions
- **Management**: Controlled by system administrator

**Other Users:**
- **Everyone Else**: Users not in owner or group
- **Rights**: Limited by "others" permissions
- **Security**: Last line of permission defense

### Ownership Commands

**chown - Change Ownership:**
```bash
# Change file owner
sudo chown newowner filename.txt

# Change owner and group
sudo chown newowner:newgroup filename.txt

# Change only group
sudo chown :newgroup filename.txt

# Recursively change ownership
sudo chown -R newowner:newgroup directory/

# Change ownership with reference
sudo chown --reference=referencefile targetfile
```

**chgrp - Change Group:**
```bash
# Change file group
sudo chgrp newgroup filename.txt

# Recursively change group
sudo chgrp -R newgroup directory/

# Change group with reference
sudo chgrp --reference=referencefile targetfile
```

## File System Navigation

### Navigation Commands

**pwd - Print Working Directory:**
```bash
# Show current directory
pwd

# Show current directory with symbolic links resolved
pwd -P
```

**ls - List Directory Contents:**
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

# List with color coding
ls --color=auto

# List with file type indicators
ls -F

# List with inode numbers
ls -i

# List with modification time
ls -lt

# List with reverse order
ls -ltr
```

**cd - Change Directory:**
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

# Go to directory with spaces
cd "My Documents"

# Go to directory with tab completion
cd /home/username/Doc<Tab>
```

### Path Types

**Absolute Paths:**
- **Definition**: Complete path from root directory
- **Example**: /home/username/Documents/file.txt
- **Use**: When you know the exact location

**Relative Paths:**
- **Definition**: Path relative to current directory
- **Example**: Documents/file.txt (from /home/username)
- **Use**: When working within a directory structure

**Path Components:**
- **. (dot)**: Current directory
- **.. (dot dot)**: Parent directory
- **~ (tilde)**: Home directory
- **- (dash)**: Previous directory

## File Operations

### File Creation

**touch - Create Empty File:**
```bash
# Create single file
touch filename.txt

# Create multiple files
touch file1.txt file2.txt file3.txt

# Create file with specific timestamp
touch -t 202312251200 filename.txt

# Create file with reference timestamp
touch -r referencefile newfile.txt
```

**echo - Create File with Content:**
```bash
# Create file with single line
echo "Hello World" > filename.txt

# Create file with multiple lines
echo -e "Line 1\nLine 2\nLine 3" > filename.txt

# Append to file
echo "New line" >> filename.txt
```

**cat - Create File Interactively:**
```bash
# Create file with cat
cat > filename.txt
# Type content
# Press Ctrl+D to save and exit

# Create file with heredoc
cat << EOF > filename.txt
Line 1
Line 2
Line 3
EOF
```

### File Copying

**cp - Copy Files:**
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

# Copy with interactive confirmation
cp -i source.txt destination.txt

# Copy with backup
cp --backup=numbered source.txt destination.txt

# Copy with update (only if source is newer)
cp -u source.txt destination.txt
```

### File Moving and Renaming

**mv - Move/Rename Files:**
```bash
# Rename file
mv oldname.txt newname.txt

# Move file to directory
mv file.txt /path/to/directory/

# Move directory
mv olddir newdir

# Move multiple files
mv file1.txt file2.txt /destination/directory/

# Move with backup
mv --backup=numbered source.txt destination.txt

# Move with interactive confirmation
mv -i source.txt destination.txt
```

### File Deletion

**rm - Remove Files:**
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

# Remove with backup
rm --backup=numbered filename.txt
```

## File Searching

### find Command

**Basic find Usage:**
```bash
# Find files by name
find /home -name "*.txt"

# Find files by type
find /home -type f

# Find directories
find /home -type d

# Find symbolic links
find /home -type l

# Find files by size
find /home -size +100M

# Find files by modification time
find /home -mtime -7

# Find files by permissions
find /home -perm 644

# Find files by owner
find /home -user username

# Find files by group
find /home -group groupname
```

**Advanced find Usage:**
```bash
# Find and execute command
find /home -name "*.txt" -exec ls -l {} \;

# Find and delete
find /home -name "*.tmp" -delete

# Find and move
find /home -name "*.log" -exec mv {} /var/log/ \;

# Find with multiple conditions
find /home -name "*.txt" -size +1M -mtime -30

# Find with exclude directories
find /home -name "*.txt" -not -path "*/.*"
```

### locate Command

**locate Usage:**
```bash
# Search for files
locate filename

# Search with pattern
locate "*.txt"

# Case-insensitive search
locate -i filename

# Limit results
locate -n 10 filename

# Show only existing files
locate -e filename

# Update locate database
sudo updatedb
```

### grep Command

**grep Usage:**
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

# Show only filenames
grep -l "search term" *.txt

# Count matches
grep -c "search term" filename.txt
```

## File Compression and Archiving

### tar Command

**Creating Archives:**
```bash
# Create tar archive
tar -cvf archive.tar file1.txt file2.txt

# Create compressed tar archive
tar -czvf archive.tar.gz file1.txt file2.txt

# Create bzip2 compressed archive
tar -cjvf archive.tar.bz2 file1.txt file2.txt

# Create archive of directory
tar -czvf backup.tar.gz /home/username/
```

**Extracting Archives:**
```bash
# Extract tar archive
tar -xvf archive.tar

# Extract compressed tar archive
tar -xzvf archive.tar.gz

# Extract bzip2 compressed archive
tar -xjvf archive.tar.bz2

# Extract to specific directory
tar -xzvf archive.tar.gz -C /destination/directory/
```

### zip/unzip Commands

**zip Usage:**
```bash
# Create zip archive
zip archive.zip file1.txt file2.txt

# Create zip archive of directory
zip -r archive.zip directory/

# Add files to existing zip
zip archive.zip newfile.txt

# Update files in zip
zip -u archive.zip file1.txt
```

**unzip Usage:**
```bash
# Extract zip archive
unzip archive.zip

# Extract to specific directory
unzip archive.zip -d /destination/directory/

# List zip contents
unzip -l archive.zip

# Extract specific files
unzip archive.zip file1.txt file2.txt
```

## File System Monitoring

### Disk Usage

**df - Disk Free Space:**
```bash
# Show disk usage
df

# Show disk usage in human-readable format
df -h

# Show disk usage for specific filesystem
df -h /home

# Show disk usage with inodes
df -i
```

**du - Disk Usage:**
```bash
# Show directory usage
du

# Show directory usage in human-readable format
du -h

# Show directory usage summary
du -sh

# Show directory usage with depth limit
du -h --max-depth=2

# Show directory usage sorted by size
du -h | sort -hr
```

### File System Health

**fsck - File System Check:**
```bash
# Check filesystem (unmounted)
sudo fsck /dev/sda1

# Check filesystem with repair
sudo fsck -y /dev/sda1

# Check filesystem with verbose output
sudo fsck -v /dev/sda1
```

**mount - Mount File Systems:**
```bash
# Show mounted filesystems
mount

# Show mounted filesystems in specific format
mount -t ext4

# Mount filesystem
sudo mount /dev/sda1 /mnt

# Unmount filesystem
sudo umount /mnt
```

## Practice Exercises

### Exercise 1: File System Navigation
**Objective**: Practice navigating the file system

**Tasks**:
1. Navigate to the root directory
2. List all directories in root
3. Navigate to your home directory
4. Create a practice directory
5. Navigate to the practice directory

**Commands to Practice**:
```bash
cd /
ls -la
cd ~
mkdir practice
cd practice
pwd
```

### Exercise 2: File Operations
**Objective**: Practice file operations

**Tasks**:
1. Create several text files
2. Copy files to a subdirectory
3. Move files to another location
4. Create symbolic links
5. Delete some files

**Commands to Practice**:
```bash
touch file1.txt file2.txt file3.txt
mkdir subdir
cp file1.txt subdir/
mv file2.txt subdir/
ln -s file3.txt link_to_file3.txt
rm file3.txt
```

### Exercise 3: File Permissions
**Objective**: Practice file permissions

**Tasks**:
1. Create a file with default permissions
2. Change file permissions using chmod
3. Create a directory with specific permissions
4. Test access with different permissions
5. Change file ownership

**Commands to Practice**:
```bash
touch testfile.txt
ls -l testfile.txt
chmod 755 testfile.txt
mkdir testdir
chmod 700 testdir
sudo chown root testfile.txt
```

### Exercise 4: File Searching
**Objective**: Practice file searching

**Tasks**:
1. Create files with different extensions
2. Search for files by name
3. Search for files by size
4. Search for text within files
5. Use locate to find files

**Commands to Practice**:
```bash
touch file1.txt file2.log file3.conf
find . -name "*.txt"
find . -size +0c
grep "test" *.txt
locate file1.txt
```

## Important Reminders

⚠️ **Remember these key points about the Linux file system:**

- **Case Sensitive**: Linux file names are case sensitive
- **No Drive Letters**: Everything is under the root directory
- **Hidden Files**: Files starting with . are hidden
- **Permissions Matter**: Always check file permissions
- **Backup Important Data**: Always backup before making changes
- **Use Absolute Paths**: When in doubt, use full paths
- **Be Careful with rm**: It can permanently delete files

**You now have a solid understanding of the Linux file system! This knowledge will be essential for all your Linux administration tasks.** 🐧✨
