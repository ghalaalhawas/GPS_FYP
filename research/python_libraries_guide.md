# Python Geospatial Libraries Research
**Date:** January 1, 2026  
**Week 1 Research**

---

## Overview

This document provides detailed information about the Python libraries we'll use for processing OpenStreetMap data and generating hazard points.

---

## 1. OSMnx

### What is OSMnx?
OSMnx is a Python library that lets you download, model, analyze, and visualize street networks from OpenStreetMap. Created by Geoff Boeing specifically for street network analysis.

### Installation:
```bash
pip install osmnx
```

### Key Capabilities:
- Download street networks from OSM
- Convert to NetworkX graphs
- Calculate network statistics
- Find shortest paths
- Identify intersections
- Geocoding and reverse geocoding
- Save/load networks in various formats

### Basic Usage:

#### Download Network by Place Name:
```python
import osmnx as ox

# Download by place name
G = ox.graph_from_place("Oxford, UK", network_type="drive")

# Download by address with distance
G = ox.graph_from_address("221B Baker Street, London", dist=1000, network_type="drive")

# Download by point (lat, lon)
G = ox.graph_from_point((51.5074, -0.1278), dist=2000, network_type="drive")

# Download by bounding box
north, south, east, west = 51.6, 51.4, -0.1, -0.3
G = ox.graph_from_bbox(north, south, east, west, network_type="drive")
```

#### Working with Graphs:
```python
# Convert to GeoDataFrames (for easier analysis)
nodes, edges = ox.graph_to_gdfs(G)

# Basic statistics
print(f"Nodes: {len(G.nodes())}")
print(f"Edges: {len(G.edges())}")

# Get basic network stats
stats = ox.basic_stats(G)
print(stats)
```

#### Finding Intersections:
```python
# Nodes with multiple streets are intersections
intersections = nodes[nodes['street_count'] > 2]

# Get all T-junctions (3 streets meeting)
t_junctions = nodes[nodes['street_count'] == 3]

# Get crossroads (4 streets)
crossroads = nodes[nodes['street_count'] == 4]
```

#### Visualization:
```python
import matplotlib.pyplot as plt

# Basic plot
fig, ax = ox.plot_graph(G, node_size=0, edge_linewidth=0.5)

# Plot with specific nodes highlighted
fig, ax = ox.plot_graph(G, node_color='gray', node_size=0)
ax.scatter(intersections['x'], intersections['y'], c='red', s=10)
plt.show()
```

### Important Attributes:

**Node Attributes:**
- `x`, `y` - Longitude, latitude
- `street_count` - Number of streets meeting at node
- `osmid` - Original OSM ID

**Edge Attributes:**
- `highway` - Road type (primary, secondary, etc.)
- `length` - Length in meters
- `maxspeed` - Speed limit (if available)
- `name` - Street name
- `oneway` - Boolean, if one-way
- `lanes` - Number of lanes (if available)
- `geometry` - LineString geometry

### Useful Functions for Our Project:
```python
# Get the nearest node to a lat/lon point
nearest_node = ox.nearest_nodes(G, X=-0.1278, Y=51.5074)

# Get the nearest edge
nearest_edge = ox.nearest_edges(G, X=-0.1278, Y=51.5074)

# Calculate shortest path
origin = ox.nearest_nodes(G, -0.1, 51.5)
destination = ox.nearest_nodes(G, -0.2, 51.6)
route = ox.shortest_path(G, origin, destination, weight='length')

# Get bearing of an edge
bearing = ox.bearing.calculate_bearing(lat1, lon1, lat2, lon2)
```

### Export Options:
```python
# Save as GraphML (preserves all attributes)
ox.save_graphml(G, "network.graphml")

# Save as shapefile
ox.save_graph_shapefile(G, filepath="network_shape")

# Save as GeoPackage
nodes, edges = ox.graph_to_gdfs(G)
nodes.to_file("nodes.gpkg", driver="GPKG")
edges.to_file("edges.gpkg", driver="GPKG")
```

---

## 2. Shapely

