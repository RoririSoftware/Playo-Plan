# AC Workers App — DESIGN.md

> Design specification for an **AC field-service attendance & job-management system**.
> This document is written to be **copy-pasted into AI design tools** (v0, Lovable, Figma AI, Galileo, Uizard) and to brief human designers. Every screen, state, and design token is spelled out.

**Working product name:** *CoolCrew* (placeholder — rename freely)
**Surfaces:** (1) **Worker Mobile App** (field technicians), (2) **Admin/Manager Web Dashboard** (managers + super admins).

---

## 1. Product Vision

An AC repair company runs a field workforce. Technicians ("workers") drive to client sites all day to install/service air-conditioners. The company needs to know:

1. **Who is working, where, and when** — geo-tagged punch in / punch out at each site.
2. **What each worker has to do today** — managers assign a list of daily calls (≈10 client visits) with location and job details.
3. **What tools & parts left the office and haven't come back** — every pipe, nut, bolt, gas cylinder, and tool a worker carries stays **pending on that worker** until returned and reconciled at the office.

Managers and super admins oversee all of it, add/manage employees, assign work, and reconcile inventory.

**Design north star:** *Super clean, super minimal, super usable by a non-technical person wearing work gloves in direct sunlight.*

---

## 2. Design Principles

| # | Principle | What it means in practice |
|---|-----------|---------------------------|
| 1 | **One job per screen** | Each screen has a single primary action. The hero action is the biggest thing on screen. |
| 2 | **Thumb-first** | Primary buttons sit in the bottom 30% of the phone screen. Min touch target **48×48dp**. |
| 3 | **Glanceable** | Status is shown with color + icon + label (never color alone). Readable outdoors at arm's length. |
| 4 | **Forgiving** | Confirm destructive actions. Offline by default — never lose a punch or a tool record due to bad signal. |
| 5 | **Low literacy friendly** | Plain words, big numbers, icons paired with short labels. Avoid jargon and dense tables on mobile. |
| 6 | **Trust through clarity** | Location, time, and quantity are always shown explicitly so workers and managers both trust the record. |

---

## 3. Design Tokens

> The single source of truth for **CoolCrew's** look & feel — clean, minimal, outdoor-readable.
> **Brand feel:** *cool, calm, trustworthy — "the comfort of a perfectly cooled room."* Crisp blues + a cyan cooling accent on lots of white space, with big confident numbers. Light theme (technicians work in daylight). Status is never color-only — always pair color + icon + word. All text meets WCAG AA.

### 3.1 Color

**Primary — Cool Blue** (brand anchor = `primary-600`).

| Token | Hex | | Token | Hex |
|-------|-----|--|-------|-----|
| `primary-50` | `#EEF4FE` | | `primary-500` | `#2A6FE4` |
| `primary-100` | `#D9E7FD` | | **`primary-600`** | **`#1565D8`** |
| `primary-200` | `#B4CFFA` | | `primary-700` | `#0F4FB0` |
| `primary-300` | `#84AEF5` | | `primary-800` | `#103F88` |
| `primary-400` | `#4F89EE` | | `primary-900` | `#11366B` |
| | | | `primary-950` (navy) | `#0B2545` |

**Accent — Cooling Cyan** (sparingly: progress, active pin, small emphasis — not for CTAs).

| Token | Hex | | Token | Hex |
|-------|-----|--|-------|-----|
| `accent-100` | `#CCF6FB` | | **`accent-500`** | **`#19C3D6`** |
| `accent-300` | `#5DDDEC` | | `accent-600` | `#109FB0` |
| `accent-400` | `#2EC8DD` | | `accent-700` | `#117E8C` |

**Neutrals — Cool Slate.**

