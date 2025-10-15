# 🧭 Stock & HPP ERP System — Event-Driven Architecture

## 📌 Overview
This project is a **modular, event-driven ERP system** built to handle **real-time stock management, FIFO (HPP calculation), and anomaly detection** for an automotive parts company.  
The system is designed to be **fast (<100ms stock validation)**, **accurate (batch-level HPP)**, and **resilient against human errors**.

---

## 🏗️ Architecture

[Warehouse & Gate Manager]
│
▼
[Stock Movement] ──▶ [Stock Balance Quick View]
│ │
│ └─▶ used for real-time stock validation
▼
[FIFO Queue (stock_inbound_remaining)]
│
▼
[HPP Queue & Worker]
│
▼
[Accounting Service]


- All transactions are recorded in `stock_movement` as a **single source of truth**.  
- `Stock Balance Quick View` keeps a **summed stock state** (inbound - outbound) for quick validation on every order.  
- FIFO queues manage only active batches (remaining_qty > 0) for **efficient HPP accuracy**.  
- Accounting is updated asynchronously through **queue events** to maintain performance.

---

## ⚙️ Tech Stack

| Layer | Technology |
|:------|:------------|
| **Frontend** | React 18, TypeScript, TailwindCSS |
| **Backend** | Go (Gin Framework, pgxpool, Redis client) |
| **Database** | PostgreSQL (triggers, functions, materialized views) |
| **Cache** | Redis (for real-time stock validation) |
| **Infra & DevOps** | Docker Compose, GitHub Actions (self-hosted runner), Ubuntu Server, Cloudflare Tunnel |

---

## 🧮 Core Concepts

### 1. Real-Time Stock Validation
- Validation uses a **simple SUM(inbound - outbound)** formula for speed.  
- Sales Orders lock stock via `stock_reservation`.  
- If stock is insufficient, the system automatically generates a **backorder**.

### 2. FIFO Live Queue
- Maintains only active batches (`remaining_qty > 0`).
- When a batch is depleted → it’s automatically removed from the queue.
- Late inputs trigger **partial reload** from the affected checkpoint.
- Efficient and minimal recalculation ensures **fast and accurate HPP**.

### 3. HPP (Cost of Goods) Queue System
- `fn_calc_hpp()` → recalculates asynchronously.  
- `fn_get_hpp()` → retrieves real-time snapshot of current HPP.
- Changes in price or retroactive inputs automatically send **events** to reprocess affected data.

### 4. Anomaly Detection
The system automatically detects and marks operational anomalies:
| Code | Meaning |
|------|----------|
| `LATE_INPUT` | Created date > actual transaction date |
| `NEGATIVE_STOCK` | Stock balance below 0 |
| `MISSING_GATE` | Gate ID missing from movement record |

---

## 🧠 Design Principles

| Principle | Description |
|:-----------|:-------------|
| **Separation of Layers** | Fast validation ≠ Accurate calculation |
| **Event-driven** | All updates go through queues, not cascaded SQL triggers |
| **Partial Refresh** | Only changed data is recalculated |
| **Tolerant to Errors** | Detects anomaly, doesn’t crash |
| **Human-proof** | Input mistakes are logged, not destructive |

---

## 🚨 Checkpoint & Recovery System
- Negative stock automatically creates a **checkpoint**, preventing backdated corruption.  
- Each SKU can have multiple checkpoints as operational indicators.  
- `stock_fix_queue` repairs only affected SKUs (no full refresh).

---

## 🚀 Results
- ⚡ Real-time validation under **100 ms**
- 💰 Accurate **batch-level HPP**
- 🧩 Modular and scalable across multiple warehouses
- 🛡️ Zero minus-stock incidents
- 🤖 Automatic anomaly detection and correction queues

---

## 👨‍💻 My Role
I was responsible for **the entire architecture and implementation**, including:
- Database design (schemas, triggers, functions, views)
- Go backend (repository, service, handler layers)
- Redis caching and async queue logic
- Frontend dashboard (React + TypeScript + Tailwind)
- CI/CD pipeline via GitHub Actions + Docker Compose

---

## 🗺️ Project Roadmap
1. ✅ Stabilize warehouse + gate + stock movement modules  
2. ✅ Build real-time stock balance view  
3. ✅ Implement FIFO queue (active batches only)  
4. ✅ Integrate async HPP queue  
5. ✅ Add checkpoint & fix queue system  
6. 🚧 Develop anomaly audit dashboard  
7. 🚧 Add Redis cache invalidation & performance monitor  

---

## 🧩 Key Quote
> **“FIFO for accuracy, SUM for speed, Queue for peace of mind.”**

---

## 📁 Related Modules
- `Department/SupplyChain/stock_service`
- `Department/FinanceHrGa/accounting_service`
- `internal/queue`
- `internal/db`
- `internal/router`

---

## 📸 Screenshots (Coming Soon)
- Real-time stock dashboard  
- FIFO & HPP visualization  
- Audit & anomaly report view  

---

## 🧱 License
This project is part of the **SP Otoparts Internal ERP**, not for public distribution.  
© 2025 M. Novel Rifqi
