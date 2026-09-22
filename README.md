# Fleet Sense — AI-Powered Mobile Urban Road Intelligence

SIH 2026 | Problem Statement SIH26124 | Bharat Electronics Limited (BEL)
Detect. Understand. Improve.

---

## Problem Statement

SIH26124 — AI-Powered Mobile Urban Intelligence Platform Using Public Transport Fleet

Municipal road authorities rely on manual inspection and citizen complaints to detect road damage — a process that is slow, incomplete, and expensive. City bus fleets already travel every road daily, equipped with cameras — but that footage is never analysed.

Fleet Sense turns every bus into a continuously sensing road-intelligence node.

---

## What We Built

| Layer | Status | Description |
|---|---|---|
| Edge AI (Pi 4) | Working | YOLOv8 Nano + Float16 TFLite running at 6-9 FPS |
| Road damage detection | Validated | mAP@0.5 = 0.796, F1 = 0.76 |
| GPS + IMU tagging | In progress | Geo-tag every detection with coordinates |
| Fleet fusion | In progress | Multi-bus event clustering + deduplication |
| GIS Dashboard | In progress | React + Google Maps API |

---

## System Architecture

```
+--------------------------------------------------+
|              EDGE -- Raspberry Pi 4              |
|                                                  |
|  Dashcam -> Frame Grab -> Preprocess -> YOLOv8   |
|      -> NMS -> Confidence Filter -> Event Pack   |
|      -> GPS Tag + IMU Corroboration              |
+--------------------+-----------------------------+
                     |
                     | JSON event packet
                     |
                     v
+--------------------------------------------------+
|              SERVER / CLOUD                      |
|                                                  |
|  Event API -> Geo-clustering -> Deduplication    |
|           -> Severity Scoring -> Database        |
+--------------------+-----------------------------+
                     |
                     v
+--------------------------------------------------+
|              GIS DASHBOARD (React)               |
|                                                  |
|  Live Map  Event Detail  Maintenance Queue       |
|  Fleet Status  Heat Maps  Dispatch Actions       |
+--------------------------------------------------+
```

---

## Edge Perception — Proven Hardware Results

Our Raspberry Pi 4 prototype is already running and validated.

```
Hardware  :  Raspberry Pi 4 (8GB RAM) + Dashcam
Model     :  YOLOv8 Nano -> Float16 TFLite (ARM-optimized)
Input     :  320x320 px
FPS       :  6-9 FPS (on-device, no GPU, no cloud)
Latency   :  ~130ms per frame
mAP@0.5   :  0.796
F1 Score  :  0.76
```

### Detected Classes (existing)

- Road Damages
- Speed Bump
- Unsurfaced Road
- HMV (Heavy Motor Vehicle)
- LMV (Light Motor Vehicle)
- Pedestrian

### SIH Upgrade (adding)

- Pothole, Cracks, Waterlogging, Zebra crossing, Road signs

---

## Our 3 Novelties

### Novelty 1 — Shadow-Aware Perception

Use scene context and lighting cues to suppress false road-anomaly detections caused by vehicle shadows and poor illumination — a known weakness in camera-only systems.

### Novelty 2 — Multi-Pass Confirmation

Three buses independently seeing the same GPS location leads to one geo-clustered, confirmed maintenance case with a higher combined confidence score. Eliminates false positives at the fleet level.

### Novelty 3 — Sensor Fusion

Vision (camera) + IMU (gyroscope/accelerometer) + GPS + temporal consistency produces more trustworthy severity and location data than camera-only detection.

---

## Tech Stack

### Edge (Raspberry Pi 4)

| Component | Technology |
|---|---|
| Video capture | OpenCV, libcamera |
| AI model | YOLOv8 Nano, Float16 TFLite |
| GPS | Python + gpsd / serial |
| IMU | Python + smbus2 (I2C, MPU6050) |
| Runtime | Python 3.11, Linux (Raspberry Pi OS) |

### Backend (Server)

| Component | Technology |
|---|---|
| API | FastAPI (Python) |
| Database | PostgreSQL + PostGIS |
| Geo-clustering | DBSCAN / Haversine |
| Deployment | Railway / Render |

### Frontend (Dashboard)

| Component | Technology |
|---|---|
| Framework | React + Vite |
| Maps | Google Maps JavaScript API |
| UI design | Google Stitch to custom CSS |
| State management | React Query |

---

## Dashboard — 4 Screens