| Token | Hex | Use | | Token | Hex | Use |
|-------|-----|-----|--|-------|-----|-----|
| `white` | `#FFFFFF` | Surfaces | | `neutral-400` | `#AAB6C7` | Placeholder/disabled |
| `neutral-50` | `#F5F7FA` | App bg | | `neutral-500` | `#7A8AA0` | Muted/labels |
| `neutral-100` | `#ECEFF4` | Subtle fill | | `neutral-700` | `#3A4A61` | Body text |
| `neutral-200` | `#E4E9F0` | Borders | | `neutral-900` | `#0B2545` | Headings (navy) |
| `neutral-300` | `#CFD7E2` | Input outline | | | | |

**Semantic / status** (base + tint bg).

| Role | Base | Tint | Used for |
|------|------|------|----------|
| `success` | `#16A34A` | `#E7F6EE` | Present · Completed · Returned in full |
| `warning` | `#F59E0B` | `#FEF3E2` | Pending / Partial returns · Due soon |
| `danger` | `#E11D48` | `#FCE8EC` | Overdue / Lost · Missed call · Errors |
| `info` | `#0EA5E9` | `#E3F4FD` | Neutral info · Upcoming · On-field |

**Semantic roles** (design with names, not raw hex): `bg`=neutral-50 · `surface`=white · `border`=neutral-200 · `text`=neutral-700 · `text-strong`=neutral-900 · `text-muted`=neutral-500 · `primary`=primary-600 · `primary-hover`=primary-700 · `accent`=accent-500 · `focus-ring`=`rgba(21,101,216,0.30)`.

**App status → token:** Present/On-duty → `success` · Off-duty/Absent → `neutral-500` · On-field/In-progress → `info` · Upcoming/Assigned → `primary` tint · Completed → `success` · Could-not-complete → `danger` · Tool Pending/Partial → `warning` · Tool Returned → `success` · Tool Overdue/Lost → `danger`.

### 3.2 Typography

- **UI font:** `Inter` (system fallback). **Display/numbers:** `Manrope` (optional) for big stats. **Tabular figures** for time, hours, quantities, counts.
- **Weights:** regular 400 · medium 500 · semibold 600 · bold 700.

| Style | Size / Line | Weight | Use |
|-------|-------------|--------|-----|
| `display` | 32 / 40 | 700 | Hero stat numbers |
| `h1` | 24 / 32 | 700 | Screen titles |
| `h2` | 20 / 28 | 600 | Section headers |
| `h3` | 17 / 24 | 600 | Card titles |
| `body` | 15 / 22 | 400 | Default text (`body-strong` = 600) |
| `label` | 13 / 18 | 500 | Field labels, captions |
| `micro` | 11 / 16 | 600 | Badges, overlines (UPPERCASE +2%) |

### 3.3 Spacing, Sizing, Radius

- **Spacing (4px grid):** 4 · 8 · 12 · 16 · 20 · 24 · 32 · 40 · 48 · 64. Mobile screen padding = 16.
- **Touch & sizing:** min touch target **48** · primary CTA height **52** · input/button **48** · icon button **44** · icons 16/20/**24**/32 · avatar 40 · tab bar 64 · app bar 56.
- **Radius:** chips 8 · inputs/buttons **12** · cards **16** · sheets/modals **24** · pills/avatars full.

### 3.4 Elevation, States, Motion

- **Shadows** (cool navy-tinted, low opacity): `sm` cards `0 2px 8px rgba(11,37,69,.06)` · `md` `0 6px 16px rgba(11,37,69,.08)` · `lg` modals `0 12px 32px rgba(11,37,69,.12)` · `sheet` `0 -4px 16px rgba(11,37,69,.10)` · `focus-ring` `0 0 0 3px rgba(21,101,216,.30)`.
- **Interaction:** hover overlay `rgba(11,37,69,.04)` · pressed `rgba(11,37,69,.08)` · disabled opacity `.45` · scrim `rgba(11,37,69,.45)`.
- **Motion:** durations fast 150 / **base 200** / slow 300 / celebrate 500ms · easing `cubic-bezier(.2,0,0,1)`. **Signature:** drawn checkmark + light haptic on Punch In/Out and Tool Return.
- **Icons:** Lucide / Phosphor, 2px stroke, 24px default — always paired with a label.

