# 🏢 Smart Elevator Control System — Database Design

> **Web Dev Cohort 2026 | Databases Assignment**

A relational database schema for **LiftGrid Systems** — an intelligent elevator control platform managing dozens of elevators across multiple high-rise buildings, handling thousands of ride requests daily.

---

## 📌 Project Overview

LiftGrid Systems serves corporate towers, malls, airports, hospitals, and residential complexes across India. Each building contains multiple elevator shafts. Users generate floor requests; the system assigns the optimal elevator, logs every ride, and tracks maintenance history.

This design supports:
- Multi-building infrastructure with independent floor layouts
- Many-to-many elevator ↔ floor service relationships
- Real-time status monitoring without polluting configuration data
- Full ride assignment and trip logging for analytics
- Complete, non-destructive maintenance history per elevator

---

## 🏗️ Core Architecture

```
Building ──< Floor
    │           │
    └──< Elevator >── ElevatorFloorService ──< Floor
             │
       ElevatorStatusLog
             │
       FloorRequest ──> RideAssignment ──> RideLog
             │
       MaintenanceRecord
```

### Why This Design Works

| Problem | Solution |
|---|---|
| Elevator serves multiple floors; floor served by multiple elevators | `ElevatorFloorService` junction table |
| Status changes shouldn't overwrite config | `ElevatorStatusLog` — append-only historical log |
| Pending vs assigned vs completed requests | `FloorRequest.status` field |
| Analytics (rides per elevator, peak hours) | `RideLog` — one row per completed trip |
| Maintenance history never deleted | `MaintenanceRecord` — separate from Elevator config |
| Temporarily disable an elevator | `ElevatorStatusLog` entry with `status = 'MAINTENANCE'` |

---

## 🗄️ Database Tables

| # | Table | Purpose |
|---|---|---|
| 1 | `Building` | Top-level entity — a physical property connected to the platform |
| 2 | `Floor` | Each floor in a building; unique per `(building_id, floor_number)` |
| 3 | `Elevator` | Static elevator config: capacity, shaft, type — no dynamic data here |
| 4 | `ElevatorFloorService` | Many-to-many junction: which elevators serve which floors |
| 5 | `ElevatorStatusLog` | Append-only status history per elevator (`IDLE`, `MOVING`, `MAINTENANCE`) |
| 6 | `FloorRequest` | A call button press — direction (`UP`/`DOWN`) from a floor |
| 7 | `RideAssignment` | 1-to-1 link from a request to the elevator that was dispatched |
| 8 | `RideLog` | Completed trip record — origin floor, destination floor, timestamps |
| 9 | `MaintenanceRecord` | Full maintenance lifecycle per elevator — scheduled, in-progress, completed |

---

## 💡 Key Design Decisions

- **Static vs Dynamic separation**: `Elevator` holds only configuration (capacity, shaft, type). All dynamic data (status, ride logs, maintenance) lives in separate tables — never overwritten.
- **`ElevatorFloorService`** solves the many-to-many: elevator A serves floors 1–10; elevator B serves floors 5–20. Both serve floor 5.
- **`FloorRequest` → `RideAssignment`** is 1-to-1 (one request, one dispatched elevator), keeping assignment logic cleanly decoupled.
- **`RideLog`** captures `origin_floor_id` and `destination_floor_id` separately from the request, enabling real trip analytics.
- **`ElevatorStatusLog`** is an append-only log. The *current* status is always the latest record for that `elevator_id` — history is never lost.
- **Unique index on `(building_id, shaft_number)`** and **`(building_id, floor_number)`** prevents duplicate records.

---

## 🚀 How to View the ER Diagram

The schema is written in **DBML** (Database Markup Language).

1. Open [dbdiagram.io](https://dbdiagram.io)
2. Copy the contents of [`Smart_Elevator_Control.dbml`](./Smart_Elevator_Control.dbml)
3. Paste into the editor — diagram renders instantly
4. Export as **PNG / PDF** for submission

---

## 📁 Repository Structure

```
Smart_Elevator_Control/
├── Smart_Elevator_Control.dbml   ← Full schema in DBML
└── README.md                      ← This file
```

---

*Built for Web Dev Cohort 2026.*
