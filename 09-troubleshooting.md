# 09. Troubleshooting Git Issues in ML Projects

## 🎯 Learning Objectives
By the end of this section, you'll know how to:
- Diagnose and fix common Git problems in ML workflows
- Recover from various Git disasters
- Handle repository corruption and performance issues
- Resolve complex merge conflicts
- Debug Git LFS problems

## 🚨 Common Git Problems in ML

### 1. Large File Issues

#### Problem: "File size exceeds GitHub's 100MB limit"
```bash
# Error message:
# remote: error: File data/dataset.csv is 150.00 MB; this exceeds GitHub's file size limit of 100.00 MB

# Solution 1: Use Git LFS
git lfs track "data/dataset.csv"
git add .gitattributes
git add data/dataset.csv
git commit -m "Track large dataset with LFS"

# Solution 2: Remove from history if already committed
git filter-branch --tree-filter 'rm -f data/dataset.csv' HEAD
git lfs track "data/dataset.csv"
# Re-add the file
git add data/dataset.csv .gitattributes
git commit -m "Add dataset with LFS tracking"
```

#### Problem: Repository becomes too large
```bash
# Check repository size
du -sh .git/

# Find large files in history
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  awk '/^blob/ {print substr($0,6)}' | \
  sort --numeric-sort --key=2 | \
  tail -20

# Clean up large files
git filter-branch --tree-filter 'find . -name "*.pkl" -delete' HEAD

# Or use BFG Repo-Cleaner
java -jar bfg.jar --strip-blobs-bigger-than 50M
```

### 2. Merge Conflict Nightmares

#### Problem: Complex merge conflicts in notebooks
```bash
# Jupyter notebook conflicts are messy
# Example conflict in notebook.ipynb:
<<<<<<< HEAD
   "execution_count": 15,
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.8945"
      ]
     }
    }
   ]
=======
   "execution_count": 23,
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.8932"
      ]
     }
    }
   ]
>>>>>>> experiment/neural-network

# Solution: Use nbstripout to prevent output conflicts
pip install nbstripout
nbstripout --install

# For existing conflicts, clear outputs and resolve manually
jupyter nbconvert --clear-output --inplace notebook.ipynb
# Then resolve remaining conflicts manually
```

#### Problem: Conflicting configuration files
```bash
# Multiple team members editing hyperparameters
# config/model_params.yaml conflict:
<<<<<<< HEAD
learning_rate: 0.001
batch_size: 32
epochs: 100
=======
learning_rate: 0.01
batch_size: 64
epochs: 50
>>>>>>> experiment/hyperparameter-tuning

# Solution: Structured conflict resolution
# 1. Understand the context of each change
git log --oneline HEAD~5..HEAD
git log --oneline experiment/hyperparameter-tuning~5..experiment/hyperparameter-tuning

# 2. Choose values based on experimental results
# 3. Document the decision
learning_rate: 0.005  # Compromise between stability and speed
batch_size: 32        # Keep smaller batch for better generalization
epochs: 75            # Increase from base but not too high

# 4. Commit with explanation
git add config/model_params.yaml
git commit -m "Resolve config conflict: compromise parameters

- Learning rate: 0.005 (between 0.001 and 0.01)
- Batch size: 32 (better generalization)
- Epochs: 75 (sufficient training without overfitting)"
```

### 3. Branch Management Issues

#### Problem: Accidentally committed to wrong branch
```bash
# You're on main but meant to commit to experiment branch
git log --oneline -3
# Shows your commit on main

# Solution: Move commits to correct branch
git branch experiment/my-work  # Create branch with current commits
git reset --hard HEAD~2        # Reset main to before your commits
git checkout experiment/my-work # Switch to experiment branch
```

#### Problem: Can't switch branches due to uncommitted changes
```bash
# Error: "Your local changes would be overwritten by checkout"

# Solution 1: Stash changes
git stash push -m "WIP: model improvements"
git checkout experiment/neural-network
# Later: git stash pop

# Solution 2: Commit changes temporarily
git add .
git commit -m "WIP: temporary commit"
git checkout experiment/neural-network
# Later: git reset --soft HEAD~1 to uncommit
```