### 3.5 Web Dashboard layout

- **Breakpoints:** sm 640 · md 768 · lg 1024 · xl 1280 · 2xl 1536. Sidebar 256 · content max 1440.
- **Z-index:** dropdown 1000 · sticky 1100 · overlay 1200 · modal 1300 · toast 1400 · tooltip 1500.

### 3.6 Core Components (build once, reuse — recipes in tokens)

- **Primary button:** bg `primary` → hover `primary-700` · text white · height 52 · radius 12 · `body-strong` · focus-ring. **Secondary:** white bg, border `neutral-300`, text `primary`. **Destructive:** bg `danger`.
- **Input:** white bg · border `neutral-300` · radius 12 · height 48 · focus → `primary` border + focus-ring · placeholder `neutral-400`.
- **Card:** `surface` bg · 1px `border` · radius 16 · `shadow-sm` · padding 16.
- **Status Pill:** tint bg + base icon/text (§3.1) · radius full · `micro` · icon + label.
- **Stat Tile:** `display` number + `label` caption, on a card.
- **Call/Job Card:** client, address, time window, status pill, distance, chevron.
- **Tool/Item Row:** item + icon, qty taken / returned / pending, status pill.
- **Quantity Stepper:** big −/＋ + editable field (48px, gloves-friendly).
- **Map Snippet:** mini map + single pin + "Navigate". **Bottom Sheet:** white, radius 24 top, `shadow-sheet`, scrim. **Top App Bar:** title + optional back + 1 action. **Bottom Tab Bar:** 4 tabs max.
- **Empty / Loading / Error / Offline:** every list defines all four (see §8).

### 3.7 Token export (paste into tools / code)

<details><summary>CSS custom properties</summary>

```css
:root{
  --primary-600:#1565D8; --primary-700:#0F4FB0; --primary-950:#0B2545; --accent-500:#19C3D6;
  --neutral-50:#F5F7FA; --neutral-200:#E4E9F0; --neutral-500:#7A8AA0; --neutral-700:#3A4A61; --neutral-900:#0B2545;
  --success:#16A34A; --warning:#F59E0B; --danger:#E11D48; --info:#0EA5E9;
  --bg:var(--neutral-50); --surface:#fff; --border:var(--neutral-200);
  --text:var(--neutral-700); --text-strong:var(--neutral-900); --text-muted:var(--neutral-500);
  --color-primary:var(--primary-600); --focus-ring:0 0 0 3px rgba(21,101,216,.30);
  --radius-md:12px; --radius-lg:16px; --radius-xl:24px;
  --shadow-sm:0 2px 8px rgba(11,37,69,.06); --shadow-sheet:0 -4px 16px rgba(11,37,69,.10);
  --font-ui:'Inter',system-ui,sans-serif; --font-display:'Manrope','Inter',sans-serif;
}
```
</details>

<details><summary>Tailwind theme.extend</summary>

```js
colors:{
  primary:{50:'#EEF4FE',100:'#D9E7FD',200:'#B4CFFA',300:'#84AEF5',400:'#4F89EE',500:'#2A6FE4',600:'#1565D8',700:'#0F4FB0',800:'#103F88',900:'#11366B',950:'#0B2545'},
  accent:{100:'#CCF6FB',300:'#5DDDEC',400:'#2EC8DD',500:'#19C3D6',600:'#109FB0',700:'#117E8C'},
  neutral:{50:'#F5F7FA',100:'#ECEFF4',200:'#E4E9F0',300:'#CFD7E2',400:'#AAB6C7',500:'#7A8AA0',600:'#5B6B83',700:'#3A4A61',800:'#25324A',900:'#0B2545'},
  success:'#16A34A',warning:'#F59E0B',danger:'#E11D48',info:'#0EA5E9',
},
borderRadius:{md:'12px',lg:'16px',xl:'24px'},
fontFamily:{sans:['Inter','system-ui','sans-serif'],display:['Manrope','Inter','sans-serif']},
```
</details>

