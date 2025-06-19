# 06. Handling Large Files with Git LFS

## 🎯 Learning Objectives
By the end of this section, you'll know how to:
- Install and configure Git LFS for ML projects
- Track large datasets, models, and binary files
- Manage LFS storage and bandwidth
- Integrate LFS into your ML workflow
- Troubleshoot common LFS issues

## 🤔 The Large File Problem in ML

### Why Regular Git Struggles with Large Files
- Git stores complete history of every file
- Large files make repository slow and bloated
- Network operations become expensive
- Clone times increase dramatically

### Common Large Files in ML Projects
- **Datasets**: CSV, Parquet, HDF5 files (>100MB)
- **Models**: Trained model files (.pkl, .joblib, .h5)
- **Media**: Images, videos, audio files
- **Binaries**: Compiled libraries, executables
- **Archives**: Compressed datasets (.zip, .tar.gz)

## 📦 What is Git LFS?

Git Large File Storage (LFS) is an extension that:
- Stores large files on a separate server
- Keeps lightweight pointers in your Git repository
- Downloads files on-demand when you need them
- Maintains Git's version control benefits

### How LFS Works
```
Regular Git:    [Repo] ← [Large File] (stored in Git history)
Git LFS:        [Repo] ← [Pointer] → [LFS Server] ← [Large File]
```

## 🛠 Installing Git LFS

### Installation
```bash
# macOS (using Homebrew)
brew install git-lfs

# Ubuntu/Debian
sudo apt install git-lfs

# Windows (download from https://git-lfs.github.io/)
# Or use Git for Windows (includes LFS)

# Verify installation
git lfs version
```

### Initialize LFS in Repository
```bash
# Navigate to your ML project
cd my-ml-project

# Initialize LFS
git lfs install

# Verify LFS is set up
git lfs env
```

## 📋 Tracking Files with LFS

### Basic File Tracking
```bash
# Track specific file types
git lfs track "*.csv"
git lfs track "*.pkl"
git lfs track "*.joblib"
git lfs track "*.h5"
git lfs track "*.hdf5"

# Track specific files
git lfs track "data/large_dataset.csv"
git lfs track "models/trained_model.pkl"

# Track files in specific directories
git lfs track "data/raw/**"
git lfs track "models/**"
```

### ML-Specific LFS Configuration
```bash
# Create comprehensive .gitattributes for ML
cat > .gitattributes << 'EOF'
# Data files
*.csv filter=lfs diff=lfs merge=lfs -text
*.tsv filter=lfs diff=lfs merge=lfs -text
*.parquet filter=lfs diff=lfs merge=lfs -text
*.h5 filter=lfs diff=lfs merge=lfs -text
*.hdf5 filter=lfs diff=lfs merge=lfs -text
*.feather filter=lfs diff=lfs merge=lfs -text

# Model files
*.pkl filter=lfs diff=lfs merge=lfs -text
*.pickle filter=lfs diff=lfs merge=lfs -text
*.joblib filter=lfs diff=lfs merge=lfs -text
*.model filter=lfs diff=lfs merge=lfs -text

# Deep learning models
*.pb filter=lfs diff=lfs merge=lfs -text
*.pth filter=lfs diff=lfs merge=lfs -text
*.pt filter=lfs diff=lfs merge=lfs -text
*.ckpt filter=lfs diff=lfs merge=lfs -text

# Compressed files
*.zip filter=lfs diff=lfs merge=lfs -text
*.tar.gz filter=lfs diff=lfs merge=lfs -text
*.rar filter=lfs diff=lfs merge=lfs -text

# Media files
*.jpg filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text
*.tiff filter=lfs diff=lfs merge=lfs -text
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.avi filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text
*.mp3 filter=lfs diff=lfs merge=lfs -text

# Jupyter notebook outputs (if you want to track them)
# *.ipynb filter=lfs diff=lfs merge=lfs -text
EOF

# Commit the .gitattributes file
git add .gitattributes
git commit -m "Configure Git LFS for ML project files"
```

