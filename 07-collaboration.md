# 07. Collaboration in ML Teams

## 🎯 Learning Objectives
By the end of this section, you'll know how to:
- Set up effective collaboration workflows for ML teams
- Conduct code reviews for ML projects
- Handle merge conflicts in ML contexts
- Use pull requests and issues effectively
- Establish team conventions and best practices

## 👥 ML Team Collaboration Challenges

### Unique Aspects of ML Collaboration
- **Experiments**: Multiple team members running different experiments
- **Data Dependencies**: Shared datasets and preprocessing pipelines
- **Model Artifacts**: Large model files and checkpoints
- **Reproducibility**: Ensuring experiments can be replicated
- **Performance Tracking**: Comparing model results across team members

## 🔄 Collaboration Workflows

### 1. Feature Branch Workflow (Recommended for ML)
```bash
# Each team member works on separate branches
main
├── alice/experiment-lstm
├── bob/feature-data-pipeline
└── charlie/experiment-ensemble

# Workflow:
# 1. Create feature branch from main
# 2. Work on experiment/feature
# 3. Create pull request
# 4. Code review
# 5. Merge to main
```

### 2. Git Flow for ML Teams
```bash
# Production-ready models
main

# Integration branch for experiments
develop

# Individual experiment branches
experiment/alice-lstm-sentiment
experiment/bob-transformer-classification
feature/charlie-data-augmentation

# Model releases
release/v1.0-production-model
release/v2.0-improved-accuracy
```

## 🚀 Setting Up Team Repository

### Repository Structure for Team Collaboration
```bash
# Create team-friendly structure
mkdir ml-team-project
cd ml-team-project
git init

# Create comprehensive structure
mkdir -p {data/{raw,processed,external},models/{experiments,production},notebooks/{exploratory,final},src/{data,models,evaluation,utils},tests,config/{dev,staging,prod},docs,scripts}

# Create team files
touch README.md CONTRIBUTING.md .gitignore requirements.txt
touch docs/EXPERIMENT_GUIDELINES.md docs/CODE_REVIEW_CHECKLIST.md
```

### Team Configuration Files
```bash
# Create CONTRIBUTING.md
cat > CONTRIBUTING.md << 'EOF'
# Contributing to ML Team Project

## Workflow
1. Create feature branch: `git checkout -b experiment/your-name-description`
2. Make changes and commit frequently
3. Push branch: `git push origin experiment/your-name-description`
4. Create Pull Request with detailed description
5. Request review from at least one team member
6. Address review feedback
7. Merge after approval

## Branch Naming Conventions
- `experiment/[name]-[description]`: For ML experiments
- `feature/[description]`: For new features
- `fix/[description]`: For bug fixes
- `data/[description]`: For data-related changes

## Commit Message Format
```
Type: Brief description (50 chars max)

Detailed explanation if needed (72 chars per line)

- List key changes
- Include performance metrics if applicable
- Reference issues: Fixes #123
```

## Code Review Guidelines
- Check code quality and documentation
- Verify experiment reproducibility
- Review model performance claims
- Ensure proper error handling
- Check for data leakage issues

## Experiment Documentation
Each experiment should include:
- Clear objective and hypothesis
- Dataset description and preprocessing steps
- Model architecture and hyperparameters
- Evaluation metrics and results
- Comparison with baseline/previous models
EOF

# Create experiment guidelines
cat > docs/EXPERIMENT_GUIDELINES.md << 'EOF'
# ML Experiment Guidelines

## Before Starting an Experiment
1. Create issue describing experiment objective
2. Create branch: `experiment/[your-name]-[experiment-name]`
3. Document hypothesis and expected outcomes
4. Set up experiment tracking

## During Experiment
1. Commit frequently with descriptive messages
2. Document parameter changes and results
3. Save intermediate results and checkpoints
4. Update experiment log regularly

## After Experiment
1. Document final results and conclusions
2. Compare with baseline models
3. Create pull request with experiment summary
4. Archive experiment artifacts properly
5. Update team knowledge base

## Experiment Branch Structure
```
experiment/alice-lstm-sentiment/
├── notebooks/
│   ├── 01-data-exploration.ipynb
│   ├── 02-model-development.ipynb
│   └── 03-results-analysis.ipynb
├── src/
│   ├── models/lstm_model.py
│   └── utils/experiment_helpers.py
├── config/
│   └── lstm_config.yaml
├── results/
│   ├── metrics.json
│   └── plots/
└── README.md  # Experiment summary
```
EOF
```

