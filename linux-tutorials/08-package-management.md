# 08 - Package Management

## Introduction to Package Management

Package management is a system for installing, updating, and removing software on Linux systems. It handles dependencies, conflicts, and provides a centralized way to manage software.

**Think of it as:**
- **Packages** = Software bundles with all necessary files
- **Package Manager** = Tool for managing packages
- **Repository** = Central location storing packages
- **Dependencies** = Other packages required for software to work
- **Metadata** = Information about packages (version, description, etc.)

## Package Management Concepts

### What is a Package?

A package is a compressed archive containing:
- **Executable files**: The actual program
- **Configuration files**: Default settings
- **Documentation**: Manual pages and help files
- **Metadata**: Package information and dependencies
- **Scripts**: Installation and removal scripts

### Package Formats

**Debian/Ubuntu (.deb):**
- **Format**: Debian package format
- **Manager**: apt, dpkg
- **Files**: .deb files
- **Distributions**: Ubuntu, Debian, Linux Mint

**Red Hat (.rpm):**
- **Format**: RPM package format
- **Manager**: yum, dnf, rpm
- **Files**: .rpm files
- **Distributions**: CentOS, RHEL, Fedora, openSUSE

**Arch Linux (.pkg.tar.xz):**
- **Format**: Arch package format
- **Manager**: pacman
- **Files**: .pkg.tar.xz files
- **Distributions**: Arch Linux, Manjaro

**Source Packages:**
- **Format**: Source code
- **Manager**: make, configure
- **Files**: .tar.gz, .tar.bz2
- **Distributions**: All (compile from source)

### Package Dependencies

**Dependency Types:**
- **Runtime Dependencies**: Required for software to run
- **Build Dependencies**: Required for compiling software
- **Optional Dependencies**: Enhance functionality but not required
- **Conflicting Dependencies**: Cannot be installed together

**Dependency Resolution:**
- **Automatic**: Package manager resolves dependencies
- **Manual**: User must resolve conflicts
- **Satisfied**: All dependencies are available
- **Unsatisfied**: Missing required dependencies

## Debian/Ubuntu Package Management

### APT (Advanced Package Tool)

**APT Commands:**
```bash
# Update package lists
sudo apt update

# Upgrade installed packages
sudo apt upgrade

# Upgrade system (including new packages)
sudo apt dist-upgrade

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

# List available updates
apt list --upgradable

# Clean package cache
sudo apt clean

# Remove unused packages
sudo apt autoremove
```

**APT Configuration:**
```bash
# Edit sources list
sudo nano /etc/apt/sources.list

# Add repository
echo "deb http://repository.url distribution component" | sudo tee -a /etc/apt/sources.list

# Update package lists after adding repository
sudo apt update

# Add repository key
wget -qO - https://repository.url/key.gpg | sudo apt-key add -
```

### DPKG (Debian Package Manager)

**DPKG Commands:**
```bash
# Install .deb package
sudo dpkg -i package.deb

# Remove package
sudo dpkg -r package-name

# Remove package and configuration files
sudo dpkg -P package-name

# Show package information
dpkg -l package-name

# List installed packages
dpkg -l

# Show package contents
dpkg -L package-name

# Show package status
dpkg -s package-name

# Fix broken packages
sudo dpkg --configure -a

# Fix missing dependencies
sudo apt install -f
```

### APT-Cache

**APT-Cache Commands:**
```bash
# Search package cache
apt-cache search keyword

# Show package information
apt-cache show package-name

# Show package dependencies
apt-cache depends package-name

# Show reverse dependencies
apt-cache rdepends package-name

# Show package statistics
apt-cache stats

# Show package policy
apt-cache policy package-name
```

## Red Hat/CentOS Package Management

### YUM (Yellowdog Updater Modified)

**YUM Commands:**
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

# List installed packages
yum list installed

# List available packages
yum list available

# Clean package cache
sudo yum clean all

# Show package history
yum history

# Undo package transaction
sudo yum history undo 1
```

### DNF (Dandified YUM)

**DNF Commands:**
```bash
# Update packages
sudo dnf update

# Install package
sudo dnf install package-name

# Remove package
sudo dnf remove package-name

# Search for packages
dnf search package-name

# Show package information
dnf info package-name

# List installed packages
dnf list installed

# List available packages
dnf list available

