---
name: skill-09-testing-and-ci
description: >-
  Stage 9 Testing and CI Pipeline skill for fintech hackathons.
  Consolidates unit, engine, API, database, and security tests into a single
  automated runner. Implements GitHub Actions CI (.github/workflows/ci.yml)
  and edge-case validation suites.
---

# Stage 9: Comprehensive Testing & GitHub CI Pipeline

## Purpose & Activation
Activate this skill after Stage 8 security hardening is complete.
Per hackathon directives: **"Test at every stage, simultaneously add unit testing... enable CI checks, skeleton structure md, gitignore and all."**
This skill consolidates all test suites into an automated test runner, covers extreme financial edge cases, establishes a strict `.gitignore`, and generates a reproducible GitHub Actions CI workflow file.

> **CRITICAL TESTING DIRECTIVES:**
> 1. **Zero External Network Dependencies**: All CI and local tests must run 100% offline using SQLite in-memory and mock fixtures.
> 2. **Financial Edge-Case Coverage**: Must test zero-holdings, extreme split ratios (`1:1000`), odd-lot fractional shares, and tax boundary conditions.
> 3. **Clean CI Workflow**: A single YAML file (`.github/workflows/ci.yml`) running backend pytest and frontend build on every push and PR.

---

## 1. Step-by-Step Implementation Sequence

### Step 1: Clean Repository Exclusions (`.gitignore`)
Provide complete `.gitignore` in repository root to ensure no junk or virtual environments are committed:

```gitignore
# Python
backend/venv/
backend/.venv/
__pycache__/
*.py[cod]
*$py.class
*.sqlite3
*.db
.pytest_cache/
.coverage
htmlcov/

# Node / Frontend
frontend/node_modules/
frontend/dist/
frontend/.cache/
*.log

# IDE & OS
.vscode/
.idea/
.DS_Store
Thumbs.db
```

### Step 2: Edge-Case Test Suite (`backend/tests/test_edge_cases.py`)
Provide tests covering non-trivial financial scenarios:

```python
from decimal import Decimal
import pytest
from app.engine.actions_engine import calculate_stock_split, calculate_cash_dividend

def test_extreme_reverse_split_fractional():
    # 7 shares in a 1:10 reverse split -> 0.7 shares -> 0 allocated, 0.7 CIL at $100 = $70.00
    res = calculate_stock_split(quantity=7, ratio_str="1:10", market_price=Decimal("100.00"))
    assert res["allocated_shares"] == 0
    assert res["fractional_share"] == Decimal("0.7")
    assert res["cash_in_lieu"] == Decimal("70.00")

def test_zero_quantity_handling():
    res = calculate_cash_dividend(quantity=0, gross_rate=Decimal("5.00"))
    assert res["gross_amount"] == Decimal("0.00")
    assert res["net_amount"] == Decimal("0.00")

def test_rounding_precision_half_up():
    # 3 shares * $1.3333 = $3.9999 -> quantize to $4.00
    res = calculate_cash_dividend(quantity=3, gross_rate=Decimal("1.3333"))
    assert res["gross_amount"] == Decimal("4.00")
```

### Step 3: GitHub Actions CI Workflow (`.github/workflows/ci.yml`)
Provide complete CI workflow:

```yaml
name: Financial Platform CI

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  backend-test:
    name: Backend Test Suite (FastAPI + Pytest)
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./backend

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"
          cache: "pip"

      - name: Install Dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run Pytest Suite
        run: |
          pytest -v --tb=short

  frontend-build:
    name: Frontend Build Check (Vite)
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./frontend

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
          cache-dependency-path: frontend/package-lock.json

      - name: Install Node Dependencies
        run: npm ci

      - name: Run Build Check
        run: npm run build
```

---

## 2. Comprehensive Test Verification (PowerShell)

Instruct the operator to run the complete consolidated backend test suite:

```powershell
# In backend/
.\venv\Scripts\python.exe -m pytest tests/ -v
```
*Expected Output: `tests/test_smoke.py`, `tests/test_engine.py`, `tests/test_api_v1.py`, `tests/test_db.py`, `tests/test_security.py`, and `tests/test_edge_cases.py` all passing (100% green).*

And test the frontend build:
```powershell
# In frontend/
npm run build
```
*Expected Output: `dist/index.html` built with zero errors.*

---

## 3. Handoff Protocol to Stage 10 (`skill-10-pr-reviewer-and-git`)

When the full test suite and CI build pass:

```markdown
### Stage 9 -> Stage 10 Handoff Packet
- **Test Suite Status**: Consolidated test suite passing (Smoke, Engine, API, DB, Security, Edge Cases)
- **CI Pipeline**: `.github/workflows/ci.yml` configured for automated PR checks
- **Repository Cleanliness**: `.gitignore` created, excluding venv, dist, and db artifacts
- **Target Next Skill**: `skill-10-pr-reviewer-and-git`
- **Immediate Next Action**: Review code diffs, structure stage-by-stage Git commits with concise conventional messages, and perform PR reviews.
```
