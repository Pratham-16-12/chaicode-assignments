# 🎪 Comic-Con Event Parking System — Database Design

> **Web Dev Cohort 2026 | Databases Assignment**

A relational database schema for a multi-zone event parking facility hosting Comic-Con India — handling thousands of vehicles across multiple days, zones, levels, and reserved categories.

---

## 📌 Project Overview

Comic-Con India attracts visitors arriving in bikes, cars, SUVs, cabs, and EVs across multiple event days. The venue has a structured parking facility split into **zones and levels**, with reserved areas for cosplayers, exhibitors, VIP guests, staff, and EV charging.

This design tracks:
- Vehicles entering and exiting the facility
- Parking spot allocation per zone/level
- Reserved and general spot categories
- Complete session, ticket, and payment lifecycle

---

## 🏗️ Core Architecture

The schema is built around **`ParkingSession`** as the temporal junction hub — the key insight that unlocks real-world correctness.

```
VehicleCategory ──< Vehicle ──< ParkingSession >── ParkingSpot >── Zone
                                      │                   │
                                   Ticket             SpotCategory
                                   Payment
```

### Why This Design Works

| Problem | Solution |
|---|---|
| Same vehicle enters multiple days | `Vehicle` holds only config; `ParkingSession` holds each visit |
| Same spot reused throughout the event | `ParkingSpot` ↔ `ParkingSession` is many-to-many over time |
| Track currently parked vehicles | Query `ParkingSession WHERE exit_time IS NULL` |
| Reserved areas (VIP, EV, Staff) | `SpotCategory` with a `rate_multiplier` per category |
| Calculate parking fee | `VehicleCategory.rate_per_hour × SpotCategory.rate_multiplier × hours_parked` |
| Spot under maintenance | `ParkingSpot.is_maintenance = true` |

---

## 🗄️ Database Tables

| # | Table | Purpose |
|---|---|---|
| 1 | `VehicleCategory` | Vehicle types: Bike, Car, SUV, Cab, EV — each with a base `rate_per_hour` |
| 2 | `Vehicle` | Unique vehicle records identified by `license_plate` |
| 3 | `Zone` | Physical parking areas/levels with `zone_type` (`ZONE` or `LEVEL`) and `floor_number` |
| 4 | `SpotCategory` | Access tiers: General, Cosplayer, Exhibitor, VIP, Staff, EV Charging — each with a `rate_multiplier` |
| 5 | `ParkingSpot` | Individual physical spaces mapped to a Zone and SpotCategory |
| 6 | `ParkingSession` | **Core junction** — records every entry/exit event with timestamps and status |
| 7 | `Ticket` | Unique ticket issued at entry, 1-to-1 with a session |
| 8 | `Payment` | Financial record settled at exit, 1-to-1 with a session |

---

## 💡 Key Design Decisions

- **`rate_per_hour` on `VehicleCategory`** + **`rate_multiplier` on `SpotCategory`** → complete, self-contained billing formula
- **`Zone.zone_type`** distinguishes surface zones from multi-story levels; **`floor_number`** captures the exact level
- **`ParkingSpot.is_maintenance`** cleanly removes a spot from allocation without deleting history
- **Unique index on `(vehicle_id, entry_time)`** in `ParkingSession` prevents duplicate active sessions
- **Unique index on `(zone_id, spot_number)`** in `ParkingSpot` prevents duplicate spot numbers per zone
- **Ticket** = issued at entry (before fee is known); **Payment** = settled at exit (after duration is calculated) — correctly decoupled

---

## 🚀 How to View the ER Diagram

The schema is written in **DBML** (Database Markup Language), which renders automatically on [dbdiagram.io](https://dbdiagram.io).

1. Open [dbdiagram.io](https://dbdiagram.io)
2. Copy the contents of [`Comic_Con_Parking_System.dbml`](./Comic_Con_Parking_System.dbml)
3. Paste into the editor — the diagram renders instantly
4. Use **Export → PNG / PDF** to save the visual

---

## 📁 Repository Structure

```
Comic_Con_Parking_System/
├── Comic_Con_Parking_System.dbml   ← Full schema in DBML
└── README.md                        ← This file
```

---

*Built for Web Dev Cohort 2026.*
