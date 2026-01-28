# System Architecture Diagram
## GPS Safety App - Ghala

This document provides visual representation of the system architecture using ASCII diagrams and detailed component descriptions.

---

## 1. High-Level System Overview

```
┌──────────────────────────────────────────────────────────────────────┐
│                        OPENSTREETMAP DATABASE                         │
│                    (Global road network data)                         │
└────────────────────────────────┬─────────────────────────────────────┘
                                 │
                                 │ Download via Overpass API / OSMnx
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────┐
│                    DATA PROCESSING PIPELINE                           │
│                         (Python Scripts)                              │
│                                                                       │
│  ┌────────────┐   ┌────────────┐   ┌────────────┐   ┌────────────┐ │
│  │   OSM      │──▶│  Junction  │──▶│   Danger   │──▶│   Hazard   │ │
│  │ Downloader │   │  Detector  │   │  Scorer    │   │ Generator  │ │
│  └────────────┘   └────────────┘   └────────────┘   └────────────┘ │
│                                                                       │
│  Input: OSM PBF/XML        Process: Analyze        Output: GeoJSON   │
└────────────────────────────────┬─────────────────────────────────────┘
                                 │
                                 │ Export GeoJSON files
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────┐
│                      HAZARD POINT DATASETS                            │
│                        (GeoJSON files)                                │
│                                                                       │
│  oxford_hazards.geojson, london_hazards.geojson, etc.                │
└────────────────────────────────┬─────────────────────────────────────┘
                                 │
                                 │ Load into app / Download from server
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────┐
│                          MOBILE APPLICATION                           │
│                        (iOS / Android)                                │
│                                                                       │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │                    USER INTERFACE LAYER                       │   │
│  │                                                               │   │
│  │  • Map View (shows user location + hazard points)            │   │
│  │  • Warning Overlay (visual alert when near hazard)           │   │
│  │  • Settings Screen (preferences, region selection)           │   │
│  └────────────────────────┬─────────────────────────────────────┘   │
│                           │                                           │
│  ┌────────────────────────▼─────────────────────────────────────┐   │
│  │                  BUSINESS LOGIC LAYER                         │   │
│  │                                                               │   │
│  │  • Location Service (GPS tracking)                           │   │
│  │  • Proximity Detector (distance calculations)                │   │
│  │  • Warning Manager (trigger logic, cooldown)                 │   │
│  │  • Direction Calculator (approach angle)                     │   │
│  └────────────────────────┬─────────────────────────────────────┘   │
│                           │                                           │
│  ┌────────────────────────▼─────────────────────────────────────┐   │
│  │                       DATA LAYER                              │   │
│  │                                                               │   │
│  │  • GeoJSON Loader (parse hazard data)                        │   │
│  │  • Spatial Index (R-tree for fast queries)                   │   │
│  │  • Local Cache (AsyncStorage / SQLite)                       │   │
│  │  • Settings Storage (user preferences)                       │   │
│  └───────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────┘
                                 │
                                 │ Real-time GPS updates
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────┐
│                           GPS SATELLITES                              │
│                    (Location data provider)                           │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 2. Data Processing Pipeline Detailed

```
┌──────────────────────────────────────────────────────────────────────┐
│                        DATA PROCESSING PIPELINE                       │
└──────────────────────────────────────────────────────────────────────┘

┌─────────────────┐
│  1. OSM DATA    │
│   DOWNLOAD      │
│                 │
│  • osmnx        │
│  • Place name   │
│  • Bounding box │
└────────┬────────┘
         │
         │ NetworkX graph (nodes + edges)
         │
         ▼
┌─────────────────┐
│  2. JUNCTION    │
│   DETECTION     │
│                 │
│  Filter nodes:  │
│  street_count   │
│  >= 3           │
└────────┬────────┘
         │
         │ Junction GeoDataFrame
         │
         ▼
┌─────────────────┐
│  3. JUNCTION    │
│   CLASSIFICATION│
│                 │
│  • T-junction   │
│  • Crossroads   │
│  • 5-way, etc.  │
└────────┬────────┘
         │
         │ Classified junctions
         │
         ▼
┌─────────────────┐
│  4. DANGER      │
│   SCORING       │
│                 │
│  Calculate:     │
│  • Type (30%)   │
│  • Speed (25%)  │
│  • Roads (25%)  │
│  • Geometry(20%)│
└────────┬────────┘
         │
         │ Junctions with danger scores
         │
         ▼
┌─────────────────┐
│  5. HAZARD      │
│   POINT GEN     │
│                 │
│  Displace 100m  │
│  along approach │
│  road           │
└────────┬────────┘
         │
         │ Hazard points
         │
         ▼
