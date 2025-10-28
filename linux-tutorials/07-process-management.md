# 07 - Process Management

## Introduction to Process Management

Process management is a core function of Linux system administration. It involves monitoring, controlling, and optimizing running processes to ensure system stability and performance.

**Think of it as:**
- **Processes** = Running programs and services
- **Process ID (PID)** = Unique identifier for each process
- **Parent Process** = Process that created another process
- **Child Process** = Process created by another process
- **Process Tree** = Hierarchical structure of processes

## Understanding Processes

### What is a Process?

A process is an instance of a running program. Each process has its own memory space, file descriptors, and system resources. Processes are the fundamental units of execution in Linux.

**Process Characteristics:**
- **Unique PID**: Each process has a unique Process ID
- **Memory Space**: Isolated memory area
- **File Descriptors**: Access to files and devices
- **Environment**: Variables and settings
- **State**: Running, sleeping, stopped, zombie

### Process States

**Running States:**
- **R (Running)**: Process is currently executing
- **S (Sleeping)**: Process is waiting for an event
- **D (Uninterruptible Sleep)**: Process is waiting for I/O
- **T (Stopped)**: Process is stopped by a signal
- **Z (Zombie)**: Process has terminated but parent hasn't read exit status

**Process State Transitions:**
- **Created → Ready**: Process is loaded into memory
- **Ready → Running**: Process is scheduled to run
- **Running → Waiting**: Process waits for I/O or event
- **Waiting → Ready**: I/O or event completes
- **Running → Terminated**: Process completes or is killed

### Process Types

**Foreground Processes:**
- **Definition**: Processes that run in the terminal
- **Characteristics**: Block terminal input until completion
- **Control**: Can be interrupted with Ctrl+C
- **Examples**: Commands like ls, cat, grep

**Background Processes:**
- **Definition**: Processes that run without blocking terminal
- **Characteristics**: Run independently of terminal
- **Control**: Can be brought to foreground or managed separately
- **Examples**: Web servers, databases, daemons

**Daemon Processes:**
- **Definition**: System service processes
- **Characteristics**: Run in background, no controlling terminal
- **Purpose**: Provide system services
- **Examples**: sshd, httpd, mysqld

## Process Information Commands

### ps - Process Status

**Basic ps Usage:**
```bash
# Show processes for current user
ps

# Show all processes
ps aux

# Show processes in tree format
ps auxf

# Show processes for specific user
ps -u username

# Show processes with custom format
ps -eo pid,ppid,cmd,%mem,%cpu
```

**ps Options:**
- **a**: Show processes for all users
- **u**: Show user-oriented format
- **x**: Show processes without controlling terminal
- **f**: Show full format
- **e**: Show all processes
- **o**: Specify output format

**Common ps Formats:**
```bash
# Show PID, command, and CPU usage
ps -eo pid,cmd,%cpu

# Show PID, user, command, and memory usage
ps -eo pid,user,cmd,%mem

# Show processes with parent-child relationships
ps -eo pid,ppid,cmd
```

### top - Process Monitor

**Basic top Usage:**
```bash
# Start top
top

# Show processes for specific user
top -u username

# Show processes sorted by CPU usage
top -o %CPU

# Show processes sorted by memory usage
top -o %MEM

# Show processes with specific PID
top -p 1234
```

**top Interactive Commands:**
- **h**: Show help
- **q**: Quit top
- **k**: Kill process
- **r**: Renice process
- **s**: Change update delay
- **M**: Sort by memory usage
- **P**: Sort by CPU usage
- **T**: Sort by time
- **u**: Show processes for specific user
- **c**: Toggle command line display

### htop - Enhanced Process Monitor

**Installation:**
```bash
# Ubuntu/Debian
sudo apt install htop

# CentOS/RHEL
sudo yum install htop

# Fedora
sudo dnf install htop

# Arch Linux
sudo pacman -S htop
```

**htop Features:**
- **Color-coded**: Different colors for different process types
- **Interactive**: Mouse support and intuitive interface
- **Search**: Find processes by name
- **Tree view**: Show process hierarchy
- **Kill processes**: Right-click to kill processes
- **Renice processes**: Change process priority

### pstree - Process Tree

**Basic Usage:**
```bash
# Show process tree
pstree

# Show process tree with PIDs
pstree -p

# Show process tree for specific user
pstree -u username

# Show process tree with command arguments
pstree -a
```

## Process Control Commands

### kill - Terminate Processes

**Basic kill Usage:**
```bash
# Kill process by PID
kill 1234

# Kill process with specific signal
kill -9 1234

# Kill process by name
killall processname

# Kill process with specific signal by name
killall -9 processname
```

