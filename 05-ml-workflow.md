# 05. ML Workflow Integration

## 🎯 Learning Objectives
By the end of this section, you'll know how to:
- Integrate Git into your ML development workflow
- Handle Jupyter notebooks effectively with Git
- Manage configuration files and hyperparameters
- Track experiments and model versions
- Set up automated workflows with Git hooks

## 🔬 ML Development Lifecycle with Git

### Typical ML Project Phases
1. **Data Exploration** → `experiment/eda-*` branches
2. **Feature Engineering** → `feature/preprocessing-*` branches  
3. **Model Development** → `experiment/model-*` branches
4. **Hyperparameter Tuning** → `experiment/tuning-*` branches
5. **Model Evaluation** → `feature/evaluation` branches
6. **Production Deployment** → `release/*` branches

## 📓 Managing Jupyter Notebooks

### The Notebook Problem
Jupyter notebooks contain:
- Code (trackable)
- Outputs (often not trackable)
- Metadata (can cause conflicts)

### Solutions for Notebook Management

#### 1. Clear Outputs Before Committing
```bash
# Manual clearing
jupyter nbconvert --clear-output --inplace notebook.ipynb

# Add to git workflow
git add notebook.ipynb
jupyter nbconvert --clear-output --inplace notebook.ipynb
git add notebook.ipynb
git commit -m "Add data analysis notebook"
```

#### 2. Use nbstripout (Recommended)
```bash
# Install nbstripout
pip install nbstripout

# Set up for repository
nbstripout --install

# Now Git automatically clears outputs
git add notebook.ipynb
git commit -m "Add analysis notebook"  # Outputs automatically cleared
```

#### 3. Git Configuration for Notebooks
```bash
# Add to .gitattributes
echo "*.ipynb filter=nbstripout" >> .gitattributes
echo "*.ipynb diff=ipynb" >> .gitattributes

# Configure diff for notebooks
git config diff.ipynb.textconv 'jupyter nbconvert --to script --stdout'
```

### Notebook Best Practices
```python
# At the top of each notebook
"""
Notebook: Customer Churn Analysis
Author: Your Name
Date: 2024-01-15
Purpose: Exploratory data analysis for churn prediction
Git Branch: experiment/churn-analysis
"""

# Use version tracking within notebooks
import os
import subprocess

def get_git_info():
    """Get current Git branch and commit hash."""
    try:
        branch = subprocess.check_output(['git', 'branch', '--show-current']).decode().strip()
        commit = subprocess.check_output(['git', 'rev-parse', '--short', 'HEAD']).decode().strip()
        return f"Branch: {branch}, Commit: {commit}"
    except:
        return "Git info not available"

print(f"Notebook run info: {get_git_info()}")
```

## ⚙️ Configuration Management

### Separating Code from Configuration
```bash
# Create config structure
mkdir -p config/{development,staging,production}

# Development config
cat > config/development/model_config.yaml << 'EOF'
model:
  type: "random_forest"
  n_estimators: 100
  max_depth: 10
  random_state: 42

training:
  test_size: 0.2
  cv_folds: 5
  
data:
  input_path: "data/raw/train.csv"
  output_path: "data/processed/train_processed.csv"

logging:
  level: "DEBUG"
  file: "logs/development.log"
EOF

# Production config  
cat > config/production/model_config.yaml << 'EOF'
model:
  type: "random_forest"
  n_estimators: 500
  max_depth: 15
  random_state: 42

training:
  test_size: 0.15
  cv_folds: 10
  
data:
  input_path: "/data/production/train.csv"
  output_path: "/data/production/train_processed.csv"

logging:
  level: "INFO"
  file: "/logs/production.log"
EOF
```