┌─────────────────┐
│  6. GEOJSON     │
│   EXPORT        │
│                 │
│  Save to file   │
│  for mobile app │
└─────────────────┘
```

---

## 3. Mobile App Architecture Detailed

```
┌──────────────────────────────────────────────────────────────────────┐
│                          MOBILE APPLICATION                           │
└──────────────────────────────────────────────────────────────────────┘

╔══════════════════════════════════════════════════════════════════════╗
║                        UI LAYER (Views)                               ║
╚══════════════════════════════════════════════════════════════════════╝

┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│   Map View   │   │   Warning    │   │   Settings   │   │    Help/     │
│              │   │   Overlay    │   │    Screen    │   │   Tutorial   │
│  • User loc  │   │              │   │              │   │              │
│  • Hazards   │   │  • Visual    │   │  • Region    │   │  • Guide     │
│  • Map tiles │   │  • Audio     │   │  • Alerts    │   │  • About     │
└──────┬───────┘   └──────┬───────┘   └──────┬───────┘   └──────┬───────┘
       │                  │                  │                  │
       └──────────────────┴──────────────────┴──────────────────┘
                                 │
╔═══════════════════════════════▼═══════════════════════════════════════╗
║                      BUSINESS LOGIC LAYER                             ║
╚═══════════════════════════════════════════════════════════════════════╝

┌────────────────────────────────────────────────────────────────────────┐
│                        LOCATION SERVICE                                 │
│                                                                        │
│  • startTracking()          • getCurrentLocation()                     │
│  • stopTracking()           • watchPosition()                          │
│  • getAccuracy()            • requestPermissions()                     │
│                                                                        │
│  Outputs: lat, lon, heading, speed, accuracy                          │
└──────────────────────────────┬─────────────────────────────────────────┘
                               │
                               ▼
┌────────────────────────────────────────────────────────────────────────┐
│                       PROXIMITY DETECTOR                                │
│                                                                        │
│  Input: User location (lat, lon)                                       │
│         Hazard points (GeoDataFrame)                                   │
│                                                                        │
│  Process:                                                              │
│  1. Query spatial index for points within 500m radius                 │
│  2. Calculate exact distances using Haversine formula                  │
│  3. Filter points within warning threshold (200m)                      │
│  4. Sort by distance (closest first)                                   │
│                                                                        │
│  Output: List of nearby hazards with distances                        │
└──────────────────────────────┬─────────────────────────────────────────┘
                               │
                               ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      DIRECTION CALCULATOR                               │
│                                                                        │
│  Input: User location, heading, hazard location, approach angle        │
│                                                                        │
│  Process:                                                              │
│  1. Calculate bearing from user to hazard                              │
│  2. Compare with hazard's approach angle                               │
│  3. Determine if user is approaching from correct direction            │
│     (within ±45° of approach angle)                                    │
│                                                                        │
│  Output: isApproaching (boolean)                                       │
└──────────────────────────────┬─────────────────────────────────────────┘
                               │
                               ▼
┌────────────────────────────────────────────────────────────────────────┐
│                         WARNING MANAGER                                 │
│                                                                        │
│  State: lastWarningTime, activeWarnings, cooldownPeriod                │
│                                                                        │
│  Logic:                                                                │
│  1. For each nearby hazard approaching from correct direction:         │
│     a. Check if distance < 200m                                        │
│     b. Check if not in cooldown period (last warning > 5 min ago)      │
│     c. Check if danger score > threshold (e.g., 0.6)                   │
│  2. If all conditions met:                                             │
│     a. Trigger visual warning (show overlay)                           │
│     b. Play audio alert (beep or voice)                                │
│     c. Optional: haptic feedback (vibration)                           │
│     d. Set lastWarningTime                                             │
│                                                                        │
│  Output: triggerWarning(hazard)                                        │
└────────────────────────────────────────────────────────────────────────┘
                               │
╔═══════════════════════════════▼═══════════════════════════════════════╗
║                           DATA LAYER                                  ║
╚═══════════════════════════════════════════════════════════════════════╝

┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
│   GeoJSON        │   │   Spatial        │   │   Local          │
│   Loader         │   │   Index          │   │   Cache          │
│                  │   │                  │   │                  │
│  • Parse files   │──▶│  • R-tree        │   │  • AsyncStorage  │
│  • Validate      │   │  • Quadtree      │   │  • SQLite        │
│  • Transform     │   │  • Fast queries  │   │  • Offline data  │
└──────────────────┘   └──────────────────┘   └──────────────────┘
```

---

## 4. Data Flow Diagram

```
USER OPENS APP
      │
      ▼
