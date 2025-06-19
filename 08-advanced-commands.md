# 08. Advanced Git Commands for ML

## 🎯 Learning Objectives
By the end of this section, you'll master:
- Advanced Git commands for complex ML workflows
- Interactive rebasing for cleaning up experiment history
- Cherry-picking for selective feature adoption
- Stashing for quick context switching
- Advanced history manipulation and recovery

## 🔄 Interactive Rebasing

### Why Rebase in ML Projects?
- Clean up messy experiment history
- Combine related commits before merging
- Reorder commits logically
- Edit commit messages for clarity
- Remove sensitive data or large files

### Basic Interactive Rebase
```bash
# Rebase last 3 commits interactively
git rebase -i HEAD~3

# Rebase from specific commit
git rebase -i abc123

# Rebase entire branch onto main
git rebase -i main
```

### Interactive Rebase Options
```bash
# Git opens editor with something like:
pick f7f3f6d Add data preprocessing
pick 310154e Fix bug in feature scaling
pick a5f4a0d Add model evaluation metrics

# Change 'pick' to:
# edit    - pause to edit this commit
# reword  - change commit message
# squash  - combine with previous commit
# fixup   - like squash but discard message
# drop    - remove this commit
# exec    - run shell command
```

### ML Experiment Cleanup Example
```bash
# Before rebase - messy history:
# f7f3f6d Add neural network model
# 310154e Fix typo
# a5f4a0d Add dropout layers
# b2c3d4e Fix another typo
# c4d5e6f Update learning rate
# d6e7f8g Final model version

# Interactive rebase to clean up
git rebase -i HEAD~6

# In editor, change to:
pick f7f3f6d Add neural network model
fixup 310154e Fix typo
squash a5f4a0d Add dropout layers
fixup b2c3d4e Fix another typo
squash c4d5e6f Update learning rate
reword d6e7f8g Final model version

# Result - clean history:
# abc123d Add neural network model with dropout and tuned learning rate
# def456e Final model version with 89% accuracy
```

### Editing Commits During Rebase
```bash
# If you chose 'edit' for a commit
git rebase -i HEAD~3

# Git pauses at the commit
# Make your changes
echo "# Updated model with better performance" >> models/README.md
git add models/README.md

# Continue rebase
git rebase --continue

# If you make a mistake
git rebase --abort
```

## 🍒 Cherry-Picking

### What is Cherry-Picking?
Cherry-picking applies specific commits from one branch to another without merging the entire branch.

### Basic Cherry-Pick
```bash
# Apply specific commit to current branch
git cherry-pick abc123

# Apply multiple commits
git cherry-pick abc123 def456 ghi789

# Apply commit range
git cherry-pick abc123..def456
```

### ML Use Cases for Cherry-Picking
```bash
# Scenario: You have a bug fix in experimental branch that you need in main
git checkout main
git cherry-pick experiment/neural-network:bug-fix-commit

# Scenario: Pick specific feature from experiment
git checkout feature/data-pipeline
git cherry-pick experiment/alice-lstm:data-augmentation-commit

# Scenario: Apply hotfix to multiple branches
git checkout main
git cherry-pick hotfix/memory-leak-fix
git checkout develop
git cherry-pick hotfix/memory-leak-fix
```

### Cherry-Pick with Modifications
```bash
# Cherry-pick without committing (to make changes)
git cherry-pick --no-commit abc123

# Make modifications
vim src/model.py

# Commit with new message
git commit -m "Apply data augmentation fix with modifications"

# Cherry-pick and edit commit message
git cherry-pick --edit abc123
```

### Handling Cherry-Pick Conflicts
```bash
# If cherry-pick causes conflicts
git cherry-pick abc123

# Git pauses with conflicts
# Edit conflicted files
vim conflicted_file.py

# Stage resolved files
git add conflicted_file.py

# Continue cherry-pick
git cherry-pick --continue

# Or abort if needed
git cherry-pick --abort
```

## 💾 Stashing for Context Switching

### Why Stash in ML Projects?
- Quickly switch between experiments
- Save work-in-progress before pulling updates
- Test different approaches without committing
- Handle urgent bug fixes during experiments

### Basic Stashing
```bash
# Stash current changes
git stash

# Stash with message
git stash push -m "WIP: hyperparameter tuning experiment"

# List stashes
git stash list

# Apply most recent stash
git stash pop

# Apply specific stash
git stash apply stash@{1}

# Drop stash without applying
git stash drop stash@{0}
```

