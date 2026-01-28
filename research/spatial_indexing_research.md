# Spatial Indexing and BSP Trees Research
**Week 4 - January 20-26, 2026**

---

## Executive Summary

For efficient proximity detection in the mobile app, we need to quickly find hazard points near the user's current location. Linear search through thousands of points would be too slow. This document researches spatial indexing techniques, with focus on R-trees as the recommended solution.

---

## 1. The Problem

### 1.1 Scenario
- Mobile app has 5,000-50,000 hazard points loaded in memory
- User's GPS updates every 1-5 seconds
- Need to find all hazards within 500m of user
- Must complete query in < 100ms for smooth performance

### 1.2 Naive Approach (Linear Search)
```python
def find_nearby_hazards_naive(user_lat, user_lon, hazards, radius_m=500):
    nearby = []
    for hazard in hazards:
        distance = calculate_distance(user_lat, user_lon, 
                                     hazard.lat, hazard.lon)
        if distance <= radius_m:
            nearby.append(hazard)
    return nearby
```

**Time Complexity:** O(n) where n = number of hazards

**Performance:**
- 10,000 points: ~50-100ms (borderline acceptable)
- 50,000 points: ~250-500ms (too slow!)

**Conclusion:** Naive approach does not scale. Need spatial index.

---

## 2. Spatial Indexing Techniques

### 2.1 Overview

Spatial index: Data structure that organizes geometric objects for efficient spatial queries.

**Common Types:**
1. **R-tree** - Most popular for geographic data
2. **QuadTree** - Divides space into quadrants
3. **K-d Tree** - Binary space partition for points
4. **Grid Index** - Simple grid cells

---

## 3. R-Tree (Recommended)

### 3.1 What is an R-tree?

R-tree (Rectangle tree) organizes spatial objects by grouping nearby objects and representing groups with minimum bounding rectangles (MBRs).

### 3.2 Structure

```
                    [Root MBR]
                   /          \
              [MBR A]        [MBR B]
             /    |   \       /    \
        [MBR1] [MBR2] [MBR3] [MBR4] [MBR5]
         /  \    /  \
      Points  Points
```

Each node contains:
- Bounding rectangle (min/max lat/lon)
- Child nodes or actual point data

### 3.3 Query Process

To find points within radius of (lat, lon):
1. Start at root
2. Check if query circle intersects node's MBR
3. If no: skip entire subtree
4. If yes: recurse into children
5. At leaf nodes, check actual points

### 3.4 Time Complexity

- **Query:** O(log n) average case
- **Insert:** O(log n)
- **Build:** O(n log n)

### 3.5 Advantages ✅
- Industry standard for geospatial data
- Excellent query performance
- Handles point, line, and polygon queries
- Well-tested implementations available
- Dynamic (supports insert/delete)

### 3.6 Disadvantages ❌
- More complex than simpler structures
- Memory overhead
- Build time required

### 3.7 Implementation Options

#### Python (Data Processing):
```python
from rtree import index

# Create R-tree index
idx = index.Index()

# Insert points (id, (minx, miny, maxx, maxy))
for i, point in enumerate(hazards):
    idx.insert(i, (point.lon, point.lat, point.lon, point.lat))

# Query within bounding box
bbox = (min_lon, min_lat, max_lon, max_lat)
nearby_ids = list(idx.intersection(bbox))
```

#### JavaScript/React Native:
```javascript
// Use rbush library
import RBush from 'rbush';

const tree = new RBush();

// Insert points
hazards.forEach(h => {
  tree.insert({
    minX: h.lon,
    minY: h.lat,
    maxX: h.lon,
    maxY: h.lat,
    data: h
  });
});

// Query
const bbox = {
  minX: userLon - 0.005,
  minY: userLat - 0.005,
  maxX: userLon + 0.005,
  maxY: userLat + 0.005
};
const results = tree.search(bbox);
```

---

