# 🏥 Clinic Appointment & Diagnostics Platform — Database Design

> **Web Dev Cohort 2026 | Databases Assignment**

A relational database schema for a modern multi-doctor clinic — managing appointments, consultations, diagnostic test prescriptions, lab reports, and payments in a clean, scalable structure.

---

## 📌 Project Overview

A modern clinic wants to digitize its operations. Patients visit doctors across multiple departments, book appointments, receive clinical consultations, get diagnostic tests prescribed, and later receive lab reports.

Key clinical realities this design handles:
- An appointment may be **cancelled, rescheduled, or result in a no-show** — it doesn't always become a consultation
- One consultation can prescribe **multiple diagnostic tests**
- Reports are generated **after** tests — not at the time of the appointment
- Payments cover consultation fees, diagnostic fees, or both

---

## 🏗️ Core Architecture

```
Department ──< Doctor ──< Appointment ──< Patient
                               │
                          Consultation ──< PrescribedTest >── DiagnosticTest
                               │                   │
                           Payment           DiagnosticReport
```

### Why This Design Works

| Problem | Solution |
|---|---|
| Appointment ≠ Consultation | Two separate tables — `Appointment` captures the booking; `Consultation` captures the actual clinical visit |
| One consultation → multiple tests | `PrescribedTest` junction between `Consultation` and `DiagnosticTest` |
| Reports generated later | `DiagnosticReport` is 1-to-1 with `PrescribedTest`, created after the test is run |
| Multiple visits by same patient | `Patient` → `Appointment` is one-to-many; each visit is independent |
| One doctor, many patients | `Doctor` → `Appointment` is one-to-many |
| Payment covers different billing items | `Payment.payment_for` field: `CONSULTATION`, `DIAGNOSTICS`, or `COMBINED` |

---

## 🗄️ Database Tables

| # | Table | Purpose |
|---|---|---|
| 1 | `Department` | Clinic departments/specialties (General Medicine, Dermatology, Orthopedics) |
| 2 | `Doctor` | Physician records — specialty, license, consultation fee |
| 3 | `Patient` | Registered patients — personal details and blood group |
| 4 | `Appointment` | Booking event — scheduled time, reason, status (can be cancelled/no-show) |
| 5 | `Consultation` | **Actual clinical visit** — 1-to-1 with Appointment; contains diagnosis and notes |
| 6 | `DiagnosticTest` | Lab test catalog — name, type, price, expected turnaround |
| 7 | `PrescribedTest` | **Junction table** — tests ordered for a specific consultation |
| 8 | `DiagnosticReport` | Lab results — 1-to-1 with PrescribedTest; generated after testing |
| 9 | `Payment` | Billing record per appointment — consultation fee and/or diagnostics |

---

## 💡 Key Design Decisions

- **`Appointment` vs `Consultation` separation** is the most important design choice. It correctly handles cancellations, no-shows, and rescheduling without data loss.
- **`DiagnosticTest`** is a catalog table (reusable). **`PrescribedTest`** is the instance — a specific test ordered for a specific consultation on a specific date.
- **`DiagnosticReport`** is 1-to-1 with `PrescribedTest` and includes `is_abnormal` as a quick flag so doctors can prioritize which reports to review first.
- **`Doctor.specialty`** is kept as an attribute (not a separate table) since a doctor typically has one defined specialty — avoids over-normalization.
- **`Doctor.consultation_fee`** enables automatic payment calculation at checkout.
- **`Patient.registered_at`** tracks when the patient first joined the clinic — useful for engagement analytics.

---

## 🚀 How to View the ER Diagram

The schema is written in **DBML** (Database Markup Language).

1. Open [dbdiagram.io](https://dbdiagram.io)
2. Copy the contents of [`Clinic_Appointment_Diagnostics.dbml`](./Clinic_Appointment_Diagnostics.dbml)
3. Paste into the editor — diagram renders instantly
4. Export as **PNG / PDF** for submission

---

## 📁 Repository Structure

```
Clinic_Appointment_Diagnostics/
├── Clinic_Appointment_Diagnostics.dbml   ← Full schema in DBML
└── README.md                              ← This file
```

---

*Built for Web Dev Cohort 2026.*
