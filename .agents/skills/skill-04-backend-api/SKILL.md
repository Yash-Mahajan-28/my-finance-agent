---
name: skill-04-backend-api
description: >-
  Stage 4 Backend API skill for FastAPI implementation.
  Builds thin main.py, modular routers matching the frozen API contract,
  strict Pydantic v2 schemas with Decimal financial precision, CORS middleware,
  interactive Swagger documentation (/docs), and automated API unit tests.
---

# Stage 4: Backend API & Service Layer (FastAPI)

## Purpose & Activation
Activate this skill after Stage 3 when `docs/API_CONTRACT_V1.md` is frozen.
The objective is to implement a robust, production-grade FastAPI backend that strictly adheres to the frozen contract for the assigned problem statement.
Enforces clean architectural separation: thin `main.py`, validated Pydantic models with decimal financial types, modular routers, and auto-generated Swagger UI (`/docs`).

> **CRITICAL ARCHITECTURAL RULES:**
> 1. **Keep `main.py` Thin**: Do NOT put route logic or business algorithms inside `main.py`. It only initializes FastAPI, applies CORS middleware, registers routers, and sets up lifecycle handlers.
> 2. **Financial Decimal Precision**: Never use Python `float` for prices, rates, or balances. Use `decimal.Decimal` or string representations to eliminate floating-point drift.
> 3. **No Auth in Initial Draft**: As specified in BNP Paribas hackathon rules, build the working API first without authentication; auth is layered on in Stage 8.
> 4. **Self-Documenting Swagger**: All endpoints must have docstrings, HTTP status codes, and example payloads visible at `/docs`.

---

## 1. Step-by-Step Implementation Sequence

Provide small, complete, copy-pasteable files in this exact order:

### Step 1: Pydantic v2 Schemas (`backend/app/schemas/`)
Define request and response contracts with strict typing based on `docs/API_CONTRACT_V1.md`.

**File**: `backend/app/schemas/contracts.py`
Provide complete code implementing:
- **Entity Response Models**: All fields from the frozen contract (IDs, strings, timestamps, statuses).
- **Summary & Metric Models**: Financial balances and calculated figures using `from decimal import Decimal`.
- **Action Request / Response Models**: Validated payload for operator actions (`allocated_amount > 0`).
- **Audit Response Model**: Schema for compliance logs (`log_id`, `timestamp`, `actor`, `action_type`, `details`).

---

### Step 2: In-Memory Seed Storage (`backend/app/services/store.py`)
Provide an isolated, thread-safe in-memory store so endpoints function immediately before the database layer is integrated in Stage 6:
- Seed with realistic data matching the problem statement.
- Provide helper methods: `get_entities()`, `get_summary()`, `record_action(data)`, `run_calculation()`, `get_audit_logs()`, `add_audit_log(actor, action_type, details)`.

---

### Step 3: Modular Routers (`backend/app/api/`)
Provide clean routers matching the frozen endpoints:
- `backend/app/api/entities.py`: `router = APIRouter(prefix="/[entities]", tags=["Core Entities"])`
- `backend/app/api/summary.py`: `router = APIRouter(prefix="/summary", tags=["Summary"])`
- `backend/app/api/actions.py`: `router = APIRouter(prefix="/actions", tags=["Workflow Actions"])`
- `backend/app/api/calculation.py`: `router = APIRouter(prefix="/calculation", tags=["Engine Calculations"])`
- `backend/app/api/audit.py`: `router = APIRouter(prefix="/audit", tags=["Compliance Audit"])`

---

### Step 4: Thin Application Entrypoint (`backend/app/main.py`)
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.api import entities, summary, actions, calculation, audit

app = FastAPI(
    title="Institutional Banking API Engine",
    description="BNP Paribas Innoversite Hackathon API Service.",
    version="1.0.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

# CORS configuration to allow local frontend connection
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173", "http://127.0.0.1:5173", "http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Register routers under /api/v1
app.include_router(entities.router, prefix="/api/v1")
app.include_router(summary.router, prefix="/api/v1")
app.include_router(actions.router, prefix="/api/v1")
app.include_router(calculation.router, prefix="/api/v1")
app.include_router(audit.router, prefix="/api/v1")

@app.get("/health", tags=["Health"])
def health_check():
    return {"status": "HEALTHY", "service": "InstitutionalEngine", "version": "1.0.0"}
```

---

## 2. Verification Protocol (PowerShell)

### Step 1: Start Server
Instruct operator to run in `backend/`:
```powershell
.\venv\Scripts\python.exe -m uvicorn app.main:app --reload --port 8000
```
*Expected: `Application startup complete. Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)`.*

### Step 2: Exercise Swagger UI
Prompt operator to open browser at: `http://127.0.0.1:8000/docs`.
- Test `GET /api/v1/[entities]`: Returns seeded records.
- Test `POST /api/v1/actions`: Submit valid JSON payload -> Returns `201 Created`.
- Test `GET /api/v1/audit/logs`: Verifies action was recorded in compliance audit stream.

### Step 3: Automated API Unit Tests (`backend/tests/test_api_v1.py`)
Run automated tests with `fastapi.testclient.TestClient`:
```powershell
.\venv\Scripts\python.exe -m pytest tests/test_api_v1.py -v
```
*Exit Condition: All endpoint tests pass.*

---

## 3. Handoff Protocol to Stage 5 (`skill-05-domain-engine`)

When Swagger and automated API tests pass:

```markdown
### Stage 4 -> Stage 5 Handoff Packet
- **Working Backend**: FastAPI server running on `http://127.0.0.1:8000`
- **Swagger Documentation**: Verified at `/docs` with all frozen contract endpoints active
- **Test Evidence**: API unit tests passing via `TestClient`
- **Current State**: Using in-memory store stubs
- **Target Next Skill**: `skill-05-domain-engine`
- **Immediate Next Action**: Implement the deterministic domain processing engine (business algorithms, financial math, exception detection, or risk calculations).
```
