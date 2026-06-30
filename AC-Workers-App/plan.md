# AC Workers App — plan.md

> Engineering & delivery plan for the **AC field-service attendance & job-management system** ("CoolCrew").
> Pairs with [DESIGN.md](DESIGN.md), which covers the UI/UX and screen specs.

---

## 1. Scope Summary

Two products on one backend:

1. **Worker Mobile App** — punch in/out with geo-location, view & complete assigned daily calls, carry/return tools & parts with full accountability. **Offline-first.**
2. **Admin/Manager Web Dashboard** — oversee attendance & live locations, add/manage employees, assign daily calls, manage clients/sites, and reconcile inventory. Roles: **Worker · Manager/Admin · Super Admin**.

**Core promises the build must guarantee:**
- A punch is always tied to a **verified time + GPS location** (and optional selfie).
- Items that leave the office stay **pending on the worker** until `returned_qty == taken_qty`.
- Nothing is lost on bad signal — **offline capture + sync**.

---

## 2. Recommended Tech Stack

| Layer | Choice | Why |
|-------|--------|-----|
| Worker mobile | **Flutter (Dart)** + Riverpod | One codebase iOS+Android, great offline + maps + camera, smooth UI |
| Local store (offline) | SQLite (Drift/Isar) + sync queue | Punches/jobs/tool ops captured offline, synced when online |
| Admin web | **Next.js (React) + TypeScript** + Tailwind + shadcn/ui | Fast responsive dashboard, tables, charts |
| Backend API | **Node.js + Fastify** (or NestJS) + TypeScript | Typed, fast, simple REST/RPC |
| Database | **PostgreSQL** + Prisma ORM + PostGIS | Relational data + geospatial queries for locations/geofence |
| Auth | JWT (access+refresh) + **phone OTP** via SMS gateway; PIN/biometric unlock on device | Workers sign in by phone; managers by email/password + optional 2FA |
| Maps & geo | Google Maps SDK (mobile) + geocoding/directions; geofence radius config | Navigation, pins, "at-site" detection |
| Storage | S3 / MinIO | Punch selfies, job photos, signatures |
| Push | Firebase Cloud Messaging | New call assigned, return reminders |
| Realtime (optional) | WebSocket/SSE | Live worker map on dashboard |
| Infra/DevOps | Docker, managed Postgres, object storage, CI/CD | Reproducible deploys |

> Stack is a recommendation — swap freely (e.g., React Native instead of Flutter, Supabase/Firebase instead of self-hosted Postgres). The data model and APIs below are stack-agnostic.

---

## 3. Data Model (core entities)

```
Organization ──1:N── Branch ──1:N── User
User { id, name, phone, email?, role[super_admin|admin|worker],
       employeeId, branchId, photoUrl, status, pinHash?, defaultShift }

Client ──1:N── Site ──1:N── ACUnit
Client { id, name, phone, notes }
Site   { id, clientId, address, geo(lat,lng), landmark }
ACUnit { id, siteId, brand, model, capacityTon, type, installDate, warrantyUntil?, amc? }

Call (Job) { id, clientId, siteId, acUnitId?, type[install|service|repair|amc],
             scheduledStart, scheduledEnd, priority, assignedWorkerId, status,
             brief, checklist[], outcome?, completedGeo?, createdBy }
  status: assigned → started → arrived → completed | could_not_complete

Attendance (Punch) { id, workerId, type[in|out], timestamp, geo(lat,lng),
                     accuracy, selfieUrl?, siteId?|callId?|office, source[online|offline-synced] }

InventoryItem { id, name, category[tool|spare_part|consumable], unit,
                stockQty, reorderLevel, barcode? }

ToolTxn (ledger) { id, itemId, workerId, callId?, type[checkout|return|consume|loss|damage],
                   qty, reason?, timestamp, reconciledBy? }

ToolHolding (derived/maintained) { workerId, itemId,
            takenQty, returnedQty, pendingQty = takenQty − returnedQty, status }
  status: pending (pending>0) | partial (0<returned<taken) | returned (pending==0)

WorkLog { id, callId, photos[], notes, partsUsed[], signatureUrl?, timestamp }
AuditLog { id, actorId, action, entity, before, after, timestamp }   # super-admin sensitive ops
```

**Key rules**
- `ToolHolding.pendingQty` drives the worker's Toolbag list; an item disappears from "pending" only when `pendingQty == 0`.
- A checkout decrements `InventoryItem.stockQty` and increments the holding; a return reverses it (unless consumed/lost/damaged, which is written off with a reason).
- Attendance & tool/job mutations are **idempotent** (client-generated UUIDs) so offline replays don't duplicate.

---

## 4. API Surface (representative)

