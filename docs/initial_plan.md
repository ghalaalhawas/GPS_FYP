# Initial Plan Document
## GPS Mobile App to Help Driving Safety Abroad

**Student Name:** Ghala Albarazi  
**Supervisor:** Crispin Cooper  
**Course:** Final Year Project  
**Submission Date:** February 2, 2026  
**Date:** January 22, 2026

---

## 1. Introduction

### Problem Statement

Driving on a narrow lane, you're always in the middle. When the lane joins a wider road, it's important to turn onto the correct side. Paradoxically, this is harder to do on an empty road, as there are no visual cues to remind the driver which side they should be on.

International drivers face significant challenges when navigating unfamiliar road networks. Dangerous junctions that locals know to approach with caution are completely unknown to visitors. Current GPS navigation apps focus on routing but don't warn about inherently hazardous road configurations. T-junctions where minor roads meet major roads with high speed differentials are particularly dangerous, yet drivers receive no advance warning.

### Proposed Solution

This project aims to develop a mobile application that enhances driving safety by warning drivers about potentially dangerous road junctions before they approach them. The app will:

- Use OpenStreetMap data to identify potentially dangerous junctions
- Track the user's GPS location in real-time
- Provide timely audio and visual warnings (200-300m before junction)
- Work offline with pre-downloaded hazard data for the region
- Consider direction of approach to provide context-aware warnings

---

## 2. Project Objectives

### Primary Objectives

1. **Data Processing System**
   - Extract and process OpenStreetMap data for target regions (initially UK)
   - Identify and classify road junctions programmatically based on danger criteria
   - Generate hazard point datasets with displaced warning points (50-100m along approach roads)

2. **Mobile Application**
   - Develop cross-platform mobile app (React Native for iOS/Android)
   - Implement accurate GPS location tracking and map visualization
   - Provide timely warnings when approaching dangerous junctions
   - Support offline operation with cached regional data

3. **Danger Classification Algorithm**
   - Develop scoring algorithm based on junction type, speed differentials, road classification mismatches, and geometry
   - Validate accuracy through testing against known dangerous locations
   - Target: >70% accuracy, <20% false positive rate

### Success Criteria

- Functional prototype app working on mobile devices
- Dangerous junctions identified from OSM data with reasonable accuracy
- Warning system provides timely alerts with low false positive rate
- App works offline with acceptable performance
- Positive user testing feedback (10-15 participants)

---

## 3. Proposed Approach

### Data Processing Pipeline

**Phase 1: Data Acquisition & Junction Identification**
- Download OSM data using Python (OSMnx library)
- Identify nodes where 3+ roads meet
- Extract road attributes (highway type, speed limits, lanes)

**Phase 2: Danger Classification**
The scoring algorithm considers four weighted factors:
- **Junction Type (30%):** T-junctions (high risk), crossroads (medium), 5+ way (high complexity), roundabouts (low)
- **Speed Differential (25%):** Large differences (>20mph) between intersecting roads
- **Road Classification Mismatch (25%):** Primary/trunk roads meeting residential streets
- **Junction Geometry (20%):** Acute angles, obtuse angles, visibility factors

**Phase 3: Hazard Point Generation**
- Create warning points displaced 50-100m up the secondary (lower classification) road
- Export as GeoJSON format with junction metadata (location, danger score, approach angle)

### Mobile Application Development

**Technology Stack:**
- **Frontend:** React Native (cross-platform iOS/Android)
- **Mapping:** React Native Maps with Expo Location for GPS
- **Data Format:** GeoJSON for hazard points
- **Spatial Optimization:** R-tree indexing (rbush) for efficient proximity queries
- **Storage:** AsyncStorage for offline caching

**Key Components:**
- GPS location tracking with high accuracy mode
- Map view displaying user location and nearby hazard points
- Proximity detection system (monitors distance to hazards)
- Warning system (visual + audio alerts at 200-300m)
- Direction-aware detection to prevent false alerts
- Cooldown logic to prevent alert spam

### Development Methodology

- **Agile/Iterative Approach:** Weekly iterations building feature by feature
- **Incremental Testing:** Test after each major feature implementation
- **Documentation:** Maintain documentation throughout development
- **Flexibility:** Adjust approach based on findings and challenges

---

## 4. Timeline and Milestones

### Key Milestones