**Kill Signals:**
- **SIGTERM (15)**: Termination signal (default)
- **SIGKILL (9)**: Force kill signal
- **SIGHUP (1)**: Hangup signal
- **SIGINT (2)**: Interrupt signal (Ctrl+C)
- **SIGSTOP (19)**: Stop signal
- **SIGCONT (18)**: Continue signal

**Signal Usage:**
```bash
# Send SIGTERM (graceful termination)
kill -TERM 1234
kill -15 1234

# Send SIGKILL (force termination)
kill -KILL 1234
kill -9 1234

# Send SIGHUP (reload configuration)
kill -HUP 1234
kill -1 1234

# Send SIGSTOP (pause process)
kill -STOP 1234
kill -19 1234

# Send SIGCONT (resume process)
kill -CONT 1234
kill -18 1234
```

### pkill - Kill Processes by Pattern

**Basic pkill Usage:**
```bash
# Kill processes by name pattern
pkill firefox

# Kill processes by full command line
pkill -f "python script.py"

# Kill processes for specific user
pkill -u username firefox

# Kill processes with specific signal
pkill -9 firefox

# Kill processes with confirmation
pkill -i firefox
```

### killall - Kill All Processes by Name

**Basic killall Usage:**
```bash
# Kill all processes with name
killall firefox

# Kill all processes with specific signal
killall -9 firefox

# Kill all processes for specific user
killall -u username firefox

# Kill all processes with confirmation
killall -i firefox

# Kill all processes except current shell
killall -v firefox
```

## Process Priority and Nice Values

### Understanding Nice Values

**Nice Value Range:**
- **-20 to 19**: Nice value range
- **-20**: Highest priority (most favorable)
- **0**: Default priority
- **19**: Lowest priority (least favorable)

**Priority Levels:**
- **High Priority**: -20 to -1
- **Normal Priority**: 0
- **Low Priority**: 1 to 19

### nice - Set Process Priority

**Basic nice Usage:**
```bash
# Run command with low priority
nice command

# Run command with specific nice value
nice -n 10 command

# Run command with high priority (requires root)
sudo nice -n -10 command
```

### renice - Change Process Priority

**Basic renice Usage:**
```bash
# Change nice value for process
renice 10 1234

# Change nice value for all processes of user
renice 10 -u username

# Change nice value for all processes in group
renice 10 -g groupname

# Change nice value for all processes with name
renice 10 -p 1234,5678,9012
```

## Background and Foreground Processes

### Job Control

**Background Processes:**
```bash
# Run command in background
command &

# Run command in background with output redirection
command > output.log 2>&1 &

# Show background jobs
jobs

# Bring job to foreground
fg %1

# Send job to background
bg %1
```

**Job Control Commands:**
- **&**: Run command in background
- **jobs**: Show current jobs
- **fg**: Bring job to foreground
- **bg**: Send job to background
- **Ctrl+Z**: Suspend current job
- **Ctrl+C**: Terminate current job

### nohup - Run Commands Immune to Hangups

**Basic nohup Usage:**
```bash
# Run command immune to hangups
nohup command &

# Run command with output redirection
nohup command > output.log 2>&1 &

# Run command with no output
nohup command > /dev/null 2>&1 &
```

### screen - Terminal Multiplexer

**Installation:**
```bash
# Ubuntu/Debian
sudo apt install screen

# CentOS/RHEL
sudo yum install screen

# Fedora
sudo dnf install screen

# Arch Linux
sudo pacman -S screen
```

**Basic screen Usage:**
```bash
# Start new screen session
screen

# Start screen session with name
screen -S sessionname

# List screen sessions
screen -ls

# Attach to screen session
screen -r sessionname

# Detach from screen session
Ctrl+A, D
```

**Screen Commands:**
- **Ctrl+A, C**: Create new window
- **Ctrl+A, N**: Next window
- **Ctrl+A, P**: Previous window
- **Ctrl+A, D**: Detach session
- **Ctrl+A, K**: Kill current window
- **Ctrl+A, ?**: Show help

### tmux - Terminal Multiplexer

**Installation:**
```bash
# Ubuntu/Debian
sudo apt install tmux

# CentOS/RHEL
sudo yum install tmux

# Fedora
sudo dnf install tmux

# Arch Linux
sudo pacman -S tmux
```

**Basic tmux Usage:**
```bash
# Start new tmux session
tmux

# Start tmux session with name
tmux new-session -s sessionname

# List tmux sessions
tmux list-sessions

# Attach to tmux session
tmux attach-session -t sessionname

# Detach from tmux session
Ctrl+B, D
```

## System Monitoring

### System Load

**Load Average:**
```bash
# Show load average
uptime

# Show load average with more detail
w

# Show load average from /proc/loadavg
cat /proc/loadavg
```

**Load Average Interpretation:**
- **1.0**: System is fully utilized
- **< 1.0**: System has spare capacity
- **> 1.0**: System is overloaded
- **3 values**: 1, 5, and 15 minute averages