### Environment-Based Configuration Loading
```python
# src/config/config_loader.py
import yaml
import os
from pathlib import Path

class ConfigLoader:
    def __init__(self, env='development'):
        self.env = env
        self.config_path = Path(f'config/{env}')
        
    def load_config(self, config_file='model_config.yaml'):
        """Load configuration from YAML file."""
        config_file_path = self.config_path / config_file
        
        if not config_file_path.exists():
            raise FileNotFoundError(f"Config file not found: {config_file_path}")
            
        with open(config_file_path, 'r') as f:
            config = yaml.safe_load(f)
            
        return config
    
    def get_model_config(self):
        """Get model-specific configuration."""
        return self.load_config('model_config.yaml')

# Usage in your ML scripts
if __name__ == "__main__":
    env = os.getenv('ML_ENV', 'development')
    config_loader = ConfigLoader(env)
    config = config_loader.get_model_config()
    
    print(f"Running in {env} mode")
    print(f"Model type: {config['model']['type']}")
```

## 🧪 Experiment Tracking with Git

### Branch-Based Experiment Tracking
```bash
# Create experiment branch with descriptive name
git checkout -b experiment/rf-hyperparameter-sweep-$(date +%Y%m%d)

# Document experiment in commit messages
git commit -m "Experiment: Random Forest hyperparameter sweep

Parameters tested:
- n_estimators: [100, 200, 500, 1000]
- max_depth: [5, 10, 15, 20, None]
- min_samples_split: [2, 5, 10]

Best result: accuracy=0.89, f1=0.87
Best params: n_estimators=500, max_depth=15, min_samples_split=5"
```

### Experiment Documentation Template
```python
# Create experiment tracking script
cat > src/utils/experiment_tracker.py << 'EOF'
import json
import os
from datetime import datetime
import subprocess

class ExperimentTracker:
    def __init__(self, experiment_name):
        self.experiment_name = experiment_name
        self.start_time = datetime.now()
        self.results = {}
        
    def log_parameters(self, params):
        """Log experiment parameters."""
        self.results['parameters'] = params
        
    def log_metrics(self, metrics):
        """Log experiment metrics."""
        self.results['metrics'] = metrics
        
    def log_git_info(self):
        """Log Git branch and commit info."""
        try:
            branch = subprocess.check_output(['git', 'branch', '--show-current']).decode().strip()
            commit = subprocess.check_output(['git', 'rev-parse', 'HEAD']).decode().strip()
            self.results['git_info'] = {
                'branch': branch,
                'commit': commit
            }
        except:
            self.results['git_info'] = {'error': 'Git info not available'}
    
    def save_results(self, output_dir='experiments'):
        """Save experiment results to JSON file."""
        os.makedirs(output_dir, exist_ok=True)
        
        self.results['experiment_name'] = self.experiment_name
        self.results['start_time'] = self.start_time.isoformat()
        self.results['end_time'] = datetime.now().isoformat()
        
        self.log_git_info()
        
        filename = f"{output_dir}/{self.experiment_name}_{self.start_time.strftime('%Y%m%d_%H%M%S')}.json"
        
        with open(filename, 'w') as f:
            json.dump(self.results, f, indent=2)
            
        print(f"Experiment results saved to: {filename}")
        return filename

# Example usage
if __name__ == "__main__":
    tracker = ExperimentTracker("random_forest_baseline")
    
    # Log parameters
    tracker.log_parameters({
        'n_estimators': 100,
        'max_depth': 10,
        'random_state': 42
    })
    
    # Log metrics (you'd get these from your model)
    tracker.log_metrics({
        'accuracy': 0.85,
        'precision': 0.83,
        'recall': 0.87,
        'f1_score': 0.85
    })
    
    # Save results
    tracker.save_results()
EOF
```

## 🏷️ Tagging Model Versions

### Semantic Versioning for Models
```bash
# Tag stable model versions
git tag -a v1.0.0 -m "Initial production model

- Random Forest classifier
- Accuracy: 0.89
- F1-Score: 0.87
- Training data: 2024-01-15 dataset"

# Tag experimental versions
git tag -a v1.1.0-exp -m "Experimental neural network model

- 3-layer neural network
- Accuracy: 0.91 (validation)
- Not production ready - needs more testing"

# Push tags to remote
git push origin --tags
```