## 📝 Pull Request Best Practices

### Creating Effective Pull Requests
```bash
# Create feature branch
git checkout -b experiment/neural-network-optimization

# Make changes and commit
git add .
git commit -m "Optimize neural network architecture

- Reduce model parameters by 30%
- Implement early stopping
- Add batch normalization
- Improve validation accuracy from 0.85 to 0.89"

# Push branch
git push origin experiment/neural-network-optimization

# Create PR through GitHub/GitLab interface
```

### PR Description Template
```markdown
## Experiment/Feature Description
Brief description of what this PR accomplishes.

## Changes Made
- [ ] Data preprocessing improvements
- [ ] Model architecture changes
- [ ] Hyperparameter tuning
- [ ] Evaluation metrics updates
- [ ] Documentation updates

## Results
### Before
- Accuracy: 0.85
- F1-Score: 0.83
- Training time: 2 hours

### After
- Accuracy: 0.89 (+0.04)
- F1-Score: 0.87 (+0.04)
- Training time: 1.5 hours (-0.5h)

## Testing
- [ ] Code runs without errors
- [ ] All tests pass
- [ ] Results are reproducible
- [ ] Documentation is updated

## Checklist
- [ ] Code follows team style guidelines
- [ ] Commit messages are descriptive
- [ ] No sensitive data in commits
- [ ] Large files use Git LFS
- [ ] Experiment is documented

## Related Issues
Fixes #123
Related to #456
```

## 🔍 Code Review for ML Projects

### ML-Specific Review Checklist
```markdown
## Code Quality
- [ ] Code is readable and well-documented
- [ ] Functions have clear docstrings
- [ ] Variable names are descriptive
- [ ] No hardcoded values (use config files)
- [ ] Proper error handling

## Data Handling
- [ ] No data leakage between train/validation/test
- [ ] Proper data preprocessing pipeline
- [ ] Data validation checks in place
- [ ] Handling of missing values is appropriate
- [ ] Feature engineering is documented

## Model Implementation
- [ ] Model architecture is clearly defined
- [ ] Hyperparameters are configurable
- [ ] Random seeds are set for reproducibility
- [ ] Model saving/loading works correctly
- [ ] Evaluation metrics are appropriate

## Experiment Tracking
- [ ] Experiment parameters are logged
- [ ] Results are properly documented
- [ ] Comparison with baseline is included
- [ ] Performance claims are supported by evidence
- [ ] Reproducibility instructions are clear

## Performance
- [ ] No obvious performance bottlenecks
- [ ] Memory usage is reasonable
- [ ] Training time is documented
- [ ] Model inference time is acceptable

## Testing
- [ ] Unit tests for key functions
- [ ] Integration tests for pipelines
- [ ] Data validation tests
- [ ] Model performance tests
```

### Conducting Code Reviews
```bash
# Check out the PR branch locally
git fetch origin
git checkout experiment/neural-network-optimization

# Run the code and verify claims
python src/train_model.py --config config/experiment_config.yaml

# Check for issues
python -m pytest tests/
python -m flake8 src/

# Review specific changes
git diff main..experiment/neural-network-optimization

# Leave feedback through GitHub/GitLab interface
```

## ⚔️ Handling Merge Conflicts in ML

### Common Conflict Scenarios
1. **Configuration files**: Multiple people editing hyperparameters
2. **Requirements.txt**: Different dependency versions
3. **Notebooks**: Conflicting cell outputs or metadata
4. **Data preprocessing**: Different approaches to same pipeline

### Resolving Configuration Conflicts
```bash
# Conflict in config/model_config.yaml
git checkout main
git pull origin main
git checkout experiment/my-experiment
git merge main

# Git shows conflict:
<<<<<<< HEAD
learning_rate: 0.001
batch_size: 32
=======
learning_rate: 0.01
batch_size: 64
>>>>>>> main

# Resolve by choosing appropriate values or combining
learning_rate: 0.005  # Compromise value
batch_size: 32        # Keep experimental value

# Stage resolved file
git add config/model_config.yaml
git commit -m "Resolve config conflict - use compromise learning rate"
```

