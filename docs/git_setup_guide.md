# Git Repository Setup Guide
**Date:** January 1, 2026  
**Week 1 Setup**

---

## Why Use Git and GitHub?

- **Version Control:** Track all changes to your code
- **Backup:** Cloud backup of your project
- **Collaboration:** Share with supervisor
- **History:** Revert to previous versions if needed
- **Portfolio:** Show your work to future employers
- **Documentation:** README and commit messages document your progress

---

## Initial Setup

### Step 1: Install Git

#### Windows:
1. Download from: https://git-scm.com/download/win
2. Run installer with default settings
3. Verify installation:
```powershell
git --version
```

#### Mac:
```bash
# Install with Homebrew
brew install git

# Or install Xcode Command Line Tools
xcode-select --install
```

#### Linux:
```bash
# Ubuntu/Debian
sudo apt-get install git

# Fedora
sudo dnf install git
```

### Step 2: Configure Git

```powershell
# Set your name (will appear in commits)
git config --global user.name "Your Name"

# Set your email
git config --global user.email "your.email@example.com"

# Set default branch name to 'main'
git config --global init.defaultBranch main

# Verify configuration
git config --list
```

### Step 3: Create GitHub Account

1. Go to: https://github.com/
2. Sign up for free account
3. Verify email address

---

## Initialize Local Repository

### Option A: Start from Existing Project (Recommended)

```powershell
# Navigate to your project directory
cd C:\Users\bmezher\Desktop\FYP\Ghala

# Initialize git repository
git init

# Add all files to staging
git add .

# Create first commit
git commit -m "Initial commit - Week 1 setup and research documents"

# Check status
git status
```

### Option B: Clone from GitHub (if you created repo online first)

```powershell
# Create repository on GitHub.com first, then:
git clone https://github.com/yourusername/ghala-gps-safety.git
cd ghala-gps-safety
```

---

## Create .gitignore File

Create a file named `.gitignore` in your project root to exclude unnecessary files:

```plaintext
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
env/
ENV/
*.egg-info/
dist/
build/

# Jupyter Notebooks
.ipynb_checkpoints/
*.ipynb_checkpoints

# IDEs
.vscode/
.idea/
*.swp
*.swo
*~

# Data files (large OSM data - don't commit to repo)
*.osm
*.osm.pbf
*.osm.bz2
data/raw/
data/processed/*.geojson
*.gpkg

# OS
.DS_Store
Thumbs.db
desktop.ini

# Logs
*.log

# Environment variables
.env
.env.local

# Mobile (React Native)
node_modules/
npm-debug.log
yarn-error.log

# Mobile (Flutter)
.dart_tool/
.flutter-plugins
.flutter-plugins-dependencies
.packages
.pub-cache/
.pub/

# Mobile (iOS)
*.xcworkspace
*.xcuserdata
Pods/
*.ipa

# Mobile (Android)
*.apk
*.aab
.gradle/
local.properties

# Temporary files
*.tmp
*.bak
*.swp
temp/
```

---

## Connect to GitHub

### Step 1: Create Repository on GitHub

1. Go to: https://github.com/new
2. Repository name: `ghala-gps-safety-app` (or similar)
3. Description: "GPS Mobile App to Help Driving Safety Abroad - FYP"
4. Choose: **Private** (or Public if you prefer)
5. Do NOT initialize with README (you already have files)
6. Click "Create repository"

### Step 2: Link Local Repository to GitHub

```powershell
# Add remote repository (replace with your GitHub username)
git remote add origin https://github.com/yourusername/ghala-gps-safety-app.git

# Verify remote
git remote -v

# Push to GitHub
git push -u origin main
```

### Step 3: Authenticate

GitHub will prompt for authentication. Two options:

#### Option A: Personal Access Token (Recommended)
1. Go to: https://github.com/settings/tokens
2. Generate new token (classic)
3. Select scopes: `repo`
4. Copy token and use as password when pushing

