# OpenStreetMap (OSM) Research Notes
**Date:** January 1, 2026  
**Week 1 Research**

---

## What is OpenStreetMap?

OpenStreetMap is a free, collaborative map of the world created by volunteers. It provides raw geographic data that can be used for various applications.

### Key Features:
- **Open Data:** Free to use under Open Database License (ODbL)
- **Global Coverage:** Worldwide street network data
- **Rich Attributes:** Road types, speed limits, one-way streets, junction types
- **Community-Driven:** Constantly updated by contributors

---

## OSM Data Structure

### 1. Basic Elements

#### Nodes
- Represent points (coordinates)
- Have latitude/longitude
- Can have tags (metadata)
- Example: Traffic light, junction point

#### Ways
- Ordered lists of nodes
- Represent roads, paths, boundaries
- Have tags describing features
- Example: A road connecting multiple intersections

#### Relations
- Logical collections of nodes/ways
- Represent complex features
- Example: Turn restrictions, routes

### 2. Tags (Key-Value Pairs)

```
highway=primary (major road)
highway=secondary (smaller road)
highway=residential (neighborhood street)
maxspeed=30 mph
oneway=yes
junction=roundabout
surface=asphalt
```

**Relevant Tags for Dangerous Junctions:**
- `highway=*` - Road classification
- `junction=*` - Junction type
- `maxspeed=*` - Speed limit
- `surface=*` - Road surface quality
- `traffic_calming=*` - Speed bumps, etc.
- `visibility=*` - Visibility conditions

---

## OSM Data Access Methods

### Option 1: Overpass API (Recommended for Research)
- Query OSM data by location/area
- Real-time data
- Filter by tags
- No download needed

**Example Query:**
```
[bbox:51.5,-0.2,51.6,-0.1];
way["highway"];
out geom;
```

### Option 2: Planet OSM (Full Dataset)
- Complete OSM database dump
- Very large (100+ GB compressed)
- For large-scale processing
- Updated weekly

### Option 3: Regional Extracts
- Geofabrik provides country/region extracts
- More manageable sizes
- Good for testing
- Example: Great Britain extract (~1GB)

### Option 4: OSMnx Library (Best for Python)
- Download data directly in Python
- Automatic network graph creation
- Built-in analysis functions
- Ideal for our project

---

## Junction Detection in OSM

### What Constitutes a Junction?

A junction is where two or more roads meet. In OSM, this is typically:
- A **node** shared by multiple **ways** (roads)
- Tagged with `highway=*` on each way

### Types of Junctions:

1. **Crossroads (4-way)**
   - Two roads intersecting
   - Most common junction type
   
2. **T-Junction (3-way)**
   - One road ending at another
   - Often more dangerous (limited visibility from secondary road)
   
3. **Y-Junction**
   - Two roads merging at an angle
   - Can be dangerous due to angles

4. **Roundabout**
   - Tagged as `junction=roundabout`
   - Generally safer (lower speeds)
   
5. **Complex Junctions**
   - 5+ roads meeting
   - May need special handling

---

## Identifying Dangerous Junctions

### Potential Danger Factors:

1. **Junction Geometry**
   - T-junctions (especially from minor to major road)
   - Acute angles (< 45° or > 135°)
   - Offset crossroads

2. **Speed Differential**
   - Secondary road with low speed joining high-speed primary road
   - Large difference in `maxspeed` tags

3. **Road Classification Mismatch**
   - `highway=residential` joining `highway=primary`
   - `highway=unclassified` joining `highway=trunk`

4. **Visibility Issues**
   - Presence of `visibility=limited` tag (rare)
   - Sharp curves before junction
   - Hills/elevation changes

5. **Traffic Controls**
   - Lack of traffic lights at busy junctions
   - Absence of `stop` or `give_way` signs

6. **Historical Data (Future Enhancement)**
   - Accident statistics from external sources
   - User-reported dangerous junctions

---

## OSMnx Library Details

### Installation:
```bash
pip install osmnx
```

### Key Functions:

