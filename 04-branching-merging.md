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
# List local branches
git branch

# List all branches (local and remote)
git branch -a

# List remote branches
git branch -r

# Show last commit on each branch
git branch -v
```

### Branch Information
```bash
# Show current branch
git branch --show-current

# Show branches merged into current branch
git branch --merged

# Show branches not yet merged
git branch --no-merged
```

## 🧪 ML Experiment Workflow with Branches

Let's walk through a complete ML experiment using branches:

### 1. Start from Main Branch
```bash
# Make sure you're on main and up to date
git checkout main
git pull origin main
```

### 2. Create Experiment Branch
```bash
# Create branch for neural network experiment
git checkout -b experiment/neural-network

# Verify you're on the new branch
git branch --show-current
```

### 3. Implement Your Experiment
```bash
# Create neural network model
cat > src/models/neural_network.py << 'EOF'
import tensorflow as tf
from tensorflow.keras import layers, models
import numpy as np

class NeuralNetworkModel:
    def __init__(self, input_dim, hidden_units=[64, 32], output_dim=1):
        self.input_dim = input_dim
        self.hidden_units = hidden_units
        self.output_dim = output_dim
        self.model = None
        
    def build_model(self):
        """Build the neural network architecture."""
        self.model = models.Sequential()
        
        # Input layer
        self.model.add(layers.Dense(
            self.hidden_units[0], 
            activation='relu', 
            input_shape=(self.input_dim,)
        ))
        
        # Hidden layers
        for units in self.hidden_units[1:]:
            self.model.add(layers.Dense(units, activation='relu'))
            self.model.add(layers.Dropout(0.2))
        
        # Output layer
        self.model.add(layers.Dense(self.output_dim, activation='sigmoid'))
        
        # Compile model
        self.model.compile(
            optimizer='adam',
            loss='binary_crossentropy',
            metrics=['accuracy']
        )
        
        return self.model
    
    def train(self, X_train, y_train, X_val, y_val, epochs=100):
        """Train the model."""
        if self.model is None:
            self.build_model()
            
        history = self.model.fit(
            X_train, y_train,
            validation_data=(X_val, y_val),
            epochs=epochs,
            batch_size=32,
            verbose=1
        )
        
        return history
    
    def predict(self, X):
        """Make predictions."""
        return self.model.predict(X)
EOF
```

### 4. Commit Your Experiment
```bash
# Add and commit the new model
git add src/models/neural_network.py
git commit -m "Add neural network model implementation

- Implement flexible NN architecture
- Add dropout for regularization
- Include training and prediction methods
- Use binary crossentropy for classification"
```

### 5. Create Experiment Notebook
```bash
# Create experiment notebook
cat > notebooks/neural_network_experiment.ipynb << 'EOF'
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Neural Network Experiment\n",
    "\n",
    "Testing neural network performance on our dataset."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "import sys\n",
    "sys.path.append('../src')\n",
    "\n",
    "from models.neural_network import NeuralNetworkModel\n",
    "import pandas as pd\n",
    "from sklearn.model_selection import train_test_split"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Load and prepare data\n",
    "# TODO: Replace with actual data loading\n",
    "# df = pd.read_csv('../data/processed/clean_dataset.csv')\n",
    "print('Neural network experiment ready!')"
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

# Commit the notebook
git add notebooks/neural_network_experiment.ipynb
git commit -m "Add neural network experiment notebook"
```

## 🔄 Merging Branches

### Types of Merges

#### 1. Fast-Forward Merge (Simple)
When main branch hasn't changed since you created your branch:

```bash
# Switch to main
git checkout main

# Merge your experiment branch
git merge experiment/neural-network
```

#### 2. Three-Way Merge (More Common)
When both branches have new commits:

```bash
# Switch to main
git checkout main

# Pull latest changes
git pull origin main

# Merge with commit message
git merge experiment/neural-network -m "Merge neural network experiment

- Add NeuralNetworkModel class
- Include experiment notebook
- Ready for testing with real data"
```

### Merge Strategies
```bash
# Create merge commit (default)
git merge experiment/neural-network

# Fast-forward only (fails if not possible)
git merge --ff-only experiment/neural-network

# Always create merge commit
git merge --no-ff experiment/neural-network

# Squash all commits into one
git merge --squash experiment/neural-network
```

## ⚔️ Handling Merge Conflicts

Conflicts happen when both branches modify the same lines of code.

### Example Conflict Scenario
```bash
# On main branch, someone updated requirements.txt
echo "scikit-learn==1.1.0" >> requirements.txt
git add requirements.txt
git commit -m "Update scikit-learn version"