#### Option B: GitHub CLI
```powershell
# Install GitHub CLI: https://cli.github.com/
# Then authenticate
gh auth login
```

---

## Basic Git Workflow

### Daily Workflow:

```powershell
# 1. Check current status
git status

# 2. Add specific files
git add filename.py
git add docs/research_notes.md

# Or add all changed files
git add .

# 3. Commit with meaningful message
git commit -m "Add OSM data processing script for junction detection"

# 4. Push to GitHub
git push

# 5. Pull latest changes (if working on multiple machines)
git pull
```

### Meaningful Commit Messages:

Good examples:
```
git commit -m "Add OSMnx script to download and visualize street networks"
git commit -m "Implement T-junction detection algorithm"
git commit -m "Fix distance calculation bug in hazard point generation"
git commit -m "Update technology stack decision document"
git commit -m "Add React Native mobile app boilerplate"
```

Bad examples (avoid):
```
git commit -m "update"
git commit -m "fixes"
git commit -m "asdf"
git commit -m "changes"
```

---

## Branching Strategy

For academic project, keep it simple:

### Main Branch Only (Simplest):
- Work directly on `main` branch
- Commit and push regularly

### Feature Branches (If you want):
```powershell
# Create branch for new feature
git checkout -b feature/danger-point-algorithm

# Work on feature, commit changes
git add .
git commit -m "Implement danger point calculation"

# Switch back to main
git checkout main

# Merge feature branch
git merge feature/danger-point-algorithm

# Delete branch
git branch -d feature/danger-point-algorithm
```

**Recommendation:** Stick with main branch for simplicity.

---

## Viewing History

```powershell
# View commit history
git log

# View compact history
git log --oneline

# View history with graph
git log --graph --oneline --all

# View changes in last commit
git show

# View changes in specific file
git log -p filename.py
```

---

## Undoing Changes

### Undo changes in working directory (before commit):
```powershell
# Discard changes to specific file
git restore filename.py

# Discard all changes
git restore .
```

### Undo staged changes (after git add, before commit):
```powershell
# Unstage specific file
git restore --staged filename.py

# Unstage all files
git restore --staged .
```

### Undo last commit (keep changes):
```powershell
git reset --soft HEAD~1
```

### Undo last commit (discard changes):
```powershell
git reset --hard HEAD~1
```

---

## Suggested Commit Schedule

### Week 1:
```powershell
# Initial setup
git add .
git commit -m "Initial project structure and research documents"
git push

# After completing tech stack research
git add docs/technology_stack_decision.md
git commit -m "Complete technology stack decision document"
git push
```

### Week 2:
```powershell
# After setting up Python environment
git add requirements.txt
git commit -m "Add Python dependencies for geospatial processing"
git push

# After first OSM data download
git add src/osm_download.py
git commit -m "Add script to download OSM data using OSMnx"
git push
```

### General Rule:
- **Commit often:** After completing any logical unit of work
- **Push daily:** At end of each work session
- **Meaningful messages:** Describe what you did, not what you're going to do

---

## Project README Template

Create `README.md` in project root:

