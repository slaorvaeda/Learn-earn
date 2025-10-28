# Complete GitHub & Git Training Guide 🚀

## Table of Contents
1. [Git Fundamentals](#git-fundamentals)
2. [GitHub Setup](#github-setup)
3. [Basic Git Commands](#basic-git-commands)
4. [Staging and Committing](#staging-and-committing)
5. [Branching and Merging](#branching-and-merging)
6. [History Traversal](#history-traversal)
7. [Remote Operations](#remote-operations)
8. [Collaborative Workflows](#collaborative-workflows)
9. [Advanced Git Features](#advanced-git-features)
10. [GitHub Features](#github-features)
11. [Best Practices](#best-practices)
12. [Troubleshooting](#troubleshooting)

---

## Git Fundamentals

### What is Git?
Git is a **distributed version control system** that tracks changes in files and coordinates work among multiple people.

### Key Concepts
- **Repository (Repo)**: A project folder with version control
- **Commit**: A snapshot of your project at a specific time
- **Branch**: A parallel version of your project
- **Remote**: A version of your repository hosted on GitHub
- **Clone**: Download a repository to your local machine

### Git vs GitHub
| Git | GitHub |
|-----|--------|
| Version control system | Git hosting service |
| Runs on your computer | Cloud-based platform |
| Tracks changes | Stores and shares repositories |
| Command-line tool | Web interface + Git |

---

## GitHub Setup

### 1. Create GitHub Account
1. Go to [github.com](https://github.com)
2. Sign up with email or social account
3. Verify your email address

### 2. Install Git
```bash
# macOS (using Homebrew)
brew install git

# Ubuntu/Debian
sudo apt update
sudo apt install git

# Windows
# Download from git-scm.com
```

### 3. Configure Git
```bash
# Set your name and email
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Check configuration
git config --list
```

### 4. SSH Key Setup (Recommended)
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Add to SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy public key to clipboard
cat ~/.ssh/id_ed25519.pub

# Add to GitHub: Settings > SSH and GPG keys > New SSH key
```

---

## Basic Git Commands

### Repository Operations
```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone https://github.com/username/repository.git
git clone git@github.com:username/repository.git  # SSH

# Check repository status
git status

# View repository information
git remote -v
git branch -a
```

### File Operations
```bash
# Add files to staging area
git add filename.txt
git add .                    # Add all files
git add *.js                 # Add all JS files
git add folder/              # Add entire folder

# Remove files
git rm filename.txt          # Remove and stage deletion
git rm --cached filename.txt # Remove from staging, keep file
```

---

## Staging and Committing

### The Three States
```
Working Directory → Staging Area → Repository
     (Modified)      (Staged)      (Committed)
```

### Staging Commands
```bash
# Stage specific files
git add file1.txt file2.txt

# Stage all changes
git add .

# Stage all modified files
git add -u

# Interactive staging (choose what to stage)
git add -i

# Stage with patch mode (partial changes)
git add -p
```

### Committing Commands
```bash
# Commit with message
git commit -m "Add new feature"

# Commit with detailed message
git commit -m "Add new feature" -m "This feature allows users to..."

# Commit all staged changes
git commit -am "Quick commit"

# Amend last commit
git commit --amend -m "Updated commit message"

# Commit with no message (opens editor)
git commit
```

### Commit Message Best Practices
```
feat: add user authentication
fix: resolve login bug
docs: update README
style: format code
refactor: improve performance
test: add unit tests
chore: update dependencies
```

---

## Branching and Merging

### Branch Operations
```bash
# List branches
git branch
git branch -a              # All branches (local + remote)
git branch -r               # Remote branches only

# Create branch
git branch feature-branch
git checkout -b feature-branch  # Create and switch

# Switch branches
git checkout main
git checkout feature-branch
git switch main              # Newer command
git switch -c new-branch     # Create and switch

# Delete branches
git branch -d feature-branch    # Safe delete
git branch -D feature-branch    # Force delete
```

### Merging
```bash
# Merge branch into current branch
git checkout main
git merge feature-branch

# Merge with no fast-forward (creates merge commit)
git merge --no-ff feature-branch

# Squash merge (combines all commits into one)
git merge --squash feature-branch
```

### Rebasing
```bash
# Rebase current branch onto main
git checkout feature-branch
git rebase main

# Interactive rebase (last 3 commits)
git rebase -i HEAD~3

# Rebase with conflict resolution
git rebase --continue
git rebase --abort
```

---

## History Traversal

### Viewing History
```bash
# View commit history
git log
git log --oneline          # One line per commit
git log --graph            # Show branch structure
git log --all --graph      # All branches with graph

# View specific commits
git log --since="2 weeks ago"
git log --author="John Doe"
git log --grep="bug fix"
git log -- file.txt        # Commits affecting specific file
```

### Navigating History
```bash
# Checkout specific commit
git checkout <commit-hash>
git checkout HEAD~1        # Previous commit
git checkout HEAD~3         # 3 commits ago

# Create branch from commit
git checkout -b new-branch <commit-hash>

# View commit details
git show <commit-hash>
git show HEAD              # Latest commit
```

### Time Travel Commands
```bash
# Reset to previous commit
git reset --soft HEAD~1    # Keep changes staged
git reset --mixed HEAD~1   # Keep changes in working directory
git reset --hard HEAD~1    # Discard all changes

# Revert commits (creates new commit)
git revert <commit-hash>
git revert HEAD            # Revert latest commit

# Cherry-pick commits
git cherry-pick <commit-hash>
```

---

## Remote Operations

### Remote Repository Management
```bash
# Add remote repository
git remote add origin https://github.com/username/repo.git
git remote add upstream https://github.com/original/repo.git

# List remotes
git remote -v

# Remove remote
git remote remove origin

# Change remote URL
git remote set-url origin https://github.com/new/repo.git
```

### Push and Pull
```bash
# Push to remote
git push origin main
git push -u origin main    # Set upstream branch
git push --all             # Push all branches
git push --tags            # Push tags

# Pull from remote
git pull origin main
git pull --rebase origin main

# Fetch without merging
git fetch origin
git fetch --all
```

### Synchronization
```bash
# Sync with remote
git fetch origin
git merge origin/main

# Force push (dangerous!)
git push --force-with-lease origin main

# Pull with rebase
git pull --rebase origin main
```

---

## Collaborative Workflows

### Fork and Clone Workflow
```bash
# 1. Fork repository on GitHub
# 2. Clone your fork
git clone https://github.com/yourusername/repo.git
cd repo

# 3. Add upstream remote
git remote add upstream https://github.com/original/repo.git

# 4. Create feature branch
git checkout -b feature-branch

# 5. Make changes and commit
git add .
git commit -m "Add new feature"

# 6. Push to your fork
git push origin feature-branch

# 7. Create Pull Request on GitHub
```

### Team Collaboration
```bash
# Update local repository
git fetch upstream
git checkout main
git merge upstream/main

# Create feature branch
git checkout -b feature/new-feature

# Work on feature
git add .
git commit -m "Implement new feature"

# Push and create PR
git push origin feature/new-feature
```

---

## Advanced Git Features

### Stashing
```bash
# Stash changes
git stash
git stash push -m "Work in progress"

# List stashes
git stash list

# Apply stash
git stash pop
git stash apply stash@{0}

# Drop stash
git stash drop stash@{0}
```

### Tagging
```bash
# Create tags
git tag v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"

# List tags
git tag
git tag -l "v1.*"

# Push tags
git push origin v1.0.0
git push origin --tags
```

### Submodules
```bash
# Add submodule
git submodule add https://github.com/user/repo.git path/to/submodule

# Clone with submodules
git clone --recursive https://github.com/user/repo.git

# Update submodules
git submodule update --remote
```

---

## GitHub Features

### GitHub Web Interface
- **Repository**: Main project page
- **Issues**: Bug reports and feature requests
- **Pull Requests**: Code review and merging
- **Actions**: CI/CD automation
- **Wiki**: Documentation
- **Projects**: Project management

### GitHub CLI
```bash
# Install GitHub CLI
brew install gh

# Authenticate
gh auth login

# Clone repository
gh repo clone username/repository

# Create issue
gh issue create --title "Bug report" --body "Description"

# Create pull request
gh pr create --title "New feature" --body "Description"

# List pull requests
gh pr list
gh pr view 123
```

### GitHub Actions (CI/CD)
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    - name: Install dependencies
      run: npm install
    - name: Run tests
      run: npm test
```

---

## Best Practices

### Commit Best Practices
1. **Small, focused commits**
2. **Clear commit messages**
3. **Test before committing**
4. **Use conventional commits**

### Branch Naming
```
feature/user-authentication
bugfix/login-error
hotfix/security-patch
release/v1.2.0
```

### Repository Structure
```
project/
├── .git/
├── .gitignore
├── README.md
├── src/
├── tests/
├── docs/
└── .github/
    └── workflows/
```

### .gitignore Examples
```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
*.o
*.so

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Environment files
.env
.env.local
```

---

## Troubleshooting

### Common Issues and Solutions

#### 1. Merge Conflicts
```bash
# When conflicts occur
git status                    # See conflicted files
# Edit files to resolve conflicts
git add resolved-file.txt
git commit -m "Resolve merge conflict"
```

#### 2. Accidental Commits
```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Change commit message
git commit --amend -m "New message"
```

#### 3. Lost Commits
```bash
# Find lost commits
git reflog
git log --oneline --all

# Recover lost commit
git checkout <commit-hash>
git checkout -b recovered-branch
```

#### 4. Wrong Branch
```bash
# Move uncommitted changes to another branch
git stash
git checkout correct-branch
git stash pop
```

#### 5. Force Push Issues
```bash
# Safe force push
git push --force-with-lease origin main

# Recover from force push
git reflog
git reset --hard HEAD@{1}
```

### Useful Commands for Debugging
```bash
# Check what changed
git diff
git diff --staged
git diff HEAD~1

# See file history
git log --follow file.txt
git blame file.txt

# Check repository health
git fsck
git gc
```

---

## Practice Exercises

### Exercise 1: Basic Workflow
```bash
# 1. Create new repository
mkdir my-project
cd my-project
git init
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"

# 2. Create GitHub repository and push
git remote add origin https://github.com/username/my-project.git
git push -u origin main
```

### Exercise 2: Branching
```bash
# 1. Create feature branch
git checkout -b feature/new-feature
echo "New feature" > feature.txt
git add feature.txt
git commit -m "Add new feature"

# 2. Switch back to main
git checkout main

# 3. Merge feature
git merge feature/new-feature
git push origin main
```

### Exercise 3: Collaboration
```bash
# 1. Fork a repository on GitHub
# 2. Clone your fork
git clone https://github.com/yourusername/forked-repo.git
cd forked-repo

# 3. Add upstream remote
git remote add upstream https://github.com/original/forked-repo.git

# 4. Sync with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## Advanced Workflows

### Git Flow
```bash
# Main branches
git checkout -b develop
git checkout -b feature/new-feature
git checkout -b release/v1.0.0
git checkout -b hotfix/critical-bug
```

### GitHub Flow
```bash
# Simple workflow
git checkout -b feature-branch
# Make changes
git add .
git commit -m "Add feature"
git push origin feature-branch
# Create Pull Request on GitHub
```

### Git Hooks
```bash
# Pre-commit hook
#!/bin/sh
npm test
# .git/hooks/pre-commit
```

---

## Summary

### Essential Commands Cheat Sheet
```bash
# Basic workflow
git init
git add .
git commit -m "Message"
git push origin main

# Branching
git checkout -b branch-name
git merge branch-name
git branch -d branch-name

# History
git log --oneline
git checkout <commit-hash>
git reset --hard HEAD~1

# Remote
git clone <url>
git remote add origin <url>
git push -u origin main
git pull origin main
```

### Key Takeaways
1. **Git tracks changes** in your code
2. **GitHub hosts** your repositories
3. **Branches** allow parallel development
4. **Commits** are snapshots of your code
5. **Pull Requests** enable code review
6. **History traversal** helps debug issues
7. **Collaboration** requires good practices

### Next Steps
1. Practice with real projects
2. Contribute to open source
3. Learn advanced Git features
4. Explore GitHub Actions
5. Master collaborative workflows

---

## Resources

### Documentation
- [Git Official Documentation](https://git-scm.com/doc)
- [GitHub Documentation](https://docs.github.com)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)

### Interactive Learning
- [Learn Git Branching](https://learngitbranching.js.org/)
- [GitHub Learning Lab](https://lab.github.com/)
- [GitKraken Git Guide](https://www.gitkraken.com/learn/git)

### Tools
- **GitHub Desktop**: GUI for Git
- **GitKraken**: Advanced Git GUI
- **VS Code**: Built-in Git support
- **GitHub CLI**: Command-line interface

---

**Happy Coding! 🚀**

Remember: Git is a powerful tool that takes time to master. Start with the basics, practice regularly, and gradually explore advanced features. The key is consistent practice and understanding the workflow!