```
Auth:      POST /auth/otp/request · /auth/otp/verify · /auth/refresh · /auth/pin
Worker me: GET  /me · GET /me/calls?date · GET /me/toolbag · GET /me/attendance
Punch:     POST /attendance/punch        { type, geo, accuracy, selfieUrl?, callId?, clientUuid }
Calls:     GET/POST/PATCH /calls · PATCH /calls/:id/status · POST /calls/:id/worklog
Tools:     POST /tools/checkout · POST /tools/return · GET /inventory · GET /inventory/holdings
Admin:     GET/POST/PATCH /workers · GET /attendance?date&branch · GET /reports/*
Sync:      POST /sync/batch  (offline queue replay, idempotent by clientUuid)
Realtime:  WS /live/locations
```
All endpoints role-guarded per the matrix in [DESIGN.md §4.3](DESIGN.md).

---

## 5. Build Phases & Milestones

> Suggested phasing. Adjust durations/effort to your team. Each phase ends with something demoable.

### Phase 0 — Foundations
- Repo, environments, CI/CD, Docker.
- Postgres + Prisma schema (entities above), seed data.
- Auth: phone OTP + JWT + roles; device PIN.
- **Demo:** log in as worker and as manager.

### Phase 1 — Attendance core (the "punch")
- Worker app shell (4 tabs) + Today screen.
- Punch in/out with GPS capture (+ optional selfie), geofence config.
- Dashboard: daily attendance table + live map of punches.
- Offline punch capture + sync.
- **Demo:** worker punches in/out from a phone; manager sees it live with location.

### Phase 2 — Calls / job management
- Manager: create & assign calls (board/calendar), clients/sites/AC units.
- Worker: Calls list/map, Call detail, status state-machine, navigate, work log (photos/notes), complete.
- Push notification on new assignment.
- **Demo:** manager assigns 10 calls; worker runs the day and completes them.

### Phase 3 — Tools & inventory accountability
- Inventory catalog + stock.
- Worker: Toolbag, Checkout (take items + qty), Return (full/partial/consume/loss).
- Pending logic (`pendingQty`) + Toolbag list behavior.
- Dashboard: who-holds-what, checkout/return ledger, overdue flags, reconcile/write-off.
- **Demo:** worker takes 10 bolts, returns 6 → 4 stay pending → manager reconciles.

### Phase 4 — Oversight, reports & admin
- Overview dashboard KPIs + attention list.
- Worker detail (attendance/calls/toolbag/performance), add/manage employees.
- Reports (attendance, productivity, tool reconciliation) + CSV/PDF export.
- Super-admin: roles/admins, branches, shop config, audit log.
- **Demo:** end-to-end management view across a full day.

### Phase 5 — Hardening, polish & launch
- Offline edge cases & sync conflict handling; concurrency (no double-spend of stock).
- Performance, empty/error/offline states, accessibility & field-readiness pass.
- Security review (authz on every endpoint, OTP rate-limiting, file access).
- App store builds, deploy, monitoring/logging, backups.
- **Deliverable:** production launch.

---

## 6. Non-Functional Requirements

- **Offline-first** worker app (punch, job updates, tool ops queue & sync; idempotent).
- **Location integrity:** store accuracy; flag low-accuracy/mocked locations; geofence "at-site" check.
- **Security:** role-based authz on every endpoint, OTP rate limiting, signed URLs for photos, encrypted secrets, audit log for sensitive actions.
- **Privacy:** location collected only while on duty; clear consent; retention policy.
- **Reliability:** no double-booking of stock; transactional checkout/return; daily DB backups.
- **Performance:** dashboard tables paginated; lists virtualized; maps clustered at scale.
- **Observability:** structured logs, error tracking, basic metrics dashboard.
- **Localization:** English + one regional language at launch; i18n-ready.

---

## 7. Open Questions (confirm with client before/early in build)

1. **Single shop or multiple branches?** (Decides whether multi-branch/super-admin is launch-scope.)
2. **Selfie at punch — required or optional?** Geofence radius for "at-site"?
3. **Consumables (gas/bolts):** do partially-used items need exact reconciliation, or just "used at site"?
4. **Worker self-checkout of tools** vs. **storekeeper issues tools** at the office counter?
5. **Customer-facing piece?** (e.g., SMS to client when technician is on the way) — in scope or later?
6. **Payroll/payment integration** (hours → salary) — in scope or export-only?
7. **Platforms:** Android-only first, or iOS + Android together?
8. **SMS provider & maps provider** preferences (cost/region).

---

## 8. Deliverables Checklist

- [ ] Worker mobile app (Android + iOS) — offline-capable.
- [ ] Admin/Manager web dashboard (responsive).
- [ ] Backend API + PostgreSQL DB + object storage.
- [ ] Role-based access (Worker / Manager / Super Admin).
- [ ] Push notifications + SMS OTP integration.
- [ ] Reports & CSV/PDF exports.
- [ ] Deployment, monitoring, backups, and handover docs.

---

*UI/UX, screens, and design tokens live in [DESIGN.md](DESIGN.md).*