### Advanced Stashing
```bash
# Stash only staged changes
git stash push --staged -m "Staged model improvements"

# Stash specific files
git stash push src/model.py config/params.yaml -m "Model and config changes"

# Stash including untracked files
git stash push -u -m "Include new experiment files"

# Create branch from stash
git stash branch experiment/stashed-work stash@{0}
```

### ML Workflow with Stashing
```bash
# Working on neural network experiment
vim src/models/neural_network.py

# Urgent: need to fix bug in main branch
git stash push -m "NN experiment in progress"

# Switch to main and fix bug
git checkout main
git pull origin main
# ... fix bug ...
git add . && git commit -m "Fix critical preprocessing bug"
git push origin main

# Back to experiment
git checkout experiment/neural-network
git stash pop

# Continue working
vim src/models/neural_network.py
```

## 🔍 Advanced History Exploration

### Powerful Log Commands
```bash
# Show commits with file changes
git log --stat

# Show commits that changed specific file
git log --follow src/models/neural_network.py

# Show commits by author
git log --author="Alice" --since="2024-01-01"

# Show commits with specific message pattern
git log --grep="experiment" --grep="neural" --all-match

# Show commits that added/removed specific text
git log -S "learning_rate" --source --all

# Show merge commits only
git log --merges --oneline

# Show branch topology
git log --graph --oneline --all
```

### Advanced Diff Commands
```bash
# Compare branches
git diff main..experiment/neural-network

# Show only file names that changed
git diff --name-only HEAD~1 HEAD

# Show diff statistics
git diff --stat main..experiment/neural-network

# Compare specific commits
git diff abc123..def456 -- src/models/

# Show diff for specific function
git diff -U10 HEAD~1 HEAD -- src/models/neural_network.py
```

### Finding Specific Changes
```bash
# Find when specific line was added/removed
git blame src/models/neural_network.py

# Find commits that introduced bugs
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
# Git will help you find the problematic commit

# Show who changed what in a file
git log -p --follow src/models/neural_network.py
```

## 🔧 Advanced Branch Operations

### Branch Manipulation
```bash
# Rename current branch
git branch -m new-experiment-name

# Rename other branch
git branch -m old-name new-name

# Copy branch
git checkout -b experiment/neural-network-copy experiment/neural-network

# Set upstream for branch
git branch --set-upstream-to=origin/experiment/neural-network

# Delete remote branch
git push origin --delete experiment/failed-experiment
```

### Advanced Merging
```bash
# Merge with custom strategy
git merge -X ours experiment/neural-network

# Merge without fast-forward (always create merge commit)
git merge --no-ff experiment/neural-network

# Merge and squash all commits
git merge --squash experiment/neural-network

# Abort merge
git merge --abort

# Create merge commit with custom message
git merge experiment/neural-network -m "Merge neural network experiment

Results:
- Accuracy improved from 0.85 to 0.89
- Training time reduced by 20%
- Model size reduced by 15%"
```

## 🔄 Rewriting History (Use with Caution!)

### Filter-Branch for Large Changes
```bash
# Remove file from entire history
git filter-branch --tree-filter 'rm -f data/sensitive_data.csv' HEAD

# Remove large files from history
git filter-branch --tree-filter 'find . -name "*.pkl" -delete' HEAD

# Change author information
git filter-branch --env-filter '
if [ "$GIT_COMMITTER_EMAIL" = "old@email.com" ]
then
    export GIT_COMMITTER_NAME="New Name"
    export GIT_COMMITTER_EMAIL="new@email.com"
fi
if [ "$GIT_AUTHOR_EMAIL" = "old@email.com" ]
then
    export GIT_AUTHOR_NAME="New Name"
    export GIT_AUTHOR_EMAIL="new@email.com"
fi
' HEAD
```

### BFG Repo-Cleaner (Recommended for large cleanups)
```bash
# Install BFG (more efficient than filter-branch)
# Download from https://rtyley.github.io/bfg-repo-cleaner/

# Remove large files
java -jar bfg.jar --strip-blobs-bigger-than 100M my-repo.git

# Remove specific files
java -jar bfg.jar --delete-files "*.pkl" my-repo.git

# Remove sensitive data
java -jar bfg.jar --replace-text passwords.txt my-repo.git
```

## 🔍 Advanced Recovery Techniques

### Reflog - Your Safety Net
```bash
# Show reflog (history of HEAD changes)
git reflog

# Show reflog for specific branch
git reflog experiment/neural-network

# Recover deleted branch
git checkout -b recovered-branch HEAD@{5}

# Recover after hard reset
git reset --hard HEAD@{2}
```

### Finding Lost Commits
```bash
# Show all unreachable commits
git fsck --lost-found

# Show dangling commits
git fsck --dangling

# Create branch from dangling commit
git branch recovered-experiment abc123
```

