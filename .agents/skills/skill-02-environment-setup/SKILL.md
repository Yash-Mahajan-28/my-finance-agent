---
name: skill-02-environment-setup
description: >-
  Stage 2 Environment Setup skill for Windows PowerShell environments.
  Establishes zero-dependency-hell runtimes for Python FastAPI and Node/Vite,
  creates minimal reproducible manifests, executes smoke tests, and hands off
  to Frontend Shell. Enforces Karpathy's Simplicity First principle.
---

# Stage 2: Environment Setup & Zero-Dependency Stabilization

## Purpose & Activation
Activate this skill immediately following Stage 1 ideation.
The objective is to establish a solid, isolated, reproducible local development environment on Windows PowerShell within **15 minutes**.
Prevents dependency conflicts, path mismatch errors, and execution policy blocks before any real feature coding begins.

> **CRITICAL POSTURE (Simplicity First):**
> - Use only standard, well-tested libraries with zero complex C-extensions or GPU requirements.
> - Never run global installs or modify system-wide execution policies.
> - Call the virtual environment interpreter directly if script activation is restricted (`.\venv\Scripts\python.exe`).
> - Keep dependency lists small (under 8 packages for backend, under 5 packages for frontend).

---

## 1. Step-by-Step Setup Protocol (Windows PowerShell)

Follow this exact sequence step by step:

### Step 1: Verify Installed Runtimes
Prompt the operator to run and report output for:
```powershell
python --version
node --version
npm --version
```
*Expected: Python 3.10+ and Node 18+.*

### Step 2: Initialize Clean Directory Structure
Provide the exact PowerShell commands to create the clean project skeleton:
```powershell
# In the project workspace root
New-Item -ItemType Directory -Force -Path backend/app/api, backend/app/engine, backend/app/models, backend/app/schemas, backend/app/services, backend/tests, frontend, docs
```

### Step 3: Minimal Backend Manifest (`backend/requirements.txt`)
Provide this pinned, stable dependency manifest for FastAPI and financial calculations:

```text
fastapi>=0.110.0,<1.0.0
uvicorn[standard]>=0.28.0,<1.0.0
pydantic>=2.6.0,<3.0.0
sqlalchemy>=2.0.0,<3.0.0
pytest>=8.0.0,<9.0.0
httpx>=0.27.0,<1.0.0
python-dotenv>=1.0.0
```
*(Notice: Strictly lightweight pure-python packages. No bulky ML frameworks unless explicitly required).*

### Step 4: Python Virtual Environment Setup & Verification
Instruct the operator to create and verify the venv in PowerShell:
```powershell
# Navigate to backend directory
cd backend

# Create isolated virtual environment
python -m venv venv

# Upgrade pip using the venv executable directly (bypasses execution policy restrictions)
.\venv\Scripts\python.exe -m pip install --upgrade pip

# Install dependencies from requirements.txt
.\venv\Scripts\python.exe -m pip install -r requirements.txt
```

### Step 5: Backend Smoke Test
Provide a minimal smoke test script to verify imports and test runner before moving on:

**File**: `backend/tests/test_smoke.py`
```python
import pytest
from decimal import Decimal
from fastapi import FastAPI
from pydantic import BaseModel

def test_environment_smoke():
    """Verifies Python runtime, Decimal arithmetic, and Pydantic v2 operations."""
    app = FastAPI()
    assert app is not None
    
    # Financial Decimal precision check (no float rounding errors)
    d1 = Decimal("100.05")
    d2 = Decimal("0.05")
    assert d1 - d2 == Decimal("100.00")

def test_pydantic_schema():
    class HealthCheck(BaseModel):
        status: str
        version: str
    
    h = HealthCheck(status="ok", version="1.0.0")
    assert h.status == "ok"
```

**Verification Command**:
```powershell
# Run smoke test in backend/
.\venv\Scripts\python.exe -m pytest tests/test_smoke.py
```
*Exit Condition: 2 passed in < 1 second.*

### Step 6: Frontend Template Initialization
Initialize a minimal, blazing-fast Vite + React frontend template:

```powershell
# From project root, navigate to frontend
cd frontend

# Initialize minimal Vite React app (JavaScript for fastest zero-type-error iteration)
npm create vite@latest . -- --template react

# Install base dependencies
npm install

# Install lightweight icons for financial dashboards
npm install lucide-react
```

**Frontend Smoke Test**:
```powershell
npm run build
```
*Exit Condition: Vite build completes with zero errors, outputting `dist/`.*

---

## 2. Windows PowerShell Gotchas & Recovery Rules

If any step fails, apply these rules immediately:
1. **Script Execution Blocked (`PSSecurityException`)**:
   - Do NOT run `Set-ExecutionPolicy Unrestricted`.
   - Instead, bypass activation and invoke binaries directly:
     `.\venv\Scripts\python.exe -m pytest` or `.\venv\Scripts\uvicorn.exe ...`
2. **Port Conflict (Port 8000 or 5173 in use)**:
   - Run backend on `http://127.0.0.1:8080`: `uvicorn app.main:app --port 8080`
   - Run frontend on `http://localhost:3000`: `npm run dev -- --port 3000`
3. **Timebox Exceeded (> 20 mins)**:
   - Fall back to standard SQLite (built into Python) and Vanilla JS / simple HTML frontend if Node package manager encounters corporate proxy issues.

---

## 3. Handoff Protocol to Stage 3 (`skill-03-frontend-shell`)

When smoke tests pass, output this Handoff Packet:

```markdown
### Stage 2 -> Stage 3 Handoff Packet
- **Verified Runtimes**:
  - Python: `backend/venv/Scripts/python.exe` (FastAPI, Pydantic v2, SQLAlchemy, Pytest verified)
  - Node: `frontend/` (Vite + React + Lucide Icons initialized, `npm run build` passing)
- **Directory Layout**: Confirmed `backend/app/`, `backend/tests/`, `frontend/src/`
- **Smoke Check Result**: 2 passing unit tests, zero build errors.
- **Target Next Skill**: `skill-03-frontend-shell`
- **Immediate Next Action**: Build responsive financial UI shell with mock Corporate Actions data and freeze the API contract.
```
