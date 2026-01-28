# Python Environment Setup Guide
**Date:** January 6, 2026 (Week 2)

## Quick Setup Instructions

### Step 1: Install Python 3.9 or higher

Download from: https://www.python.org/downloads/

**Windows:** Make sure to check "Add Python to PATH" during installation.

Verify installation:
```powershell
python --version
```

### Step 2: Create Virtual Environment

```powershell
# Navigate to project directory
cd C:\Users\bmezher\Desktop\FYP\Ghala

# Create virtual environment
python -m venv venv

# Activate virtual environment (Windows)
.\venv\Scripts\activate

# You should see (venv) in your prompt
```

### Step 3: Install Dependencies

```powershell
# Make sure virtual environment is activated
pip install --upgrade pip

# Install all dependencies from requirements.txt
pip install -r requirements.txt

# This will install:
# - osmnx (OpenStreetMap network analysis)
# - shapely (geometric operations)
# - geopandas (geospatial dataframes)
# - pyproj (coordinate transformations)
# - matplotlib, folium (visualization)
# - jupyter (notebooks)
# - and other dependencies
```

### Step 4: Verify Installation

```powershell
# Test imports
python -c "import osmnx; print('OSMnx version:', osmnx.__version__)"
python -c "import geopandas; print('GeoPandas version:', geopandas.__version__)"
python -c "import shapely; print('Shapely version:', shapely.__version__)"
```

If all imports succeed, your environment is ready!

## Troubleshooting

### Issue: GDAL/Fiona installation fails on Windows

**Solution:** Use conda instead:
```powershell
# Install Anaconda or Miniconda first
# Then create environment with conda
conda create -n ghala python=3.9
conda activate ghala
conda install -c conda-forge osmnx geopandas shapely pyproj folium matplotlib jupyter
```

### Issue: Permission errors during pip install

**Solution:** Run command prompt as administrator or add `--user` flag:
```powershell
pip install --user -r requirements.txt
```

## IDE Setup

### VS Code (Recommended)
1. Install Python extension
2. Select interpreter: Press `Ctrl+Shift+P`, type "Python: Select Interpreter"
3. Choose the venv interpreter: `.\venv\Scripts\python.exe`

### PyCharm
1. File → Settings → Project → Python Interpreter
2. Add Interpreter → Existing environment
3. Select `venv\Scripts\python.exe`

## Testing Your Setup

Run the test script to verify everything works:

```powershell
python src/test_environment.py
```

---

**Status:** Week 2 Complete ✅  
**Last Updated:** January 6, 2026