| Date | Milestone | Deliverable |
|------|-----------|-------------|
| **Feb 2, 2026** | **Initial Plan Submission** | This document |
| **Mar 2, 2026** | Data Processing Complete | Python scripts and hazard datasets |
| **Apr 6, 2026** | Core App Functionality | Working mobile app with warnings |
| **May 4, 2026** | Testing Complete | Evaluation data collected |
| **Jun 1, 2026** | Evaluation Complete | Analysis and findings |
| **Jul 5, 2026** | **Final Report Submission** | Complete project report |

### Phase Breakdown

**Weeks 1-9 (Jan-Feb): Foundation & Data Processing**
- Week 1-2: ✅ Research, environment setup, technology decisions
- Week 3-5: Draft initial plan, create OSM parser module, mobile app Hello World
- Week 6-7: Junction identification and danger scoring algorithm
- Week 8-9: Mobile app GPS foundation, data export to GeoJSON

**Weeks 10-17 (Mar-Apr): Core Development**
- Week 10-11: Data integration in app, proximity detection
- Week 12-13: Warning system implementation, offline caching
- Week 14-15: Spatial optimization (R-tree), direction detection
- Week 16-17: Enhanced dataset generation, testing and refinement

**Weeks 18-22 (May): Evaluation**
- Week 18-19: UI polish, evaluation design
- Week 20-21: User testing execution (10-15 participants), analysis
- Week 22: Report planning and initial drafting

**Weeks 23-28 (Jun-Jul): Final Report**
- Weeks 23-27: Report writing (all chapters, figures, diagrams)
- Week 28: Final proofreading, buffer for adjustments, submission

---

## 5. Risks and Mitigation

### Technical Risks

**Risk 1: OSM Data Quality**
- **Issue:** Incomplete or inaccurate data in some regions
- **Mitigation:** Focus on well-mapped regions (UK, Western Europe); implement data quality checks; handle missing attributes gracefully

**Risk 2: GPS Accuracy Limitations**
- **Issue:** GPS inaccuracy in urban canyons, tunnels
- **Mitigation:** Use high-accuracy mode; add buffer zones to warning thresholds; document limitations

**Risk 3: Mobile Development Complexity**
- **Issue:** Unfamiliarity with mobile development may slow progress
- **Mitigation:** Started early (Week 2); using React Native (accessible framework); leveraging online resources

**Risk 4: Performance on Mobile Devices**
- **Issue:** Spatial queries may be slow, battery drain
- **Mitigation:** Implement R-tree spatial indexing (Week 14); optimize data structures; regular device testing

### Project Management Risks

**Risk 5: Time Management**
- **Issue:** Underestimating task complexity
- **Mitigation:** Follow weekly plan; build in buffer time; prioritize core features over nice-to-have additions

**Risk 6: Scope Creep**
- **Issue:** Adding too many features
- **Mitigation:** Define clear MVP (Minimum Viable Product); focus on core objectives first; consult supervisor on priorities

---

## 6. Evaluation Strategy

### Evaluation Methods

**Quantitative Evaluation:**
- Junction identification accuracy (precision, recall, F1 score)
- Warning timing accuracy (200-300m ± 50m target)
- False positive rate (<20% target)
- System performance (response time, battery usage, memory)

**Qualitative Evaluation:**
- User testing with 10-15 participants (simulated driving scenarios)
- System Usability Scale (SUS) questionnaires (target: >68)
- Semi-structured interviews about warning timing, helpfulness, design
- Observation of user interactions with app

**Validation Approach:**
- Compare identified junctions against known dangerous locations
- Real-world GPS testing for warning distance accuracy
- Performance profiling on various mobile devices
- Expert review from driving instructors or road safety professionals (if possible)

---

## 7. Expected Contributions

This project addresses a gap in current navigation technology by proactively warning about dangerous road configurations based on infrastructure characteristics rather than reactive accident data. The system benefits international drivers, tourists, and anyone navigating unfamiliar territories by providing advance warning of challenging junctions, allowing time to slow down and increase alertness.

**Key Contributions:**
- Novel danger scoring algorithm for road junctions based on OSM data
- Direction-aware proximity warning system for mobile devices
- Offline-capable mobile app using spatial indexing for performance
- Evaluation framework for junction danger identification systems

---

## Appendix: Questions for Supervisor Meeting (Week 1)

1. Is the project scope appropriate for a final year project?
2. Any specific recommendations for validating the danger scoring algorithm?
3. Suggestions for user testing recruitment and evaluation design?
4. Any specific UK regions to focus on for testing (Oxford chosen for initial work)?
5. Expected level of technical detail in final report compared to this plan?
