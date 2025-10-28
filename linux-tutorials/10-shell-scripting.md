# 10 - Shell Scripting

## Introduction to Shell Scripting

Shell scripting is the art of writing programs using shell commands to automate tasks, create utilities, and build complex workflows. It's one of the most powerful tools in a Linux administrator's arsenal.

**Think of it as:**
- **Scripts** = Programs written in shell language
- **Automation** = Making repetitive tasks automatic
- **Variables** = Containers for storing data
- **Functions** = Reusable blocks of code
- **Loops** = Repeating actions multiple times

## Shell Scripting Basics

### What is a Shell Script?

A shell script is a text file containing a sequence of shell commands that are executed in order. It can include:
- **Commands**: Regular shell commands
- **Variables**: Data storage
- **Control Structures**: Loops and conditionals
- **Functions**: Reusable code blocks
- **Comments**: Documentation

### Shell Types

**Bash (Bourne Again Shell):**
- **Default**: Most Linux distributions
- **Features**: Advanced features, arrays, functions
- **Compatibility**: POSIX compliant
- **Use Cases**: General purpose scripting

**Zsh (Z Shell):**
- **Features**: Advanced completion, themes
- **Compatibility**: Bash compatible
- **Use Cases**: Interactive use and scripting

**Dash (Debian Almquist Shell):**
- **Features**: Lightweight, fast
- **Compatibility**: POSIX compliant
- **Use Cases**: System scripts, init scripts

**Fish (Friendly Interactive Shell):**
- **Features**: User-friendly, syntax highlighting
- **Compatibility**: Limited compatibility
- **Use Cases**: Interactive use

### Creating Shell Scripts

**Script Structure:**
```bash
#!/bin/bash
# Script description
# Author: Your Name
# Date: 2023-12-25

# Variable declarations
VARIABLE="value"

# Function definitions
function_name() {
    # Function body
}

# Main script logic
echo "Hello, World!"
```

**Making Scripts Executable:**
```bash
# Make script executable
chmod +x script.sh

# Run script
./script.sh

# Run script with bash
bash script.sh
```

## Variables and Data Types

### Variable Declaration

**Basic Variables:**
```bash
# String variable
NAME="John Doe"

# Numeric variable
AGE=25

# Boolean variable
IS_ACTIVE=true

# Array variable
FRUITS=("apple" "banana" "cherry")

# Environment variable
export DATABASE_URL="mysql://localhost:3306/mydb"
```

**Variable Naming Rules:**
- **Start with letter or underscore**
- **Contain letters, numbers, underscores**
- **Case sensitive**
- **No spaces around = sign**

### Variable Usage

**Accessing Variables:**
```bash
# Basic access
echo $NAME

# Curly braces (recommended)
echo ${NAME}

# Array access
echo ${FRUITS[0]}

# Array length
echo ${#FRUITS[@]}

# All array elements
echo ${FRUITS[@]}
```

**Variable Modifiers:**
```bash
# Default value
echo ${NAME:-"Default Name"}

# Assign default value
echo ${NAME:="Default Name"}

# Error if unset
echo ${NAME:?"Name is required"}

# Remove shortest match from beginning
echo ${NAME#John }

# Remove longest match from beginning
echo ${NAME##John }

# Remove shortest match from end
echo ${NAME% Doe}

# Remove longest match from end
echo ${NAME%% Doe}
```

### Special Variables

**Built-in Variables:**
```bash
# Script arguments
echo $0    # Script name
echo $1    # First argument
echo $2    # Second argument
echo $#    # Number of arguments
echo $@    # All arguments
echo $*    # All arguments as single string

# Process information
echo $$    # Process ID
echo $!    # Last background process ID
echo $?    # Exit status of last command

# Script information
echo $0    # Script name
echo $BASH_VERSION    # Bash version
echo $HOME    # Home directory
echo $PWD     # Current directory
```

## Input and Output

### Reading Input

**Basic Input:**
```bash
# Read single input
read -p "Enter your name: " NAME
echo "Hello, $NAME!"

# Read multiple inputs
read -p "Enter first name: " FIRST_NAME
read -p "Enter last name: " LAST_NAME
echo "Full name: $FIRST_NAME $LAST_NAME"

# Read with timeout
read -t 10 -p "Enter input (10 second timeout): " INPUT
```

