# 04. Branching & Merging for ML Projects

## 🎯 Learning Objectives
By the end of this section, you'll understand:
- How to create and manage branches for ML experiments
- Different branching strategies for ML workflows
- How to merge branches and resolve conflicts
- Best practices for organizing ML experiments with branches

## 🌳 Understanding Branches

Think of branches as parallel universes of your code where you can:
- Experiment with different models
- Try new features without breaking main code
- Collaborate on different parts of the project
- Keep stable and experimental code separate

```
main branch:     A---B---C---F---G
                  \         /
feature branch:    D---E---/
```

## 🆕 Creating and Switching Branches

### Basic Branch Commands
```bash
# Create a new branch
git branch experiment-lstm

# Switch to the branch
git checkout experiment-lstm

# Create and switch in one command
git checkout -b experiment-random-forest

# Modern way (Git 2.23+)
git switch -c experiment-xgboost

# Switch back to main
git checkout main
git switch main  # modern way
```

### Branch Naming for ML Projects
```bash
# Feature branches
git checkout -b feature/data-augmentation
git checkout -b feature/model-ensemble

# Experiment branches
git checkout -b experiment/hyperparameter-tuning
git checkout -b experiment/different-architecture

# Bug fix branches
git checkout -b fix/data-leakage
git checkout -b fix/memory-optimization

# Research branches
git checkout -b research/new-algorithm
git checkout -b research/literature-review
```

## 📋 Managing Branches

### Viewing Branches
```bash
# List local branches (* shows current branch)
git branch

# List all branches (local and remote)
git branch -a

# List remote branches
git branch -r

# Show last commit on each branch
git branch -v

# Show current branch
git branch --show-current
```

### Branch Information
```bash
# Show branches merged into current branch
git branch --merged

# Show branches not yet merged
git branch --no-merged

# Show branch tracking information
git branch -vv
```

## 🔄 Complete Branching Workflow

### Scenario: Adding a New ML Model

#### Step 1: Start from Main
```bash
# Make sure you're on main and up to date
git checkout main
git pull origin main

# Check status
git status
# Should show: "On branch main, nothing to commit, working tree clean"
```

#### Step 2: Create Feature Branch
```bash
# Create and switch to new branch
git checkout -b feature/random-forest-model

# Verify you're on the new branch
git branch --show-current
# Should show: feature/random-forest-model
```

#### Step 3: Work on Your Feature
```bash
# Create your new model file
echo "# Random Forest Model Implementation" > models/random_forest.py

# Add some actual code
cat >> models/random_forest.py << 'EOF'
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, classification_report

class RandomForestModel:
    def __init__(self, n_estimators=100, random_state=42):
        self.model = RandomForestClassifier(
            n_estimators=n_estimators,
            random_state=random_state
        )
        self.is_trained = False
    
    def train(self, X_train, y_train):
        """Train the Random Forest model."""
        self.model.fit(X_train, y_train)
        self.is_trained = True
        return self
    
    def predict(self, X_test):
        """Make predictions."""
        if not self.is_trained:
            raise ValueError("Model must be trained before making predictions")
        return self.model.predict(X_test)
    
    def evaluate(self, X_test, y_test):
        """Evaluate model performance."""
        predictions = self.predict(X_test)
        accuracy = accuracy_score(y_test, predictions)
        report = classification_report(y_test, predictions)
        return accuracy, report
EOF
```

#### Step 4: Commit Your Changes
```bash
# Check what changed
git status
git diff

# Add and commit
git add models/random_forest.py
git commit -m "Add Random Forest model implementation

- Implement RandomForestModel class
- Add training, prediction, and evaluation methods
- Include proper error handling for untrained model"
```

#### Step 5: Push Your Branch
```bash
# Push the branch to remote repository
git push -u origin feature/random-forest-model

# The -u flag sets up tracking so future pushes can just use 'git push'
```

## 🔀 Merging Branches

### Method 1: Merge (Creates Merge Commit)
```bash
# Switch back to main
git checkout main

# Make sure main is up to date
git pull origin main

# Merge your feature branch
git merge feature/random-forest-model

# Push the merged changes
git push origin main
```

### Method 2: Rebase (Linear History)
```bash
# On your feature branch
git checkout feature/random-forest-model

# Rebase onto main (replays your commits on top of main)
git rebase main

# Switch to main and merge (fast-forward)
git checkout main
git merge feature/random-forest-model

# Push
git push origin main
```

### Method 3: Squash Merge (Combines all commits into one)
```bash
# On main branch
git checkout main
git pull origin main

# Squash merge (combines all feature commits into one)
git merge --squash feature/random-forest-model

# This stages all changes but doesn't commit
git commit -m "Add Random Forest model with full implementation"

# Push
git push origin main
```

## 🛠️ Handling Merge Conflicts