```markdown
# Ghala - GPS Safety App for Driving Abroad

**Final Year Project - 2025/2026**  
**Author:** [Your Name]  
**Supervisor:** Crispin Cooper

## Project Description

A mobile application to help drivers stay safe when driving abroad by warning them about dangerous road junctions before they approach them. The app uses OpenStreetMap data to identify potentially hazardous junctions and provides timely audio/visual warnings.

## Project Structure

```
Ghala/
├── docs/               # Documentation
│   ├── technology_stack_decision.md
│   └── initial_plan.md
├── research/           # Research notes
│   ├── osm_research_notes.md
│   ├── python_libraries_guide.md
│   └── mobile_frameworks_comparison.md
├── src/                # Source code
│   ├── data_processing/    # Python scripts for OSM data
│   └── mobile_app/         # Mobile application code
├── data/               # Data files (not committed)
│   ├── raw/           # Raw OSM data
│   └── processed/     # Processed hazard points
├── requirements.txt    # Python dependencies
└── README.md          # This file
```

## Technology Stack

### Data Processing:
- Python 3.9+
- Libraries: osmnx, shapely, geopandas, pyproj

### Mobile App:
- [To be decided: React Native / Flutter / Native iOS]

## Setup Instructions

### Python Environment:
```bash
python -m venv venv
venv\Scripts\activate  # Windows
pip install -r requirements.txt
```

### Mobile App:
[To be added in Week 2]

## Development Timeline

- **Week 1 (Jan 1-5):** Research and technology stack decision ✅
- **Week 2 (Jan 6-12):** Environment setup and initial data exploration
- **Week 5 (Jan 27-Feb 2):** Initial plan submission (Feb 2)
- **Jul 5, 2026:** Final report submission

## Key Milestones

| Date | Milestone |
|------|-----------|
| Feb 2, 2026 | Initial Plan Submission |
| Mar 2, 2026 | Data processing complete |
| Apr 6, 2026 | Core app functionality complete |
| May 4, 2026 | Testing and refinement complete |
| Jun 1, 2026 | Evaluation complete |
| Jul 5, 2026 | **Final Report Submission** |

## Current Status

**Week 1 (Jan 1-5):** ✅ Completed
- ✅ Project structure created
- ✅ Technology research completed
- ✅ Git repository initialized
- 🔄 Technology stack decision in progress

## License

[Academic Project - University]

## Contact

[Your email address]
```

---

## Git Best Practices for FYP

### DO:
- ✅ Commit frequently (multiple times per day)
- ✅ Write clear commit messages
- ✅ Push to GitHub daily (backup!)
- ✅ Use `.gitignore` to exclude large files
- ✅ Keep sensitive data out of repo
- ✅ Update README regularly

### DON'T:
- ❌ Commit large data files (OSM data > 100MB)
- ❌ Commit API keys or passwords
- ❌ Go weeks without committing
- ❌ Use vague commit messages
- ❌ Commit broken code (make sure it works first)

---

## Sharing with Supervisor

### Option 1: Add Supervisor as Collaborator
1. Go to repository Settings → Collaborators
2. Add supervisor's GitHub username
3. They can view and comment on code

### Option 2: Make Repository Public
- Anyone can view
- Good for portfolio

### Option 3: Send Occasional Exports
```powershell
# Create a zip of your project
# Exclude large files manually
```

---

## Useful Git Commands Reference

```powershell
# Status and info
git status                  # Current status
git log                    # Commit history
git diff                   # Show unstaged changes

# Basic workflow
git add <file>             # Stage specific file
git add .                  # Stage all changes
git commit -m "message"    # Commit staged changes
git push                   # Push to GitHub
git pull                   # Pull from GitHub

# Branching
git branch                 # List branches
git branch <name>          # Create branch
git checkout <name>        # Switch branch
git checkout -b <name>     # Create and switch
git merge <branch>         # Merge branch

# Undoing
git restore <file>         # Discard changes
git restore --staged <f>   # Unstage file
git reset --soft HEAD~1    # Undo last commit (keep changes)

# Remote
git remote -v              # View remotes
git remote add origin <url> # Add remote
```

---

## Week 1 Git Checklist

- [ ] Install Git
- [ ] Configure user name and email
- [ ] Create GitHub account
- [ ] Initialize local repository
- [ ] Create .gitignore file
- [ ] Create README.md
- [ ] Make initial commit
- [ ] Create GitHub repository
- [ ] Push to GitHub
- [ ] Verify files appear on GitHub

---

## Next Steps

1. **Complete Week 1 checklist above**
2. **Make first commit with research documents**
3. **Push to GitHub**
4. **Share repository with supervisor (optional)**
5. **Continue committing regularly throughout project**

---

**Remember:** Git is your safety net. Commit often, push daily!

---

**Last Updated:** January 1, 2026
