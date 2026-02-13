# Assignment 4 — System Design (Multi-Clinic Appointment Scheduling)

This folder contains my **System Design** submission for MedForAll.

- **Main document:** [`SYSTEM_DESIGN.md`](./SYSTEM_DESIGN.md) (includes Mermaid diagrams)

---

## How to review

1. Open `SYSTEM_DESIGN.md` in GitHub.
2. Mermaid diagrams render directly in GitHub (no setup required).

---

## What this design covers (high level)

- Multi-clinic scheduling at scale (hundreds of clinics, high daily appointment volume)
- Real-time availability updates for web and mobile
- Scheduling rules + exceptions (working hours, PTO/holidays, buffers)
- Resource constraints (provider / room / equipment)
- Strong consistency to prevent double booking
- Auditability and operational readiness (logging/metrics/alerts)

---

## Repository contents

- `SYSTEM_DESIGN.md` — complete written design
- `README.md` — this overview

---

## What I would improve with more time

* Support true multi-resource capacity without sub-resource modeling (careful locking + aggregation).
* Add smarter slot ranking (provider preferences, patient preferences, load balancing across rooms).
* Add “soft holds” for booking flow (temporary reservation holds during checkout) with TTL and cleanup.
* Add privacy threat model and formal access control matrix (RBAC/ABAC per clinic/provider).
