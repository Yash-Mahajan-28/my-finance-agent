---
name: skill-03-frontend-shell
description: >-
  Stage 3 Frontend Shell & Contract Freezing skill for fintech dashboards.
  Builds responsive Institutional Banking UI (Data Blotter, Workflow/Decision Modal,
  Summary KPI Cards, Audit Trail) using labeled mock data.
  Freezes OpenAPI/JSON schemas in docs/API_CONTRACT_V1.md before backend implementation.
---

# Stage 3: Frontend Shell & Contract Freezing

## Purpose & Activation
Activate this skill after Stage 2 environment setup is verified.
Per hackathon directives, **the frontend shell must precede backend implementation**.
The goal is to build an impressive, high-density institutional financial web interface that runs immediately on realistic mock data, and to **freeze the API data contract** (`docs/API_CONTRACT_V1.md`) so backend development can proceed with zero ambiguity.

> **DESIGN & AESTHETIC DIRECTIVES:**
> - Institutional Banking aesthetic: Sleek dark navy (`#0B1120`), slate surfaces (`#1E293B`), emerald green accents for positive metrics/approvals (`#10B981`), amber for pending alerts (`#F59E0B`), and crisp typography.
> - High-density tabular layouts with status pills, numeric alignment, and interactive modals.
> - Zero placeholders: Seed with realistic institutional records matching the problem statement.

---

## 1. Step-by-Step Execution Protocol

### Step 1: Freeze the API Data Contract First
Before writing frontend code, generate the formal contract based on the entities and workflow decided in the **Stage 1 Handoff Packet**. Both frontend and backend will adhere to this contract without deviation.

**File**: `docs/API_CONTRACT_V1.md`
Enforce this universal 5-endpoint institutional structure:

```markdown
# [Domain System Name]: API Contract v1.0 (Frozen)

Base URL: `http://127.0.0.1:8000/api/v1`

### 1. GET `/[primary-entities]`
Returns list of active records, alerts, or market items.
- Response `200 OK`: Array of entity objects with IDs, timestamps, status pills, and numeric amounts.

### 2. GET `/[account-or-portfolio-summary]`
Returns summary state (e.g. balances, risk metrics, or portfolio holdings).
- Response `200 OK`: Object with financial amounts (strictly as decimal strings, e.g. `"250000.00"`).

### 3. POST `/[primary-action-or-workflow]`
Executes an operator decision (e.g., submit election, approve trade, resolve exception).
- Request Body: JSON with `entity_id`, `actor_id`, `decision_choice`, `allocated_amount`.
- Response `201 Created`: Object with `confirmation_id`, `status: "CONFIRMED"`, `audit_ref`.

### 4. POST `/[reconciliation-or-calculation]`
Executes automated engine calculation, break detection, or impact simulation.
- Response `200 OK`: Output metrics, variance checks, and status (`"RECONCILED"` or `"BREAKS_DETECTED"`).

### 5. GET `/audit/logs`
Returns immutable chronological compliance audit trail.
- Response `200 OK`: Array of audit records (`log_id`, `timestamp`, `actor`, `action_type`, `details`).
```

---

### Step 2: Implement Centralized Mock API Adapter
Create a clean API client that switches seamlessly between mock data and the live backend via a single toggle:

**File**: `frontend/src/api/client.js`
Provide complete runnable code:
- Toggle `const USE_MOCK = true;` (switched to `false` in Stage 7).
- Contains realistic mock responses strictly matching `docs/API_CONTRACT_V1.md`.
- Export standardized functions: `fetchPrimaryEntities()`, `fetchSummary()`, `submitAction(data)`, `runCalculation()`, `fetchAuditLogs()`.

---

### Step 3: Implement Institutional Dashboard Components
Provide modular, responsive components tailored to the domain:
1. `frontend/src/components/Navbar.jsx`: Header with system status indicator ("MOCK ENGINE ACTIVE" / "LIVE BACKEND"), system clock, and active role pill (`Compliance Ops`, `Portfolio Manager`, or `Auditor`).
2. `frontend/src/components/DataBlotter.jsx`: High-density data table with filters, search, status badges, and action buttons.
3. `frontend/src/components/SummaryCards.jsx`: KPI metric cards showing financial totals, open exceptions, and calculated metrics.
4. `frontend/src/components/ActionModal.jsx`: Interactive popup triggered by the user to submit decisions, view impact preview, and confirm with an audit note.
5. `frontend/src/components/AuditTrailView.jsx`: Chronological event log displaying live audit entries with timestamp and actor attribution.
6. `frontend/src/App.jsx`: Main view wiring navbar, summary cards, data blotter, modal, and audit log.

---

### Step 4: Styling
Provide high-contrast, institutional styling in `frontend/src/index.css` using modern CSS variables and dark mode defaults.

---

## 2. Verification Protocol (PowerShell)

Instruct operator to run:
```powershell
# In frontend/
npm run dev
```
Open `http://localhost:5173` in browser.

### Observable Acceptance Checks:
1. Dashboard loads instantly with dark capital-markets theme.
2. Seeded records display in the blotter with proper status badges.
3. Clicking an action button opens the Action Modal with realistic fields.
4. Submitting the action updates the status to "CONFIRMED" and appends an entry to the Audit Log.
5. Summary KPI cards update to reflect the change.

---

## 3. Handoff Protocol to Stage 4 (`skill-04-backend-api`)

When the UI renders and interacts smoothly with mock data, output this Handoff Packet:

```markdown
### Stage 3 -> Stage 4 Handoff Packet
- **Frozen Contract**: `docs/API_CONTRACT_V1.md` (Endpoints, models, exact field names)
- **Frontend State**: Working React UI running on `frontend/src/api/client.js` (`USE_MOCK = true`)
- **Key Views Ready**: Data Blotter, Action Modal, Summary KPIs, Audit Log
- **Target Next Skill**: `skill-04-backend-api`
- **Immediate Next Action**: Build FastAPI backend skeleton with routers, Pydantic schemas with decimal types, and auto-generated Swagger documentation matching the frozen contract.
```
