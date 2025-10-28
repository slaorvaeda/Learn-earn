# 05 - Text Editors

## Introduction to Text Editors

Text editors are essential tools for Linux users, system administrators, and developers. They allow you to create, edit, and manipulate text files, configuration files, scripts, and code.

**Think of it as:**
- **Text Editor** = Your digital pen and paper
- **Configuration Files** = Settings that control how programs work
- **Scripts** = Automated sequences of commands
- **Code** = Instructions for computers to follow

## Types of Text Editors

### Command Line Editors

**Terminal-Based Editors:**
- **Vim/Vi**: Powerful, modal editor
- **Nano**: User-friendly, simple editor
- **Emacs**: Extensible, feature-rich editor
- **Pico**: Simple, easy-to-use editor

**Advantages:**
- **Remote Access**: Work over SSH connections
- **Lightweight**: Minimal resource usage
- **Universal**: Available on all Linux systems
- **Powerful**: Advanced features and customization

### Graphical Editors

**GUI-Based Editors:**
- **Gedit**: GNOME's default text editor
- **Kate**: KDE's advanced text editor
- **Mousepad**: XFCE's lightweight editor
- **VS Code**: Microsoft's code editor
- **Sublime Text**: Commercial code editor

**Advantages:**
- **User-Friendly**: Intuitive interface
- **Visual**: Syntax highlighting and formatting
- **Plugins**: Extensible with add-ons
- **Multi-Window**: Multiple files simultaneously

## Vim Editor

### What is Vim?

Vim (Vi Improved) is a powerful, modal text editor that's available on virtually every Linux system. It's based on the original Vi editor and offers extensive features for text editing.

**Vim Modes:**
- **Normal Mode**: Default mode for navigation and commands
- **Insert Mode**: For typing and editing text
- **Visual Mode**: For selecting text
- **Command Mode**: For executing commands

### Vim Installation

**Install Vim:**
```bash
# Ubuntu/Debian
sudo apt install vim

# CentOS/RHEL
sudo yum install vim

# Fedora
sudo dnf install vim

# Arch Linux
sudo pacman -S vim
```

**Check Vim Version:**
```bash
vim --version
```

### Vim Basics

**Starting Vim:**
```bash
# Open Vim
vim

# Open file with Vim
vim filename.txt

# Open multiple files
vim file1.txt file2.txt

# Open file at specific line
vim +10 filename.txt
```

**Exiting Vim:**
```bash
# Save and exit
:wq
:x
ZZ

# Exit without saving
:q!

# Save without exiting
:w

# Save as different file
:w newfilename.txt
```

### Vim Modes

**Normal Mode (Default):**
- **Purpose**: Navigation and commands
- **Enter**: Press Esc key
- **Commands**: Single character commands
- **Navigation**: Arrow keys or hjkl

**Insert Mode:**
- **Purpose**: Typing and editing text
- **Enter**: Press i, a, o, O, I, A
- **Exit**: Press Esc key
- **Commands**: Type normally

**Visual Mode:**
- **Purpose**: Selecting text
- **Enter**: Press v, V, or Ctrl+v
- **Exit**: Press Esc key
- **Commands**: Select text with arrow keys

**Command Mode:**
- **Purpose**: Executing commands
- **Enter**: Press : (colon)
- **Exit**: Press Esc key
- **Commands**: Type commands and press Enter

### Vim Navigation

**Basic Movement:**
```bash
# Arrow keys (if available)
↑ ↓ ← →

# Vim movement keys
h - Left
j - Down
k - Up
l - Right

# Word movement
w - Next word
b - Previous word
e - End of word

# Line movement
0 - Beginning of line
$ - End of line
^ - First non-blank character

# Screen movement
Ctrl+f - Page down
Ctrl+b - Page up
H - Top of screen
M - Middle of screen
L - Bottom of screen
```

**Advanced Movement:**
```bash
# Jump to specific line
:10 - Go to line 10
G - Go to last line
gg - Go to first line

# Search movement
/pattern - Search forward
?pattern - Search backward
n - Next search result
N - Previous search result

# Character search
f<char> - Find character forward
F<char> - Find character backward
t<char> - Find character forward (before)
T<char> - Find character backward (before)
```