### Model Registry with Git
```python
# src/models/model_registry.py
import json
import os
from pathlib import Path
import subprocess

class ModelRegistry:
    def __init__(self, registry_path='models/registry.json'):
        self.registry_path = Path(registry_path)
        self.registry = self._load_registry()
    
    def _load_registry(self):
        """Load existing model registry or create new one."""
        if self.registry_path.exists():
            with open(self.registry_path, 'r') as f:
                return json.load(f)
        return {'models': []}
    
    def register_model(self, model_name, model_path, metrics, description=""):
        """Register a new model version."""
        # Get Git info
        try:
            commit = subprocess.check_output(['git', 'rev-parse', 'HEAD']).decode().strip()
            branch = subprocess.check_output(['git', 'branch', '--show-current']).decode().strip()
        except:
            commit, branch = "unknown", "unknown"
        
        model_info = {
            'name': model_name,
            'path': str(model_path),
            'metrics': metrics,
            'description': description,
            'git_commit': commit,
            'git_branch': branch,
            'registered_at': datetime.now().isoformat()
        }
        
        self.registry['models'].append(model_info)
        self._save_registry()
        
        print(f"Model {model_name} registered successfully")
        return model_info
    
    def _save_registry(self):
        """Save registry to disk."""
        self.registry_path.parent.mkdir(parents=True, exist_ok=True)
        with open(self.registry_path, 'w') as f:
            json.dump(self.registry, f, indent=2)
    
    def list_models(self):
        """List all registered models."""
        return self.registry['models']
    
    def get_best_model(self, metric='accuracy'):
        """Get model with best performance on specified metric."""
        if not self.registry['models']:
            return None
            
        best_model = max(
            self.registry['models'],
            key=lambda x: x['metrics'].get(metric, 0)
        )
        return best_model
```

## 🔄 Automated Workflows with Git Hooks

### Pre-commit Hook for ML Projects
```bash
# Create pre-commit hook
mkdir -p .git/hooks

cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

echo "Running pre-commit checks for ML project..."

# Check for large files
large_files=$(find . -size +100M -not -path "./.git/*" -not -path "./data/*")
if [ ! -z "$large_files" ]; then
    echo "Error: Large files detected (>100MB):"
    echo "$large_files"
    echo "Consider using Git LFS or adding to .gitignore"
    exit 1
fi

# Clear notebook outputs
echo "Clearing notebook outputs..."
find . -name "*.ipynb" -not -path "./.git/*" -exec jupyter nbconvert --clear-output --inplace {} \;

# Run basic Python syntax check
echo "Checking Python syntax..."
python_files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')
if [ ! -z "$python_files" ]; then
    for file in $python_files; do
        python -m py_compile "$file"
        if [ $? -ne 0 ]; then
            echo "Error: Python syntax error in $file"
            exit 1
        fi
    done
fi

# Check for secrets (basic check)
echo "Checking for potential secrets..."
if git diff --cached | grep -E "(api_key|password|secret|token)" -i; then
    echo "Warning: Potential secrets detected in staged changes"
    echo "Please review and remove sensitive information"
    exit 1
fi

echo "Pre-commit checks passed!"
EOF

chmod +x .git/hooks/pre-commit
```

### Post-commit Hook for Experiment Logging
```bash
cat > .git/hooks/post-commit << 'EOF'
#!/bin/bash

# Log commit to experiment tracking
if [ -f "experiments/current_experiment.txt" ]; then
    experiment_name=$(cat experiments/current_experiment.txt)
    commit_hash=$(git rev-parse HEAD)
    commit_msg=$(git log -1 --pretty=%B)
    
    echo "$(date): Commit $commit_hash in experiment $experiment_name" >> experiments/experiment_log.txt
    echo "Message: $commit_msg" >> experiments/experiment_log.txt
    echo "---" >> experiments/experiment_log.txt
fi
EOF

chmod +x .git/hooks/post-commit
```