## 4. QuadTree

### 4.1 What is a QuadTree?

Recursively divides 2D space into four quadrants. Each node has 4 children (NW, NE, SW, SE).

### 4.2 Structure

```
        ┌─────────────┐
        │             │
        │     NW  NE  │
        │      +      │
        │     SW  SE  │
        │             │
        └─────────────┘
```

Each quadrant subdivides when it contains too many points (e.g., > 4).

### 4.3 Time Complexity

- **Query:** O(log n) average, O(n) worst case
- **Insert:** O(log n)
- **Build:** O(n log n)

### 4.4 Advantages ✅
- Simple conceptual model
- Good for uniformly distributed data
- Easy to implement
- Works well for 2D point data

### 4.5 Disadvantages ❌
- Performance degrades with clustered data
- Fixed spatial decomposition (not adaptive)
- Not as efficient as R-tree for geographic data
- Worse for non-point geometries

### 4.6 When to Use
- Simple projects
- Educational purposes
- Uniformly distributed points
- If R-tree library not available

---

## 5. K-d Tree

### 5.1 What is a K-d Tree?

Binary space partitioning tree. Alternates splitting on x and y axes.

### 5.2 Structure

```
           Split on X
          /           \
    Split on Y     Split on Y
     /    \         /    \
  Points Points  Points Points
```

### 5.3 Time Complexity

- **Query:** O(log n) average, O(√n) worst case
- **Build:** O(n log n)

### 5.4 Advantages ✅
- Simple algorithm
- Good for k-nearest neighbor queries
- Included in SciPy (easy to use in Python)

### 5.5 Disadvantages ❌
- Not balanced (can degrade to O(n))
- Static (difficult to insert/delete)
- Less efficient than R-tree for range queries
- Not designed for geographic data

### 5.6 Python Example

```python
from scipy.spatial import KDTree

# Build tree
points = np.array([[lat1, lon1], [lat2, lon2], ...])
tree = KDTree(points)

# Query k nearest neighbors
distances, indices = tree.query([user_lat, user_lon], k=10)

# Query within radius
indices = tree.query_ball_point([user_lat, user_lon], r=0.005)
```

---

## 6. Grid Index

### 6.1 What is a Grid Index?

Divides space into fixed grid cells. Each cell contains list of points within it.

### 6.2 Structure

```
┌───┬───┬───┬───┐
│ 0 │ 1 │ 2 │ 3 │
├───┼───┼───┼───┤
│ 4 │ 5 │ 6 │ 7 │
├───┼───┼───┼───┤
│ 8 │ 9 │10 │11 │
├───┼───┼───┼───┤
│12 │13 │14 │15 │
└───┴───┴───┴───┘
```

### 6.3 Time Complexity

- **Query:** O(1) average case
- **Insert:** O(1)
- **Build:** O(n)

### 6.4 Advantages ✅
- Extremely simple to implement
- Very fast queries if cells sized correctly
- Minimal memory overhead
- O(1) average case

### 6.5 Disadvantages ❌
- Requires choosing appropriate cell size
- Poor performance if data clustered in few cells
- Not adaptive to data distribution
- Edge cases (points on boundaries)

### 6.6 Implementation

```javascript
class GridIndex {
  constructor(bounds, cellSize) {
    this.cellSize = cellSize;
    this.grid = new Map();
  }
  
  getCellKey(lat, lon) {
    const x = Math.floor(lon / this.cellSize);
    const y = Math.floor(lat / this.cellSize);
    return `${x},${y}`;
  }
  
  insert(point) {
    const key = this.getCellKey(point.lat, point.lon);
    if (!this.grid.has(key)) {
      this.grid.set(key, []);
    }
    this.grid.get(key).push(point);
  }
  
  query(lat, lon, radius) {
    // Check current cell and neighbors
    const results = [];
    const cellRadius = Math.ceil(radius / this.cellSize);
    
    for (let dx = -cellRadius; dx <= cellRadius; dx++) {
      for (let dy = -cellRadius; dy <= cellRadius; dy++) {
        const key = this.getCellKey(lat + dy*this.cellSize, 
                                    lon + dx*this.cellSize);
        if (this.grid.has(key)) {
          results.push(...this.grid.get(key));
        }
      }
    }
    return results;
  }
}
```