### Vim Editing

**Inserting Text:**
```bash
# Insert before cursor
i - Insert mode
I - Insert at beginning of line

# Insert after cursor
a - Append mode
A - Append at end of line

# Insert new line
o - New line below
O - New line above

# Replace mode
r - Replace single character
R - Replace mode
```

**Deleting Text:**
```bash
# Delete characters
x - Delete character under cursor
X - Delete character before cursor

# Delete words
dw - Delete word
d$ - Delete to end of line
d0 - Delete to beginning of line

# Delete lines
dd - Delete entire line
D - Delete from cursor to end of line

# Delete with count
3dd - Delete 3 lines
5x - Delete 5 characters
```

**Copying and Pasting:**
```bash
# Copy (yank)
yy - Copy entire line
yw - Copy word
y$ - Copy to end of line
y0 - Copy to beginning of line

# Paste
p - Paste after cursor
P - Paste before cursor

# Cut (delete and copy)
dd - Cut line
dw - Cut word
```

**Undo and Redo:**
```bash
# Undo
u - Undo last change
U - Undo all changes on line

# Redo
Ctrl+r - Redo
```

### Vim Commands

**File Operations:**
```bash
# Save file
:w - Save file
:w filename - Save as new file
:wq - Save and quit
:x - Save and quit

# Quit
:q - Quit
:q! - Quit without saving
ZZ - Save and quit

# Read file
:r filename - Read file into current file
:r !command - Read command output
```

**Search and Replace:**
```bash
# Search
/pattern - Search forward
?pattern - Search backward
n - Next search result
N - Previous search result

# Replace
:s/old/new - Replace first occurrence on line
:s/old/new/g - Replace all occurrences on line
:%s/old/new/g - Replace all occurrences in file
:%s/old/new/gc - Replace with confirmation
```

**Line Operations:**
```bash
# Line numbers
:set number - Show line numbers
:set nonumber - Hide line numbers
:set nu - Show line numbers (short)
:set nonu - Hide line numbers (short)

# Line operations
:10,20d - Delete lines 10-20
:10,20y - Copy lines 10-20
:10,20m30 - Move lines 10-20 after line 30
:10,20co30 - Copy lines 10-20 after line 30
```

### Vim Configuration

**Vimrc File:**
```bash
# Edit vimrc file
vim ~/.vimrc

# Example vimrc configuration
set number
set tabstop=4
set shiftwidth=4
set expandtab
set autoindent
set smartindent
set hlsearch
set incsearch
set ignorecase
set smartcase
set showmatch
set ruler
set laststatus=2
set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ [BUFFER=%n]\ %{strftime('%c')}
```

**Vim Plugins:**
```bash
# Install Vundle (plugin manager)
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

# Add to vimrc
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
Plugin 'tpope/vim-fugitive'
Plugin 'scrooloose/nerdtree'
call vundle#end()
```

## Nano Editor

### What is Nano?

Nano is a simple, user-friendly text editor that's perfect for beginners. It provides a straightforward interface with helpful shortcuts displayed at the bottom of the screen.

**Nano Features:**
- **Simple Interface**: Easy to use for beginners
- **Helpful Shortcuts**: Displayed at bottom of screen
- **Syntax Highlighting**: Available for many languages
- **Search and Replace**: Built-in search functionality

### Nano Installation

**Install Nano:**
```bash
# Ubuntu/Debian
sudo apt install nano

# CentOS/RHEL
sudo yum install nano

# Fedora
sudo dnf install nano

# Arch Linux
sudo pacman -S nano
```

**Check Nano Version:**
```bash
nano --version
```

### Nano Basics

**Starting Nano:**
```bash
# Open Nano
nano

# Open file with Nano
nano filename.txt

# Open file with line numbers
nano -l filename.txt

# Open file with syntax highlighting
nano -Y sh filename.sh
```

**Exiting Nano:**
```bash
# Save and exit
Ctrl+X, then Y, then Enter

# Exit without saving
Ctrl+X, then N

# Save without exiting
Ctrl+O
```

### Nano Shortcuts

**File Operations:**
```bash
# Save file
Ctrl+O - Write out (save)

# Exit
Ctrl+X - Exit

# Read file
Ctrl+R - Read file

# Write to file
Ctrl+W - Write to file
```