**Input Options:**
```bash
# Silent input (for passwords)
read -s -p "Enter password: " PASSWORD

# Read into array
read -a ARRAY <<< "apple banana cherry"

# Read from file
while read line; do
    echo "Line: $line"
done < file.txt
```

### Output Commands

**Echo Command:**
```bash
# Basic output
echo "Hello, World!"

# Output without newline
echo -n "Enter your name: "

# Output with escape sequences
echo -e "Line 1\nLine 2\tTabbed"

# Output to file
echo "Hello, World!" > output.txt

# Append to file
echo "New line" >> output.txt
```

**Printf Command:**
```bash
# Formatted output
printf "Name: %s, Age: %d\n" "John" 25

# Format numbers
printf "Price: $%.2f\n" 19.99

# Format with padding
printf "%-10s %5d\n" "Apple" 10
printf "%-10s %5d\n" "Banana" 5
```

## Control Structures

### Conditional Statements

**If-Then-Else:**
```bash
# Basic if statement
if [ $AGE -ge 18 ]; then
    echo "You are an adult"
fi

# If-else statement
if [ $AGE -ge 18 ]; then
    echo "You are an adult"
else
    echo "You are a minor"
fi

# If-elif-else statement
if [ $AGE -lt 13 ]; then
    echo "You are a child"
elif [ $AGE -lt 20 ]; then
    echo "You are a teenager"
else
    echo "You are an adult"
fi
```

**Test Conditions:**
```bash
# String comparisons
[ "$NAME" = "John" ]
[ "$NAME" != "John" ]
[ -n "$NAME" ]        # Non-empty string
[ -z "$NAME" ]        # Empty string

# Numeric comparisons
[ $AGE -eq 25 ]       # Equal
[ $AGE -ne 25 ]       # Not equal
[ $AGE -lt 25 ]       # Less than
[ $AGE -le 25 ]       # Less than or equal
[ $AGE -gt 25 ]       # Greater than
[ $AGE -ge 25 ]       # Greater than or equal

# File tests
[ -f "file.txt" ]     # File exists
[ -d "directory" ]    # Directory exists
[ -r "file.txt" ]     # File is readable
[ -w "file.txt" ]     # File is writable
[ -x "file.txt" ]     # File is executable
```

### Loops

**For Loops:**
```bash
# Basic for loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# For loop with range
for i in {1..5}; do
    echo "Number: $i"
done

# For loop with step
for i in {1..10..2}; do
    echo "Number: $i"
done

# For loop with array
FRUITS=("apple" "banana" "cherry")
for fruit in "${FRUITS[@]}"; do
    echo "Fruit: $fruit"
done

# For loop with files
for file in *.txt; do
    echo "File: $file"
done
```

**While Loops:**
```bash
# Basic while loop
counter=1
while [ $counter -le 5 ]; do
    echo "Counter: $counter"
    counter=$((counter + 1))
done

# While loop with input
while read line; do
    echo "Line: $line"
done < file.txt

# Infinite loop
while true; do
    echo "Press Ctrl+C to stop"
    sleep 1
done
```

**Until Loops:**
```bash
# Basic until loop
counter=1
until [ $counter -gt 5 ]; do
    echo "Counter: $counter"
    counter=$((counter + 1))
done
```

### Case Statements

**Case Statement:**
```bash
# Basic case statement
case $1 in
    "start")
        echo "Starting service..."
        ;;
    "stop")
        echo "Stopping service..."
        ;;
    "restart")
        echo "Restarting service..."
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        ;;
esac
```

## Functions

### Function Definition

**Basic Functions:**
```bash
# Function definition
function_name() {
    echo "Hello from function"
}

# Function with parameters
greet() {
    local name=$1
    echo "Hello, $name!"
}

# Function with return value
add() {
    local a=$1
    local b=$2
    echo $((a + b))
}
```

**Function Usage:**
```bash
# Call function
function_name

# Call function with parameters
greet "John"

# Call function and capture return value
result=$(add 5 3)
echo "Result: $result"
```

**Function Scope:**
```bash
# Global variable
GLOBAL_VAR="global"

# Function with local variable
my_function() {
    local LOCAL_VAR="local"
    echo "Global: $GLOBAL_VAR"
    echo "Local: $LOCAL_VAR"
}

# Call function
my_function
echo "Outside function - Global: $GLOBAL_VAR"
echo "Outside function - Local: $LOCAL_VAR"  # This will be empty
```