### Resolving Notebook Conflicts
```bash
# Use nbstripout to avoid output conflicts
pip install nbstripout
nbstripout --install

# For existing conflicts, clear outputs and merge
jupyter nbconvert --clear-output --inplace notebook.ipynb
git add notebook.ipynb
git commit -m "Clear notebook outputs before merge"
```

## 🤝 Team Synchronization

### Daily Sync Workflow
```bash
# Start of day - sync with team
git checkout main
git pull origin main

# Create/switch to your branch
git checkout experiment/my-current-work

# Rebase onto latest main (optional but recommended)
git rebase main

# If conflicts, resolve and continue
git rebase --continue

# Work on your changes...
# End of day - push your work
git push origin experiment/my-current-work
```

### Sharing Experiment Results
```bash
# Create experiment summary branch
git checkout -b results/weekly-experiments-summary

# Create summary document
cat > docs/experiment_results_$(date +%Y%m%d).md << 'EOF'
# Weekly Experiment Results

## Team: ML Engineering
## Date: $(date +%Y-%m-%d)

### Completed Experiments

#### Alice - LSTM Sentiment Analysis
- **Branch**: experiment/alice-lstm-sentiment
- **Status**: Complete
- **Results**: Accuracy 0.89, F1 0.87
- **Next Steps**: Deploy to staging

#### Bob - Data Pipeline Optimization
- **Branch**: feature/bob-data-pipeline
- **Status**: In Progress
- **Results**: 40% faster preprocessing
- **Next Steps**: Add error handling

#### Charlie - Ensemble Methods
- **Branch**: experiment/charlie-ensemble
- **Status**: Complete
- **Results**: Accuracy 0.91 (best so far!)
- **Next Steps**: Code review needed

### Key Findings
- Ensemble methods show most promise
- Data pipeline optimizations significant
- Need to standardize evaluation metrics

### Action Items
- [ ] Review Charlie's ensemble implementation
- [ ] Merge Alice's LSTM model
- [ ] Complete Bob's pipeline work
- [ ] Plan next sprint experiments
EOF

git add docs/experiment_results_$(date +%Y%m%d).md
git commit -m "Add weekly experiment results summary"
git push origin results/weekly-experiments-summary
```

## 🔧 Team Tools and Automation

### Git Hooks for Team Consistency
```bash
# Shared pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

echo "Running team pre-commit checks..."

# Check Python code style
if command -v flake8 &> /dev/null; then
    echo "Checking Python style..."
    flake8 src/ tests/
    if [ $? -ne 0 ]; then
        echo "Style check failed. Please fix issues before committing."
        exit 1
    fi
fi

# Check for large files not in LFS
large_files=$(find . -size +50M -not -path "./.git/*" -not -path "./data/*")
if [ ! -z "$large_files" ]; then
    echo "Large files detected. Consider using Git LFS:"
    echo "$large_files"
    exit 1
fi

# Clear notebook outputs
if command -v nbstripout &> /dev/null; then
    echo "Clearing notebook outputs..."
    nbstripout notebooks/*.ipynb
fi

echo "Pre-commit checks passed!"
EOF

chmod +x .git/hooks/pre-commit
```

### Issue Templates
```bash
# Create GitHub issue templates
mkdir -p .github/ISSUE_TEMPLATE

# Experiment proposal template
cat > .github/ISSUE_TEMPLATE/experiment_proposal.md << 'EOF'
---
name: Experiment Proposal
about: Propose a new ML experiment
title: '[EXPERIMENT] '
labels: experiment
assignees: ''
---

## Experiment Objective
Describe what you want to achieve with this experiment.

## Hypothesis
What do you expect to happen?

## Approach
- [ ] Data preparation steps
- [ ] Model architecture/algorithm
- [ ] Evaluation metrics
- [ ] Success criteria

## Resources Needed
- Compute requirements
- Data requirements
- Timeline estimate

## Related Work
Link to relevant papers, previous experiments, or issues.
EOF

# Bug report template
cat > .github/ISSUE_TEMPLATE/bug_report.md << 'EOF'
---
name: Bug Report
about: Report a bug in the ML pipeline
title: '[BUG] '
labels: bug
assignees: ''
---

## Bug Description
Clear description of what the bug is.

## Steps to Reproduce
1. Run command '...'
2. With config '...'
3. See error

## Expected Behavior
What should have happened.

## Environment
- Python version:
- Key package versions:
- Operating system:
- Git branch:

## Additional Context
Any other context about the problem.
EOF
```

