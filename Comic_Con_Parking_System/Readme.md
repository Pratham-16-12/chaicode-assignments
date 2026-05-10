# Comic-Con Event Parking System - Database Architecture

A robust relational database schema designed to handle the dynamic parking requirements of a large-scale convention venue hosting Comic-Con India.

## 📌 Project Overview

This project models a multi-zone event parking system capable of tracking thousands of visitors arriving in various vehicle types across multiple days. The architecture explicitly decouples vehicles, physical parking spots, and parking sessions to support real-world event constraints—such as the same vehicle entering multiple times, or a single parking spot being reused throughout the weekend.

## 🏗️ Core Architecture & Logic

The database is structured around a central hub (`ParkingSession`) to ensure maximum scalability and practicality.

- **Dynamic Session Management:** Vehicles and Parking Spots are connected via the `ParkingSession` junction table. This many-to-many relationship over time ensures data isn't overwritten when a car leaves and returns.
- **Role-Based Spot Allocation:** Implemented a `SpotCategory` structure to smoothly handle reserved areas for Cosplayers, Exhibitors, VIP guests, Staff, and EV Charging.
- **Live Availability Tracking:** The system easily tracks currently parked vehicles by querying `ParkingSession` records where the `exit_time` is `NULL` and the status is `Active`.
- **Decoupled Billing & Ticketing:** `Ticket` and `Payment` are strictly separated into 1-to-1 relationships with the session. This reflects real-world operations where tickets are generated upon entry, but payment amounts are calculated and recorded upon exit.

## 🗄️ Database Tables

1. **`VehicleCategory`**: Classifications like Bike, Car, SUV, Cab, or EV.
2. **`Vehicle`**: Stores unique vehicle records via License Plate.
3. **`Zone`**: Represents physical parking areas (Levels, Zones) and capacities.
4. **`SpotCategory`**: Access restrictions (General, VIP, Staff, EV).
5. **`ParkingSpot`**: Physical spaces mapped to specific zones and categories.
6. **`ParkingSession`**: The core junction table recording entry/exit timestamps.
7. **`Ticket`**: Records unique ticket numbers issued per session.
8. **`Payment`**: Handles financial transactions and payment status.

## 🚀 How to View the ER Diagram

The schema is written in DBML (Database Markup Language). To visualize the Entity-Relationship Diagram:

1. Copy the contents of `Comic_Con_Parking_System.dbml`.
2. Navigate to [dbdiagram.io](https://dbdiagram.io).
3. Paste the code into the editor to automatically render the database relationships.

---

_Built for the Web Dev Cohort 2026._