### What is Shapely?
Shapely is a Python library for manipulation and analysis of geometric objects. It's the standard for geometry operations in Python.

### Installation:
```bash
pip install shapely
```

### Key Capabilities:
- Create and manipulate geometric shapes
- Calculate distances, areas, lengths
- Geometric operations (intersection, union, buffer)
- Point-in-polygon tests
- Line operations

### Basic Usage:

#### Creating Geometries:
```python
from shapely.geometry import Point, LineString, Polygon

# Create a point (longitude, latitude)
point = Point(-0.1278, 51.5074)

# Create a line
line = LineString([(-0.1, 51.5), (-0.2, 51.6), (-0.15, 51.7)])

# Create a polygon
polygon = Polygon([(-0.1, 51.5), (-0.2, 51.5), (-0.2, 51.6), (-0.1, 51.6)])
```

#### Distance Calculations:
```python
from shapely.geometry import Point

point1 = Point(0, 0)
point2 = Point(3, 4)

# Calculate distance (in coordinate units)
distance = point1.distance(point2)
print(distance)  # 5.0

# Note: For geographic coordinates, use GeoPandas or pyproj for accurate distances
```

#### Point Displacement (Critical for Our Project):
```python
from shapely.geometry import Point, LineString
from shapely.affinity import translate

# Original junction point
junction = Point(-0.1278, 51.5074)

# Get direction of secondary road (as a vector)
# Assume secondary road goes from junction to another point
road_end = Point(-0.1279, 51.5084)

# Calculate vector
dx = road_end.x - junction.x
dy = road_end.y - junction.y

# Normalize and scale to 100m (approximately 0.001 degrees)
# Note: Need proper conversion for accurate distance
distance_offset = 0.001  # ~100m in degrees (rough)

# Create displaced point
hazard_point = translate(junction, xoff=dx*distance_offset, yoff=dy*distance_offset)
```

#### Buffer Operations:
```python
from shapely.geometry import Point

# Create a 200m buffer around a point
# Note: For geographic coordinates, need to project first
point = Point(-0.1278, 51.5074)
buffer = point.buffer(0.002)  # Approximate 200m in degrees

# Check if another point is within buffer
other_point = Point(-0.1279, 51.5075)
is_near = other_point.within(buffer)
```

#### Geometric Operations:
```python
from shapely.geometry import Polygon, LineString

# Intersection
poly1 = Polygon([(0, 0), (2, 0), (2, 2), (0, 2)])
poly2 = Polygon([(1, 1), (3, 1), (3, 3), (1, 3)])
intersection = poly1.intersection(poly2)

# Union
union = poly1.union(poly2)

# Difference
difference = poly1.difference(poly2)

# Check if geometries intersect
do_intersect = poly1.intersects(poly2)
```

### Useful for Junction Analysis:
```python
from shapely.geometry import Point, LineString
import math

def calculate_angle_between_roads(junction_point, point1, point2):
    """Calculate angle between two roads at a junction"""
    # Vectors from junction to other points
    v1 = (point1.x - junction_point.x, point1.y - junction_point.y)
    v2 = (point2.x - junction_point.x, point2.y - junction_point.y)
    
    # Calculate angle using dot product
    dot = v1[0]*v2[0] + v1[1]*v2[1]
    det = v1[0]*v2[1] - v1[1]*v2[0]
    angle = math.atan2(det, dot)
    angle_degrees = math.degrees(angle)
    
    return abs(angle_degrees)

# Acute angles (< 45°) might indicate dangerous junctions
```

---

## 3. GeoPandas

### What is GeoPandas?
GeoPandas extends pandas to work with geospatial data. It combines the power of pandas DataFrames with geometric operations from Shapely.

### Installation:
```bash
pip install geopandas
```

### Key Capabilities:
- Work with geospatial data in DataFrame format
- Read/write various GIS formats (Shapefile, GeoJSON, GPKG)
- Coordinate reference system (CRS) transformations
- Spatial joins and operations
- Integration with matplotlib for plotting

### Basic Usage:

