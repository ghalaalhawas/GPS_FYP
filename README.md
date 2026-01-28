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
│   └── git_setup_guide.md
├── research/           # Research notes
│   ├── osm_research_notes.md
│   ├── python_libraries_guide.md
│   └── mobile_frameworks_comparison.md
├── src/                # Source code (to be added)
│   ├── data_processing/    # Python scripts for OSM data
│   └── mobile_app/         # Mobile application code
├── data/               # Data files (not committed to git)
│   ├── raw/           # Raw OSM data
│   └── processed/     # Processed hazard points
├── plant.txt          # Weekly project plan
└── README.md          # This file
```

## Technology Stack

### Data Processing:
- Python 3.9+
- Libraries: osmnx, shapely, geopandas, pyproj
- Jupyter notebooks for exploration
- GeoJSON format for data export

### Mobile App:
- **To be decided by end of Week 1**
- Options: React Native, Flutter, or Native iOS (Swift)
- See [mobile_frameworks_comparison.md](research/mobile_frameworks_comparison.md) for details

## Setup Instructions

### Python Environment:
```bash
# Create virtual environment
python -m venv venv

# Activate (Windows)
venv\Scripts\activate

# Activate (Mac/Linux)
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

### Downloading OSM Data:

The processed data and visualizations are included in this repository under `data/processed/` and `data/visualizations/`.

If you need to download fresh OSM data:

**Option 1: Using OSMnx (Recommended)**
```python
import osmnx as ox

# Download by place name
G = ox.graph_from_place('Oxford, UK', network_type='drive')

# Download by bounding box
G = ox.graph_from_bbox(north, south, east, west, network_type='drive')

# Save to file
ox.save_graphml(G, filepath='data/raw/oxford.graphml')
```

**Option 2: Download from Geofabrik**
1. Visit: https://download.geofabrik.de/
2. Select your region (e.g., Europe → Great Britain)
3. Download `.osm.pbf` file
4. Place in `data/raw/` directory

**Option 3: Using Overpass API**
```bash
# Install osmium-tool
# Then extract specific areas as needed
```

### Mobile App:
```bash
# Navigate to mobile app directory
cd mobile_app/GhalaSafetyApp

# Install dependencies
npm install

# Start development server
npx expo start

# Scan QR code with Expo Go app on your phone
```

## Development Timeline

### January 2026
- **Week 1 (Jan 1-5):** Research and technology stack decision ✅
- **Week 2 (Jan 6-12):** Environment setup and initial data exploration
- **Week 3 (Jan 13-19):** Initial plan preparation
- **Week 4 (Jan 20-26):** Initial plan finalization
- **Week 5 (Jan 27-Feb 2):** **Initial plan submission (Feb 2)**

### Key Milestones

| Date | Milestone |
|------|-----------|
| Feb 2, 2026 | Initial Plan Submission |
| Mar 2, 2026 | Data processing complete |
| Apr 6, 2026 | Core app functionality complete |
| May 4, 2026 | Testing and refinement complete |
| Jun 1, 2026 | Evaluation complete |
| **Jul 5, 2026** | **Final Report Submission** |

## Current Status

### Week 1 (Jan 1-5): Project Setup & Initial Research ✅
**Completed:**
- ✅ Project structure created
- ✅ Technology research completed
  - OpenStreetMap data structure and APIs
  - Python geospatial libraries (osmnx, shapely, geopandas, pyproj)
  - Mobile development frameworks comparison
- ✅ Research documents created
- ✅ Git setup guide prepared
- 🔄 Technology stack decision document in progress

**Next Steps (Week 2):**
- Set up Python development environment
- Download and explore sample OSM data
- Create initial data visualization
- Make final mobile framework decision
- Set up version control (Git/GitHub)

## Research Documents

All research completed in Week 1:

1. **[OSM Research Notes](research/osm_research_notes.md)**
   - OpenStreetMap data structure
   - Junction detection methods
   - OSMnx library usage examples

2. **[Python Libraries Guide](research/python_libraries_guide.md)**
   - Detailed documentation for osmnx, shapely, geopandas, pyproj
   - Code examples and use cases
   - Installation instructions

3. **[Mobile Frameworks Comparison](research/mobile_frameworks_comparison.md)**
   - React Native vs Flutter vs Native iOS/Android
   - Pros and cons of each framework
   - Decision matrix and recommendations

4. **[Technology Stack Decision](docs/technology_stack_decision.md)**
   - Final technology decisions with justification
   - Data format choices (GeoJSON)
   - Development environment setup

5. **[Git Setup Guide](docs/git_setup_guide.md)**
   - Version control setup instructions
   - Git workflow and best practices
   - GitHub repository creation

## Core Functionality

### Phase 1: Data Processing (Feb-Mar)
- Download OpenStreetMap data for target regions
- Identify road junctions programmatically
- Classify junction types (T-junction, crossroads, etc.)
- Calculate "danger scores" based on:
  - Junction geometry and angles
  - Speed differentials between roads
  - Road classification mismatch
  - Visibility factors
- Generate hazard points (displaced 50-100m up secondary road)
- Export as GeoJSON format

### Phase 2: Mobile App (Feb-May)
- GPS location tracking with high accuracy
- Load and cache hazard point data
- Proximity detection (warn within 200m of hazard)
- Directional awareness (only warn from correct approach)
- Visual and audio warning system
- Offline capability with local caching
- User settings and preferences

### Phase 3: Optimization (Apr)
- Spatial indexing (BSP tree or R-tree)
- Performance optimization for mobile
- Battery usage optimization
- Background location updates

### Phase 4: Evaluation (May)
- User testing and feedback
- Accuracy measurements
- Performance benchmarking
- Safety impact assessment

## Risk Management

**Risk 1: Mobile development environment issues**
- Mitigation: Start early, use cross-platform framework if needed, seek help promptly

**Risk 2: OSM data processing too complex**
- Mitigation: Start with small region, use existing libraries, simplify if needed

**Risk 3: GPS accuracy issues**
- Mitigation: Test early, adjust thresholds, document limitations

**Risk 4: Time management**
- Mitigation: Follow weekly plan, adjust if falling behind, prioritize core features

## Questions for Supervisor

1. Any preference on mobile framework? (React Native vs Flutter vs Native)
2. Expected dataset size - how much of UK/Europe should we cover?
3. Are there specific types of junctions to focus on?
4. Should the app work completely offline or require internet?
5. Any specific evaluation criteria for the app?

## Resources

### Documentation
- OSMnx: https://osmnx.readthedocs.io/
- Shapely: https://shapely.readthedocs.io/
- GeoPandas: https://geopandas.org/
- React Native: https://reactnative.dev/
- Flutter: https://flutter.dev/

### Data Sources
- OpenStreetMap: https://www.openstreetmap.org/
- Geofabrik: https://download.geofabrik.de/

## License

Academic Project - [Your University]

## Contact

**Student:** [Your Name]  
**Email:** [Your Email]  
**Supervisor:** Crispin Cooper

---

**Last Updated:** January 1, 2026  
**Project Start Date:** January 1, 2026  
**Submission Deadline:** July 5, 2026