### 6.7 When to Use
- Quick prototyping
- Small datasets
- Uniformly distributed data
- Simple is acceptable

---

## 7. Binary Space Partition (BSP) Tree

### 7.1 What is a BSP Tree?

Recursively divides space using arbitrary planes (not just axis-aligned). More commonly used in 3D graphics than geographic applications.

### 7.2 Relevance to Our Project

**Not recommended** for this project because:
- Overly complex for 2D point queries
- Designed for 3D rendering problems
- R-tree is better suited for geographic data

**Conclusion:** BSP trees are not the right choice for our use case.

---

## 8. Comparison Matrix

| Feature | R-tree | QuadTree | K-d Tree | Grid Index |
|---------|--------|----------|----------|------------|
| **Query Speed** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Build Speed** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Memory** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Simplicity** | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Dynamic** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Geo-optimized** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |

---

## 9. Recommendation

### 9.1 Primary Choice: R-tree

**For our project, use R-tree because:**
1. ✅ Industry standard for geospatial queries
2. ✅ Best performance for range queries
3. ✅ Well-tested libraries available
4. ✅ Handles varying data densities well
5. ✅ Suitable for mobile devices

### 9.2 Implementation Libraries

#### Python (Data Processing):
```bash
pip install rtree
```

#### JavaScript/React Native:
```bash
npm install rbush
```

### 9.3 Fallback: Grid Index

If R-tree proves too complex or has issues:
- Implement simple grid index
- Cell size: ~0.01° (roughly 1km)
- Good enough for < 10,000 points
- Much simpler code

---

## 10. Implementation Plan

### 10.1 Week 14 (Mar 31 - Apr 6): Implementation

#### Step 1: Benchmark Naive Approach
```python
import time

# Test with 10,000 points
start = time.time()
nearby = find_nearby_naive(user_lat, user_lon, hazards)
elapsed = time.time() - start
print(f"Naive search: {elapsed*1000:.2f} ms")
```

#### Step 2: Implement R-tree
```python
from rtree import index

# Build index
idx = index.Index()
for i, h in enumerate(hazards):
    idx.insert(i, (h.lon, h.lat, h.lon, h.lat))

# Query
start = time.time()
# Create bounding box (500m = ~0.0045 degrees)
bbox = (user_lon - 0.0045, user_lat - 0.0045,
        user_lon + 0.0045, user_lat + 0.0045)
nearby_ids = list(idx.intersection(bbox))
elapsed = time.time() - start
print(f"R-tree search: {elapsed*1000:.2f} ms")
```

#### Step 3: Benchmark and Compare

Expected results:
- Naive (10k points): 50-100ms
- R-tree (10k points): 1-5ms
- Speedup: 10-100x