#### Creating GeoDataFrames:
```python
import geopandas as gpd
from shapely.geometry import Point

# Create from scratch
data = {
    'id': [1, 2, 3],
    'name': ['Junction A', 'Junction B', 'Junction C'],
    'geometry': [Point(-0.1, 51.5), Point(-0.2, 51.6), Point(-0.15, 51.55)]
}
gdf = gpd.GeoDataFrame(data, crs="EPSG:4326")
```

#### Reading/Writing Files:
```python
# Read GeoJSON
gdf = gpd.read_file("hazards.geojson")

# Read Shapefile
gdf = gpd.read_file("hazards.shp")

# Write GeoJSON
gdf.to_file("output.geojson", driver="GeoJSON")

# Write to GeoPackage
gdf.to_file("output.gpkg", driver="GPKG")
```

#### CRS Transformations:
```python
# Check current CRS
print(gdf.crs)  # EPSG:4326 (WGS84 - lat/lon)

# Project to British National Grid for accurate UK measurements
gdf_projected = gdf.to_crs("EPSG:27700")

# Calculate accurate distances in meters
gdf_projected['distance'] = gdf_projected.geometry.distance(some_point)

# Project back to WGS84 for GPS compatibility
gdf_wgs84 = gdf_projected.to_crs("EPSG:4326")
```

#### Spatial Operations:
```python
# Spatial join - find all hazards within buffers
buffers = points_gdf.buffer(0.002)  # Create buffers
hazards_within = gpd.sjoin(hazards_gdf, buffers, predicate='within')

# Filter by distance
user_location = Point(-0.1278, 51.5074)
gdf['distance_to_user'] = gdf.geometry.distance(user_location)
nearby = gdf[gdf['distance_to_user'] < 0.01]
```

#### Plotting:
```python
import matplotlib.pyplot as plt

# Simple plot
gdf.plot()
plt.show()

# Styled plot
fig, ax = plt.subplots(figsize=(10, 10))
gdf.plot(ax=ax, color='red', markersize=50, alpha=0.6)
ax.set_title("Dangerous Junctions")
plt.show()

# Plot with basemap (requires contextily)
import contextily as ctx
ax = gdf.to_crs(epsg=3857).plot(figsize=(10, 10), alpha=0.5, color='red')
ctx.add_basemap(ax)
plt.show()
```

### Critical for Our Project:
```python
import geopandas as gpd
import osmnx as ox

# Get OSM data
G = ox.graph_from_place("Oxford, UK", network_type="drive")
nodes, edges = ox.graph_to_gdfs(G)

# Filter junctions
junctions = nodes[nodes['street_count'] >= 3].copy()

# Project to calculate accurate distances
junctions_proj = junctions.to_crs("EPSG:27700")  # British National Grid

# Calculate danger scores (placeholder)
junctions_proj['danger_score'] = 0.5  # To be implemented

# Export for mobile app (back to WGS84)
output = junctions_proj.to_crs("EPSG:4326")
output.to_file("hazard_points.geojson", driver="GeoJSON")
```

---

## 4. PyProj

### What is PyProj?
PyProj provides Python interfaces to PROJ (cartographic projections library). It handles coordinate transformations and distance calculations.

### Installation:
```bash
pip install pyproj
```

### Key Capabilities:
- Transform coordinates between different CRS
- Calculate accurate distances on Earth's surface
- Handle various map projections

### Basic Usage:

#### Coordinate Transformation:
```python
from pyproj import Transformer

# Create transformer from WGS84 (lat/lon) to British National Grid
transformer = Transformer.from_crs("EPSG:4326", "EPSG:27700", always_xy=True)

# Transform coordinates (longitude, latitude) -> (easting, northing)
lon, lat = -0.1278, 51.5074
easting, northing = transformer.transform(lon, lat)
print(f"Easting: {easting}, Northing: {northing}")
```