### When Conflicts Occur
```bash
# During merge, if conflicts occur:
git merge feature/data-preprocessing
# Auto-merging data/preprocessing.py
# CONFLICT (content): Merge conflict in data/preprocessing.py
# Automatic merge failed; fix conflicts and then commit the result.
```

### Resolving Conflicts
1. **Check conflict status:**
```bash
git status
# Shows files with conflicts
```

2. **Open conflicted files and look for conflict markers:**
```python
# In your file, you'll see:
<<<<<<< HEAD
# Code from main branch
def preprocess_data(df):
    return df.dropna()
=======
# Code from feature branch  
def preprocess_data(df):
    return df.fillna(0)
>>>>>>> feature/data-preprocessing
```

3. **Resolve the conflict by editing the file:**
```python
# Choose one version or combine them:
def preprocess_data(df):
    # Use fillna for numeric columns, dropna for others
    numeric_cols = df.select_dtypes(include=[np.number]).columns
    df[numeric_cols] = df[numeric_cols].fillna(0)
    return df.dropna()
```

4. **Mark as resolved and commit:**
```bash
# Add the resolved file
git add data/preprocessing.py

# Commit the merge
git commit -m "Merge feature/data-preprocessing

Resolved conflict in preprocessing function by combining
both fillna and dropna approaches"
```

## 🧹 Cleaning Up Branches

### Delete Branches After Merging
```bash
# Delete local branch (safe - only works if merged)
git branch -d feature/random-forest-model

# Force delete local branch (use with caution)
git branch -D feature/random-forest-model

# Delete remote branch
git push origin --delete feature/random-forest-model
```

### List and Clean Old Branches
```bash
# See which branches are merged
git branch --merged

# Clean up remote tracking branches that no longer exist
git remote prune origin

# Interactive cleanup
git branch --merged | grep -v "main\|master" | xargs -n 1 git branch -d
```

## 🎯 ML-Specific Branching Strategies

### 1. Experiment Branches
```bash
# For each ML experiment
git checkout -b experiment/lstm-attention-model
git checkout -b experiment/hyperparameter-search-v2
git checkout -b experiment/data-augmentation-test
```

### 2. Model Development Workflow
```bash
# Start experiment
git checkout -b experiment/new-architecture

# Work on model, commit frequently
git add model.py
git commit -m "Initial model structure"

git add training.py  
git commit -m "Add training loop"

git add evaluation.py
git commit -m "Add evaluation metrics"

# If experiment succeeds, merge to main
git checkout main
git merge experiment/new-architecture

# If experiment fails, just delete the branch
git branch -D experiment/new-architecture
```

### 3. Collaborative ML Workflow
```bash
# Team member 1: Data preprocessing
git checkout -b feature/data-pipeline

# Team member 2: Model development  
git checkout -b feature/model-architecture

# Team member 3: Evaluation metrics
git checkout -b feature/evaluation-suite

# Each person works independently, then merges back
```

## 🚨 Common Branching Mistakes to Avoid

### 1. Working Directly on Main
```bash
# ❌ Don't do this
git checkout main
# ... make changes directly ...
git commit -m "Quick fix"

# ✅ Do this instead
git checkout -b fix/quick-issue
# ... make changes ...
git commit -m "Fix issue"
git checkout main
git merge fix/quick-issue
```

### 2. Not Keeping Main Up to Date
```bash
# ❌ Creating branch from outdated main
git checkout main
# (main is 5 commits behind)
git checkout -b new-feature

# ✅ Always update main first
git checkout main
git pull origin main
git checkout -b new-feature
```

### 3. Large, Long-Running Branches
```bash
# ❌ One huge branch with everything
git checkout -b massive-refactor
# ... 50 commits later ...

# ✅ Break into smaller branches
git checkout -b refactor/data-loading
git checkout -b refactor/model-training  
git checkout -b refactor/evaluation
```

## 📊 Visualizing Branch History

### Useful Commands for Understanding History
```bash
# Pretty graph of commits
git log --oneline --graph --all

# See branch relationships
git show-branch

# Detailed history with branches
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --all
```

## 🎯 Quick Reference

### Essential Branch Commands
```bash
# Create and switch to branch
git checkout -b branch-name

# Switch branches
git checkout main
git switch branch-name

# See all branches
git branch -a

# Merge branch into current
git merge branch-name

# Delete branch
git branch -d branch-name

# Push branch to remote
git push -u origin branch-name
```

### Typical ML Workflow
1. `git checkout main && git pull` - Start from updated main
2. `git checkout -b experiment/new-idea` - Create experiment branch
3. Work, add, commit changes
4. `git push -u origin experiment/new-idea` - Push branch
5. If successful: merge to main
6. If unsuccessful: delete branch

Remember: Branches are cheap and fast in Git. Use them liberally for experiments! 