## 🧪 LFS Workflow Example

Let's walk through a complete workflow with large files:

### 1. Set Up LFS Tracking
```bash
# Initialize LFS if not done already
git lfs install

# Track model files
git lfs track "*.pkl"
git lfs track "*.joblib"

# Commit tracking configuration
git add .gitattributes
git commit -m "Set up LFS tracking for model files"
```

### 2. Add Large Files
```bash
# Create a sample large model file (simulating a trained model)
python3 -c "
import pickle
import numpy as np

# Create a large dummy model (simulating a trained model)
large_model = {
    'weights': np.random.rand(10000, 1000),
    'biases': np.random.rand(1000),
    'metadata': {'accuracy': 0.95, 'epochs': 100}
}

with open('models/large_model.pkl', 'wb') as f:
    pickle.dump(large_model, f)

print('Created large model file (approx 80MB)')
"

# Check file size
ls -lh models/large_model.pkl

# Add the file (LFS will handle it automatically)
git add models/large_model.pkl

# Check LFS status
git lfs status

# Commit the file
git commit -m "Add trained model with 95% accuracy

Model details:
- Architecture: Deep neural network
- Training epochs: 100
- Validation accuracy: 0.95
- File size: ~80MB"
```

### 3. Push to Remote with LFS
```bash
# Push to remote (LFS files go to LFS server)
git push origin main

# Check LFS transfer
git lfs logs last
```

## 📊 Managing LFS Files

### Viewing LFS Information
```bash
# List tracked files
git lfs ls-files

# Show LFS tracking patterns
git lfs track

# Check LFS status
git lfs status

# View LFS environment info
git lfs env
```

### LFS File History
```bash
# View history of LFS files
git log --follow models/large_model.pkl

# Show LFS pointer content
git show HEAD:models/large_model.pkl

# Compare LFS file versions
git lfs diff HEAD~1..HEAD
```

## 💾 LFS Storage Management

### Checking Storage Usage
```bash
# Check local LFS cache size
git lfs fsck

# Show LFS objects in cache
git lfs ls-files --size

# Check remote LFS storage (if provider supports it)
git lfs ls-files --size --all
```

### Cleaning Up LFS Cache
```bash
# Clean old LFS files from cache
git lfs prune

# Clean with specific retention period
git lfs prune --recent

# Force clean all unreferenced files
git lfs prune --verify-remote --verbose
```

## 🔄 Cloning Repositories with LFS

### Standard Clone
```bash
# Clone repository (downloads LFS pointers)
git clone https://github.com/username/ml-project.git

# Download LFS files
cd ml-project
git lfs pull
```

### Clone with LFS Files
```bash
# Clone and download LFS files in one step
git lfs clone https://github.com/username/ml-project.git

# Or use standard clone with LFS flag
git clone --filter=blob:none https://github.com/username/ml-project.git
cd ml-project
git lfs checkout
```

### Partial LFS Downloads
```bash
# Clone without downloading LFS files initially
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/username/ml-project.git

# Download specific LFS files when needed
cd ml-project
git lfs pull --include="models/specific_model.pkl"

# Download LFS files for specific directory
git lfs pull --include="data/processed/*"
```

## 🎯 LFS Best Practices for ML

### File Organization Strategy
```bash
# Organize by file type and size
data/
├── raw/           # Large raw datasets (LFS)
├── processed/     # Processed datasets (LFS if large)
├── samples/       # Small sample datasets (regular Git)
└── metadata/      # Data descriptions (regular Git)

models/
├── trained/       # Large trained models (LFS)
├── checkpoints/   # Model checkpoints (LFS)
├── configs/       # Model configurations (regular Git)
└── metrics/       # Performance metrics (regular Git)
```

### Size Thresholds
```bash
# Set up size-based tracking
git lfs track --above=100MB

# Track files larger than specific sizes
find . -size +50M -name "*.csv" -exec git lfs track {} \;
```

