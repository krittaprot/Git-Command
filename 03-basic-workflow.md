# 03. Basic Git Workflow

## 🎯 Learning Objectives
By the end of this section, you'll master:
- The fundamental Git workflow: add, commit, push, pull
- How to track changes in ML projects
- Writing effective commit messages
- Managing file changes and history

## 🔄 The Core Git Workflow

```
Working Directory → Staging Area → Local Repository → Remote Repository
    (git add)      (git commit)       (git push)
```

## 📝 Adding Files to Staging Area

### Basic Add Commands
```bash
# Add specific file
git add filename.py

# Add multiple files
git add file1.py file2.py

# Add all Python files
git add *.py

# Add all files in current directory
git add .

# Add all files in repository
git add -A
```

### Interactive Adding (Useful for ML notebooks)
```bash
# Interactive staging - choose what to stage
git add -i

# Add parts of files (patch mode)
git add -p filename.py
```

### ML-Specific Adding Examples
```bash
# Add all source code
git add src/

# Add configuration files
git add config/ *.yaml *.json

# Add notebooks (be careful with outputs)
git add notebooks/*.ipynb

# Add requirements but not data
git add requirements.txt setup.py
```

## 💾 Committing Changes

### Basic Commit
```bash
# Commit with message
git commit -m "Add data preprocessing pipeline"

# Commit with detailed message
git commit -m "Add data preprocessing pipeline

- Implement missing value imputation
- Add feature scaling functions
- Create data validation checks"
```

### Advanced Commit Options
```bash
# Commit all tracked files (skip staging)
git commit -am "Update model parameters"

# Amend last commit (change message or add files)
git commit --amend -m "New commit message"

# Commit with longer message using editor
git commit
```

## 🚀 Pushing Changes

### Important Concept: Git Pushes Commits, Not Files
**Key Understanding**: Git doesn't push individual files. Instead, it pushes commits that contain changes to files.

```
Your Workflow:
1. Edit files
2. git add (stage specific files)
3. git commit (create a commit with those files)  
4. git push (send the commit to remote repository)
```

### Basic Push
```bash
# Push to remote repository
git push

# Push and set upstream (first time)
git push -u origin main

# Push specific branch
git push origin feature-branch
```

### Working with Specific Files - The Right Way
```bash
# Scenario: You only want to push changes to one file

# 1. Add only the file you want to include
git add models/neural_network.py

# 2. Commit just that file
git commit -m "Update neural network architecture"

# 3. Push the commit (which contains your file changes)
git push

# The commit goes to remote, bringing your file changes with it
```

### Multiple Files Example
```bash
# Add specific files you want to push
git add config.yaml requirements.txt models/

# Commit them together
git commit -m "Update configuration, dependencies, and models"

# Push the commit
git push

# All three changes are now on the remote repository
```

### What Happens During Push
```bash
# Before push - check what will be pushed
git log origin/main..HEAD

# This shows commits that exist locally but not on remote
# Each commit contains the file changes you made

# After push, remote repository has your commits
# and therefore has your file changes
```

### Force Push (Use with caution!)
```bash
# Force push (overwrites remote history)
git push --force

# Safer force push (won't overwrite others' work)
git push --force-with-lease
```

## 📥 Pulling Changes

### Basic Pull
```bash
# Pull changes from remote
git pull

# Pull from specific remote and branch
git pull origin main
```

### Pull vs Fetch
```bash
# Fetch changes without merging
git fetch

# View what was fetched
git log HEAD..origin/main

# Then merge manually
git merge origin/main
```

## 🧪 Complete Workflow Example

Let's work through a complete ML project workflow:

### 1. Start with a Clean Repository
```bash
# Check status
git status

# Should show "working tree clean"
```

### 2. Create ML Files
```bash
# Create a data processing script
cat > src/data_preprocessing.py << 'EOF'
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler

def load_data(filepath):
    """Load data from CSV file."""
    return pd.read_csv(filepath)

def preprocess_data(df):
    """Clean and preprocess the data."""
    # Remove missing values
    df_clean = df.dropna()
    
    # Scale numerical features
    scaler = StandardScaler()
    numerical_cols = df_clean.select_dtypes(include=[np.number]).columns
    df_clean[numerical_cols] = scaler.fit_transform(df_clean[numerical_cols])
    
    return df_clean, scaler

if __name__ == "__main__":
    # Example usage
    data = load_data("data/raw/dataset.csv")
    processed_data, scaler = preprocess_data(data)
    processed_data.to_csv("data/processed/clean_dataset.csv", index=False)
EOF
```

### 3. Check What Changed
```bash
# See status
git status

# See detailed changes
git diff
```