**Editing:**
```bash
# Cut text
Ctrl+K - Cut current line

# Paste text
Ctrl+U - Paste

# Copy text
Alt+6 - Copy text

# Undo
Alt+U - Undo

# Redo
Alt+E - Redo
```

**Navigation:**
```bash
# Move cursor
Ctrl+F - Forward one character
Ctrl+B - Backward one character
Ctrl+P - Previous line
Ctrl+N - Next line

# Move by word
Ctrl+Space - Forward one word
Alt+Space - Backward one word

# Move to beginning/end
Ctrl+A - Beginning of line
Ctrl+E - End of line
Ctrl+Y - Previous page
Ctrl+V - Next page
```

**Search and Replace:**
```bash
# Search
Ctrl+W - Search
Alt+W - Search next

# Replace
Ctrl+\ - Replace
```

### Nano Configuration

**Nanorc File:**
```bash
# Edit nanorc file
nano ~/.nanorc

# Example nanorc configuration
set autoindent
set tabsize 4
set tabstospaces
set mouse
set linenumbers
set softwrap
set atblanks
set backup
set backupdir ~/.nano/backups/
set historylog
set historylog ~/.nano/history
```

## Emacs Editor

### What is Emacs?

Emacs is a powerful, extensible text editor that's popular among programmers and system administrators. It offers extensive customization options and can be used for much more than just text editing.

**Emacs Features:**
- **Extensible**: Highly customizable with Lisp
- **Multi-Mode**: Different modes for different file types
- **Integrated**: Built-in tools and utilities
- **Powerful**: Advanced editing capabilities

### Emacs Installation

**Install Emacs:**
```bash
# Ubuntu/Debian
sudo apt install emacs

# CentOS/RHEL
sudo yum install emacs

# Fedora
sudo dnf install emacs

# Arch Linux
sudo pacman -S emacs
```

**Check Emacs Version:**
```bash
emacs --version
```

### Emacs Basics

**Starting Emacs:**
```bash
# Open Emacs
emacs

# Open file with Emacs
emacs filename.txt

# Open file in terminal
emacs -nw filename.txt
```

**Exiting Emacs:**
```bash
# Save and exit
Ctrl+X, Ctrl+C

# Exit without saving
Ctrl+X, Ctrl+C, then N

# Save without exiting
Ctrl+X, Ctrl+S
```

### Emacs Shortcuts

**File Operations:**
```bash
# Open file
Ctrl+X, Ctrl+F - Find file

# Save file
Ctrl+X, Ctrl+S - Save file

# Save as
Ctrl+X, Ctrl+W - Write file

# Exit
Ctrl+X, Ctrl+C - Exit Emacs
```

**Editing:**
```bash
# Cut text
Ctrl+W - Cut region

# Paste text
Ctrl+Y - Paste

# Copy text
Alt+W - Copy region

# Undo
Ctrl+X, U - Undo
```

**Navigation:**
```bash
# Move cursor
Ctrl+F - Forward one character
Ctrl+B - Backward one character
Ctrl+P - Previous line
Ctrl+N - Next line

# Move by word
Alt+F - Forward one word
Alt+B - Backward one word

# Move by line
Ctrl+A - Beginning of line
Ctrl+E - End of line
```

## Graphical Text Editors

### Gedit (GNOME)

**Installation:**
```bash
# Ubuntu/Debian
sudo apt install gedit

# CentOS/RHEL
sudo yum install gedit

# Fedora
sudo dnf install gedit
```

**Features:**
- **Syntax Highlighting**: For many programming languages
- **Plugins**: Extensible with plugins
- **Tabs**: Multiple files in one window
- **Search and Replace**: Advanced search functionality

### Kate (KDE)

**Installation:**
```bash
# Ubuntu/Debian
sudo apt install kate

# CentOS/RHEL
sudo yum install kate

# Fedora
sudo dnf install kate
```

**Features:**
- **Advanced Features**: Split view, terminal integration
- **Plugins**: Extensive plugin system
- **Session Management**: Save and restore sessions
- **Code Folding**: Collapse code sections

### VS Code