## Error Handling

### Exit Codes

**Exit Status:**
```bash
# Check exit status
command
if [ $? -eq 0 ]; then
    echo "Command succeeded"
else
    echo "Command failed"
fi

# Set exit status
exit 0    # Success
exit 1    # General error
exit 2    # Misuse of shell builtins
exit 126  # Command invoked cannot execute
exit 127  # Command not found
exit 128  # Invalid exit argument
```

**Error Handling:**
```bash
# Exit on error
set -e

# Exit on undefined variable
set -u

# Exit on pipe failure
set -o pipefail

# Disable exit on error
set +e
```

### Trap Commands

**Signal Handling:**
```bash
# Trap function for cleanup
cleanup() {
    echo "Cleaning up..."
    rm -f temp_file.txt
}

# Set trap
trap cleanup EXIT

# Trap specific signals
trap 'echo "Received SIGINT"; exit 1' INT
trap 'echo "Received SIGTERM"; exit 1' TERM
```

## File Operations

### File Reading

**Reading Files:**
```bash
# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < file.txt

# Read file into array
mapfile -t lines < file.txt
for line in "${lines[@]}"; do
    echo "Line: $line"
done

# Read file with line numbers
line_number=1
while IFS= read -r line; do
    echo "$line_number: $line"
    ((line_number++))
done < file.txt
```

### File Writing

**Writing Files:**
```bash
# Write to file
echo "Hello, World!" > output.txt

# Append to file
echo "New line" >> output.txt

# Write multiple lines
cat > output.txt << EOF
Line 1
Line 2
Line 3
EOF

# Write with formatting
printf "Name: %s\nAge: %d\n" "John" 25 > output.txt
```

## Advanced Features

### Arrays

**Array Operations:**
```bash
# Create array
FRUITS=("apple" "banana" "cherry")

# Access elements
echo ${FRUITS[0]}    # First element
echo ${FRUITS[1]}    # Second element
echo ${FRUITS[@]}    # All elements

# Array length
echo ${#FRUITS[@]}

# Add element
FRUITS+=("date")

# Remove element
unset FRUITS[1]

# Iterate through array
for fruit in "${FRUITS[@]}"; do
    echo "Fruit: $fruit"
done
```

### Regular Expressions

**Pattern Matching:**
```bash
# String matching
if [[ $NAME =~ ^[A-Za-z]+$ ]]; then
    echo "Name contains only letters"
fi

# Extract pattern
if [[ $EMAIL =~ ^([a-zA-Z0-9._%+-]+)@([a-zA-Z0-9.-]+\.[a-zA-Z]{2,})$ ]]; then
    echo "Valid email: ${BASH_REMATCH[1]}@${BASH_REMATCH[2]}"
fi

# Replace pattern
NEW_NAME=${NAME/John/Jane}
echo "New name: $NEW_NAME"
```

### Process Substitution

**Process Substitution:**
```bash
# Compare two files
diff <(sort file1.txt) <(sort file2.txt)

# Process output
while read line; do
    echo "Processed: $line"
done < <(grep "pattern" file.txt)

# Multiple inputs
paste <(cut -d',' -f1 file1.csv) <(cut -d',' -f2 file2.csv)
```

## Script Examples

### System Information Script

```bash
#!/bin/bash
# System Information Script

echo "=== System Information ==="
echo "Hostname: $(hostname)"
echo "Kernel: $(uname -r)"
echo "Uptime: $(uptime -p)"
echo "Memory: $(free -h | grep Mem | awk '{print $3 "/" $2}')"
echo "Disk Usage: $(df -h / | tail -1 | awk '{print $5}')"
echo "CPU Load: $(uptime | awk -F'load average:' '{print $2}')"
```

### File Backup Script

```bash
#!/bin/bash
# File Backup Script

BACKUP_DIR="/backup"
SOURCE_DIR="/home/user/documents"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$DATE.tar.gz"

# Create backup directory
mkdir -p $BACKUP_DIR

# Create backup
tar -czf $BACKUP_DIR/$BACKUP_FILE $SOURCE_DIR

# Check if backup was successful
if [ $? -eq 0 ]; then
    echo "Backup created successfully: $BACKUP_FILE"
else
    echo "Backup failed!"
    exit 1
fi
```