# Clean package cache
sudo dnf clean all

# Show package history
dnf history

# Undo package transaction
sudo dnf history undo 1
```

### RPM (Red Hat Package Manager)

**RPM Commands:**
```bash
# Install .rpm package
sudo rpm -i package.rpm

# Remove package
sudo rpm -e package-name

# Show package information
rpm -qi package-name

# List installed packages
rpm -qa

# Show package contents
rpm -ql package-name

# Show package dependencies
rpm -qR package-name

# Verify package integrity
rpm -V package-name

# Show package files
rpm -qf /path/to/file
```

## Arch Linux Package Management

### Pacman

**Pacman Commands:**
```bash
# Update package database
sudo pacman -Sy

# Update all packages
sudo pacman -Syu

# Install package
sudo pacman -S package-name

# Remove package
sudo pacman -R package-name

# Remove package and dependencies
sudo pacman -Rs package-name

# Search for packages
pacman -Ss package-name

# Show package information
pacman -Si package-name

# List installed packages
pacman -Q

# List explicitly installed packages
pacman -Qe

# List package dependencies
pacman -Qi package-name

# Clean package cache
sudo pacman -Sc

# Clean all cached packages
sudo pacman -Scc
```

### AUR (Arch User Repository)

**AUR Helpers:**
```bash
# Install yay (AUR helper)
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si

# Search AUR packages
yay -Ss package-name

# Install AUR package
yay -S package-name

# Update AUR packages
yay -Syu

# Remove AUR package
yay -R package-name
```

## Package Repositories

### Repository Types

**Official Repositories:**
- **Main**: Core system packages
- **Restricted**: Proprietary drivers
- **Universe**: Community-maintained packages
- **Multiverse**: Non-free packages

**Third-Party Repositories:**
- **PPA**: Personal Package Archives (Ubuntu)
- **EPEL**: Extra Packages for Enterprise Linux
- **RPM Fusion**: Community packages for Red Hat
- **AUR**: Arch User Repository

### Repository Management

**Ubuntu/Debian Repositories:**
```bash
# List repositories
cat /etc/apt/sources.list

# Add PPA repository
sudo add-apt-repository ppa:repository/name

# Remove PPA repository
sudo add-apt-repository --remove ppa:repository/name

# Update package lists
sudo apt update
```

**Red Hat/CentOS Repositories:**
```bash
# List repositories
yum repolist

# Enable repository
sudo yum-config-manager --enable repository-name

# Disable repository
sudo yum-config-manager --disable repository-name

# Add repository
sudo yum-config-manager --add-repo repository-url
```

## Package Dependencies

### Dependency Management

**Installing Dependencies:**
```bash
# Install package with dependencies
sudo apt install package-name

# Install build dependencies
sudo apt build-dep package-name

# Install recommended packages
sudo apt install --install-recommends package-name

# Install suggested packages
sudo apt install --install-suggests package-name
```

**Resolving Dependencies:**
```bash
# Fix broken dependencies
sudo apt install -f

# Check for broken packages
sudo apt check

# Fix broken packages
sudo dpkg --configure -a

# Reinstall package
sudo apt reinstall package-name
```

### Dependency Issues

**Common Problems:**
- **Missing Dependencies**: Required packages not available
- **Conflicting Dependencies**: Packages that cannot coexist
- **Broken Dependencies**: Corrupted package database
- **Circular Dependencies**: Packages depending on each other

**Solutions:**
```bash
# Force install package
sudo dpkg -i --force-depends package.deb

# Remove conflicting packages
sudo apt remove conflicting-package

# Clean package cache
sudo apt clean

# Update package database
sudo apt update
```

## Package Security

### GPG Keys

**Managing GPG Keys:**
```bash
# Add repository key
wget -qO - https://repository.url/key.gpg | sudo apt-key add -

# List trusted keys
apt-key list

# Remove key
sudo apt-key del key-id

# Update key
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys key-id
```

### Package Verification

**Verifying Packages:**
```bash
# Verify package integrity
dpkg --verify package-name

# Check package signatures
apt-key verify package.deb

# Verify package checksums
sha256sum package.deb
```

## Snap Packages

### Snap Package Manager

**Snap Commands:**
```bash
# Install snap package
sudo snap install package-name

# Remove snap package
sudo snap remove package-name