<details><summary>Flutter / Dart (worker app)</summary>

```dart
class AppColors{
  static const primary=Color(0xFF1565D8), navy=Color(0xFF0B2545), accent=Color(0xFF19C3D6);
  static const bg=Color(0xFFF5F7FA), surface=Color(0xFFFFFFFF), border=Color(0xFFE4E9F0);
  static const text=Color(0xFF3A4A61), textMuted=Color(0xFF7A8AA0);
  static const success=Color(0xFF16A34A), warning=Color(0xFFF59E0B), danger=Color(0xFFE11D48), info=Color(0xFF0EA5E9);
}
final coolCrewTheme=ThemeData(
  useMaterial3:true, scaffoldBackgroundColor:AppColors.bg, fontFamily:'Inter',
  colorScheme:ColorScheme.fromSeed(seedColor:AppColors.primary, primary:AppColors.primary,
    secondary:AppColors.accent, surface:AppColors.surface, error:AppColors.danger),
);
```
</details>

> Tokens are a proposal — easy to re-skin (e.g., swap the brand blue) before client sign-off.

---

## 4. Information Architecture

### 4.1 Worker Mobile App — navigation map

```
Splash
  └─ Auth (Phone → OTP → optional set 4-digit PIN)
        └─ App Shell (Bottom Tab Bar — 4 tabs)
              ├─ TODAY (Home)        ← hero: Punch In/Out + today's stats + next call
              ├─ CALLS (My Jobs)     ← today's assigned calls, list + map toggle
              │     └─ Call Detail   ← client, AC unit, navigate, checklist, complete
              ├─ TOOLBAG (My Items)  ← items I'm carrying, pending returns
              │     ├─ Checkout flow (take items)
              │     └─ Return flow (return at office)
              └─ PROFILE             ← me, attendance history, settings, help, logout
```

### 4.2 Admin / Manager Web Dashboard — navigation map

```
Login
  └─ Dashboard Shell (left sidebar nav)
        ├─ OVERVIEW        ← KPIs + live worker map + today's pulse
        ├─ ATTENDANCE      ← daily punches, map, timesheets, exports
        ├─ WORKERS         ← employee list, add employee, worker detail
        ├─ CALLS / JOBS    ← schedule board + calendar, assign calls, call detail
        ├─ CLIENTS / SITES ← client directory, site & AC-unit details, service history
        ├─ INVENTORY/TOOLS ← catalog, stock, checkout ledger, who-holds-what, pending
        ├─ REPORTS         ← attendance, productivity, tool reconciliation
        └─ SETTINGS        ← roles & admins, branches, shop config, audit log (super admin)
```

### 4.3 Roles & access (drives what each screen shows)

| Capability | Worker | Manager / Admin | Super Admin |
|---|:--:|:--:|:--:|
| Punch in/out (self) | ✅ | ✅ | ✅ |
| See own assigned calls & tools | ✅ | ✅ | ✅ |
| View all workers' attendance & location | — | ✅ | ✅ |
| Assign/edit calls | — | ✅ | ✅ |
| Add/edit employees | — | ✅ | ✅ |
| Manage inventory & reconcile tools | — | ✅ | ✅ |
| Manage admins, roles, branches, audit log | — | — | ✅ |

---

## 5. Worker Mobile App — Screen Specs

> Format per screen: **Purpose · Layout (top→bottom) · Components · States · Notes.** Each is detailed enough to generate directly.

### 5.0 Splash
- **Purpose:** brand moment + silent auth/session check.
- **Layout:** centered logo on `primary` gradient (navy→primary), subtle cooling animation. Auto-routes to Auth or Today.