### Log Monitoring Script

```bash
#!/bin/bash
# Log Monitoring Script

LOG_FILE="/var/log/syslog"
PATTERN="ERROR"
THRESHOLD=10

# Count error occurrences
ERROR_COUNT=$(grep -c "$PATTERN" $LOG_FILE)

echo "Error count: $ERROR_COUNT"

if [ $ERROR_COUNT -gt $THRESHOLD ]; then
    echo "ALERT: High error count detected!"
    # Send notification (email, etc.)
fi
```

## Practice Exercises

### Exercise 1: Basic Script Creation
**Objective**: Create a basic shell script

**Tasks**:
1. Create a script that greets the user
2. Ask for user input
3. Display personalized message
4. Make the script executable
5. Test the script

**Script Example**:
```bash
#!/bin/bash
echo "Welcome to the greeting script!"
read -p "What's your name? " NAME
echo "Hello, $NAME! Nice to meet you."
```

### Exercise 2: File Operations Script
**Objective**: Create a script for file operations

**Tasks**:
1. Create a script that lists files in a directory
2. Count the number of files
3. Show file sizes
4. Create a summary report
5. Save the report to a file

**Script Example**:
```bash
#!/bin/bash
DIRECTORY=$1
if [ -z "$DIRECTORY" ]; then
    DIRECTORY="."
fi

echo "=== File Report for $DIRECTORY ==="
echo "Total files: $(find $DIRECTORY -type f | wc -l)"
echo "Total directories: $(find $DIRECTORY -type d | wc -l)"
echo "Total size: $(du -sh $DIRECTORY | cut -f1)"
```

### Exercise 3: System Monitoring Script
**Objective**: Create a system monitoring script

**Tasks**:
1. Create a script that monitors system resources
2. Check CPU usage, memory usage, disk space
3. Alert if thresholds are exceeded
4. Log the results
5. Run the script periodically

**Script Example**:
```bash
#!/bin/bash
LOG_FILE="/var/log/system_monitor.log"
DATE=$(date)

# Check disk usage
DISK_USAGE=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
if [ $DISK_USAGE -gt 80 ]; then
    echo "$DATE: WARNING - Disk usage is ${DISK_USAGE}%" >> $LOG_FILE
fi

# Check memory usage
MEMORY_USAGE=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
if [ $MEMORY_USAGE -gt 80 ]; then
    echo "$DATE: WARNING - Memory usage is ${MEMORY_USAGE}%" >> $LOG_FILE
fi
```

### Exercise 4: Advanced Script with Functions
**Objective**: Create an advanced script with functions

**Tasks**:
1. Create a script with multiple functions
2. Implement error handling
3. Use arrays and loops
4. Add command-line argument parsing
5. Include help documentation

**Script Example**:
```bash
#!/bin/bash

# Function to show help
show_help() {
    echo "Usage: $0 [OPTIONS]"
    echo "Options:"
    echo "  -h, --help     Show this help message"
    echo "  -v, --verbose  Enable verbose output"
    echo "  -f, --file     Specify input file"
}

# Function to process files
process_file() {
    local file=$1
    if [ -f "$file" ]; then
        echo "Processing file: $file"
        # Add file processing logic here
    else
        echo "Error: File $file not found"
        return 1
    fi
}

# Main script logic
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            show_help
            exit 0
            ;;
        -v|--verbose)
            VERBOSE=true
            shift
            ;;
        -f|--file)
            FILE="$2"
            shift 2
            ;;
        *)
            echo "Unknown option: $1"
            show_help
            exit 1
            ;;
    esac
done

# Process file if specified
if [ -n "$FILE" ]; then
    process_file "$FILE"
fi
```

## Important Reminders

⚠️ **Remember these key points about shell scripting:**

- **Test Thoroughly**: Always test scripts before using them in production
- **Handle Errors**: Implement proper error handling and exit codes
- **Use Quotes**: Always quote variables to prevent word splitting
- **Document Code**: Add comments to explain complex logic
- **Follow Conventions**: Use consistent naming and formatting
- **Validate Input**: Always validate user input and command-line arguments
- **Keep It Simple**: Write clear, readable code rather than clever one-liners

**Shell scripting is a powerful tool for automation and system administration. Mastering shell scripting will make you a more efficient and effective Linux administrator.** 🐧✨
