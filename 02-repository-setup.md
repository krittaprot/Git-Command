# 02. Repository Setup for ML Projects

## 🎯 Learning Objectives
By the end of this section, you'll know how to:
- Initialize new Git repositories
- Clone existing repositories
- Set up remote repositories
- Create proper ML project structure with Git

## 🆕 Creating a New Repository

### Initialize a Local Repository
```bash
# Navigate to your project directory
cd my-ml-project

# Initialize Git repository
git init

# Check the hidden .git directory was created
ls -la
```

### Create Repository with Initial Structure
```bash
# Create and navigate to new directory
mkdir sentiment-analysis-project
cd sentiment-analysis-project

# Initialize repository
git init

# Create ML project structure
mkdir -p {data/{raw,processed,external},models,notebooks,src,tests,config}
touch README.md .gitignore requirements.txt setup.py
```

## 📥 Cloning Existing Repositories

### Basic Clone
```bash
# Clone a repository
git clone https://github.com/username/repo-name.git

# Clone with custom directory name
git clone https://github.com/username/repo-name.git my-local-name

# Clone specific branch
git clone -b branch-name https://github.com/username/repo-name.git
```

### Clone for ML Projects
```bash
# Clone a popular ML repository
git clone https://github.com/scikit-learn/scikit-learn.git

# Clone only recent history (faster for large repos)
git clone --depth 1 https://github.com/tensorflow/tensorflow.git

# Clone without downloading LFS files initially
git clone --filter=blob:none https://github.com/username/large-ml-repo.git
```

## 🌐 Working with Remotes

### Understanding Remotes
Remotes are versions of your repository hosted elsewhere (GitHub, GitLab, etc.).

```bash
# View current remotes
git remote -v

# Add a remote
git remote add origin https://github.com/username/repo-name.git

# Change remote URL
git remote set-url origin https://github.com/username/new-repo-name.git

# Remove a remote
git remote remove origin
```

### Setting Up GitHub Repository

1. **Create repository on GitHub** (through web interface)

2. **Connect local repository to GitHub:**
```bash
# Add GitHub as remote origin
git remote add origin https://github.com/yourusername/your-repo.git

# Verify remote was added
git remote -v
```

## 📁 ML Project Structure Template

Here's a recommended structure for ML projects:

```bash
# Create comprehensive ML project structure
mkdir ml-project-template
cd ml-project-template
git init

# Create directory structure
mkdir -p {data/{raw,processed,external},models/{trained,saved},notebooks/{exploratory,final},src/{data,features,models,visualization},tests,config,docs,scripts}

# Create essential files
touch README.md
touch requirements.txt
touch setup.py
touch .gitignore
touch config/config.yaml
touch src/__init__.py
touch tests/__init__.py

# Create sample files
echo "# ML Project Template" > README.md
echo "jupyter" > requirements.txt
echo "pandas" >> requirements.txt
echo "scikit-learn" >> requirements.txt
echo "matplotlib" >> requirements.txt
```

## 🚫 Setting Up .gitignore for ML Projects

Create a comprehensive `.gitignore` file:

```bash
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
env.bak/
venv.bak/

# Jupyter Notebook
.ipynb_checkpoints

# Data files
*.csv
*.h5
*.hdf5
*.pkl
*.pickle
data/raw/*
data/processed/*
!data/raw/.gitkeep
!data/processed/.gitkeep

# Model files
*.joblib
*.model
*.pkl
models/trained/*
models/saved/*
!models/trained/.gitkeep
!models/saved/.gitkeep

# Logs
*.log
logs/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# MLflow
mlruns/

# Weights & Biases
wandb/

# TensorBoard
runs/

# Large files
*.zip
*.tar.gz
*.rar
EOF
```

## 🧪 Hands-on Exercise: Complete Setup

Let's create a complete ML project setup:

```bash
# 1. Create new project
mkdir house-price-prediction
cd house-price-prediction

# 2. Initialize Git
git init

# 3. Create structure
mkdir -p {data/{raw,processed},models,notebooks,src/{data,models},tests}

# 4. Create files
touch README.md requirements.txt .gitignore
touch src/__init__.py src/data/__init__.py src/models/__init__.py

# 5. Add content to README
cat > README.md << 'EOF'
# House Price Prediction

A machine learning project to predict house prices using regression models.

## Project Structure
```
├── data/
│   ├── raw/          # Original, immutable data
│   └── processed/    # Cleaned, transformed data
├── models/           # Trained model files
├── notebooks/        # Jupyter notebooks
├── src/              # Source code
│   ├── data/         # Data processing modules
│   └── models/       # Model training modules
└── tests/            # Unit tests
```

## Setup
```bash
pip install -r requirements.txt
```
EOF

# 6. Add dependencies
cat > requirements.txt << 'EOF'
pandas>=1.3.0
numpy>=1.21.0
scikit-learn>=1.0.0
matplotlib>=3.4.0
seaborn>=0.11.0
jupyter>=1.0.0
EOF

# 7. Create .gitignore (use the one from previous section)

# 8. Check status
git status
```

## 🔗 Connecting to GitHub

### Method 1: HTTPS (Recommended for beginners)
```bash
# Add remote
git remote add origin https://github.com/yourusername/house-price-prediction.git

# Make initial commit (we'll learn this in next section)
git add .
git commit -m "Initial project setup"

# Push to GitHub
git push -u origin main
```

### Method 2: SSH (More secure, requires setup)
```bash
# First, set up SSH keys (one-time setup)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Add SSH key to GitHub account (copy public key)
cat ~/.ssh/id_ed25519.pub

# Add remote with SSH
git remote add origin git@github.com:yourusername/house-price-prediction.git
```

## 🔍 Repository Information Commands

```bash
# Check repository status
git status

# View remote information
git remote -v
git remote show origin

# Check repository configuration
git config --list --show-origin

# View repository log
git log --oneline

# Check which branch you're on
git branch
```

## 💡 ML-Specific Tips

### For Data Science Projects
- Use descriptive repository names (`customer-churn-analysis`, not `project1`)
- Include data source information in README
- Set up proper `.gitignore` from the start
- Consider using Git LFS for datasets > 100MB

### For Model Development
- Separate repositories for different models/experiments
- Use meaningful branch names (`feature/lstm-model`, `experiment/hyperparameter-tuning`)
- Include model performance metrics in commit messages

### For Team Projects
- Establish naming conventions early
- Use organization repositories for shared projects
- Set up branch protection rules on main branch

## 🤔 Common Issues and Solutions

**Problem**: "fatal: not a git repository"
**Solution**: Make sure you're in the right directory and have run `git init`

**Problem**: "remote origin already exists"
**Solution**: Remove existing remote first: `git remote remove origin`

**Problem**: Large files causing slow clones
**Solution**: Use `git clone --depth 1` or set up Git LFS

## 📝 Key Takeaways

1. Always initialize Git at the start of new projects
2. Set up proper project structure and `.gitignore` early
3. Use descriptive repository names and README files
4. Connect to remote repositories for backup and collaboration
5. ML projects have specific needs for handling data and models

## 🚀 Next Steps

In the next section, we'll learn the basic Git workflow: adding, committing, and pushing changes to track your ML project development.

---

**Practice Challenge**: Create a new ML project repository, set up the complete structure, create a meaningful README, and connect it to GitHub. Try cloning someone else's ML repository to see different project structures. 