### 5.1 Auth — Phone Entry
- **Purpose:** worker signs in with mobile number.
- **Layout:** Logo → H1 "Welcome back" → caption → country code (+91) + phone input → full-width **Send OTP** (bottom) → small "Trouble signing in? Call office" link.
- **States:** invalid number inline error; loading on Send OTP.

### 5.2 Auth — OTP Verification
- **Layout:** H1 "Enter the code" → caption with masked number + edit link → 6-box auto-focus OTP → resend countdown (30s) → auto-submit on fill.
- **States:** wrong code (shake + danger), expired, loading.

### 5.3 Auth — Set PIN (first login only)
- **Purpose:** fast daily unlock without OTP.
- **Layout:** "Create a 4-digit PIN" → numeric keypad → confirm PIN. Optional biometric toggle. Skippable.

### 5.4 TODAY (Home) — **hero screen**
- **Purpose:** the one screen a worker checks all day. Punch + what's next.
- **Layout (top→bottom):**
  1. **Top bar:** greeting + name, avatar (→Profile), date, small notification bell.
  2. **PUNCH CARD (hero):** giant status — `OFF DUTY` / `ON DUTY since 9:02 AM`. Big primary button:
     - When off duty → **"Punch In"** (blue).
     - When on duty → **"Punch Out"** (outline/danger) + live worked-time counter.
     - Shows current location chip ("📍 Detected: MG Road") and a GPS-status dot.
  3. **Today at a glance:** 3 Stat Tiles — *Calls: 4/10 done · Hours: 3h 12m · Pending tools: 5*.
  4. **Next call card:** the next assigned job (client, time window, distance, **Navigate** + **Start**).
  5. **Pending returns banner** (if any): warning-tinted — "You have 5 items to return to office" → Toolbag.
- **States:** off-duty / on-duty / on-break; GPS off (blocking explainer to enable location); no calls assigned (friendly empty); offline (queued banner).
- **Notes:** Location permission is mandatory to punch. If denied, show a clear "Enable location to punch in" sheet.

