# 01. Git Basics for ML Engineers

## 🎯 Learning Objectives
By the end of this section, you'll understand:
- What Git is and why it's essential for ML projects
- Basic Git concepts and terminology
- How to configure Git for your ML workflow

## 📖 What is Git?

Git is a distributed version control system that tracks changes in files over time. For ML engineers, it's invaluable for:

- **Code versioning**: Track changes in your ML scripts and notebooks
- **Experiment tracking**: Maintain different versions of models and experiments
- **Collaboration**: Work with team members on ML projects
- **Reproducibility**: Ensure your ML experiments can be reproduced

## 🔧 Initial Setup

### Check Git Installation
```bash
git --version
```

### Configure Your Identity
```bash
# Set your name and email (required for commits)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Verify your configuration
git config --list
```

### Useful Global Configurations for ML
```bash
# Set default editor (useful for commit messages)
git config --global core.editor "code --wait"  # VS Code
# or
git config --global core.editor "vim"          # Vim

# Set default branch name
git config --global init.defaultBranch main

# Enable colored output
git config --global color.ui auto

# Set up better diff for Jupyter notebooks
git config --global diff.ipynb.textconv "jupyter nbconvert --to script --stdout"
```

## 📚 Essential Git Concepts

### Repository (Repo)
A folder containing your project files and Git's tracking information.

### Working Directory
The current state of files in your project folder.

### Staging Area (Index)
A temporary area where you prepare changes before committing them.

### Commit
A snapshot of your project at a specific point in time.

### Branch
A parallel version of your repository where you can work on different features.

### Remote
A version of your repository hosted on a server (like GitHub).

## 🔄 The Git Workflow

```
Working Directory → Staging Area → Repository → Remote Repository
      (add)           (commit)        (push)
```

## 🎯 Basic Commands Overview

### Getting Help
```bash
# General help
git help

# Help for specific command
git help add
git help commit
```

### Repository Status
```bash
# Check status of your repository
git status

# View commit history
git log
git log --oneline    # Compact view
git log --graph      # Visual representation
```

## 🧪 Hands-on Exercise

Let's practice with a simple ML project structure:

1. **Create a practice directory:**
```bash
mkdir ml-git-practice
cd ml-git-practice
```

2. **Initialize a Git repository:**
```bash
git init
```

3. **Create a typical ML project structure:**
```bash
mkdir data models notebooks src
touch README.md requirements.txt
echo "# My ML Project" > README.md
echo "numpy==1.21.0" > requirements.txt
echo "pandas==1.3.0" >> requirements.txt
echo "scikit-learn==1.0.0" >> requirements.txt
```

4. **Check the status:**
```bash
git status
```

You should see untracked files listed in red.

## 💡 ML-Specific Tips

### Files to Track
- ✅ Source code (`.py`, `.R`)
- ✅ Configuration files (`.yaml`, `.json`)
- ✅ Requirements/environment files
- ✅ Documentation (`.md`)
- ✅ Small reference datasets

### Files NOT to Track (we'll cover `.gitignore` later)
- ❌ Large datasets
- ❌ Model files (unless small)
- ❌ Jupyter notebook checkpoints
- ❌ Cache files
- ❌ Virtual environment folders

## 🤔 Common Questions

**Q: Should I commit Jupyter notebooks?**
A: Yes, but be careful about output cells. Consider clearing outputs before committing or use tools like `nbstripout`.

**Q: How often should I commit?**
A: Commit frequently with logical, atomic changes. Each commit should represent a complete, working state.

**Q: What makes a good commit message?**
A: Be descriptive and specific. For ML projects:
- ✅ "Add data preprocessing pipeline"
- ✅ "Fix bug in model evaluation metrics"
- ❌ "Updated code"
- ❌ "Changes"

## 📝 Key Takeaways

1. Git is essential for ML project management and collaboration
2. Proper configuration saves time and prevents issues
3. Understanding the basic workflow is crucial
4. ML projects have specific considerations for what to track

## 🚀 Next Steps

In the next section, we'll learn how to create and clone repositories, setting up the foundation for your ML projects.

---

**Practice Challenge**: Set up your Git configuration and create a sample ML project structure. Try running `git status` and observe what Git tells you about your files. 