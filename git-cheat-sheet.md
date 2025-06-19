# Git Command Cheat Sheet 🚀

## 📋 Table of Contents
- [Basic Workflow](#basic-workflow)
- [Repository Setup](#repository-setup)
- [Branching & Switching](#branching--switching)
- [Merging](#merging)
- [Checking Status & History](#checking-status--history)
- [Remote Operations](#remote-operations)
- [Undoing Changes](#undoing-changes)
- [File Operations](#file-operations)
- [Advanced Commands](#advanced-commands)
- [Emergency Commands](#emergency-commands)

---

## Basic Workflow

### The Core Git Flow
```bash
# 1. Check what changed
git status

# 2. Add files to staging
git add filename.py          # Add specific file
git add .                    # Add all files
git add *.py                 # Add all Python files

# 3. Commit changes
git commit -m "Your message"

# 4. Push to remote
git push
```

### Quick Commit All Changes
```bash
# Add and commit all tracked files in one command
git commit -am "Update all tracked files"
```

---

## Repository Setup

### Starting a New Repository
```bash
# Initialize new repository
git init

# Clone existing repository
git clone https://github.com/username/repo.git

# Add remote origin
git remote add origin https://github.com/username/repo.git

# First push (set upstream)
git push -u origin main
```

### Check Repository Info
```bash
# Show remote repositories
git remote -v

# Show repository configuration
git config --list
```

---

## Branching & Switching

### Creating Branches
```bash
# Create new branch
git branch branch-name

# Create and switch to new branch
git checkout -b branch-name
git switch -c branch-name        # Modern way

# Create branch from specific commit
git checkout -b branch-name commit-hash
```

### Switching Branches
```bash
# Switch to existing branch
git checkout branch-name
git switch branch-name           # Modern way

# Switch to previous branch
git checkout -
git switch -
```

### Managing Branches
```bash
# List all branches
git branch                       # Local branches
git branch -a                    # All branches (local + remote)
git branch -r                    # Remote branches only

# Show current branch
git branch --show-current

# Delete branch
git branch -d branch-name        # Safe delete (only if merged)
git branch -D branch-name        # Force delete

# Delete remote branch
git push origin --delete branch-name
```

---

## Merging

### Basic Merging
```bash
# Merge branch into current branch
git merge branch-name

# Merge with custom commit message
git merge branch-name -m "Merge message"

# Squash merge (combine all commits into one)
git merge --squash branch-name
```

### Merge Strategies
```bash
# Fast-forward merge only
git merge --ff-only branch-name

# Always create merge commit
git merge --no-ff branch-name

# Abort merge if conflicts occur
git merge --abort
```

---

## Checking Status & History

### Status & Changes
```bash
# Check repository status
git status

# Show changes in files
git diff                         # Unstaged changes
git diff --staged                # Staged changes
git diff HEAD                    # All changes

# Show file changes summary
git diff --stat
git diff --name-only            # Just file names
```

### History & Logs
```bash
# Show commit history
git log
git log --oneline               # Compact view
git log --graph --all           # Visual branch graph
git log -n 5                    # Last 5 commits

# Show specific file history
git log filename.py

# Show changes in each commit
git log -p
```

### Comparing Branches
```bash
# Compare two branches
git diff branch1..branch2

# Show commits in branch1 not in branch2
git log branch2..branch1

# Show commits unique to each branch
git log branch1...branch2
```

---

## Remote Operations

### Fetching & Pulling
```bash
# Fetch changes from remote (don't merge)
git fetch

# Pull changes from remote (fetch + merge)
git pull

# Pull from specific remote and branch
git pull origin main

# Pull with rebase instead of merge
git pull --rebase
```

### Pushing
```bash
# Push to remote
git push

# Push and set upstream tracking
git push -u origin branch-name

# Push all branches
git push --all

# Force push (dangerous!)
git push --force
git push --force-with-lease      # Safer force push
```

### Remote Management
```bash
# Show remote repositories
git remote -v

# Add remote repository
git remote add name url

# Remove remote
git remote remove name

# Rename remote
git remote rename old-name new-name

# Clean up remote references
git remote prune origin
```

---

## Undoing Changes

### Unstaging & Discarding
```bash
# Unstage file (keep changes)
git restore --staged filename.py
git reset HEAD filename.py      # Old way

# Discard changes in working directory
git restore filename.py
git checkout -- filename.py     # Old way

# Discard all changes
git restore .
```

### Resetting Commits
```bash
# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (keep changes unstaged)
git reset HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Reset to specific commit
git reset --hard commit-hash
```

### Reverting Commits
```bash
# Create new commit that undoes a previous commit
git revert commit-hash

# Revert merge commit
git revert -m 1 commit-hash
```

---

## File Operations

### Adding Files
```bash
# Add specific files
git add file1.py file2.py

# Add all files
git add .
git add -A

# Add all files of specific type
git add *.py
git add src/

# Interactive adding
git add -i
git add -p                       # Patch mode
```

### Removing Files
```bash
# Remove file from Git and filesystem
git rm filename.py

# Remove file from Git only (keep in filesystem)
git rm --cached filename.py

# Remove directory
git rm -r directory/
```

### Moving/Renaming Files
```bash
# Rename/move file
git mv old-name.py new-name.py

# This is equivalent to:
# mv old-name.py new-name.py
# git rm old-name.py
# git add new-name.py
```

---

## Advanced Commands

### Stashing
```bash
# Stash current changes
git stash
git stash push -m "Work in progress"

# List stashes
git stash list

# Apply stash
git stash apply                  # Keep stash
git stash pop                    # Apply and remove stash

# Apply specific stash
git stash apply stash@{2}

# Drop stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

### Rebasing
```bash
# Rebase current branch onto main
git rebase main

# Interactive rebase (clean up commits)
git rebase -i HEAD~3

# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort
```

### Cherry-picking
```bash
# Apply specific commit to current branch
git cherry-pick commit-hash

# Cherry-pick multiple commits
git cherry-pick commit1 commit2

# Cherry-pick without committing
git cherry-pick --no-commit commit-hash
```

### Tags
```bash
# Create tag
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"

# List tags
git tag

# Push tags
git push origin v1.0.0
git push origin --tags

# Delete tag
git tag -d v1.0.0
git push origin --delete v1.0.0
```

---

## Emergency Commands

### Fix Common Issues
```bash
# Remove Git lock file
rm -f .git/index.lock

# Fix "detached HEAD" state
git checkout main

# Recover deleted branch (if you know the commit hash)
git checkout -b recovered-branch commit-hash

# Find lost commits
git reflog
```

### Conflict Resolution
```bash
# Show files with conflicts
git status

# After resolving conflicts manually:
git add conflicted-file.py
git commit

# Use merge tool
git mergetool

# Abort merge
git merge --abort
```

### Clean Repository
```bash
# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Show what would be removed (dry run)
git clean -n
```

---

## 🎯 Most Common Daily Commands

### Essential Workflow
```bash
git status                       # Check what's changed
git add .                        # Stage all changes
git commit -m "message"          # Commit changes
git push                         # Push to remote
git pull                         # Get latest changes
```

### Branch Workflow
```bash
git checkout main                # Switch to main
git pull                         # Update main
git checkout -b feature-branch   # Create new branch
# ... work on feature ...
git add .                        # Stage changes
git commit -m "Add feature"      # Commit
git push -u origin feature-branch # Push branch
git checkout main                # Back to main
git merge feature-branch         # Merge feature
git push                         # Push merged changes
git branch -d feature-branch     # Delete local branch
```

### Quick Reference
```bash
git log --oneline -10            # Last 10 commits
git diff HEAD~1                  # Changes since last commit
git show commit-hash             # Show specific commit
git blame filename.py            # Who changed what
```

---

## 💡 Pro Tips

1. **Always check status**: `git status` before and after operations
2. **Use descriptive commit messages**: Help your future self
3. **Pull before push**: Avoid conflicts by staying up to date
4. **Branch for features**: Never work directly on main
5. **Clean up branches**: Delete merged branches regularly
6. **Use .gitignore**: Don't commit unnecessary files
7. **Commit often**: Small, focused commits are better
8. **Test before push**: Make sure your code works

---

## 🚨 Commands to Use with Caution

```bash
git push --force                 # Can overwrite others' work
git reset --hard                 # Permanently deletes changes
git clean -fd                    # Removes untracked files permanently
git rebase                       # Changes commit history
git push --force-with-lease      # Safer than --force
```

---

**Remember**: When in doubt, check `git status` and `git log` to understand your current state! 