### 4. Stage and Commit
```bash
# Add the new file
git add src/data_preprocessing.py

# Check what's staged
git status

# Commit with descriptive message
git commit -m "Add data preprocessing pipeline

- Implement data loading function
- Add missing value handling
- Include feature scaling with StandardScaler
- Add example usage in main block"
```

### 5. Create a Notebook
```bash
# Create analysis notebook
cat > notebooks/exploratory_analysis.ipynb << 'EOF'
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Exploratory Data Analysis\n",
    "\n",
    "Initial exploration of the dataset."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt\n",
    "import seaborn as sns\n",
    "\n",
    "# Load data\n",
    "df = pd.read_csv('../data/raw/dataset.csv')\n",
    "df.head()"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
EOF
```

### 6. Stage and Commit Notebook
```bash
# Add notebook
git add notebooks/exploratory_analysis.ipynb

# Commit
git commit -m "Add initial exploratory data analysis notebook"
```

### 7. Push All Changes
```bash
# Push to remote repository
git push
```

## 📊 Viewing History and Changes

### Log Commands
```bash
# View commit history
git log

# Compact view
git log --oneline

# Show file changes
git log --stat

# Show actual changes
git log -p

# Filter by author
git log --author="Your Name"

# Filter by date
git log --since="2023-01-01" --until="2023-12-31"
```

### Diff Commands
```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged

# Compare specific commits
git diff commit1 commit2

# Compare specific files
git diff HEAD~1 filename.py
```

## ✍️ Writing Good Commit Messages

### ML-Specific Commit Message Examples

**Good Examples:**
```bash
git commit -m "Add random forest model with hyperparameter tuning"

git commit -m "Fix data leakage in feature engineering pipeline"

git commit -m "Improve model accuracy from 0.85 to 0.89

- Add polynomial features
- Implement cross-validation
- Tune regularization parameters"

git commit -m "Add unit tests for data preprocessing functions"
```

**Bad Examples:**
```bash
git commit -m "update"
git commit -m "fix bug"
git commit -m "changes"
git commit -m "notebook stuff"
```

### Commit Message Template
```
Type: Brief description (50 chars or less)

More detailed explanation if needed. Wrap at 72 characters.
Explain what and why, not how.

- Use bullet points for multiple changes
- Reference issue numbers if applicable
- Include performance improvements or metrics
```

## 🔍 Checking File Status

### Status Commands
```bash
# Full status
git status

# Short status
git status -s

# Show ignored files too
git status --ignored
```

### Understanding Status Output
- **Untracked**: New files not yet added to Git
- **Modified**: Changed files not yet staged
- **Staged**: Files ready to be committed
- **Clean**: No changes to commit

## 🧹 Undoing Changes

### Unstage Files
```bash
# Unstage specific file
git reset filename.py

# Unstage all files
git reset
```

### Discard Changes
```bash
# Discard changes in working directory
git checkout -- filename.py

# Discard all changes
git checkout -- .

# Reset to last commit (careful!)
git reset --hard HEAD
```

## 🎯 ML Workflow Best Practices

### Commit Frequency
- Commit after each logical unit of work
- Before switching tasks or taking breaks
- After successful experiments or improvements
- Before major refactoring

### What to Commit Together
- ✅ Related changes (e.g., function + its test)
- ✅ Bug fix and its documentation update
- ✅ Model improvement and updated metrics
- ❌ Unrelated changes in same commit
- ❌ Work-in-progress code that doesn't run

### Staging Strategy for ML
```bash
# Stage related files together
git add src/models/random_forest.py tests/test_random_forest.py

# Stage configuration changes
git add config/model_params.yaml config/training_config.json

# Commit them together
git commit -m "Add random forest model with configuration and tests"
```

## 🤔 Common Issues and Solutions

**Problem**: Committed wrong files
**Solution**: Use `git reset --soft HEAD~1` to undo commit but keep changes

**Problem**: Want to change last commit message
**Solution**: Use `git commit --amend -m "New message"`

**Problem**: Large file accidentally committed
**Solution**: Use `git rm --cached large_file.csv` then commit removal

**Problem**: Merge conflicts during pull
**Solution**: We'll cover this in the collaboration section!

## 📝 Key Takeaways

1. The basic workflow is: add → commit → push
2. Stage changes thoughtfully, not just `git add .`
3. Write descriptive commit messages
4. Commit frequently with logical units of work
5. Always check status before and after operations
6. Use diff to review changes before committing

## 🚀 Next Steps

In the next section, we'll learn about branching and merging - essential skills for managing different experiments and features in ML projects.

---

**Practice Challenge**: Create several files in your ML project, make changes, and practice the complete workflow. Try staging files selectively, writing good commit messages, and pushing to your remote repository. Experiment with viewing history and undoing changes. 