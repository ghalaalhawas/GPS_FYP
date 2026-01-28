# Project Description
## GPS Mobile App to Help Driving Safety Abroad

**Student Name:** [Your Name]  
**Supervisor:** Crispin Cooper  
**Course:** Final Year Project  
**Date:** January 21, 2026

---

## Overview

Driving on a narrow lane, you're always in the middle. When the lane joins a wider road, it's important to turn onto the correct side. Paradoxically this is harder to do on an empty road, as there are no visual cues to remind the driver which side they should be on. This project proposes development of a mobile app to remind drivers as they approach situations such as these.

---

## The Challenge

Drivers face significant risks when navigating unfamiliar road networks, particularly when driving abroad. Dangerous junctions that locals know to approach with caution are completely unknown to visitors. Current GPS navigation apps focus on routing but don't warn about inherently hazardous road configurations. T-junctions where minor roads meet major roads with high speed differentials are particularly dangerous, yet drivers receive no advance warning about these challenging navigation scenarios.

The problem is compounded by:
- **Lack of Local Knowledge:** Notorious dangerous spots are unknown to international drivers
- **Navigation Distraction:** Focus on following directions reduces hazard awareness
- **Different Road Standards:** Road design varies between countries, creating unexpected scenarios
- **Critical Decision Points:** Complex junctions require quick decisions without adequate warning

---

## The Solution

This project develops a mobile application that proactively enhances driving safety by warning drivers about potentially dangerous road junctions before they reach them. The system uses OpenStreetMap data to identify hazardous junctions based on road geometry, speed differentials, and classification mismatches.

**Core Functionality:**
- Uses OpenStreetMap data to identify potentially dangerous junctions
- Tracks the user's GPS location in real-time
- Provides timely audio and visual warnings (200-300m before junction)
- Works offline with pre-downloaded hazard data for the region
- Considers direction of approach to provide context-aware warnings

---

## How It Works

### Data Processing Pipeline
The system downloads OSM road networks for target regions, identifies multi-way intersections (3+ roads meeting), and calculates danger scores based on a weighted algorithm:

1. **Junction Type (30% weight)** - T-junctions, crossroads, complex 5+ way junctions
2. **Speed Differential (25% weight)** - Large differences between intersecting roads
3. **Road Classification Mismatch (25% weight)** - Primary roads meeting residential streets
4. **Junction Geometry (20% weight)** - Acute angles, obtuse angles, visibility factors

The pipeline generates hazard warning points displaced 50-100m along the approach road (typically the minor/secondary road), storing junction location, hazard point location, approach angle, and danger score in GeoJSON format.

### Mobile Application
The mobile app implements GPS tracking with high accuracy, loads GeoJSON hazard data for the region, and uses spatial proximity detection to monitor the user's distance to hazard points. When the user approaches within 200-300m of a hazard from the correct direction, the app triggers both visual and audio warnings. The system includes intelligent cooldown logic to prevent alert spam and works entirely offline once regional data is downloaded.

---

## Technical Implementation

**Data Processing Stack:**
- Python 3.9+ with OSMnx, GeoPandas, Shapely for geospatial analysis
- OSM data processing to identify and classify junctions
- Danger scoring algorithm implementation
- GeoJSON export for mobile consumption

**Mobile Application:**
- React Native for cross-platform compatibility (iOS/Android)
- Expo Location for GPS tracking
- React Native Maps for visualization
- R-tree spatial indexing (rbush) for O(log n) query performance
- AsyncStorage for offline data caching

**Key Design Decisions:**
- GeoJSON format for wide compatibility and human readability
- Hazard points displaced from actual junction for advance warning
- Direction-aware detection to prevent false alerts
- Spatial indexing for real-time performance on mobile devices

---

## Project Objectives

**Primary Objectives:**
1. Develop functional prototype mobile app working on iOS and/or Android
2. Accurately identify dangerous junctions from OSM data (>70% accuracy target)
3. Provide timely warnings with low false positive rate (<20% target)
4. Support offline operation with pre-downloaded regional datasets
5. Create intuitive, non-distracting user interface suitable for driving

**Secondary Objectives:**
- Optimize spatial queries for mobile performance
- Implement user preferences and settings
- Support multiple regions with downloadable datasets
- Validate effectiveness through user testing (10-15 participants)

---

## Expected Impact

This app fills a critical gap in navigation technology by proactively warning about dangerous road configurations regardless of whether accidents have recently occurred. Unlike existing navigation apps that show traffic conditions and accidents, this system identifies inherently dangerous infrastructure based on road characteristics and geometry.

The solution particularly benefits:
- International drivers unfamiliar with local road networks
- Drivers in rural areas with varying road standards
- Tourists and business travelers in foreign countries
- Anyone navigating unfamiliar territories

By providing advance warning of challenging junctions, the app gives drivers time to slow down, increase alertness, and prepare for potentially difficult navigation scenarios, ultimately enhancing road safety for drivers abroad.

---

## Evaluation Strategy

The project will be validated through:
- **Technical Testing:** Junction identification accuracy, warning timing precision, system performance metrics
- **User Testing:** 10-15 participants in simulated driving scenarios
- **Usability Evaluation:** System Usability Scale (SUS) questionnaires and interviews
- **Performance Analysis:** Battery usage, query response times, GPS accuracy

Success criteria include functional prototype delivery, acceptable accuracy rates, positive user feedback, and offline functionality with good mobile performance.