```python
import osmnx as ox

# Download street network for an area
G = ox.graph_from_place("Oxford, UK", network_type="drive")

# Download by bounding box
G = ox.graph_from_bbox(north, south, east, west, network_type="drive")

# Get nodes and edges
nodes, edges = ox.graph_to_gdfs(G)

# Find intersections
intersections = nodes[nodes['street_count'] > 2]

# Save to file
ox.save_graphml(G, "oxford_network.graphml")
```

### Network Types:
- `drive` - Roads accessible by car (recommended for our use)
- `walk` - Pedestrian paths
- `bike` - Bicycle paths
- `all` - All roads and paths

---

## Sample Data Exploration Plan (Week 2)

1. **Choose Test Region:**
   - Small town or city district
   - Suggestions: Oxford, Cambridge, or specific London borough
   - Should have variety of junction types

2. **Download Data:**
   ```python
   import osmnx as ox
   G = ox.graph_from_place("Oxford, UK", network_type="drive")
   ```

3. **Identify Junctions:**
   - Extract nodes with multiple connections
   - Classify by number of roads (3-way, 4-way, etc.)

4. **Analyze Road Types:**
   - Check `highway` tags at each junction
   - Calculate speed differentials

5. **Visualize:**
   - Plot network with matplotlib
   - Highlight different junction types
   - Create interactive map with folium

---

## Code Snippets for Week 2

### Basic OSM Data Download
```python
import osmnx as ox
import matplotlib.pyplot as plt

# Download street network
place = "Oxford, UK"
G = ox.graph_from_place(place, network_type="drive")

# Plot
fig, ax = ox.plot_graph(G, node_size=0, edge_linewidth=0.5)
plt.show()

# Convert to GeoDataFrames
nodes, edges = ox.graph_to_gdfs(G)

# Find junctions (nodes with 3+ streets)
junctions = nodes[nodes['street_count'] >= 3].copy()
print(f"Found {len(junctions)} junctions")

# Analyze junction types
print(junctions['street_count'].value_counts())
```

### Extract Junction Angles
```python
import numpy as np

def calculate_junction_angles(G, node_id):
    """Calculate angles between roads at a junction"""
    # Get edges connected to this node
    in_edges = G.in_edges(node_id, data=True)
    out_edges = G.out_edges(node_id, data=True)
    
    # Calculate bearings
    # (Implementation needed - use geometry)
    
    return angles

# To be developed in Week 2
```

---

## Useful OSM Tags Reference

| Tag | Purpose | Example Values |
|-----|---------|----------------|
| `highway` | Road type | primary, secondary, residential |
| `maxspeed` | Speed limit | 30 mph, 50 mph |
| `oneway` | One-way street | yes, no |
| `lanes` | Number of lanes | 1, 2, 3 |
| `junction` | Junction type | roundabout |
| `surface` | Road surface | asphalt, gravel |
| `traffic_calming` | Calming measures | bump, hump |
| `lit` | Street lighting | yes, no |

---

## Resources & Links

### Official Documentation:
- OSM Wiki: https://wiki.openstreetmap.org/
- OSM Tag Reference: https://wiki.openstreetmap.org/wiki/Map_Features
- Overpass API: https://overpass-api.de/

### Data Sources:
- Geofabrik Downloads: https://download.geofabrik.de/
- BBBike Extracts: https://extract.bbbike.org/

### Tools:
- OSMnx Documentation: https://osmnx.readthedocs.io/
- Overpass Turbo (Web Tool): https://overpass-turbo.eu/

### Academic Papers:
- Boeing, G. (2017). "OSMnx: New methods for acquiring, constructing, analyzing, and visualizing complex street networks"
- (Add more papers on junction safety analysis)

---

## Questions to Investigate:

- [ ] How accurate is OSM data for road junctions in UK?
- [ ] What percentage of junctions have speed limit data?
- [ ] How to handle missing data (e.g., no maxspeed tag)?
- [ ] Can we extract elevation data from OSM or need external source?
- [ ] How to validate our dangerous junction detection algorithm?

---

## Next Steps:
1. Install OSMnx and dependencies
2. Download sample data for test region
3. Create visualization of junctions
4. Experiment with junction classification
5. Document findings for Week 2 deliverable

---

**Status:** Initial Research - Week 1  
**Last Updated:** January 1, 2026