# On your branch, you also updated requirements.txt
git checkout experiment/neural-network
echo "tensorflow==2.8.0" >> requirements.txt
git add requirements.txt
git commit -m "Add tensorflow dependency"

# Now try to merge - conflict!
git checkout main
git merge experiment/neural-network
```

### Resolving Conflicts
```bash
# Git will show conflict markers in the file:
# <<<<<<< HEAD
# scikit-learn==1.1.0
# =======
# tensorflow==2.8.0
# >>>>>>> experiment/neural-network

# Edit the file to resolve conflicts
# Keep both dependencies:
scikit-learn==1.1.0
tensorflow==2.8.0

# Stage the resolved file
git add requirements.txt

# Complete the merge
git commit -m "Merge neural network experiment with updated dependencies"
```

### Conflict Resolution Tools
```bash
# Use merge tool
git mergetool

# Abort merge if needed
git merge --abort

# Show conflicts
git status
```

## 🧹 Branch Cleanup

### Deleting Branches
```bash
# Delete merged branch (safe)
git branch -d experiment/neural-network

# Force delete branch (even if not merged)
git branch -D experiment/failed-attempt

# Delete remote branch
git push origin --delete experiment/neural-network
```

### Pruning Remote Branches
```bash
# Remove references to deleted remote branches
git remote prune origin

# Or during fetch
git fetch --prune
```

## 🚀 ML Branching Strategies

### 1. Feature Branch Strategy
```bash
# Main branch for stable code
main

# Feature branches for new capabilities
feature/data-pipeline
feature/model-evaluation
feature/hyperparameter-tuning
```

### 2. Experiment Branch Strategy
```bash
# Main branch for production code
main

# Experiment branches for different approaches
experiment/baseline-model
experiment/deep-learning
experiment/ensemble-methods
experiment/feature-engineering
```

### 3. Git Flow for ML
```bash
# Production ready code
main

# Integration branch
develop

# Feature branches
feature/new-algorithm
feature/data-preprocessing

# Release branches
release/v1.0
release/v2.0

# Hotfix branches
hotfix/critical-bug
```

## 📊 Comparing Branches

### Diff Between Branches
```bash
# Compare two branches
git diff main..experiment/neural-network

# Show only file names
git diff --name-only main..experiment/neural-network

# Show statistics
git diff --stat main..experiment/neural-network
```

### Log Differences
```bash
# Show commits in experiment branch not in main
git log main..experiment/neural-network

# Show commits in both branches
git log main...experiment/neural-network
```

## 🎯 Best Practices for ML Branches

### Branch Naming Conventions
- Use descriptive names: `experiment/lstm-sentiment-analysis`
- Include ticket numbers: `feature/JIRA-123-data-pipeline`
- Use consistent prefixes: `experiment/`, `feature/`, `fix/`

### When to Branch
- ✅ New model experiments
- ✅ Feature engineering attempts
- ✅ Hyperparameter tuning sessions
- ✅ Algorithm comparisons
- ✅ Bug fixes

### When to Merge
- ✅ Experiment shows promising results
- ✅ Feature is complete and tested
- ✅ Code review is approved
- ✅ All tests pass

### Branch Hygiene
- Keep branches focused on single experiments
- Merge or delete branches regularly
- Don't let branches live too long
- Rebase before merging to keep history clean

## 🔄 Advanced: Rebasing

Rebasing rewrites history to create a cleaner timeline:

```bash
# Rebase your branch onto latest main
git checkout experiment/neural-network
git rebase main

# Interactive rebase to clean up commits
git rebase -i HEAD~3
```

**Warning**: Only rebase branches that haven't been shared!

## 📝 Key Takeaways

1. Branches enable parallel development and experimentation
2. Use descriptive branch names with consistent conventions
3. Merge strategies depend on your workflow needs
4. Resolve conflicts carefully, especially in data and config files
5. Clean up branches regularly to avoid clutter
6. ML projects benefit from experiment-focused branching

## 🚀 Next Steps

In the next section, we'll explore how Git integrates with ML workflows, including handling notebooks, data versioning, and experiment tracking.

---

**Practice Challenge**: Create multiple experiment branches, implement different approaches to the same problem, and practice merging them back. Try creating a merge conflict intentionally and resolve it. 