┌─────────────────────────────────────────┐
│  App checks for cached hazard data      │
│  for current region                     │
└──────────────┬──────────────────────────┘
               │
       ┌───────┴───────┐
       │               │
  [FOUND]         [NOT FOUND]
       │               │
       │               ▼
       │      ┌─────────────────────┐
       │      │  Prompt to download │
       │      │  region data        │
       │      └──────────┬──────────┘
       │                 │
       │                 ▼
       │      ┌─────────────────────┐
       │      │  Download GeoJSON   │
       │      │  Save to cache      │
       │      └──────────┬──────────┘
       │                 │
       └─────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  Load hazard points into memory         │
│  Build spatial index (R-tree)           │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  Request GPS permissions                │
│  Start location tracking                │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  Display map with user location         │
└──────────────┬──────────────────────────┘
               │
               │ [CONTINUOUS LOOP]
               │
               ▼
    ┌──────────────────────┐
    │  GPS update received │
    │  (every 1-5 seconds) │
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │  Query spatial index │
    │  for nearby points   │
    │  (within 500m)       │
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │  Calculate distances │
    │  to each point       │
    └──────────┬───────────┘
               │
       ┌───────┴───────┐
       │               │
  [< 200m]        [> 200m]
       │               │
       │               ▼
       │     ┌─────────────────┐
       │     │  No action      │
       │     │  Continue loop  │
       │     └─────────────────┘
       │
       ▼
┌─────────────────────────────────────────┐
│  Check if approaching from correct      │
│  direction                              │
└──────────────┬──────────────────────────┘
               │
       ┌───────┴───────┐
       │               │
    [YES]           [NO]
       │               │
       │               ▼
       │     ┌─────────────────┐
       │     │  No warning     │
       │     │  Continue loop  │
       │     └─────────────────┘
       │
       ▼
┌─────────────────────────────────────────┐
│  Check cooldown                         │
│  (last warning > 5 minutes ago?)        │
└──────────────┬──────────────────────────┘
               │
       ┌───────┴───────┐
       │               │
    [YES]           [NO]
       │               │
       │               ▼
       │     ┌─────────────────┐
       │     │  Skip warning   │
       │     │  Continue loop  │
       │     └─────────────────┘
       │
       ▼
┌─────────────────────────────────────────┐
│  TRIGGER WARNING                        │
│  • Show visual overlay                  │
│  • Play audio alert                     │
│  • Vibrate (optional)                   │
│  • Log event                            │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  Set lastWarningTime = now              │
└──────────────┬──────────────────────────┘
               │
               ▼
    [Continue GPS loop]
```

---

## 5. Component Interaction Diagram

```
┌──────────┐         ┌─────────────┐         ┌──────────────┐
│   GPS    │────────▶│  Location   │────────▶│   Proximity  │
│ Sensor   │ coords  │  Service    │ (x,y)   │   Detector   │
└──────────┘         └─────────────┘         └───────┬──────┘
                                                      │
                                                      │ nearby hazards
                                                      │
                     ┌────────────────────────────────┘
                     │
                     ▼
              ┌──────────────┐
              │  Direction   │
              │  Calculator  │
              └───────┬──────┘
                      │
                      │ filtered hazards
                      │
                      ▼
              ┌──────────────┐         ┌──────────────┐
              │   Warning    │────────▶│      UI      │
              │   Manager    │ trigger │   Overlay    │
              └───────┬──────┘         └──────────────┘
                      │
                      │
                      ▼
              ┌──────────────┐
              │    Audio     │
              │   Service    │
              └──────────────┘

┌────────────────────────────────────────────────────────────┐
│              SPATIAL INDEX (R-tree)                         │
│                                                            │
│  Provides fast queries:                                    │
│  • getNearbyPoints(lat, lon, radius)                      │
│  • Returns only points within radius                       │
│  • O(log n) query time vs O(n) linear search              │
└────────────────────────────────────────────────────────────┘
```

---

## 6. Technology Stack Mapping

```
┌─────────────────────────────────────────────────────────────────┐
│                      TECHNOLOGY LAYERS                           │
└─────────────────────────────────────────────────────────────────┘

DATA PROCESSING:
┌────────────────┬────────────────────────────────────────────────┐
│ Python 3.9+    │ Main language                                  │
├────────────────┼────────────────────────────────────────────────┤
│ OSMnx          │ Download & analyze OSM networks               │
│ GeoPandas      │ Geospatial dataframes                         │
│ Shapely        │ Geometric operations                          │
│ PyProj         │ Coordinate transformations                    │
│ Matplotlib     │ Visualization                                 │
└────────────────┴────────────────────────────────────────────────┘

