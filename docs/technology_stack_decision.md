# Technology Stack Decision Document
**Project:** GPS Mobile App to Help Driving Safety Abroad  
**Date:** January 1, 2026  
**Author:** [Your Name]

---

## Executive Summary
This document outlines the technology stack decisions for the GPS safety app project, comparing various options for data processing and mobile development.

---

## 1. Data Processing Stack

### Backend Language: Python ✅ **SELECTED**

**Pros:**
- Excellent geospatial library ecosystem (`osmnx`, `shapely`, `geopandas`)
- OSMnx specifically designed for OpenStreetMap data processing
- Strong data analysis capabilities with pandas/numpy
- Easy prototyping and iteration
- Well-documented for academic/research projects

**Cons:**
- Slower than compiled languages (mitigated by libraries using C extensions)
- Not ideal for real-time mobile backend (not needed for this project)

**Decision:** Python is ideal for offline data processing and analysis.

---

## 2. Python Libraries for Geospatial Processing

### Core Libraries:

#### OSMnx ✅ **SELECTED**
- **Purpose:** Download, model, analyze OSM street networks
- **Pros:** 
  - Direct OSM API integration
  - Network analysis built-in
  - Easy junction/intersection detection
  - Active development and good documentation
- **Installation:** `pip install osmnx`

#### Shapely ✅ **SELECTED**
- **Purpose:** Geometric operations (points, lines, polygons)
- **Pros:**
  - Industry standard for geometry manipulation
  - Point displacement calculations
  - Distance calculations
- **Installation:** `pip install shapely`

#### GeoPandas ✅ **SELECTED**
- **Purpose:** Geospatial dataframes (like pandas with GIS)
- **Pros:**
  - Handles CRS transformations
  - Easy data export (GeoJSON, Shapefile)
  - Integrates with matplotlib for visualization
- **Installation:** `pip install geopandas`

#### PyProj ✅ **SELECTED**
- **Purpose:** Coordinate reference system transformations
- **Pros:**
  - Accurate distance calculations
  - Convert between WGS84 (GPS) and projected coordinates
- **Installation:** `pip install pyproj`

### Additional Libraries to Consider:
- **Folium:** Interactive map visualization for testing/validation
- **Matplotlib:** Static map visualization
- **NetworkX:** Graph analysis (already included with OSMnx)

---

## 3. Mobile Development Framework

### Option A: React Native
**Pros:**
- Cross-platform (iOS + Android from single codebase)
- JavaScript/TypeScript - widely known
- Large community and ecosystem
- React Native Maps library for map integration
- Good GPS/location APIs via expo-location or react-native-geolocation
- Hot reload for faster development

**Cons:**
- Performance overhead vs native
- Bridge between JS and native code can cause issues
- App size larger than native

**Best For:** If you know JavaScript/web development

---

### Option B: Flutter
**Pros:**
- Cross-platform (iOS + Android)
- Excellent performance (compiles to native)
- Beautiful UI widgets out of the box
- Growing ecosystem
- Google Maps Flutter plugin
- Good location services support
- Hot reload

**Cons:**
- Dart language learning curve
- Smaller community than React Native
- Larger app size

**Best For:** If you want best performance with cross-platform

---

### Option C: Native Development (Swift/Kotlin)
**Pros:**
- Best performance
- Full access to platform APIs
- Smallest app size
- Best debugging tools

**Cons:**
- Two separate codebases (2x development time)
- Need to learn two languages (Swift + Kotlin/Java)
- Harder to maintain

**Best For:** If performance is critical or iOS-only

---

### Option D: Native iOS Only (Swift)
**Pros:**
- Focus on one platform
- Best iOS performance
- Native MapKit integration
- Core Location framework for GPS
- Xcode simulator for testing

**Cons:**
- iOS only (no Android)
- Swift learning curve if not familiar

**Best For:** Academic project with limited time, if you have a Mac

---

## 4. Recommended Technology Stack

### ✅ **FINAL RECOMMENDATION:**

#### Data Processing:
- **Language:** Python 3.9+
- **Core Libraries:** osmnx, shapely, geopandas, pyproj
- **Development:** Jupyter notebooks for exploration, Python scripts for production
- **Data Format:** GeoJSON (human-readable, widely supported)
- **Version Control:** Git + GitHub

#### Mobile App:
- **Framework:** **[TO BE DECIDED - Week 1 Research]**
- **Recommendation:** Start with **React Native** OR **Flutter** for cross-platform
  - Choose React Native if you know JavaScript
  - Choose Flutter if learning a new language is acceptable and want best performance
  - Consider iOS-only native if limited time and have Mac/iOS device

#### Development Environment:
- **Python IDE:** VS Code with Python extension OR PyCharm
- **Mobile IDE:** 
  - React Native: VS Code
  - Flutter: VS Code or Android Studio
  - iOS Native: Xcode
- **Version Control:** Git + GitHub
- **Documentation:** Markdown files in `/docs`

---

## 5. Data Format Decision

### GeoJSON ✅ **SELECTED**

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-0.1276, 51.5074]
      },
      "properties": {
        "id": "hazard_001",
        "danger_score": 0.85,
        "junction_type": "T-junction",
        "approach_angle": 270,
        "road_primary": "A1",
        "road_secondary": "B123",
        "description": "Poor visibility T-junction"
      }
    }
  ]
}
```

**Pros:**
- Human-readable
- Native support in most mapping libraries
- Easy to validate and debug
- Smaller than XML alternatives

---

## 6. Spatial Optimization Strategy

### Binary Space Partition (BSP) Tree or Alternatives:

**Options to Research:**
1. **R-Tree** (via `rtree` Python library) - Industry standard for spatial indexing
2. **K-d Tree** (via `scipy.spatial`) - Good for point-based queries
3. **QuadTree** - Simpler, good for 2D spatial data
4. **Custom grid-based system** - Simple but effective for mobile

**Decision:** Defer until Week 14, but likely **R-Tree** for Python processing, custom grid or R-Tree for mobile app.

---

## 7. Next Steps (Week 1)

- [ ] Install Python and set up virtual environment
- [ ] Install geospatial libraries
- [ ] Download sample OSM data for test region
- [ ] Create "Hello World" script with osmnx
- [ ] Research mobile framework and make final decision
- [ ] Set up Git repository
- [ ] Create initial project structure

---

## 8. Questions for Supervisor Meeting

1. Any preference on mobile framework? (React Native vs Flutter vs Native)
2. Expected dataset size - how much of UK/Europe should we cover?
3. Are there specific types of junctions to focus on?
4. Should the app work completely offline or require internet?
5. Any specific evaluation criteria for the app?

---

## References

- OSMnx Documentation: https://osmnx.readthedocs.io/
- Shapely Documentation: https://shapely.readthedocs.io/
- GeoPandas Documentation: https://geopandas.org/
- React Native Documentation: https://reactnative.dev/
- Flutter Documentation: https://flutter.dev/

---

**Status:** DRAFT - To be finalized by Feb 2, 2026  
**Last Updated:** January 1, 2026