#### Problem: Deleted branch accidentally
```bash
# "I deleted my experiment branch with important work!"

# Solution: Use reflog to recover
git reflog
# Look for the branch deletion or last commit
# Example output:
# abc123 HEAD@{5}: checkout: moving from experiment/neural-network to main
# def456 HEAD@{6}: commit: Add neural network implementation

# Recover the branch
git checkout -b experiment/neural-network-recovered def456
```

### 4. Remote Repository Issues

#### Problem: Can't push due to non-fast-forward
```bash
# Error: "Updates were rejected because the remote contains work that you do not have locally"

# Solution 1: Pull and merge
git pull origin main
# Resolve any conflicts
git push origin main

# Solution 2: Rebase (cleaner history)
git pull --rebase origin main
# Resolve conflicts if any
git push origin main

# Solution 3: Force push (dangerous - use with caution)
git push --force-with-lease origin main
```

#### Problem: Wrong remote URL
```bash
# You cloned from wrong repository or URL changed

# Check current remote
git remote -v

# Change remote URL
git remote set-url origin https://github.com/correct-user/correct-repo.git

# Or remove and re-add remote
git remote remove origin
git remote add origin https://github.com/correct-user/correct-repo.git
```

## 🔧 Git LFS Troubleshooting

### Problem: LFS files not downloading
```bash
# Cloned repository but LFS files are just pointers

# Check LFS status
git lfs ls-files
git lfs status

# Download LFS files
git lfs pull

# If still having issues, check LFS installation
git lfs version
git lfs env
```

### Problem: LFS bandwidth exceeded
```bash
# GitHub LFS bandwidth limit reached

# Solution 1: Use selective download
git lfs pull --include="models/current_model.pkl"

# Solution 2: Clone without LFS initially
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/user/repo.git
cd repo
git lfs pull --include="essential_files/*"

# Solution 3: Use alternative storage
git config lfs.url https://your-lfs-server.com/repo.git/info/lfs
```

### Problem: LFS files corrupted
```bash
# LFS files show as corrupted or wrong size

# Check LFS integrity
git lfs fsck

# Re-download specific files
git lfs pull --include="models/model.pkl"

# Reset LFS cache
rm -rf .git/lfs/objects/*
git lfs pull
```

## 🔍 Debugging Git Performance

### Problem: Git operations are very slow
```bash
# Check repository size
du -sh .git/

# Check for large files in history
git count-objects -vH

# Check packed objects
git gc --aggressive --prune=now

# Analyze what's taking space
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort --numeric-sort --key=2 | \
  cut -c 1-12,41- | \
  $(command -v gnumfmt || echo numfmt) --field=2 --to=iec-i --suffix=B --padding=7 --round=nearest
```

### Problem: Git status is slow
```bash
# Large working directory with many files

# Check what's being tracked
git ls-files | wc -l

# Use sparse checkout for large repositories
git config core.sparseCheckout true
echo "src/" > .git/info/sparse-checkout
echo "config/" >> .git/info/sparse-checkout
git read-tree -m -u HEAD

# Skip worktree for large directories
git update-index --skip-worktree data/
```

## 🔄 Recovery Scenarios

### Scenario 1: Accidentally deleted important files
```bash
# "I deleted my model files and can't get them back!"

# Check if files are in Git history
git log --oneline --follow -- models/important_model.pkl

# Restore from last commit
git checkout HEAD -- models/important_model.pkl

# Or restore from specific commit
git checkout abc123 -- models/important_model.pkl

# If not in Git, check stash
git stash list
git stash show -p stash@{0}
```

### Scenario 2: Corrupted Git repository
```bash
# Repository shows errors or won't work

# Check repository integrity
git fsck --full

# Try to repair
git gc --aggressive --prune=now

# If severely corrupted, clone fresh and copy work
cd ..
git clone https://github.com/user/repo.git repo-backup
cd repo-backup
# Copy your uncommitted work from corrupted repo
```

### Scenario 3: Wrong commit in main branch
```bash
# Accidentally merged experimental code to main

# Option 1: Revert the merge
git revert -m 1 abc123  # abc123 is the merge commit

# Option 2: Reset to before the merge (if not pushed)
git reset --hard HEAD~1

# Option 3: Create fix commit
# Make corrections
git add .
git commit -m "Fix issues from experimental merge"
```