**Installation:**
```bash
# Download from Microsoft website
# Or install via package manager

# Ubuntu/Debian
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

**Features:**
- **IntelliSense**: Smart code completion
- **Debugging**: Built-in debugger
- **Git Integration**: Version control integration
- **Extensions**: Extensive extension marketplace

## Text Editor Comparison

### Feature Comparison

| Feature | Vim | Nano | Emacs | Gedit | VS Code |
|---------|-----|------|-------|-------|---------|
| **Learning Curve** | Steep | Easy | Steep | Easy | Easy |
| **Resource Usage** | Low | Low | Medium | Medium | High |
| **Remote Access** | Yes | Yes | Yes | No | No |
| **Customization** | High | Medium | Very High | Medium | High |
| **Plugins** | Many | Few | Many | Some | Many |
| **Syntax Highlighting** | Yes | Yes | Yes | Yes | Yes |
| **Multi-Cursor** | No | No | No | No | Yes |

### When to Use Which Editor

**Vim:**
- **System Administration**: Editing configuration files
- **Remote Work**: Working over SSH
- **Power Users**: Advanced text manipulation
- **Scripting**: Writing shell scripts

**Nano:**
- **Beginners**: Learning Linux
- **Quick Edits**: Simple text editing
- **Configuration**: Editing config files
- **Learning**: Understanding text editors

**Emacs:**
- **Programming**: Extensive development features
- **Customization**: Highly customizable environment
- **Power Users**: Advanced users who want control
- **Lisp Development**: Lisp programming

**Graphical Editors:**
- **Development**: Full-featured development environment
- **Multiple Files**: Working with many files
- **Collaboration**: Team development
- **Modern Features**: IntelliSense, debugging

## Practice Exercises

### Exercise 1: Vim Basics
**Objective**: Learn basic Vim operations

**Tasks**:
1. Open Vim and create a new file
2. Enter insert mode and type some text
3. Save the file and exit
4. Open the file again and practice navigation
5. Practice copying, cutting, and pasting text

**Commands to Practice**:
```bash
vim practice.txt
# Press i to enter insert mode
# Type some text
# Press Esc to exit insert mode
# Type :wq to save and quit
```

### Exercise 2: Nano Basics
**Objective**: Learn basic Nano operations

**Tasks**:
1. Open Nano and create a new file
2. Type some text and save it
3. Practice using Nano shortcuts
4. Search for text in the file
5. Exit Nano

**Commands to Practice**:
```bash
nano practice.txt
# Type some text
# Press Ctrl+O to save
# Press Ctrl+W to search
# Press Ctrl+X to exit
```

### Exercise 3: Configuration File Editing
**Objective**: Practice editing configuration files

**Tasks**:
1. Create a simple configuration file
2. Edit it with different editors
3. Practice searching and replacing text
4. Save changes and verify
5. Compare different editors

**Commands to Practice**:
```bash
# Create config file
cat > config.txt << EOF
# Configuration file
server_name=example.com
port=8080
debug=true
EOF

# Edit with different editors
vim config.txt
nano config.txt
```

### Exercise 4: Script Editing
**Objective**: Practice editing shell scripts

**Tasks**:
1. Create a simple shell script
2. Edit it with Vim
3. Add syntax highlighting
4. Test the script
5. Make improvements

**Commands to Practice**:
```bash
# Create shell script
cat > script.sh << EOF
#!/bin/bash
echo "Hello World"
echo "Current date: $(date)"
EOF

# Edit with Vim
vim script.sh
# Add more commands
# Save and exit
# Make executable
chmod +x script.sh
# Run script
./script.sh
```

## Important Reminders

⚠️ **Remember these key points about text editors:**

- **Choose the Right Tool**: Different editors for different tasks
- **Learn One Well**: Master one editor before learning others
- **Practice Regularly**: Use editors daily to build familiarity
- **Save Frequently**: Always save your work
- **Learn Shortcuts**: Keyboard shortcuts save time
- **Backup Important Files**: Always backup before major edits
- **Use Version Control**: Track changes with Git

**Text editors are essential tools for Linux users. Whether you choose Vim, Nano, or a graphical editor, mastering text editing will make you more productive and efficient.** 🐧✨
