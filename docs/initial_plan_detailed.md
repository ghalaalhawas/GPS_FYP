# Initial Plan Document
## GPS Mobile App to Help Driving Safety Abroad

**Student Name:** Ghala Albarazi  
**Supervisor:** Crispin Cooper  
**Course:** Final Year Project  
**Submission Date:** February 2, 2026

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Background and Motivation](#2-background-and-motivation)
3. [Project Objectives](#3-project-objectives)
4. [Literature Review](#4-literature-review)
5. [Methodology](#5-methodology)
6. [System Architecture](#6-system-architecture)
7. [Technology Stack](#7-technology-stack)
8. [Implementation Plan](#8-implementation-plan)
9. [Evaluation Strategy](#9-evaluation-strategy)
10. [Risk Assessment](#10-risk-assessment)
11. [Timeline and Milestones](#11-timeline-and-milestones)
12. [References](#12-references)

---

## 1. Introduction

### 1.1 Project Overview

This project aims to develop a mobile application that enhances driving safety for people driving in unfamiliar regions, particularly when driving abroad. The app will warn drivers about potentially dangerous road junctions before they approach them, giving drivers time to slow down and prepare for challenging navigation scenarios.

### 1.2 Problem Statement

Drivers face significant challenges when navigating unfamiliar road networks:

- **Lack of Local Knowledge:** Dangerous junctions that locals know to approach with caution are unknown to visitors
- **Navigation Distraction:** GPS navigation apps focus on routing but not on warning about hazardous road configurations
- **Different Road Standards:** Road design standards vary between countries, creating unexpected scenarios
- **Critical Decision Points:** T-junctions where minor roads meet major roads with speed differentials can be particularly dangerous

### 1.3 Proposed Solution

A mobile application that:
1. Uses OpenStreetMap data to identify potentially dangerous junctions
2. Tracks the user's GPS location in real-time
3. Provides timely audio and visual warnings when approaching hazardous junctions
4. Works offline with pre-downloaded hazard data for the region
5. Considers direction of approach to provide context-aware warnings

### 1.4 Success Criteria

The project will be considered successful if:
- ✅ A functional prototype app is developed and tested
- ✅ Dangerous junctions are identified using OSM data with reasonable accuracy
- ✅ Warning system provides timely alerts (200-300m before junction)
- ✅ False positive rate is acceptably low (< 20% in testing)
- ✅ App works offline with acceptable performance on mobile devices
- ✅ User testing provides positive feedback on usefulness and usability

---

## 2. Background and Motivation

### 2.1 Road Safety Statistics

Road traffic accidents are a significant cause of injury and death globally. Many accidents occur at junctions, particularly where:
- Minor roads meet major roads
- Visibility is limited
- Speed differentials are high
- Road geometry creates challenging angles

### 2.2 Challenges for International Drivers

Drivers in unfamiliar territories face:
- **Unfamiliar Road Rules:** Different priority rules and signage
- **Different Road Design:** Varying standards for junction design
- **Unknown Hazards:** No local knowledge of "notorious" dangerous spots
- **Navigation Cognitive Load:** Focus on following directions reduces hazard awareness

### 2.3 Current Solutions and Gaps

**Existing navigation apps (Google Maps, Waze, Apple Maps):**
- ✅ Provide routing and turn-by-turn navigation
- ✅ Show traffic conditions and accidents
- ❌ Do not warn about inherently dangerous junction configurations
- ❌ Do not consider approach direction and speed differential

**This project fills the gap** by:
- Proactively identifying dangerous junctions based on road geometry and classification
- Providing advance warning regardless of whether an accident has recently occurred
- Considering the approach direction and road characteristics

---

## 3. Project Objectives

### 3.1 Primary Objectives

1. **Data Processing System**
   - Extract and process OpenStreetMap data for target regions
   - Identify road junctions programmatically
   - Classify junctions by danger level based on defined criteria
   - Generate hazard point dataset displaced along approach roads

2. **Mobile Application**
   - Develop cross-platform mobile app (iOS and/or Android)
   - Implement accurate GPS location tracking
   - Display user location on map with hazard points
   - Provide timely warnings when approaching dangerous junctions
   - Support offline operation with cached data

3. **Warning System**
   - Trigger warnings at appropriate distance (200-300m)
   - Provide both visual and audio alerts
   - Implement directional awareness (warn from correct approach angle)
   - Prevent alert spam with appropriate cooldown logic

### 3.2 Secondary Objectives

- Optimize spatial queries for mobile performance
- Implement user preferences and settings
- Create visually appealing and intuitive UI
- Support multiple regions with downloadable datasets

### 3.3 Research Questions

1. **What characteristics of road junctions correlate with danger?**
   - Junction geometry (T-junctions, acute angles, offset crossroads)
   - Speed differentials between intersecting roads
   - Road classification mismatches
   - Visibility factors

2. **How can we effectively identify these junctions from OSM data?**
   - What OSM attributes are most reliable?
   - How can we handle missing or incomplete data?
   - What algorithms work best for junction classification?

3. **How can we provide warnings that are helpful without being annoying?**
   - What is the optimal warning distance?
   - How should warnings be presented (audio, visual, haptic)?
   - How to minimize false positives while maintaining sensitivity?

---

## 4. Literature Review

### 4.1 Road Junction Safety Research

**Factors contributing to junction accidents:**
- Limited visibility due to obstructions or geometry
- Speed differentials between roads
- Driver expectation violations
- Complex junction configurations (5+ roads)
- T-junctions from minor to major roads

**References:**
- Research on T-junction safety and speed differentials
- Studies on junction geometry and accident rates
- Analysis of rural vs urban junction characteristics

### 4.2 OpenStreetMap and Geospatial Analysis

**OSM for Transportation Research:**
- OSM data quality and completeness studies
- Use of OSM for routing and navigation
- OSM tagging standards for roads and junctions

**Key Literature:**
- Boeing, G. (2017). "OSMnx: New methods for acquiring, constructing, analyzing, and visualizing complex street networks." *Computers, Environment and Urban Systems*
- OSM Wiki documentation on highway tagging and junction classification

### 4.3 Mobile Warning Systems

**Existing warning system research:**
- Driver distraction studies
- Effective warning timing and presentation
- Context-aware mobile systems
- Location-based services on mobile devices

### 4.4 Spatial Indexing and Performance

**Spatial data structures:**
- R-trees for geographic data
- QuadTrees for 2D spatial indexing
- Performance optimization for mobile devices

---

## 5. Methodology

### 5.1 Data Processing Methodology

#### Phase 1: Data Acquisition
- Download OSM data for target regions using OSMnx
- Focus initially on UK (Oxford area for testing)
- Extract road network as graph structure

#### Phase 2: Junction Identification
- Identify nodes where 3+ roads meet
- Extract road attributes (highway type, maxspeed, lanes, etc.)
- Calculate junction geometry (angles between roads)

#### Phase 3: Danger Classification
**Scoring algorithm based on:**

1. **Junction Type (30% weight)**
   - T-junctions: High risk (especially minor-to-major)
   - Crossroads: Medium risk
   - 5+ way junctions: High risk (complexity)
   - Roundabouts: Low risk (controlled)

2. **Speed Differential (25% weight)**
   - Calculate difference in maxspeed tags
   - Large differential (>20mph) = higher risk
   - Missing maxspeed inferred from road classification

3. **Road Classification Mismatch (25% weight)**
   - Primary/trunk meeting residential: High risk
   - Secondary meeting tertiary: Medium risk
   - Similar classifications: Lower risk

4. **Junction Geometry (20% weight)**
   - Acute angles (<45°): Higher risk
   - Obtuse angles (>135°): Higher risk
   - Right angles (~90°): Lower risk

5. **Additional Factors**
   - Presence of traffic signals: Reduces risk
   - Roundabout: Significantly reduces risk
   - Rural vs urban context

#### Phase 4: Hazard Point Generation
- For high-risk junctions, create hazard warning points
- Displace point 50-100m up the secondary (lower classification) road
- Store: junction location, hazard point location, approach angle, danger score
- Export as GeoJSON format

### 5.2 Mobile App Development Methodology

#### Phase 1: Framework Selection and Setup
- Choose between React Native, Flutter, or Native iOS
- Set up development environment
- Create basic app structure

#### Phase 2: Core Functionality
- Implement GPS location tracking
- Create map view showing user location
- Load and parse GeoJSON hazard data
- Implement spatial proximity detection

#### Phase 3: Warning System
- Detect when user is within threshold distance of hazard
- Check if approaching from correct direction
- Trigger visual and audio warning
- Implement cooldown to prevent spam

#### Phase 4: Optimization
- Implement spatial caching (load only nearby points)
- Optimize for battery usage
- Test and refine thresholds

### 5.3 Agile Development Approach

- **Weekly iterations** following project plan
- **Incremental development:** Build feature by feature
- **Regular testing:** Test after each major feature
- **Documentation:** Document as we go, not at the end
- **Flexibility:** Adjust approach based on findings

---

## 6. System Architecture

### 6.1 Overall System Design

```
┌─────────────────────────────────────────────────────────┐
│                   OpenStreetMap Data                     │
│              (Road network, junction data)               │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Data Processing Pipeline                    │
│                  (Python Scripts)                        │
│                                                          │
│  • Download OSM data (OSMnx)                            │
│  • Identify junctions                                    │
│  • Calculate danger scores                               │
│  • Generate hazard points                                │
│  • Export GeoJSON                                        │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Hazard Point Dataset                        │
│                  (GeoJSON files)                         │
│                                                          │
│  One file per region:                                    │
│  • Junction location                                     │
│  • Hazard point location (displaced)                     │
│  • Danger score                                          │
│  • Approach angle                                        │
│  • Metadata                                              │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                  Mobile Application                      │
│                                                          │
│  ┌────────────────────────────────────────────────┐    │
│  │         User Interface Layer                    │    │
│  │  • Map view with user location                  │    │
│  │  • Warning overlay (visual alert)               │    │
│  │  • Settings and preferences                     │    │
│  └────────────────────┬───────────────────────────┘    │
│                       │                                  │
│  ┌────────────────────▼───────────────────────────┐    │
│  │       Business Logic Layer                      │    │
│  │  • GPS location tracking                        │    │
│  │  • Proximity detection                          │    │
│  │  • Warning trigger logic                        │    │
│  │  • Direction calculation                        │    │
│  └────────────────────┬───────────────────────────┘    │
│                       │                                  │
│  ┌────────────────────▼───────────────────────────┐    │
│  │         Data Layer                              │    │
│  │  • Load GeoJSON data                            │    │
│  │  • Spatial indexing (R-tree/QuadTree)          │    │
│  │  • Local caching                                │    │
│  │  • User preferences storage                     │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### 6.2 Data Processing Architecture

**Input:** OpenStreetMap PBF/XML files  
**Processing:** Python scripts using osmnx, geopandas, shapely  
**Output:** GeoJSON files for mobile app

**Key Components:**
1. **OSM Data Downloader:** Downloads regional street networks
2. **Junction Detector:** Identifies multi-way intersections
3. **Danger Classifier:** Scores junctions based on criteria
4. **Hazard Generator:** Creates displaced warning points
5. **Data Exporter:** Outputs optimized GeoJSON

### 6.3 Mobile App Architecture

**Platform:** Cross-platform (React Native recommended) or Native iOS

**Key Components:**
1. **Location Service:** High-accuracy GPS tracking
2. **Map Component:** Visual display of user and hazards
3. **Spatial Query Engine:** Efficient nearest-neighbor search
4. **Warning Manager:** Alert triggering and cooldown logic
5. **Data Manager:** Loading and caching hazard data
6. **Settings Manager:** User preferences

---

## 7. Technology Stack

### 7.1 Data Processing Stack

**Language:** Python 3.9+

**Core Libraries:**
- **OSMnx 1.9+:** OpenStreetMap data download and network analysis
- **GeoPandas 0.14+:** Geospatial dataframes and GIS operations
- **Shapely 2.0+:** Geometric operations and calculations
- **PyProj 3.6+:** Coordinate transformations and distance calculations
- **Pandas:** Data manipulation
- **Matplotlib/Folium:** Visualization

**Development Environment:**
- Jupyter notebooks for exploration
- Python scripts for production processing
- Git for version control

### 7.2 Mobile App Stack

**Recommended: React Native**

**Rationale:**
- Cross-platform (iOS + Android from single codebase)
- JavaScript/TypeScript - widely accessible
- Excellent mapping library support
- Good GPS/location APIs
- Large community and resources

**Key Libraries:**
- **React Native Maps:** Native map views
- **Expo Location** or **@react-native-community/geolocation:** GPS
- **AsyncStorage:** Local data storage
- **React Navigation:** App navigation

**Alternative: Flutter** (if better performance needed)

**Alternative: Native iOS (Swift)** (if focusing on iOS only)

### 7.3 Data Format

**GeoJSON** selected for hazard point storage:
- Human-readable JSON format
- Native support in mapping libraries
- Widely compatible
- Reasonable file sizes

**Structure:**
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [longitude, latitude]
      },
      "properties": {
        "id": "hazard_001",
        "junction_id": "osm_12345",
        "danger_score": 0.85,
        "junction_type": "T-junction",
        "approach_angle": 270,
        "road_primary": "A40",
        "road_secondary": "B4150",
        "speed_primary": "60 mph",
        "speed_secondary": "30 mph"
      }
    }
  ]
}
```

---

## 8. Implementation Plan

### 8.1 Phase 1: Data Processing (Weeks 2-9)

**Week 2 (Jan 6-12): Environment Setup** ✅ COMPLETED
- Set up Python environment with required libraries
- Download sample OSM data (Oxford, UK)
- Create initial visualization
- Set up Git repository

**Week 6 (Feb 3-9): Junction Identification**
- Implement script to extract all junctions
- Classify by type (T, crossroads, 5-way, etc.)
- Visualize junction distribution

**Week 7 (Feb 10-16): Danger Scoring Algorithm**
- Implement danger score calculation
- Test with multiple regions
- Refine criteria based on findings

**Week 9 (Feb 24-Mar 2): Data Export**
- Generate GeoJSON datasets
- Optimize file size
- Create datasets for multiple test regions

### 8.2 Phase 2: Mobile App Development (Weeks 2-13)

**Week 2-4: Framework Selection and Setup** ✅ IN PROGRESS
- Choose mobile framework
- Set up development environment
- Create project structure

**Week 5 (Jan 27-Feb 2): Basic GPS Functionality**
- Implement GPS location tracking
- Create "Hello World" app with map view

**Week 8 (Feb 17-23): Map and Location**
- Full map view implementation
- User location tracking
- Test on device

**Week 10 (Mar 3-9): Data Integration**
- Load GeoJSON hazard data
- Display hazard points on map

**Week 11 (Mar 10-16): Proximity Detection**
- Implement distance calculations
- Test accuracy and performance

**Week 12 (Mar 17-23): Warning System**
- Visual and audio warnings
- Warning cooldown logic

**Week 13 (Mar 24-30): Offline Caching**
- Implement local storage
- Background data refresh

### 8.3 Phase 3: Optimization (Weeks 14-17)

**Week 14 (Mar 31-Apr 6): Spatial Indexing**
- Implement R-tree or QuadTree
- Performance benchmarking

**Week 15 (Apr 7-13): Direction Detection**
- Detect approach direction
- Context-aware warnings

**Week 16 (Apr 14-20): Enhanced Dataset**
- Expand to larger regions
- Refine danger criteria

**Week 17 (Apr 21-27): Testing and Refinement**
- Real-world testing
- Bug fixes and improvements

### 8.4 Phase 4: Evaluation and Documentation (Weeks 18-28)

**Weeks 18-21 (Apr 28-May 25): Evaluation**
- User testing
- Data collection
- Analysis and refinement

**Weeks 22-28 (May 26-Jul 6): Report Writing**
- Draft all chapters
- Create figures and diagrams
- Final proofreading and submission

---

## 9. Evaluation Strategy

### 9.1 Evaluation Objectives

1. **Assess accuracy of dangerous junction identification**
2. **Measure effectiveness of warning system**
3. **Evaluate user experience and usability**
4. **Measure system performance**

### 9.2 Evaluation Methods

#### 9.2.1 Quantitative Evaluation

**Junction Identification Accuracy:**
- Compare identified junctions against known dangerous locations
- Calculate precision and recall
- Measure false positive and false negative rates

**Warning System Performance:**
- Measure warning distance accuracy (should be 200-300m)
- Count false positives (warnings where not needed)
- Measure warning timing consistency
- GPS accuracy analysis

**System Performance:**
- Measure spatial query response times
- Battery usage monitoring
- Memory consumption analysis
- App startup time

**Metrics:**
```
Accuracy = (True Positives + True Negatives) / Total
Precision = True Positives / (True Positives + False Positives)
Recall = True Positives / (True Positives + False Negatives)
F1 Score = 2 × (Precision × Recall) / (Precision + Recall)
```

#### 9.2.2 Qualitative Evaluation

**User Testing (n=10-15 participants):**
- Simulated driving tests with app
- Think-aloud protocols
- Post-test interviews
- Usability questionnaires (SUS - System Usability Scale)

**Questions to explore:**
- Is the warning timing appropriate?
- Are warnings helpful or distracting?
- Is the visual design clear and intuitive?
- Would users actually use this app while driving?
- What improvements would users suggest?

**Data Collection Methods:**
- Structured questionnaires
- Semi-structured interviews
- Screen recordings
- Usage logs from app

### 9.3 Evaluation Criteria

**Success Metrics:**

| Criterion | Target | Measurement |
|-----------|--------|-------------|
| Junction identification accuracy | > 70% | Compare with known dangerous junctions |
| False positive rate | < 20% | User testing feedback |
| Warning timing accuracy | 200-300m ± 50m | GPS logging |
| System response time | < 100ms | Performance profiling |
| User satisfaction (SUS) | > 68 (average) | Questionnaire |
| Battery usage | < 10% per hour | Device monitoring |

### 9.4 Validation Approach

**Technical Validation:**
1. Unit tests for key algorithms
2. Integration tests for app components
3. Performance tests on various devices
4. Accuracy tests with GPS simulation

**User Validation:**
1. Expert review (driving instructors, road safety experts)
2. Pilot testing with small group
3. Full user study with diverse participants
4. Iterative refinement based on feedback

---

## 10. Risk Assessment

### 10.1 Technical Risks

**Risk 1: OSM Data Quality Issues**
- **Description:** Incomplete or inaccurate OSM data in some regions
- **Impact:** Medium - May miss junctions or misclassify them
- **Probability:** Medium
- **Mitigation:**
  - Focus on well-mapped regions (UK, Western Europe)
  - Implement data quality checks
  - Handle missing attributes gracefully
  - Document limitations

**Risk 2: GPS Accuracy Limitations**
- **Description:** GPS may be inaccurate in urban canyons, tunnels
- **Impact:** High - Warnings may be mistimed
- **Probability:** High in certain environments
- **Mitigation:**
  - Use high-accuracy GPS mode
  - Implement sensor fusion if possible
  - Add buffer zones to warning thresholds
  - Document limitations clearly

**Risk 3: Mobile Development Complexity**
- **Description:** Unfamiliarity with mobile development may slow progress
- **Impact:** High - Could delay timeline
- **Probability:** Medium
- **Mitigation:**
  - Start early (Week 2-4)
  - Use cross-platform framework (React Native)
  - Seek help from online resources/communities
  - Simplify features if needed

**Risk 4: Performance on Mobile Devices**
- **Description:** Spatial queries may be slow, battery drain
- **Impact:** Medium - App may be unusable
- **Probability:** Medium
- **Mitigation:**
  - Implement spatial indexing early (Week 14)
  - Optimize data structures
  - Test on real devices regularly
  - Profile and optimize code

**Risk 5: Data Processing Complexity**
- **Description:** Junction danger scoring may be complex
- **Impact:** Medium - May need simplified algorithm
- **Probability:** Low
- **Mitigation:**
  - Start with simple heuristics
  - Iterate and refine
  - Use existing research on junction safety
  - Consult supervisor

### 10.2 Project Management Risks

**Risk 6: Time Management**
- **Description:** Underestimating task complexity
- **Impact:** High - May miss deadlines
- **Probability:** Medium
- **Mitigation:**
  - Follow weekly plan strictly
  - Build in buffer time
  - Prioritize core features
  - Adjust scope if falling behind

**Risk 7: Scope Creep**
- **Description:** Adding too many features
- **Impact:** Medium - Delays core functionality
- **Probability:** Medium
- **Mitigation:**
  - Define clear MVP (Minimum Viable Product)
  - Focus on core objectives first
  - Track "nice-to-have" vs "must-have"
  - Consult supervisor on priorities

### 10.3 Risk Monitoring

- **Weekly progress tracking** against plan
- **Regular supervisor meetings** to identify issues early
- **Git commit history** as evidence of consistent work
- **Weekly reflection** in project journal

---

## 11. Timeline and Milestones

### 11.1 Overall Timeline

**Project Duration:** January 1 - July 5, 2026 (27 weeks)

### 11.2 Key Milestones

| Date | Milestone | Deliverable |
|------|-----------|-------------|
| **Feb 2, 2026** | **Initial Plan Submission** | This document |
| **Mar 2, 2026** | Data Processing Complete | Python scripts and hazard datasets |
| **Apr 6, 2026** | Core App Functionality | Working mobile app with warnings |
| **May 4, 2026** | Testing Complete | Evaluation data collected |
| **Jun 1, 2026** | Evaluation Complete | Analysis and findings documented |
| **Jul 5, 2026** | **Final Report Submission** | Complete project report |

### 11.3 Weekly Breakdown

**January 2026:**
- Week 1 (Jan 1-5): ✅ Research and technology decisions
- Week 2 (Jan 6-12): ✅ Environment setup, initial OSM exploration
- Week 3 (Jan 13-19): Draft initial plan (this document)
- Week 4 (Jan 20-26): Finalize plan, begin mobile setup
- Week 5 (Jan 27-Feb 2): Submit plan, basic OSM parser

**February 2026:**
- Week 6 (Feb 3-9): Junction identification script
- Week 7 (Feb 10-16): Danger scoring algorithm
- Week 8 (Feb 17-23): Mobile app GPS foundation
- Week 9 (Feb 24-Mar 2): Data export and format optimization

**March 2026:**
- Week 10 (Mar 3-9): Data integration in app
- Week 11 (Mar 10-16): Proximity detection
- Week 12 (Mar 17-23): Warning system
- Week 13 (Mar 24-30): Caching system

**April 2026:**
- Week 14 (Mar 31-Apr 6): Spatial optimization
- Week 15 (Apr 7-13): Direction detection
- Week 16 (Apr 14-20): Enhanced dataset
- Week 17 (Apr 21-27): Testing and refinement

**May 2026:**
- Week 18 (Apr 28-May 4): UI polish
- Week 19 (May 5-11): Evaluation design
- Week 20 (May 12-18): Evaluation execution
- Week 21 (May 19-25): Evaluation analysis
- Week 22 (May 26-Jun 1): Report planning

**June-July 2026:**
- Weeks 23-27: Report writing
- Week 28: Buffer for final adjustments

### 11.4 Critical Path

**Most Critical Tasks:**
1. Initial plan submission (Feb 2) - **NEXT IMMEDIATE DEADLINE**
2. Junction identification algorithm (Week 6-7)
3. Mobile app GPS functionality (Week 5, 8)
4. Warning system integration (Week 12)
5. User testing (Week 20)
6. Final report writing (Weeks 23-27)

---

## 12. References

### 12.1 OpenStreetMap and Geospatial

- Boeing, G. (2017). OSMnx: New methods for acquiring, constructing, analyzing, and visualizing complex street networks. *Computers, Environment and Urban Systems*, 65, 126-139.
- OpenStreetMap Wiki. (2024). Map Features. https://wiki.openstreetmap.org/wiki/Map_Features
- OpenStreetMap Wiki. (2024). Highway Tag. https://wiki.openstreetmap.org/wiki/Key:highway

### 12.2 Road Safety

- [To be added: Academic papers on junction safety]
- [To be added: Traffic accident statistics sources]
- [To be added: Road design standards]

### 12.3 Mobile Development

- React Native Documentation. https://reactnative.dev/
- Flutter Documentation. https://flutter.dev/
- Apple Developer Documentation - MapKit. https://developer.apple.com/documentation/mapkit/

### 12.4 Technical References

- Python Geospatial Development. Erik Westra. Packt Publishing.
- GeoPandas Documentation. https://geopandas.org/
- Shapely Documentation. https://shapely.readthedocs.io/

---

## Appendices

### Appendix A: Supervisor Meeting Schedule

- **Meeting 1:** Week 5 (Jan 27-Feb 2) - Discuss initial plan
- **Meeting 2:** Week 10-11 (March) - Review progress on app development
- **Meeting 3:** Week 16-17 (April) - Discuss testing strategy
- **Meeting 4:** Week 21-22 (May) - Review evaluation results
- **Meeting 5:** Week 26 (June) - Final report review

### Appendix B: Questions for Supervisor

1. Is the scope appropriate for a final year project?
2. Any recommendations on mobile framework choice?
3. Suggestions for validating danger scoring algorithm?
4. Recommendations for user testing recruitment?
5. Any specific UK regions to focus on for testing?
6. Expected level of technical detail in final report?

### Appendix C: Technology Stack Decision Document

See separate document: `docs/technology_stack_decision.md`

### Appendix D: Project Repository

GitHub: [To be added after repository creation]

---

**Document Status:** FINAL DRAFT  
**Version:** 1.0  
**Date:** January 22, 2026  
**Ready for Submission:** February 2, 2026