## 🛠 Diagnostic Commands

### Git Health Check Commands
```bash
# Repository integrity check
git fsck --full --strict

# Check for corruption
git fsck --unreachable

# Verify pack files
git verify-pack -v .git/objects/pack/*.idx

# Check configuration
git config --list --show-origin

# Check hooks
ls -la .git/hooks/

# Check reflog
git reflog --all
```

### Performance Analysis
```bash
# Repository statistics
git count-objects -vH

# Largest files in repository
git ls-tree -r -t -l --full-name HEAD | sort -n -k 4 | tail -20

# Most active files (commit frequency)
git log --name-only --pretty=format: | sort | uniq -c | sort -rg | head -20

# Branch analysis
git for-each-ref --format='%(refname:short) %(committerdate)' refs/heads | sort -k2
```

## 🔧 Preventive Measures

### Set up Git aliases for common fixes
```bash
# Add helpful aliases
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
git config --global alias.fix-whitespace '!git diff --check HEAD~1'

# ML-specific aliases
git config --global alias.clean-notebooks '!find . -name "*.ipynb" -exec jupyter nbconvert --clear-output --inplace {} \;'
git config --global alias.check-large '!find . -size +50M -not -path "./.git/*" -ls'
```

### Pre-commit hooks for prevention
```bash
# Create comprehensive pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

echo "Running pre-commit checks..."

# Check for large files
large_files=$(find . -size +100M -not -path "./.git/*" -not -path "./data/*")
if [ ! -z "$large_files" ]; then
    echo "Error: Large files detected:"
    echo "$large_files"
    echo "Use Git LFS for files larger than 100MB"
    exit 1
fi

# Check for sensitive data patterns
if git diff --cached | grep -E "(password|secret|key|token)" -i; then
    echo "Warning: Potential sensitive data detected"
    echo "Please review your changes"
    exit 1
fi

# Clear notebook outputs
if command -v nbstripout >/dev/null 2>&1; then
    nbstripout notebooks/*.ipynb 2>/dev/null || true
fi

# Check Python syntax
python_files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')
for file in $python_files; do
    if ! python -m py_compile "$file"; then
        echo "Python syntax error in $file"
        exit 1
    fi
done

echo "Pre-commit checks passed!"
EOF

chmod +x .git/hooks/pre-commit
```

## 📚 Emergency Procedures

### Complete Disaster Recovery
```bash
# When everything goes wrong, follow this procedure:

# 1. Don't panic - Git rarely loses data permanently
# 2. Create a backup of current state
cp -r .git .git-backup

# 3. Check reflog for recent activity
git reflog --all

# 4. Look for your work in dangling commits
git fsck --lost-found

# 5. If repository is corrupted, clone fresh
git clone https://github.com/user/repo.git repo-fresh
cd repo-fresh

# 6. Copy any uncommitted work from old repo
# 7. Use reflog to find and recover lost commits
git branch recovery-branch abc123  # Use commit hash from reflog
```

### Data Recovery Checklist
1. **Check reflog**: `git reflog --all`
2. **Check stash**: `git stash list`
3. **Check dangling commits**: `git fsck --lost-found`
4. **Check backup branches**: `git branch -a | grep backup`
5. **Check remote**: `git fetch --all`
6. **Check file history**: `git log --follow -- filename`
7. **Check working directory**: `find . -name "*.orig" -o -name "*~"`

## 📝 Key Takeaways

1. **Prevention is better than cure** - use hooks and good practices
2. **Git rarely loses data** - reflog and fsck are your friends
3. **Large files need special handling** - use Git LFS properly
4. **Conflicts are normal** - learn to resolve them systematically
5. **Backup before major operations** - create safety branches
6. **Document your recovery** - help future you and teammates
7. **Practice recovery scenarios** - don't wait for disasters

## 🚀 Next Steps

In the final section, we'll cover best practices and establish workflows that prevent most of these issues from occurring in the first place.

---

**Practice Challenge**: Intentionally create some of these problems in a test repository and practice the recovery techniques. Set up preventive measures like hooks and aliases. Create a "disaster recovery" plan for your team. 