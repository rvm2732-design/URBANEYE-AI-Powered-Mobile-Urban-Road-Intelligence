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

UrbanEye — System Architecture

UrbanEye follows a layered architecture in which the React frontend provides the operational interface, Google Maps provides geographic visualization, Supabase provides persistent PostgreSQL storage, and the application API layer manages access to detection data.

┌─────────────────────────────────────────────────────────────┐
│                         URBANEYE                             │
│              Urban Road Intelligence Platform               │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                        │
│                     React + Vite                             │
│                                                             │
│  GIS Dashboard │ Event Detail │ Maintenance │ Fleet │ Reports│
└───────────────┬──────────────────────────────┬───────────────┘
                │                              │
                ▼                              ▼
┌──────────────────────────┐       ┌───────────────────────────┐
│   GOOGLE MAPS LAYER      │       │      API / DATA LAYER     │
│                          │       │                           │
│ Google Maps JavaScript   │       │ events.js                 │
│ API                      │       │ Supabase Client            │
│                          │       │                           │
│ Detection markers        │       │ Data retrieval/normalizing │
└──────────────────────────┘       └─────────────┬─────────────┘
                                                 │
                                                 ▼
                                  ┌───────────────────────────┐
                                  │       DATA LAYER           │
                                  │                           │
                                  │        Supabase            │
                                  │      PostgreSQL DB         │
                                  │                           │
                                  │       detections           │
                                  └───────────────────────────┘
1. UI/UX Architecture — Stitch → React

The interface development started with Stitch as the UI/UX design and prototyping layer.

Design Concept
      │
      ▼
   Stitch
      │
      │ UI / UX reference
      ▼
React Components
      │
      ▼
Tailwind CSS
      │
      ▼
Operational Dashboard

Stitch was used to establish the visual direction and screen structure.

The final application was then implemented as actual React components rather than relying on the Stitch prototype itself.

Dashboard modules
UrbanEye
│
├── GIS Dashboard
├── Event Detail
├── Maintenance Queue
├── Fleet Status
└── Reports

This gives us a clear separation between:

Stitch = design/prototyping

and

React + Tailwind = implemented application

2. Frontend Architecture

The frontend is built using React + Vite, with Tailwind CSS providing the styling system.

React Application
│
├── Pages
│   ├── Dashboard
│   ├── EventDetail
│   ├── MaintenanceQueue
│   ├── FleetStatus
│   └── Reports
│
├── Components
│   ├── Layout
│   ├── Map
│   ├── Detection
│   ├── Queue
│   └── Fleet
│
├── API
│   ├── events.js
│   └── supabase.js
│
└── Styles
    └── Tailwind CSS
Frontend responsibilities

The React application is responsible for:

rendering the operational dashboard
retrieving detection records
displaying geographic events
displaying individual event information
generating the maintenance queue
aggregating detections by vehicle
displaying fleet status
presenting reports and analytics
3. API / Data Access Architecture

Instead of allowing every page to directly communicate with the database, UrbanEye uses an application data-access layer.

React Page
    │
    ▼
getDetections()
    │
    ▼
src/api/events.js
    │
    ▼
Supabase Client
    │
    ▼
Supabase Database

The current detection retrieval function:

const { data, error } = await supabase
  .from("detections")
  .select("*")
  .order("timestamp", { ascending: false });

The API layer also normalizes database fields for the frontend.

For example:

latitude   → lat
longitude  → lng
bus_id     → busId

This prevents database-specific naming from leaking throughout the UI.

4. Supabase Architecture

Supabase currently provides the persistent backend data layer.

                    SUPABASE
                       │
              ┌────────┴────────┐
              │                 │
         PostgreSQL            RLS
          Database         Access Policies
              │
              ▼
        detections table

The detections table currently stores information such as:

id
bus_id
route
type
severity
confidence
latitude
longitude
speed
imu_confirmed
timestamp
location
status

This means detection information is persisted outside the React application.

The frontend can retrieve the same records whenever the application loads.

5. Database Architecture

The current database model is centered around the detection event.

Detection Event
│
├── Identity
│   └── id
│
├── Vehicle
│   ├── bus_id
│   └── route
│
├── Detection
│   ├── type
│   ├── severity
│   └── confidence
│
├── Location
│   ├── latitude
│   ├── longitude
│   └── location
│
├── Telemetry
│   ├── speed
│   └── imu_confirmed
│
└── Event State
    ├── timestamp
    └── status

This event-centric structure is important because the same detection can be consumed by multiple dashboard modules.

6. Google Maps Architecture

Google Maps is used as the geographic visualization layer.

Supabase
   │
   │ latitude + longitude
   ▼
getDetections()
   │
   ▼
Dashboard
   │
   ▼
GISMap.jsx
   │
   ▼
Google Maps JavaScript API
   │
   ▼
Real Map + Detection Marker

The coordinates stored in Supabase are converted into map positions:

const position = {
  lat: detection.lat,
  lng: detection.lng,
};

The detection is then displayed as a marker on the Google Map.

Therefore:

Database detection → geographic coordinates → map visualization

7. Dashboard Data Architecture

One of the important architectural decisions is that the dashboard screens don't maintain separate copies of the detection data.

Instead:

                 Supabase
                    │
                    ▼
              getDetections()
                    │
                    ▼
              React Application
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼
     GIS Map    Event Detail   Queue
        │           │           │
        └───────────┼───────────┘
                    │
                    ▼
               Fleet Status
                    │
                    ▼
                 Reports

This means a detection inserted into Supabase can automatically become part of multiple operational views.

For example:

BUS-204
Pothole
Critical
96.8%
20.5524, 76.5699

can appear as:

a map marker
an event detail
a maintenance queue item
a fleet detection
a report statistic
8. Security Architecture

The frontend uses the Supabase publishable key, not the secret/service-role key.

React Frontend
      │
      │ Publishable Key
      ▼
Supabase
      │
      ▼
Row Level Security
      │
      ▼
Allowed Operations

Row Level Security is enabled for the detection table.

The secret/service-role credential is not exposed in the frontend.

9. Current End-to-End Architecture
    
                         URBANEYE
                            │
                            ▼
                  ┌──────────────────┐
                  │ React + Vite      │
                  │ Tailwind CSS      │
                  └────────┬─────────┘
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
      Google Maps API             Application API
             │                           │
             │                           ▼
             │                   Supabase Client
             │                           │
             │                           ▼
             │                     Supabase
             │                     PostgreSQL
             │                           │
             └─────────────┬─────────────┘
                           │
                           ▼
                    Detection Events

10. Architecture - Story

| Layer            | Technology                 | Role                                       |
| ---------------- | -------------------------- | ------------------------------------------ |
| UI/UX Design     | Stitch                     | Interface prototyping and visual reference |
| Frontend         | React                      | Application interface                      |
| Build Tool       | Vite                       | Development/build pipeline                 |
| Styling          | Tailwind CSS               | UI styling                                 |
| Maps             | Google Maps JavaScript API | Geographic visualization                   |
| Data Access      | Supabase JS                | Database communication                     |
| Backend Database | Supabase PostgreSQL        | Persistent detection storage               |
| Security         | Supabase RLS               | Database access control                    |
| Future Edge AI   | YOLOv8 / Raspberry Pi      | Road-event perception                      |
| Future Sensors   | GPS + IMU                  | Localization and event verification        |