## 📊 ML Pipeline Integration

### Integrating with ML Pipelines
```python
# src/pipeline/ml_pipeline.py
import subprocess
import os
from pathlib import Path

class MLPipeline:
    def __init__(self, config_path='config/development/model_config.yaml'):
        self.config_path = config_path
        self.git_info = self._get_git_info()
    
    def _get_git_info(self):
        """Get current Git information."""
        try:
            return {
                'branch': subprocess.check_output(['git', 'branch', '--show-current']).decode().strip(),
                'commit': subprocess.check_output(['git', 'rev-parse', 'HEAD']).decode().strip(),
                'is_dirty': len(subprocess.check_output(['git', 'status', '--porcelain']).decode().strip()) > 0
            }
        except:
            return {'error': 'Git info not available'}
    
    def run_pipeline(self):
        """Run the complete ML pipeline."""
        print(f"Running ML pipeline...")
        print(f"Git branch: {self.git_info.get('branch', 'unknown')}")
        print(f"Git commit: {self.git_info.get('commit', 'unknown')}")
        
        if self.git_info.get('is_dirty'):
            print("Warning: Working directory has uncommitted changes")
        
        # Your pipeline steps here
        self._load_data()
        self._preprocess_data()
        self._train_model()
        self._evaluate_model()
        
    def _load_data(self):
        """Load and validate data."""
        print("Loading data...")
        # Implementation here
        
    def _preprocess_data(self):
        """Preprocess data."""
        print("Preprocessing data...")
        # Implementation here
        
    def _train_model(self):
        """Train model."""
        print("Training model...")
        # Implementation here
        
    def _evaluate_model(self):
        """Evaluate model."""
        print("Evaluating model...")
        # Implementation here
```

## 📝 Documentation as Code

### README Template for ML Projects
```markdown
# Project Name

## Git Workflow

### Branch Strategy
- `main`: Production-ready code
- `develop`: Integration branch
- `experiment/*`: Individual experiments
- `feature/*`: New features
- `fix/*`: Bug fixes

### Experiment Tracking
Each experiment should:
1. Create a new branch: `git checkout -b experiment/descriptive-name`
2. Document parameters in commit messages
3. Tag successful experiments: `git tag -a v1.x.x-exp`
4. Merge successful experiments to develop

### Model Versioning
- Use semantic versioning for models
- Tag releases: `git tag -a v1.0.0 -m "Production model v1.0.0"`
- Document model performance in tags

## Setup
```bash
git clone <repository>
cd <project>
pip install -r requirements.txt
nbstripout --install  # For notebook management
```
```

## 🎯 Best Practices Summary

### Do's ✅
- Use branches for experiments
- Clear notebook outputs before committing
- Document experiments in commit messages
- Tag stable model versions
- Keep configuration separate from code
- Use Git hooks for automation
- Track experiment metadata

### Don'ts ❌
- Don't commit large data files
- Don't commit model files without LFS
- Don't commit notebooks with outputs
- Don't put secrets in code
- Don't work directly on main branch
- Don't forget to document experiments

## 📝 Key Takeaways

1. Git integrates seamlessly with ML workflows when properly configured
2. Notebooks need special handling for version control
3. Configuration management is crucial for reproducible experiments
4. Experiment tracking can be enhanced with Git metadata
5. Automation through hooks improves workflow consistency
6. Documentation and tagging help organize ML project history

## 🚀 Next Steps

In the next section, we'll learn how to handle large files in ML projects using Git LFS (Large File Storage).

---

**Practice Challenge**: Set up a complete ML workflow with Git integration. Create experiment branches, configure notebook handling, set up experiment tracking, and practice the full development cycle from data exploration to model deployment. 