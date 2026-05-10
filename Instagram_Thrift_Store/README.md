# 👗 Instagram Thrift Creator Store — Database Design

> **Web Dev Cohort 2026 | Databases Assignment**

A relational database schema for a growing Instagram-based thrift and handmade product store — evolving from DM-based orders into a properly structured inventory, order, payment, and shipping management system.

---

## 📌 Project Overview

A small creator sells thrifted fashion items and handmade products through Instagram DMs and WhatsApp. As the business grows, the owner needs to manage products, track stock, handle customer orders, and maintain payment and delivery records.

The critical business distinction:
- **Thrift items** → unique physical pieces (max 1 in stock), have a `condition` rating
- **Handmade items** → producible in batches, can have multiple units in stock

This design supports both product types cleanly within one schema.

---

## 🏗️ Core Architecture

```
ProductCategory ──< Product ──< OrderItem >── Order ──< Customer
                        │                        │
                  ProductImage               Payment
                                             Shipment
```

### Why This Design Works

| Problem | Solution |
|---|---|
| Thrift vs handmade items | `product_type` (`THRIFT`/`HANDMADE`) + `condition` field (only relevant for thrift) |
| Unique thrift items (only 1 in stock) | `stock_quantity` = 1 enforced at app level; `is_available` flips to false when sold |
| One order, many products | `OrderItem` junction table with `unit_price` snapshot and `subtotal` |
| Price changes after order | `OrderItem.unit_price` stores the price *at time of purchase* — not a live reference |
| Payment and shipping tracking | `Payment` and `Shipment` are 1-to-1 with `Order`, keeping data clean and separated |
| Multiple orders per customer | `Customer` → `Order` is one-to-many |

---

## 🗄️ Database Tables

| # | Table | Purpose |
|---|---|---|
| 1 | `Customer` | Registered buyers — identified by Instagram handle, phone, or email |
| 2 | `ProductCategory` | Tops, Bottoms, Accessories, Footwear, Handmade Decor, etc. |
| 3 | `Product` | Core catalog — includes `product_type`, `condition`, `size`, `color`, `stock_quantity` |
| 4 | `ProductImage` | Multiple images per product; one marked as `is_primary` |
| 5 | `Order` | A customer's purchase event with total amount and order status |
| 6 | `OrderItem` | **Junction table** — each product line in an order with price snapshot |
| 7 | `Payment` | 1-to-1 with Order — method, status, transaction reference |
| 8 | `Shipment` | 1-to-1 with Order — carrier, tracking, address snapshot, delivery status |

---

## 💡 Key Design Decisions

- **`product_type`** (`THRIFT` / `HANDMADE`) + **`condition`** on `Product` cleanly distinguish the two business models without requiring separate tables.
- **`OrderItem.unit_price`** is a snapshot at time of order — price changes don't retroactively alter order history.
- **`Shipment.shipping_address`** is captured at the time of shipping, not pulled live from `Customer.shipping_address` — handles address updates correctly.
- **`ProductImage`** is a separate table to support Instagram-style multi-image galleries per product.
- **`Payment`** and **`Shipment`** are both 1-to-1 with `Order` — they're separate concerns (paying ≠ shipping).
- **`is_available`** on `Product` allows manual unlisting without deleting the product and its order history.

---

## 🚀 How to View the ER Diagram

The schema is written in **DBML** (Database Markup Language).

1. Open [dbdiagram.io](https://dbdiagram.io)
2. Copy the contents of [`Instagram_Thrift_Store.dbml`](./Instagram_Thrift_Store.dbml)
3. Paste into the editor — diagram renders instantly
4. Export as **PNG / PDF** for submission

---

## 📁 Repository Structure

```
Instagram_Thrift_Store/
├── Instagram_Thrift_Store.dbml   ← Full schema in DBML
└── README.md                      ← This file
```

---

*Built for Web Dev Cohort 2026.*
