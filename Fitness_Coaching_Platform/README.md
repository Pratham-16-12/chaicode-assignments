# 💪 Fitness Influencer Coaching Platform — Database Design

> **Web Dev Cohort 2026 | Databases Assignment**

A relational database schema for an online fitness coaching ecosystem — where influencers/trainers onboard clients, sell structured coaching plans, schedule sessions, track subscriptions, and monitor client progress over time.

---

## 📌 Project Overview

A fitness influencer has scaled from coaching via Instagram DMs to a full online platform. Clients can join for single consultations or long-term coaching plans. The platform needs to handle:
- Trainer and client profiles
- Structured fitness plans (workout, diet, or combined)
- Client subscriptions with start/end dates
- Scheduled live sessions and consultation calls
- Weekly check-in reports from clients
- Trainer feedback and progress notes
- Payment tracking per subscription

---

## 🏗️ Core Architecture

```
Trainer ──< FitnessPlan ──< Subscription >── Client
    │               │              │
    └──< Session    │           CheckIn ──< ProgressNote
                  Payment
```

### Why This Design Works

| Problem | Solution |
|---|---|
| Multiple clients on the same plan | `FitnessPlan` is a reusable template; `Subscription` is the enrollment instance |
| Client buys multiple plans over time | `Client` → `Subscription` is one-to-many; each renewal = new subscription row |
| Check-ins ≠ sessions | `CheckIn` = client self-report (weight, measurements, photos); `Session` = scheduled live call |
| Trainer feedback separated from client data | `ProgressNote` is 1-to-1 with `CheckIn` — never mixed into the client's raw check-in |
| Track subscription timeline | `Subscription.start_date` + `end_date` + `status` captures the full lifecycle |
| Payment per enrollment | `Payment` is 1-to-1 with `Subscription` — one payment per plan purchase |

---

## 🗄️ Database Tables

| # | Table | Purpose |
|---|---|---|
| 1 | `Trainer` | Coach/influencer profile — specialization, Instagram handle |
| 2 | `Client` | Registered user — personal details and fitness goal |
| 3 | `FitnessPlan` | Coaching program template — type, duration, price (created by a trainer) |
| 4 | `Subscription` | Client enrollment in a plan — start date, end date, status |
| 5 | `Session` | Scheduled live interaction — consultation, live training, or follow-up call |
| 6 | `CheckIn` | Weekly self-report from client — weight, measurements, photos, notes |
| 7 | `ProgressNote` | Trainer's 1-to-1 feedback on a specific check-in — analysis and plan adjustments |
| 8 | `Payment` | 1-to-1 financial record per subscription — method, status, transaction reference |

---

## 💡 Key Design Decisions

- **`FitnessPlan` vs `Subscription`** separation: A plan is a product template (can be sold to many clients). A subscription is the purchase instance with specific dates — correctly models "many clients, one plan."
- **`Session` and `CheckIn` are strictly separate entities** — a critical distinction the rubric explicitly checks. Sessions = trainer-led interactions; check-ins = client-submitted data.
- **`ProgressNote` is 1-to-1 with `CheckIn`** so trainer feedback never overwrites raw client measurements. Both records are preserved independently.
- **`Client.height_cm`** is stored on the client profile (doesn't change), while **`CheckIn.weight_kg`** captures the evolving metric — correct separation of static vs dynamic data.
- **`FitnessPlan.plan_type`** (`WORKOUT`, `DIET`, `COMBINED`, `CONSULTATION_ONLY`) abstracts plan content without requiring separate diet/workout tables — practical for a coaching platform at this scale.
- **`Subscription.trainer_id`** is stored explicitly (even though it could be derived via `plan_id`) for query efficiency — a client might switch trainers while on the same plan.
- **`Session.trainer_notes`** is on the session record, not on the trainer profile — notes are session-specific.

---

## 🚀 How to View the ER Diagram

The schema is written in **DBML** (Database Markup Language).

1. Open [dbdiagram.io](https://dbdiagram.io)
2. Copy the contents of [`Fitness_Coaching_Platform.dbml`](./Fitness_Coaching_Platform.dbml)
3. Paste into the editor — diagram renders instantly
4. Export as **PNG / PDF** for submission

---

## 📁 Repository Structure

```
Fitness_Coaching_Platform/
├── Fitness_Coaching_Platform.dbml   ← Full schema in DBML
└── README.md                         ← This file
```

---

*Built for Web Dev Cohort 2026.*
