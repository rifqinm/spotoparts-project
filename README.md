# SP Otoparts ERP System — Modular Event-Driven Architecture

## Overview

SP Otoparts ERP is a modular, event-driven enterprise system designed to manage the entire business process — from purchasing and production to inventory, sales, finance, and HR — with automation, consistency, and fault tolerance.
It combines real-time speed with batch-level accuracy, supported by a fully synchronized PostgreSQL architecture.

---

## Architecture Overview

```
[Purchase & Request Flow]
    |
    v
[Warehouse & Stock Engine] --> [Stock Balance Quick View]
|      |
|      --> Real-time validation + anomaly detection
v
[FIFO + HPP Queue Engine]
|
v
[Accounting & Financial Journal]
|
v
[Dashboard & Reporting Layer]
|
--> [HR + Attendance + Issue Tracking]
```

* Every transaction flows through an event queue to ensure consistency across departments
* Stock movement acts as the single source of truth for all inventory changes
* FIFO and HPP queues ensure accurate cost-of-goods calculations without performance loss
* Finance modules automatically log journal entries based on operational data
* HR and Attendance modules integrate employee presence and activity logging

---

## Tech Stack

* Frontend: React 18, TypeScript, TailwindCSS
* Backend: Go (Gin Framework, pgxpool, Redis client)
* Database: PostgreSQL (functions, triggers, materialized views)
* Cache / Queue: Redis (real-time validation and async processing)
* Infrastructure: Docker Compose, GitHub Actions (self-hosted runner), Ubuntu Server
* Security: Role-based Access Control, Page Access, Cloudflare Tunnel

---

## Core Capabilities

### Stock & Warehouse

* Real-time stock validation using SUM(inbound − outbound)
* FIFO-based queue for batch-level HPP precision
* Automatic anomaly detection (late input, negative stock, missing gate)
* Checkpoint and recovery system for isolated SKU repair

### Purchasing & Production

* Full purchasing flow: Request → PO → Setor / Outsourcing → Finishing → FG
* Item-level traceability through order reference
* Integrated cost tracking for assemblies and components

### Sales & Distribution

* End-to-end flow: SO → SJ → NC → DO
* Automatic backorder and plan alignment
* Real-time stock linkage on every order

### Finance & Accounting

* Automated journal entries based on system events
* Integrated customer and supplier payment tracking
* Master account mapping with ledger synchronization
* Grade-based customer credit limit (plafon) management

### Human Resource

* Attendance tracking with RFID integration
* Policy-based scheduling per department or unit
* Employee activity and audit logs

### System & Monitoring

* Centralized system log for critical actions
* Issue tracking with comments, priority, and department routing
* Branding and production time tracking
* Background queues for recalculation and cache refresh

---

## Design Principles

* Domain Modularity: Each department operates independently but stays synchronized through event queues
* Partial Refresh: Only changed data is recalculated
* Asynchronous Processing: Non-blocking transactions for high performance
* Anomaly Tolerance: System detects and isolates errors without corrupting historical data
* Human-Proof Input: Human input mistakes are logged and auditable, never destructive

---

## Checkpoint & Recovery

* Automatic checkpointing prevents backdated corruption
* Repair queues (stock_fix_queue, hpp_queue) only process affected SKUs
* No full rebuild — keeping uptime and accuracy intact

---

## Results

* ⚡ Real-time validation under 100 ms
* 💰 Accurate batch-level HPP
* 🧩 Modular and scalable across multiple warehouses
* 🛡️ Zero minus-stock incidents
* 🤖 Automatic anomaly detection and correction queues

---

## My Role

* Designed the overall system architecture and data flow
* Built PostgreSQL schema, triggers, functions, and optimized queries
* Developed backend services in Go using repository-service-handler structure
* Implemented Redis caching and async event queues
* Created frontend dashboards using React + TypeScript + TailwindCSS
* Set up CI/CD automation with Docker and GitHub Actions

---

## Roadmap

* ✅ Stable warehouse, purchasing, and stock modules
* ✅ FIFO & HPP queue system integrated
* ✅ Accounting and journal automation implemented
* ✅ HR & Attendance system connected
* ⚠️ Anomaly audit dashboard under development
* ⚠️ Cache invalidation and performance monitoring planned

---

## Key Quote

> One source of truth, many departments — all synchronized by events.

---

## License

Part of SP Otoparts Internal ERP System — not for public distribution.
© 2025 M. Novel Rifqi