#### Step 4: Integrate into Mobile App
```javascript
import RBush from 'rbush';

class HazardManager {
  constructor() {
    this.tree = new RBush();
    this.hazards = [];
  }
  
  loadHazards(geojson) {
    this.hazards = geojson.features.map(f => ({
      lon: f.geometry.coordinates[0],
      lat: f.geometry.coordinates[1],
      ...f.properties
    }));
    
    // Build R-tree
    this.hazards.forEach(h => {
      this.tree.insert({
        minX: h.lon,
        minY: h.lat,
        maxX: h.lon,
        maxY: h.lat,
        data: h
      });
    });
  }
  
  getNearbyHazards(userLat, userLon, radiusM = 500) {
    // Convert radius to degrees (rough approximation)
    const radiusDeg = radiusM / 111000; // 1° ≈ 111km
    
    const bbox = {
      minX: userLon - radiusDeg,
      minY: userLat - radiusDeg,
      maxX: userLon + radiusDeg,
      maxY: userLat + radiusDeg
    };
    
    const candidates = this.tree.search(bbox);
    
    // Filter by exact distance
    return candidates.filter(c => {
      const dist = this.calculateDistance(
        userLat, userLon, 
        c.data.lat, c.data.lon
      );
      return dist <= radiusM;
    }).map(c => c.data);
  }
  
  calculateDistance(lat1, lon1, lat2, lon2) {
    // Haversine formula
    const R = 6371000; // Earth radius in meters
    const dLat = (lat2 - lat1) * Math.PI / 180;
    const dLon = (lon2 - lon1) * Math.PI / 180;
    const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
              Math.cos(lat1 * Math.PI / 180) * 
              Math.cos(lat2 * Math.PI / 180) *
              Math.sin(dLon/2) * Math.sin(dLon/2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
    return R * c;
  }
}
```

---

## 11. Performance Targets

### 11.1 Query Performance

| Dataset Size | Naive | R-tree | Target |
|--------------|-------|--------|--------|
| 1,000 points | 5ms | <1ms | ✅ |
| 10,000 points | 50ms | <5ms | ✅ |
| 50,000 points | 250ms | <10ms | ✅ |
| 100,000 points | 500ms | <20ms | ✅ |

### 11.2 Memory Usage

- R-tree overhead: ~20-30% of point data
- 10,000 points ≈ 2MB data + 0.5MB index = 2.5MB total
- Acceptable for mobile devices

### 11.3 Build Time

- One-time cost when loading data
- 10,000 points: ~100ms
- Acceptable on app startup

---

## 12. Alternative: Simplified Approach

If R-tree proves problematic, use this simplified grid approach:

```javascript
class SimpleGridIndex {
  constructor() {
    this.gridSize = 0.01; // ~1km cells
    this.grid = new Map();
  }
  
  insert(hazard) {
    const key = this.getGridKey(hazard.lat, hazard.lon);
    if (!this.grid.has(key)) {
      this.grid.set(key, []);
    }
    this.grid.get(key).push(hazard);
  }
  
  getGridKey(lat, lon) {
    const x = Math.floor(lon / this.gridSize);
    const y = Math.floor(lat / this.gridSize);
    return `${x},${y}`;
  }
  
  query(lat, lon, radius = 500) {
    const results = [];
    // Check 3x3 grid around user
    for (let dx = -1; dx <= 1; dx++) {
      for (let dy = -1; dy <= 1; dy++) {
        const key = this.getGridKey(
          lat + dy * this.gridSize,
          lon + dx * this.gridSize
        );
        if (this.grid.has(key)) {
          results.push(...this.grid.get(key));
        }
      }
    }
    return results;
  }
}
```

---

## 13. Conclusion

### 13.1 Decision

**Use R-tree (rbush library for React Native)**

### 13.2 Rationale

1. Best performance for geospatial queries
2. Well-tested library available
3. Industry standard approach
4. Suitable for mobile performance requirements
5. Handles our expected dataset sizes efficiently

### 13.3 Next Steps (Week 14)

1. Implement naive approach first (Week 11-12)
2. Measure performance issues (Week 13)
3. Implement R-tree optimization (Week 14)
4. Benchmark improvements
5. Document findings in final report

---

## References

- Guttman, A. (1984). "R-trees: A Dynamic Index Structure for Spatial Searching"
- rbush JavaScript library: https://github.com/mourner/rbush
- rtree Python library: https://rtree.readthedocs.io/
- Spatial Database Systems (Rigaux, Scholl, Voisard)

---

**Document Status:** Complete  
**Week:** 4 (January 20-26, 2026)  
**Last Updated:** January 21, 2026