### Emergency Recovery
```bash
# If you accidentally deleted important work
git reflog
# Find the commit hash before the mistake
git checkout -b emergency-recovery abc123

# If you force-pushed and lost commits
git reflog origin/main
# Find the commit before force-push
git push origin abc123:main --force-with-lease
```

## 🎯 ML-Specific Advanced Workflows

### Experiment Branch Management
```bash
# Create experiment tracking branch
git checkout -b experiments/tracking
git checkout --orphan experiments/tracking  # Clean branch with no history

# Track all experiments
cat > experiments.md << 'EOF'
# Experiment Tracking

## Active Experiments
- experiment/alice-lstm: LSTM for sentiment analysis
- experiment/bob-transformer: Transformer architecture
- experiment/charlie-ensemble: Ensemble methods

## Completed Experiments
- experiment/baseline-model: Random Forest baseline (accuracy: 0.82)
- experiment/svm-model: SVM with RBF kernel (accuracy: 0.79)
EOF

git add experiments.md
git commit -m "Initialize experiment tracking"

# Merge experiment summaries
git checkout experiments/tracking
git merge --no-ff --allow-unrelated-histories experiment/alice-lstm
```

### Model Version Management
```bash
# Create model registry branch
git checkout --orphan model-registry

# Track model versions
cat > model_registry.json << 'EOF'
{
  "models": [
    {
      "name": "sentiment_classifier_v1",
      "commit": "abc123",
      "accuracy": 0.89,
      "f1_score": 0.87,
      "training_date": "2024-01-15",
      "status": "production"
    },
    {
      "name": "sentiment_classifier_v2",
      "commit": "def456",
      "accuracy": 0.91,
      "f1_score": 0.89,
      "training_date": "2024-01-20",
      "status": "testing"
    }
  ]
}
EOF

git add model_registry.json
git commit -m "Initialize model registry"

# Update registry when deploying models
echo "Model deployed to production" | git commit-tree HEAD^{tree} -p HEAD -F -
```

## 🛠 Custom Git Commands for ML

### Create Custom Git Aliases
```bash
# Add to ~/.gitconfig or use git config --global
git config --global alias.experiment-start '!f() { git checkout -b experiment/$(whoami)-$1; }; f'
git config --global alias.experiment-finish '!f() { git checkout main && git merge --no-ff experiment/$(whoami)-$1; }; f'
git config --global alias.model-tag '!f() { git tag -a model-v$1 -m "Model version $1 - Accuracy: $2"; }; f'

# Usage:
git experiment-start lstm-sentiment
git experiment-finish lstm-sentiment
git model-tag 1.0 0.89
```

### Custom Scripts
```bash
# Create git-ml-summary script
cat > /usr/local/bin/git-ml-summary << 'EOF'
#!/bin/bash
echo "=== ML Project Summary ==="
echo "Current branch: $(git branch --show-current)"
echo "Last commit: $(git log -1 --oneline)"
echo "Experiment branches:"
git branch | grep experiment/ | head -5
echo "Model tags:"
git tag | grep model- | tail -5
echo "Recent activity:"
git log --oneline --since="1 week ago" | head -10
EOF

chmod +x /usr/local/bin/git-ml-summary

# Usage:
git ml-summary
```

## ⚠️ Safety Best Practices

### Before Rewriting History
```bash
# Always create backup branch
git branch backup-before-rebase

# Check what will be changed
git rebase -i --dry-run HEAD~5

# Use --force-with-lease instead of --force
git push --force-with-lease origin experiment/neural-network
```

### Recovery Checklist
1. **Check reflog**: `git reflog`
2. **Look for backups**: `git branch | grep backup`
3. **Check remote**: `git fetch origin`
4. **Use fsck**: `git fsck --lost-found`
5. **Create recovery branch**: `git checkout -b recovery abc123`

## 📝 Key Takeaways

1. **Interactive rebase** cleans up messy experiment history
2. **Cherry-picking** selectively applies changes across branches
3. **Stashing** enables quick context switching during experiments
4. **Advanced log/diff** commands help explore complex histories
5. **History rewriting** is powerful but dangerous - use with caution
6. **Reflog** is your safety net for recovery
7. **Custom aliases** streamline repetitive ML workflows

## 🚀 Next Steps

In the next section, we'll learn troubleshooting techniques for common Git issues in ML projects and how to recover from various problematic situations.

---

**Practice Challenge**: Set up a complex experiment history, practice interactive rebasing to clean it up, try cherry-picking features between branches, and experiment with stashing during context switches. Create custom aliases for your ML workflow. 