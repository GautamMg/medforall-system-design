# Assignment 4 — System Design: Multi-Clinic Appointment Scheduling

Technical design document for a scheduling system supporting 500 clinics, 50,000 appointments/day, real-time availability, and complex resource constraints.

**[Read the full design ->](./SYSTEM_DESIGN.md)**

---

## Quick orientation

The design is a single Markdown document with diagrams. No setup required — just open the link above.

**Sections at a glance:**

1. **System Overview** — architecture, capacity planning (back-of-envelope math), technology choices
2. **Data Model** — PostgreSQL schema, exclusion constraints for double-booking prevention
3. **Availability Engine** — slot computation algorithm, concurrent booking handling, timezone/DST
4. **API Design** — REST endpoints, real-time WebSocket channel, caching + rate limiting
5. **Frontend Architecture** — state management, optimistic updates, collaborative scheduling, offline sync
6. **Security & Multi-Tenancy** — clinic isolation via RLS, PHI protection, RBAC
7. **Scalability & Reliability** — failure modes, database scaling phases, consistency model
8. **Observability** — structured logging, metrics, alerting, the "slot explanation" debug endpoint
9. **Rollout Strategy** — phased deployment with feature flags

---

## Key design decisions (and why)

| Decision | Rationale |
|---|---|
| **Availability as a derived projection** | Appointments/reservations are the source of truth. Availability is computed, not stored — eliminates an entire class of consistency bugs. |
| **PostgreSQL exclusion constraints** for double-booking prevention | Correctness is enforced at the DB level, not application logic. Two racing API servers can't both succeed — the constraint rejects the overlap. |
| **Sub-resource modeling** for room/equipment capacity | Simpler and more correct than aggregation-based capacity counting. Trades some flexibility for the guarantee that the exclusion constraint handles everything uniformly. |
| **Real-time as cache invalidation signals** | WebSocket events tell clients *what changed*, not *the new state*. Clients refetch via normal API calls. Simple, scalable, no complex state sync protocol. |
| **Transactional outbox** for side effects | Notifications and EHR sync are async but guaranteed. No "appointment booked but reminder never sent" failures. |

---

## What I'd improve with more time

These aren't gaps in the design as such — they're just the next iteration I would prioritize if this were a real project:

- **Soft holds during booking flow** — temporary reservation with TTL so a patient doesn't lose a slot while filling out a form. Requires a cleanup job for expired holds.
- **Smarter slot ranking** — weight slots by provider preference, patient history, room proximity, and load balancing. Currently slots are returned in chronological order.
- **Formal access control matrix** — the design describes RBAC at a high level. A production system needs a detailed permission matrix (who can reschedule across clinics, who can view audit logs, etc.).
- **Capacity modeling without sub-resources** — the sub-resource approach works well but gets unwieldy for high-capacity resources (e.g., a 20-chair infusion center). Aggregation-based capacity with careful locking would be the next step.
- **Load testing results** — I'd want to validate the capacity estimates with actual benchmarks against the availability computation query under realistic data volumes.

---

## File structure

```
assignment-4-system-design/
|-- assets/
|-- SYSTEM_DESIGN.md    # Complete technical design document
|-- README.md           # This file
```