MOBILE APP:
┌────────────────┬────────────────────────────────────────────────┐
│ React Native   │ Cross-platform framework                      │
│  (or Flutter)  │                                               │
├────────────────┼────────────────────────────────────────────────┤
│ React Native   │ Native map views                              │
│ Maps           │                                               │
├────────────────┼────────────────────────────────────────────────┤
│ Expo Location  │ GPS and location services                     │
├────────────────┼────────────────────────────────────────────────┤
│ AsyncStorage   │ Local data storage                            │
├────────────────┼────────────────────────────────────────────────┤
│ JavaScript/    │ Programming language                          │
│ TypeScript     │                                               │
└────────────────┴────────────────────────────────────────────────┘

DATA FORMAT:
┌────────────────┬────────────────────────────────────────────────┐
│ GeoJSON        │ Hazard point storage format                   │
└────────────────┴────────────────────────────────────────────────┘

VERSION CONTROL:
┌────────────────┬────────────────────────────────────────────────┐
│ Git + GitHub   │ Source code management                        │
└────────────────┴────────────────────────────────────────────────┘
```

---

## 7. Deployment Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                      DEVELOPMENT WORKFLOW                         │
└──────────────────────────────────────────────────────────────────┘

PYTHON DATA PROCESSING:
┌────────────┐      ┌────────────┐      ┌────────────┐
│ OSM Data   │─────▶│  Python    │─────▶│  GeoJSON   │
│ Download   │      │  Scripts   │      │  Files     │
└────────────┘      └────────────┘      └────────────┘
                                              │
                                              │
                                              ▼
                                    ┌─────────────────┐
                                    │ File Storage    │
                                    │ (Local or Cloud)│
                                    └─────────────────┘

MOBILE APP DEVELOPMENT:
┌────────────┐      ┌────────────┐      ┌────────────┐
│ Code       │─────▶│  Build     │─────▶│  Test on   │
│ (JS/TS)    │      │  (Metro)   │      │  Device    │
└────────────┘      └────────────┘      └────────────┘

DEPLOYMENT (FUTURE):
┌────────────┐      ┌────────────┐      ┌────────────┐
│ GeoJSON    │─────▶│  CDN or    │─────▶│  Mobile    │
│ Datasets   │      │  Server    │      │  App       │
└────────────┘      └────────────┘      └────────────┘
```

---

## 8. File Structure

```
Ghala/
│
├── docs/                          # Documentation
│   ├── initial_plan.md
│   ├── architecture.md           # This file
│   ├── technology_stack_decision.md
│   └── git_setup_guide.md
│
├── research/                      # Research notes
│   ├── osm_research_notes.md
│   ├── python_libraries_guide.md
│   └── mobile_frameworks_comparison.md
│
├── src/                          # Source code
│   ├── 01_download_osm_data.py  # OSM downloader
│   ├── 02_detect_junctions.py   # Junction detector
│   ├── 03_score_danger.py       # Danger scoring (Week 7)
│   ├── 04_generate_hazards.py   # Hazard point generator (Week 7)
│   └── 05_export_data.py        # GeoJSON exporter (Week 9)
│
├── data/                         # Data files (not in git)
│   ├── raw/                     # Raw OSM data
│   │   ├── oxford_network.graphml
│   │   ├── oxford_nodes.geojson
│   │   └── oxford_edges.geojson
│   ├── processed/               # Processed data
│   │   └── oxford_junctions.geojson
│   └── visualizations/          # Generated plots
│       ├── oxford_network.png
│       └── oxford_junctions_by_type.png
│
├── mobile_app/                   # Mobile app (Week 5+)
│   ├── src/
│   │   ├── components/
│   │   ├── services/
│   │   └── utils/
│   ├── package.json
│   └── App.js
│
├── requirements.txt              # Python dependencies
├── .gitignore
├── README.md
└── plant.txt                     # Project plan
```

---

## 9. Key Design Decisions

### 9.1 Why GeoJSON?
- Human-readable format
- Native support in mapping libraries
- Smaller than alternatives (Shapefile, GeoPackage)
- Easy to validate and debug

### 9.2 Why Spatial Index?
- Linear search through 10,000+ points is too slow
- R-tree provides O(log n) query time
- Essential for real-time mobile performance

### 9.3 Why Displace Hazard Points?
- Warn before reaching junction
- Give driver time to prepare
- 100m displacement ≈ 2-4 seconds at 60 mph
- Placed on approach road, not at junction itself

### 9.4 Why React Native?
- Single codebase for iOS and Android
- Good mapping and GPS library support
- Faster development than native
- Acceptable performance for this use case

---

**Document Version:** 1.0  
**Last Updated:** January 19, 2026  
**Part of:** Initial Plan Document