## 🔧 Advanced LFS Configuration

### Custom LFS Configuration
```bash
# Configure LFS settings
git config lfs.fetchrecentalways true
git config lfs.fetchrecentrefsdays 7
git config lfs.fetchrecentcommitsdays 0

# Set concurrent transfers
git config lfs.concurrenttransfers 8

# Configure LFS URL (for custom servers)
git config lfs.url https://your-lfs-server.com/path
```

### LFS Hooks Integration
```bash
# Pre-push hook to verify LFS files
cat > .git/hooks/pre-push << 'EOF'
#!/bin/bash

echo "Checking LFS files before push..."

# Verify LFS files are properly tracked
lfs_files=$(git lfs ls-files --name-only)
large_files=$(find . -size +100M -type f -not -path "./.git/*")

for file in $large_files; do
    if ! echo "$lfs_files" | grep -q "$file"; then
        echo "Warning: Large file not tracked by LFS: $file"
        echo "Consider running: git lfs track \"$file\""
    fi
done

echo "LFS check complete"
EOF

chmod +x .git/hooks/pre-push
```

## 🚨 Troubleshooting LFS Issues

### Common Problems and Solutions

#### Problem: "This exceeds GitHub's file size limit"
```bash
# Solution: Track file with LFS
git lfs track "large_file.csv"
git add .gitattributes large_file.csv
git commit -m "Add large file with LFS tracking"
```

#### Problem: LFS files not downloading
```bash
# Check LFS installation
git lfs version

# Manually pull LFS files
git lfs pull

# Reset LFS cache
git lfs fsck --pointers
```

#### Problem: Repository size still large after LFS
```bash
# Check what's taking space
git lfs ls-files --size

# Clean up LFS cache
git lfs prune

# Remove files from Git history (if needed)
git filter-branch --tree-filter 'git lfs ls-files | git lfs clean'
```

#### Problem: LFS bandwidth exceeded
```bash
# Use sparse checkout to download only needed files
git config core.sparseCheckout true
echo "models/current_model.pkl" > .git/info/sparse-checkout
git lfs pull
```

## 💡 Integration with ML Platforms

### GitHub LFS
```bash
# GitHub provides 1GB free LFS storage
# Check your usage at: https://github.com/settings/billing

# For larger needs, consider GitHub LFS pricing
# Or use alternative storage backends
```

### Alternative LFS Backends
```bash
# Configure custom LFS server
git config lfs.url https://your-lfs-server.com/repo.git/info/lfs

# Use cloud storage (AWS S3, Google Cloud, etc.)
# Requires additional setup and authentication
```

## 📋 LFS Workflow Checklist

### Before Starting Project
- [ ] Install Git LFS
- [ ] Initialize LFS in repository
- [ ] Set up .gitattributes for ML file types
- [ ] Configure size thresholds

### During Development
- [ ] Track large files before adding them
- [ ] Use descriptive commit messages for model files
- [ ] Regularly check LFS status
- [ ] Monitor storage usage

### Before Sharing/Deployment
- [ ] Verify all large files are LFS-tracked
- [ ] Test clone and LFS download process
- [ ] Document LFS requirements in README
- [ ] Consider bandwidth implications for collaborators

## 📝 Key Takeaways

1. Git LFS is essential for ML projects with large files
2. Set up tracking patterns early in your project
3. Use .gitattributes to automatically track file types
4. Monitor storage usage and clean up regularly
5. Test your LFS setup with collaborators
6. Consider bandwidth costs for large-scale projects
7. Keep small metadata files in regular Git

## 🚀 Next Steps

In the next section, we'll learn about collaboration workflows, including code reviews, conflict resolution, and team Git practices for ML projects.

---

**Practice Challenge**: Set up Git LFS in a sample ML project, create some large dummy files (datasets and models), practice the complete workflow from tracking to pushing and cloning. Try different LFS commands and configurations to get comfortable with the system. 