## 📊 Team Performance Tracking

### Model Performance Dashboard
```python
# Create team performance tracking script
cat > scripts/generate_team_dashboard.py << 'EOF'
#!/usr/bin/env python3
"""
Generate team performance dashboard from Git history and experiment logs.
"""

import json
import subprocess
import pandas as pd
from datetime import datetime, timedelta
import matplotlib.pyplot as plt

def get_recent_experiments(days=30):
    """Get recent experiments from git branches."""
    # Get all experiment branches
    result = subprocess.run(
        ['git', 'branch', '-a', '--format=%(refname:short)'],
        capture_output=True, text=True
    )
    
    branches = [b.strip() for b in result.stdout.split('\n') 
                if 'experiment/' in b and 'origin/' not in b]
    
    experiments = []
    for branch in branches:
        # Get branch info
        result = subprocess.run(
            ['git', 'log', '-1', '--format=%an|%ad|%s', '--date=iso', branch],
            capture_output=True, text=True
        )
        
        if result.stdout:
            author, date, message = result.stdout.strip().split('|', 2)
            experiments.append({
                'branch': branch,
                'author': author,
                'date': date,
                'message': message
            })
    
    return experiments

def generate_dashboard():
    """Generate team dashboard."""
    experiments = get_recent_experiments()
    
    # Create summary
    print("# ML Team Dashboard")
    print(f"Generated: {datetime.now().strftime('%Y-%m-%d %H:%M')}")
    print()
    
    # Active experiments
    print("## Active Experiments")
    for exp in experiments[-10:]:  # Last 10 experiments
        print(f"- **{exp['branch']}** by {exp['author']}")
        print(f"  - {exp['message']}")
        print(f"  - {exp['date']}")
        print()
    
    # Team activity
    authors = [exp['author'] for exp in experiments]
    author_counts = pd.Series(authors).value_counts()
    
    print("## Team Activity (Last 30 days)")
    for author, count in author_counts.items():
        print(f"- {author}: {count} experiments")
    
if __name__ == "__main__":
    generate_dashboard()
EOF

chmod +x scripts/generate_team_dashboard.py
```

## 🎯 Best Practices Summary

### Do's ✅
- **Communication**: Regular sync meetings and updates
- **Documentation**: Clear experiment documentation
- **Code Review**: Thorough reviews for all changes
- **Branching**: Use descriptive branch names
- **Conflicts**: Resolve conflicts promptly
- **Standards**: Follow team coding standards
- **Testing**: Write tests for shared code

### Don'ts ❌
- **Direct commits**: Don't commit directly to main
- **Large PRs**: Avoid massive pull requests
- **Unclear messages**: Don't use vague commit messages
- **Merge conflicts**: Don't ignore merge conflicts
- **Solo work**: Don't work in isolation
- **Undocumented experiments**: Don't skip documentation
- **Force pushes**: Don't force push to shared branches

## 📝 Key Takeaways

1. **Clear Workflows**: Establish and follow team Git workflows
2. **Communication**: Regular communication prevents conflicts
3. **Documentation**: Good documentation enables collaboration
4. **Code Reviews**: Reviews improve code quality and knowledge sharing
5. **Conflict Resolution**: Address conflicts quickly and clearly
6. **Automation**: Use tools to enforce consistency
7. **Tracking**: Monitor team progress and experiment results

## 🚀 Next Steps

In the next section, we'll learn advanced Git commands that are particularly useful for ML workflows, including rebasing, cherry-picking, and history manipulation.

---

**Practice Challenge**: Set up a team repository with proper structure, create pull request templates, practice code reviews with a partner, and simulate resolving merge conflicts. Try the complete collaboration workflow from branch creation to merge. 