# List installed snaps
snap list

# Update snap packages
sudo snap refresh

# Show snap information
snap info package-name

# Search snap packages
snap find package-name

# Enable snap service
sudo snap enable package-name

# Disable snap service
sudo snap disable package-name
```

## Flatpak Packages

### Flatpak Package Manager

**Flatpak Commands:**
```bash
# Install flatpak
sudo apt install flatpak

# Add Flathub repository
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Install flatpak package
flatpak install flathub package-name

# Remove flatpak package
flatpak uninstall package-name

# List installed flatpaks
flatpak list

# Update flatpak packages
flatpak update

# Show flatpak information
flatpak info package-name

# Search flatpak packages
flatpak search package-name
```

## Package Management Best Practices

### System Maintenance

**Regular Updates:**
```bash
# Update package lists
sudo apt update

# Upgrade packages
sudo apt upgrade

# Clean package cache
sudo apt clean

# Remove unused packages
sudo apt autoremove

# Check for security updates
sudo apt list --upgradable
```

**System Cleanup:**
```bash
# Remove orphaned packages
sudo apt autoremove

# Clean package cache
sudo apt clean

# Remove old kernels
sudo apt autoremove --purge

# Clean temporary files
sudo apt autoclean
```

### Package Selection

**Choosing Packages:**
- **Official Repositories**: Prefer official packages
- **Version Compatibility**: Check version requirements
- **Dependencies**: Consider dependency impact
- **Security**: Verify package sources
- **Updates**: Check update frequency

**Package Information:**
```bash
# Show package information
apt show package-name

# Show package dependencies
apt depends package-name

# Show reverse dependencies
apt rdepends package-name

# Show package files
dpkg -L package-name
```

## Practice Exercises

### Exercise 1: Basic Package Management
**Objective**: Practice basic package operations

**Tasks**:
1. Update package lists
2. Search for a package
3. Install a package
4. Show package information
5. Remove the package

**Commands to Practice**:
```bash
# Update package lists
sudo apt update

# Search for package
apt search firefox

# Install package
sudo apt install firefox

# Show package information
apt show firefox

# Remove package
sudo apt remove firefox
```

### Exercise 2: Repository Management
**Objective**: Practice managing repositories

**Tasks**:
1. List current repositories
2. Add a new repository
3. Update package lists
4. Install package from new repository
5. Remove the repository

**Commands to Practice**:
```bash
# List repositories
cat /etc/apt/sources.list

# Add repository
sudo add-apt-repository ppa:example/ppa

# Update package lists
sudo apt update

# Install package
sudo apt install package-name

# Remove repository
sudo add-apt-repository --remove ppa:example/ppa
```

### Exercise 3: Dependency Management
**Objective**: Practice managing dependencies

**Tasks**:
1. Install a package with dependencies
2. Check package dependencies
3. Resolve dependency conflicts
4. Clean up unused packages
5. Verify system integrity

**Commands to Practice**:
```bash
# Install package with dependencies
sudo apt install package-name

# Check dependencies
apt depends package-name

# Fix broken dependencies
sudo apt install -f

# Clean unused packages
sudo apt autoremove

# Check system integrity
sudo apt check
```

### Exercise 4: Package Troubleshooting
**Objective**: Practice troubleshooting package issues

**Tasks**:
1. Create a package problem
2. Diagnose the issue
3. Apply appropriate solution
4. Verify the fix
5. Document the solution

**Commands to Practice**:
```bash
# Check for broken packages
sudo apt check

# Fix broken packages
sudo dpkg --configure -a

# Reinstall package
sudo apt reinstall package-name

# Clean package cache
sudo apt clean

# Update package database
sudo apt update
```

## Important Reminders

⚠️ **Remember these key points about package management:**

- **Update Regularly**: Keep your system updated for security and stability
- **Use Official Repositories**: Prefer official packages over third-party
- **Check Dependencies**: Understand package dependencies before installing
- **Backup Before Major Changes**: Always backup before major system updates
- **Read Package Information**: Check package details before installing
- **Clean Up Regularly**: Remove unused packages and clean caches
- **Verify Sources**: Only install packages from trusted sources

**Package management is essential for maintaining a healthy Linux system. Understanding how to install, update, and manage packages will help you keep your system secure and up-to-date.** 🐧✨