#### Distance Calculation:
```python
from pyproj import Geod

# Create geodesic calculator (WGS84 ellipsoid)
geod = Geod(ellps="WGS84")

# Calculate distance between two points (in meters)
lon1, lat1 = -0.1278, 51.5074  # London
lon2, lat2 = -0.1279, 51.5084  # Nearby point

# Forward azimuth, back azimuth, distance
fwd_azimuth, back_azimuth, distance = geod.inv(lon1, lat1, lon2, lat2)
print(f"Distance: {distance:.2f} meters")
```

#### Point Displacement (Accurate):
```python
from pyproj import Geod

geod = Geod(ellps="WGS84")

# Junction coordinates
lon, lat = -0.1278, 51.5074

# Direction (azimuth) - e.g., 90° = East, 0° = North
azimuth = 90  # degrees

# Distance to displace (in meters)
distance = 100  # 100 meters

# Calculate new point
lon2, lat2, back_azimuth = geod.fwd(lon, lat, azimuth, distance)
print(f"New point: {lon2}, {lat2}")
```

### Critical for Our Project:
```python
from pyproj import Geod
import math

def create_hazard_point(junction_lon, junction_lat, road_direction_deg, offset_meters=100):
    """
    Create hazard point offset from junction along secondary road
    
    Args:
        junction_lon: Junction longitude
        junction_lat: Junction latitude
        road_direction_deg: Direction of secondary road (0-360°, 0=North)
        offset_meters: Distance to offset (default 100m)
    
    Returns:
        tuple: (hazard_lon, hazard_lat)
    """
    geod = Geod(ellps="WGS84")
    
    # Calculate point offset along road direction
    hazard_lon, hazard_lat, _ = geod.fwd(
        junction_lon, 
        junction_lat, 
        road_direction_deg, 
        offset_meters
    )
    
    return hazard_lon, hazard_lat

# Example usage
hazard_lon, hazard_lat = create_hazard_point(-0.1278, 51.5074, 90, 100)
```

---

## 5. Additional Libraries

### NetworkX (Included with OSMnx)
- Graph analysis
- Already used by OSMnx internally
- Useful for path finding and network analysis

### Folium (For Interactive Visualization)
```bash
pip install folium
```

```python
import folium

# Create map centered on location
m = folium.Map(location=[51.5074, -0.1278], zoom_start=13)

# Add markers
folium.Marker(
    [51.5074, -0.1278],
    popup="Dangerous Junction",
    icon=folium.Icon(color='red', icon='warning-sign')
).add_to(m)

# Save to HTML
m.save("map.html")
```

### Matplotlib (For Static Plots)
```bash
pip install matplotlib
```

### Contextily (For Basemaps)
```bash
pip install contextily
```

---

## Installation Commands Summary

```bash
# Create virtual environment
python -m venv venv

# Activate (Windows)
venv\Scripts\activate

# Activate (Mac/Linux)
source venv/bin/activate

# Install all libraries
pip install osmnx shapely geopandas pyproj folium matplotlib contextily

# Alternatively, create requirements.txt:
# osmnx>=1.9.0
# shapely>=2.0.0
# geopandas>=0.14.0
# pyproj>=3.6.0
# folium>=0.15.0
# matplotlib>=3.8.0
# contextily>=1.5.0

# Then install from requirements.txt
pip install -r requirements.txt
```

---

## Common Issues and Solutions

### Issue 1: GDAL/Fiona Installation on Windows
**Solution:** Use conda instead of pip:
```bash
conda install -c conda-forge osmnx geopandas
```

### Issue 2: Shapely Performance
**Solution:** Ensure you have a recent version with C speedups:
```bash
pip install shapely --upgrade
```

### Issue 3: CRS Warnings
**Solution:** Always specify CRS explicitly:
```python
gdf = gpd.GeoDataFrame(data, crs="EPSG:4326")
```

---

## Next Steps (Week 2)

1. Install all libraries in virtual environment
2. Test each library with simple examples
3. Download sample OSM data using OSMnx
4. Extract junctions using GeoPandas
5. Calculate accurate distances using PyProj
6. Create sample hazard point dataset

---

**Status:** Week 1 Research  
**Last Updated:** January 1, 2026