### 5.5 Punch In — Confirm Sheet
- **Purpose:** capture a trustworthy attendance record.
- **Layout (bottom sheet):** map snippet with detected pin + accuracy → address text → optional **selfie capture** (if shop requires) → optional "Which site?" picker (link to a today's call or "Office / Other") → big **Confirm Punch In**.
- **States:** low GPS accuracy warning; selfie required vs optional; success → checkmark animation → returns to Today now showing ON DUTY.

### 5.6 Punch Out — Confirm Sheet
- **Layout:** worked-time summary (in/out times, total) → optional note ("Day complete / leaving early") → reminder if pending tools or unfinished calls ("3 calls not completed — punch out anyway?") → **Confirm Punch Out**.

### 5.7 CALLS (My Jobs) — List
- **Purpose:** today's assigned visits.
- **Layout:** Top bar "Today's Calls" + **List/Map toggle** + date selector (default today). Summary strip (Assigned 10 · Done 4 · Pending 6). Vertical list of **Call Cards** grouped by status (Up next → Upcoming → Completed).
- **Call Card:** client name, service type tag (Install / Service / Repair / AMC), address (1 line), time window, **distance**, status pill, chevron.
- **Map mode:** full map with pins (color by status), tap pin → mini call card → open detail.
- **States:** empty ("No calls assigned yet — check with your manager"), loading skeleton, offline (last-synced badge).

### 5.8 Call Detail
- **Purpose:** everything needed to do the job + mark progress.
- **Layout (top→bottom):**
  1. Header: client name, status pill, service type.
  2. **Map snippet + Navigate** (opens Google/Apple Maps) + **Call client** button.
  3. **Client & site info:** address, contact, gate/landmark notes.
  4. **AC unit details:** brand, model, capacity (ton), type (split/window/cassette), last service date, warranty flag.
  5. **Job brief / complaint:** what to do (text from manager).
  6. **Checklist** (optional, manager-defined): tick tasks.
  7. **Tools for this job:** quick link to add items to Toolbag tagged to this call.
  8. **Work log:** add photos (before/after), notes, parts used.
  9. **Sticky bottom CTA:** state machine → **Start Job** → **Mark Arrived** (auto geo-check) → **Complete Job**. Completing prompts: outcome (Done / Needs revisit / Customer not available), optional signature/photo.
- **States:** not started / in progress / completed / could-not-complete; offline (changes queue).

### 5.9 TOOLBAG (My Items) — **the inventory-accountability screen**
- **Purpose:** show exactly what the worker is holding and what's still pending return. This is the core "tool stays pending until returned" feature.
- **Layout (top→bottom):**
  1. Header "My Toolbag" + summary: **Carrying 12 items · 5 pending return**.
  2. **Pending-return banner** (warning) if anything outstanding.
  3. Segmented filter: **Pending** | **Returned** | **All**.
  4. **Item rows**, each showing: item name + icon, **Taken: 10 · Returned: 6 · Pending: 4**, status pill (Pending/Partial/Returned), date taken, tagged call (if any). Tap → item history.
  5. Floating **"Take Items"** button (checkout) — usually used at office before leaving.
- **States:** all returned (success empty — "All clear! Nothing pending 🎉"), has-pending, offline.
- **Notes on data model behavior:**
  - An item carried out stays **Pending** (or **Partial**) and appears on the worker's list until `returned_qty == taken_qty`.
  - Quantities are explicit: **taken**, **returned**, **pending = taken − returned**.
  - Consumables (nuts/bolts/gas) may be partially returned or fully consumed; "Mark consumed/used at site" is allowed and reduces pending with a reason.

### 5.10 Checkout — "Take Items" Flow
- **Purpose:** record what's leaving the office with this worker.
- **Layout:** Search/scan bar (barcode/QR optional) → catalog list of tools/parts → tap item → **Quantity Stepper** (big −/＋) → optional "tag to a call" → cart of selected items → **Confirm Checkout**. Confirmation shows total items now assigned to worker.
- **States:** out-of-stock item disabled with note; over-quantity guard (can't take more than available); success → items now appear in Toolbag as Pending.

### 5.11 Return — Flow (at office)
- **Purpose:** reconcile items back to office.
- **Layout:** "Return Items" → list of currently pending items each with a **Return qty stepper** (defaults to full pending qty) → option per item: *Return to stock* / *Mark used/consumed (reason)* / *Report damaged-lost (reason)* → **Confirm Return**.
- **States:** partial return (item stays Partial/Pending with reduced qty), full return (item → Returned, leaves pending list), damaged/lost (flag for manager). Success animation + updated counts.

### 5.12 PROFILE
- **Layout:** avatar, name, employee ID, role, branch. List: **My Attendance** (history calendar + per-day in/out, hours), **My Tool History**, **Settings** (language, notifications, change PIN, biometric), **Help / Call office**, **Logout**.
- **Attendance history detail:** month calendar with present/absent/half markers → tap day → punch timeline with map points and hours.

---

## 6. Admin / Manager Web Dashboard — Screen Specs

> Responsive web (desktop-first, works on tablet). Left sidebar nav, top bar with branch switcher + search + profile.

### 6.1 Login
- Email/phone + password (+ optional OTP/2FA). Clean centered card on branded background. "Forgot password".

### 6.2 OVERVIEW (Dashboard)
- **KPI row (Stat Tiles):** Present today (X/Y), On-field now, Calls completed / total, Avg calls per worker, **Tools out (pending returns)**, Overdue returns.
- **Live worker map:** pins of workers' latest punch/location, color by status (on-field / at office / off duty). Click pin → worker quick card.
- **Today's pulse:** feed of recent events (punch in/out, job completed, tool checkout/return) with timestamps.
- **Attention list:** missed calls, workers not punched in, overdue tools — each actionable.

### 6.3 ATTENDANCE
- **Daily view:** date picker + branch filter. Table: worker, punch-in, punch-out, total hours, status (Present/Absent/Half/On-leave), # punches, map link. Color-coded rows.
- **Map view:** all punch locations on a map for the selected day.
- **Timesheet view:** weekly/monthly per worker; totals. **Export CSV/PDF**.
- **Detail drawer:** click a worker-day → full punch timeline with each geo-point, accuracy, selfie thumbnail, and which call it was tied to.

### 6.4 WORKERS (Employees)
- **List:** searchable/filterable table — name, employee ID, role, branch, phone, status (active/inactive), today's status, pending tools count.
- **Add Employee (modal/page):** name, phone, employee ID, role (worker/manager), branch, joining date, photo, emergency contact, default shift. Sends invite/credentials.
- **Worker Detail (tabs):**
  - *Overview:* contact, role, current status, today's calls, location.
  - *Attendance:* history + hours + map.
  - *Calls:* assigned/completed history with outcomes.
  - *Toolbag:* items currently held + full checkout/return ledger; flag overdue.
  - *Performance:* calls/day, completion rate, on-time rate.
- Actions: edit, deactivate, reset PIN, message.

### 6.5 CALLS / JOBS (Scheduling)
- **Assign board / calendar:** day view with a column per worker; drag-create call cards into a worker's day (manager assigns the ~10 daily calls here). Toggle list/calendar/map.
- **Create / Edit Call (panel):** client/site (search or create), service type (Install/Service/Repair/AMC), AC unit, scheduled time window, priority, assigned worker, complaint/brief, checklist template, required tools/parts (optional pre-allocation).
- **Call Detail:** status, assigned worker, timeline (assigned → started → arrived → completed), worker's notes/photos, geo of completion, outcome. Reassign / reschedule actions.
- **Bulk assign / auto-distribute:** optional helper to balance calls across available workers by location/load.

### 6.6 CLIENTS / SITES
- **Directory:** searchable list of clients/sites — name, address, # AC units, last service, AMC status.
- **Client Detail:** contact info, multiple sites/addresses, **AC units list** (brand, model, capacity, install date, warranty/AMC), full **service history**, and quick **"Create call for this client"**.

### 6.7 INVENTORY / TOOLS — **reconciliation hub**
- **Catalog:** items table — name, type (Tool / Spare-part / Consumable), unit, **in stock**, **out (held by workers)**, reorder level, low-stock flag. Add/edit item, barcode/QR.
- **Who-holds-what:** matrix/list of every outstanding item by worker — taken / returned / **pending**, date out, age (days held), tied call. Highlight **overdue** (warning/danger).
- **Checkout/Return ledger:** chronological log of every checkout, return, consumption, damage/loss — with worker, qty, reason, timestamp. Filterable, exportable.
- **Reconcile action:** manager can confirm returns, write off consumed/lost/damaged with reason (audit-logged), and adjust stock.
- **States to design:** healthy, low stock, overdue items present.

### 6.8 REPORTS
- Attendance summary (per worker/branch/date range), productivity (calls completed, on-time %), **tool reconciliation** (out vs returned vs lost/consumed, shrinkage), employee leaderboard. All exportable (CSV/PDF), with simple charts (bar/line/donut).

### 6.9 SETTINGS (admin) / SUPER ADMIN extras
- **Roles & Admins:** invite/manage managers & admins, role permissions matrix.
- **Branches:** multi-location setup (super admin), assign workers/managers per branch.
- **Shop config:** work hours/shifts, geofence radius for "at site" detection, whether selfie is required at punch, checklist templates, service types, leave types.
- **Audit log (super admin):** sensitive actions (write-offs, role changes, deletions) with who/when.

---

## 7. Key User Flows (for prototyping)

1. **Daily worker flow:** Open app → Today → **Punch In** (location captured) → Calls → open next Call → Navigate → Mark Arrived → do job → Complete → repeat → end of day **Punch Out** (reminded about pending tools).
2. **Tool accountability flow:** At office **Take Items** (qty per item) → items show **Pending** in Toolbag all day → back at office **Return Items** (full/partial/consumed/lost) → pending clears when returned == taken → manager sees reconciliation in Inventory.
3. **Manager assign flow:** Overview spots free capacity → Calls board → create/drag ~10 calls onto each worker for the day → worker sees them instantly under Calls.
4. **Onboarding flow:** Manager → Workers → Add Employee → worker gets credentials → first login (OTP → set PIN) → ready.

---

## 8. Universal States (define for every list/data screen)

| State | Worker app treatment | Dashboard treatment |
|-------|----------------------|---------------------|
| **Loading** | Skeleton cards (no spinners on lists) | Skeleton rows |
| **Empty** | Friendly line illustration + 1 sentence + (optional) action | Empty table with guidance + primary action |
| **Error** | Inline card "Couldn't load — Retry" | Toast + inline retry |
| **Offline** | Banner "You're offline — changes will sync" + queued badge on items; punches/returns stored locally | Read-only stale badge with last-sync time |
| **Permission needed** | Blocking but friendly sheet (location/camera) with "Enable" | n/a |

> **Offline is a first-class requirement** for the worker app: punches, job updates, and tool checkouts/returns must be capturable with no signal and sync automatically when back online.

---

## 9. Accessibility & Field-Readiness Checklist

- Min touch target 48×48dp; primary actions reachable one-handed (bottom of screen).
- Body text ≥15px; key numbers large. AA contrast everywhere; tested in bright light.
- Status = icon + label + color (never color alone).
- Full support for one-handed, gloved use; large steppers for quantity.
- Localization-ready (English + regional language toggle); avoid baked-in text in images.
- Haptic + visual confirmation on punch and tool actions.

---

## 10. Using This Doc with AI Design Tools

**Step 1 — Set up the design system.** Paste §3 (tokens) and ask the tool to create a style/theme: colors, type scale, spacing, radius, and the core components in §3.5.

**Step 2 — Generate screens one group at a time.** For best results, prompt screen-by-screen. Example master prompt:

> "Design a mobile screen for an AC field-technician app called CoolCrew. Use this design system: primary `#1565D8`, accent cyan `#19C3D6`, navy text `#0B2545`, background `#F5F7FA`, white cards with 16px radius, Inter font, large tabular numbers. Clean, minimal, high-contrast for outdoor use, 48px touch targets, primary button full-width at the bottom.
> **Screen: TODAY (Home).** Top bar with greeting + avatar + date + bell. A large hero 'Punch Card' showing duty status and a big primary 'Punch In' button with a detected-location chip. Below: three stat tiles (Calls 4/10, Hours 3h 12m, Pending tools 5). Then a 'Next call' card with client, time window, distance, Navigate + Start buttons. A warning banner if there are pending tool returns. Bottom tab bar with 4 tabs: Today, Calls, Toolbag, Profile."

**Step 3 — Reuse components.** Tell the tool to reuse the Call Card, Tool Row, Status Pill, and Stat Tile across screens so the kit stays consistent.

**Screen checklist to generate:**

*Worker app:* Splash · Phone · OTP · Set PIN · Today · Punch-In sheet · Punch-Out sheet · Calls list · Calls map · Call detail · Toolbag · Checkout · Return · Profile · Attendance history.

*Dashboard:* Login · Overview · Attendance (daily/map/timesheet) · Workers list · Add employee · Worker detail · Calls board · Create call · Call detail · Clients · Client detail · Inventory catalog · Who-holds-what · Ledger · Reports · Settings/Roles.

---

*See [plan.md](plan.md) for the data model, tech stack, and build/delivery plan.*
