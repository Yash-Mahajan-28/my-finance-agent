---
name: skill-07-system-integration
description: >-
  Stage 7 System Integration skill for connecting React frontend to live FastAPI.
  Switches mock adapter to live HTTP calls, coordinates CORS and network states
  (loading, error banners, optimistic UI updates), and verifies the complete
  end-to-end institutional workflow.
---

# Stage 7: End-to-End System Integration & Live Connection

## Purpose & Activation
Activate this skill after Stage 6 database persistence is verified.
The objective is to connect the React frontend shell to the live FastAPI backend, deprecate mock data, and verify the full end-to-end institutional workflow:
**Data Ingestion ➔ Engine Calculation ➔ Operator Action ➔ Database Persistence ➔ Audit Log Refresh.**

> **INTEGRATION PRINCIPLES (Karpathy's Surgical Changes):**
> 1. **Single Point of Switch**: Flip `USE_MOCK = false` inside `frontend/src/api/client.js` without rewriting component JSX.
> 2. **Explicit Error & Loading States**: Every async API call must handle network errors gracefully (show red alert toast/banner, never crash the UI).
> 3. **Live End-to-End Validation**: Prove data flows bidirectionally from browser clicks to SQL tables and back.

---

## 1. Step-by-Step Integration Protocol

### Step 1: Switch Frontend Adapter to Live Backend
Instruct operator to update `frontend/src/api/client.js`:

```javascript
// Toggle mock flag to false
const USE_MOCK = false;
const API_BASE_URL = "http://127.0.0.1:8000/api/v1";

export async function fetchPrimaryEntities() {
  if (USE_MOCK) return MOCK_ENTITIES;
  const res = await fetch(`${API_BASE_URL}/entities`);
  if (!res.ok) throw new Error(`Failed to load entities: ${res.statusText}`);
  return await res.json();
}

export async function fetchSummary() {
  if (USE_MOCK) return MOCK_SUMMARY;
  const res = await fetch(`${API_BASE_URL}/summary`);
  if (!res.ok) throw new Error(`Failed to load summary: ${res.statusText}`);
  return await res.json();
}

export async function submitAction(payload) {
  if (USE_MOCK) return MOCK_ACTION_RESPONSE;
  const res = await fetch(`${API_BASE_URL}/actions`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload)
  });
  if (!res.ok) {
    const errorData = await res.json().catch(() => ({}));
    throw new Error(errorData.detail || `Action submission failed: ${res.statusText}`);
  }
  return await res.json();
}

export async function runCalculation() {
  if (USE_MOCK) return MOCK_CALC_RESPONSE;
  const res = await fetch(`${API_BASE_URL}/calculation`, {
    method: "POST"
  });
  if (!res.ok) throw new Error(`Calculation failed: ${res.statusText}`);
  return await res.json();
}

export async function fetchAuditLogs() {
  if (USE_MOCK) return MOCK_AUDIT_LOGS;
  const res = await fetch(`${API_BASE_URL}/audit/logs`);
  if (!res.ok) throw new Error(`Failed to load audit logs: ${res.statusText}`);
  return await res.json();
}
```

---

### Step 2: Connect Backend Routers to Database Repositories
Update backend API routers to inject the database session (`db: Session = Depends(get_db)`) and call repository functions instead of in-memory lists.

---

### Step 3: Implement Frontend Feedback States
Ensure `frontend/src/App.jsx` handles:
- **Loading State**: Displays subtle pulsing skeletons or "Connecting to Institutional Engine..." indicator while initial data loads.
- **Error Banner**: If backend is down, displays: *"Backend offline on http://127.0.0.1:8000. Start Uvicorn to resume live operations."*
- **Success Toasts**: When an action is confirmed, shows confirmation pill with confirmation ID and audit reference.

---

## 2. End-to-End Verification Protocol (PowerShell)

Instruct the operator to run both servers concurrently:

### Terminal 1 (Backend):
```powershell
cd backend
.\venv\Scripts\python.exe -m uvicorn app.main:app --reload --port 8000
```

### Terminal 2 (Frontend):
```powershell
cd frontend
npm run dev
```

### Human Operator Verification Script:
1. Open `http://localhost:5173`.
2. Observe Navbar badge: **"LIVE DATABASE ACTIVE"**.
3. Verify data records load dynamically from the SQLite database.
4. Click an action button, enter payload, and submit.
5. Verify:
   - Success alert appears with allocated amount or calculation result.
   - Blotter status updates to **"CONFIRMED"**.
   - Audit Log view instantly populates with the newly logged transaction.
6. Refresh the page to confirm changes persist in the database.

---

## 3. Integration Troubleshooting Matrix

| Symptom | Cause | Surgical Fix |
| :--- | :--- | :--- |
| `TypeError: Failed to fetch` | CORS blocked or backend not running on port 8000 | Verify backend CORS contains `http://localhost:5173`; verify uvicorn is running. |
| 422 Unprocessable Entity | Frontend payload missing required Pydantic field | Inspect payload in browser DevTools Network tab against Pydantic schema. |
| DB Locked error in SQLite | Concurrent write conflict on Windows | Verify `connect_args={"check_same_thread": False}` is active in `session.py`. |

---

## 4. Handoff Protocol to Stage 8 (`skill-08-fintech-security-auth`)

When the live end-to-end journey is confirmed functional:

```markdown
### Stage 7 -> Stage 8 Handoff Packet
- **Integration Status**: 100% Live (Frontend connected to FastAPI and SQLite)
- **Verified Workflow**: Live data ingestion, engine calculation, database persistence, and audit logging confirmed
- **Working URLs**: Frontend at `http://localhost:5173`, Backend at `http://127.0.0.1:8000/docs`
- **Target Next Skill**: `skill-08-fintech-security-auth`
- **Immediate Next Action**: Implement Role-Based Access Control (RBAC), secure token/header authentication, input sanitization, and negative authorization unit tests.
```