| Screen | Purpose |
|---|---|
| GIS Live Map | Severity-colored pins on real city map (critical / moderate / minor) |
| Event Detail | Detection frame + GPS + confidence + cross-validation + dispatch |
| Maintenance Queue | Priority-sorted list of confirmed defects |
| Fleet Status | Active buses — camera health, GPS signal, route coverage |

---

## Full Pipeline

```
VIDEO -> AI -> EVENT -> GEO -> FUSE -> TRUST -> ACT
Camera  YOLOv8  Class+conf  GPS  Repeat  IMU+Shadow  GIS
```

---

## Feasibility and Validation

| Layer | What exists | SIH upgrade | Validation metric |
|---|---|---|---|
| AI perception | Multi-anomaly detector | Extend classes | mAP / F1 / per-class recall |
| Edge deployment | Hardware-software prototype | Optimize + quantize | FPS, latency, power |
| Localization | Video events | GPS + timestamp + route ID | Geo-error / event completeness |
| Sensor fusion | Road anomaly outputs | IMU combination + confidence fusion | False-positive reduction |
| Fleet intelligence | Single-device logic | Multi-bus ingestion + deduplication | Duplicate-collapse rate |
| Urban platform | Prototype output | GIS map + heat maps + maintenance queue | End-to-end alert latency |

Validation Plan: mAP/F1, edge FPS/latency, localization error, false-positive rate, duplicate-collapse rate, event-to-dashboard latency, route coverage.

---

## Impact

- Transport Authorities — Road-condition inventory, maintenance queue, evidence-backed alerts
- Citizens / Road Safety — Earlier hazard identification, faster response
- City Decision Support — GIS layers + heat maps + severity leads to right repair at the right place
- Scalability — One route to one city to multi-city state transport fleet

KEY OUTCOME: continuous sensing -> trusted evidence -> prioritized action

---

## Research and References

| # | Paper |
|---|---|
| R1 | YOLOv8 Road Damage Detection — IEEE Xplore 2023 |
| R2 | YOLOv8-PD Pavement Distress Detection — Nature Scientific Reports 2024 |
| R3 | TFLite vs TensorFlow on Raspberry Pi 4 — ResearchGate 2023 |
| R4 | IMU Road Anomaly Detection — PMC / MDPI |
| R5 | GPS + IMU Sensor Fusion for Vehicles — IEEE Xplore |
| R6 | Crowdsensing Road Monitoring using Vehicles — Springer 2020 |
| R7 | YOLOv8 on Edge Devices Benchmark — arXiv 2026 |

---

## Live Demo

Raspberry Pi 4 running YOLOv8 Nano detecting road anomalies in real time at 7.89 FPS — no GPU, no cloud.

Demo video: https://youtu.be/UumkTK9gZ_Q

---

## Team — Fleet Sense

| Role | Domain |
|---|---|
| ML / Model Training | YOLOv8, TFLite, dataset annotation |
| Edge Hardware | Raspberry Pi 4, GPS, IMU wiring |
| Backend API | FastAPI, PostgreSQL, geo-clustering |
| Frontend Dashboard | React, Google Maps API |
| Documentation | PPT, report, research |

College: SSGMCE Shegaon
Problem Statement: SIH26124 — Bharat Electronics Limited (BEL)
Category: Software + Hardware
Hackathon: Smart India Hackathon 2026

---

## Repository Structure

```
fleet-sense/
|
+-- edge/                    # Raspberry Pi 4 code
|   +-- detect.py            # Main detection loop
|   +-- gps_reader.py        # GPS module interface
|   +-- imu_reader.py        # IMU sensor interface
|   +-- event_uploader.py    # Upload confirmed events
|   +-- models/              # YOLOv8 TFLite model files
|
+-- backend/                 # FastAPI server
|   +-- main.py              # API endpoints
|   +-- geo_cluster.py       # Geo-clustering logic
|   +-- severity_scorer.py   # Severity calculation
|   +-- database/            # PostgreSQL + PostGIS setup
|
+-- dashboard/               # React frontend
|   +-- src/
|   |   +-- pages/           # GIS Map, Event Detail, Queue, Fleet
|   |   +-- components/      # Shared UI components
|   |   +-- api/             # Backend API calls
|   +-- public/
|
+-- docs/                    # Documentation
    +-- SIH_PPT.pdf
    +-- technical_approach.pdf
```

---

SIH 2026 | SIH26124 | AI-Powered Mobile Urban Intelligence Platform Using Public Transport Fleet | Fleet Sense | SSGMCE Shegaon