### Memory Usage

**Memory Information:**
```bash
# Show memory usage
free -h

# Show memory usage in MB
free -m

# Show memory usage in GB
free -g

# Show memory usage continuously
watch -n 1 free -h
```

**Memory Types:**
- **Total**: Total physical memory
- **Used**: Memory currently in use
- **Free**: Memory not in use
- **Available**: Memory available for new processes
- **Shared**: Memory shared between processes
- **Buffers**: Memory used for buffers
- **Cached**: Memory used for cache

### CPU Usage

**CPU Information:**
```bash
# Show CPU information
cat /proc/cpuinfo

# Show CPU usage
top

# Show CPU usage with specific format
ps -eo pid,cmd,%cpu --sort=-%cpu | head -10
```

**CPU Monitoring:**
```bash
# Monitor CPU usage
htop

# Monitor CPU usage with specific interval
top -d 1

# Monitor CPU usage for specific process
top -p 1234
```

## Process Troubleshooting

### Common Process Issues

**High CPU Usage:**
```bash
# Find processes with high CPU usage
ps aux --sort=-%cpu | head -10

# Monitor CPU usage in real-time
top -o %CPU

# Kill process with high CPU usage
kill -9 1234
```

**High Memory Usage:**
```bash
# Find processes with high memory usage
ps aux --sort=-%mem | head -10

# Monitor memory usage in real-time
top -o %MEM

# Check memory usage by process
pmap 1234
```

**Zombie Processes:**
```bash
# Find zombie processes
ps aux | grep -w Z

# Kill parent process to clean up zombies
kill -9 parent_pid
```

**Stuck Processes:**
```bash
# Find processes in uninterruptible sleep
ps aux | grep -w D

# Force kill stuck processes
kill -9 1234
```

### Process Debugging

**strace - System Call Tracer:**
```bash
# Trace system calls
strace command

# Trace system calls with output to file
strace -o trace.log command

# Trace system calls for running process
strace -p 1234
```

**ltrace - Library Call Tracer:**
```bash
# Trace library calls
ltrace command

# Trace library calls with output to file
ltrace -o trace.log command
```

**gdb - GNU Debugger:**
```bash
# Attach to running process
gdb -p 1234

# Debug core dump
gdb program core
```

## Practice Exercises

### Exercise 1: Process Monitoring
**Objective**: Practice monitoring processes

**Tasks**:
1. Start several processes
2. Monitor them with different tools
3. Identify processes with high resource usage
4. Practice killing processes
5. Monitor system load

**Commands to Practice**:
```bash
# Start processes
firefox &
gedit &
gnome-calculator &

# Monitor processes
ps aux
top
htop

# Kill processes
kill %1
killall firefox
```

### Exercise 2: Process Priority Management
**Objective**: Practice managing process priorities

**Tasks**:
1. Start processes with different priorities
2. Change process priorities
3. Monitor process behavior
4. Test priority effects
5. Reset priorities

**Commands to Practice**:
```bash
# Start processes with different priorities
nice -n 10 firefox &
nice -n -5 gedit &

# Check priorities
ps -eo pid,cmd,ni

# Change priorities
renice 15 1234
renice -10 5678
```

### Exercise 3: Background Process Management
**Objective**: Practice managing background processes

**Tasks**:
1. Start processes in background
2. Manage job control
3. Use screen/tmux
4. Practice process suspension
5. Test process resumption

**Commands to Practice**:
```bash
# Start background processes
firefox &
gedit &

# Manage jobs
jobs
fg %1
bg %1

# Use screen
screen -S test
# Start process
# Detach with Ctrl+A, D
screen -r test
```

### Exercise 4: Process Troubleshooting
**Objective**: Practice troubleshooting process issues

**Tasks**:
1. Create high CPU usage scenario
2. Monitor system resources
3. Identify problematic processes
4. Apply troubleshooting techniques
5. Document solutions

**Commands to Practice**:
```bash
# Create high CPU usage
yes > /dev/null &

# Monitor system
top
htop
ps aux --sort=-%cpu

# Kill problematic process
kill -9 1234
```

## Important Reminders

⚠️ **Remember these key points about process management:**

- **Monitor Regularly**: Keep an eye on system processes and resources
- **Use Appropriate Tools**: Choose the right tool for the task
- **Be Careful with kill -9**: Only use as last resort
- **Understand Process States**: Know what different states mean
- **Manage Resources**: Monitor CPU, memory, and I/O usage
- **Document Issues**: Keep records of process problems and solutions
- **Test Changes**: Always test process management changes

**Process management is essential for maintaining system stability and performance. Understanding how to monitor, control, and optimize processes will make you a more effective Linux administrator.** 🐧✨
