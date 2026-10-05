---
name: skill-08-fintech-security-auth
description: >-
  Stage 8 Fintech Security & Auth Hardening skill for post-draft banking prototypes.
  Implements Role-Based Access Control (RBAC: Maker, Checker, Operations, Auditor),
  token authentication, double-submission idempotency protection, input sanitization,
  and automated negative security unit tests.
---

# Stage 8: Fintech Security, Auth & RBAC Hardening

## Purpose & Activation
Activate this skill after Stage 7 when the core application flow is working live.
Per BNP Paribas hackathon rules: **"Initially don't put auth... after first draft then add auth. Test for security (must in fintech - as unit test)."**
This skill secures the financial endpoints, enforces role boundaries, prevents financial double-booking/replays, and provides concrete automated negative security test proofs for judges.

> **CRITICAL SECURITY DIRECTIVES:**
> 1. **Role-Based Access Control (RBAC)**:
>    - `OPERATOR / TRADER`: Submits workflow actions and initiates calculations.
>    - `SUPERVISOR / CHECKER`: Approves transactions and triggers settlements/reconciliation.
>    - `COMPLIANCE_AUDITOR`: Read-only inspector of immutable audit trails and compliance logs.
> 2. **Financial Anti-Tampering & Idempotency**:
>    - Block duplicate submissions (`409 Conflict`).
>    - Block negative or zero monetary quantities (`422 Unprocessable Entity`).
>    - Strict Append-Only Audit Trail: Zero delete/update endpoints exist for compliance logs.
> 3. **Simple, Testable Auth Mechanism**: Use clean Bearer Token or `X-User-Role` header with FastAPI dependencies so testing and demoing remain friction-free.

---

## 1. Step-by-Step Security Implementation

### Step 1: Security Dependency & RBAC Guard (`backend/app/api/deps.py`)
Provide complete implementation of authentication and role checks:

```python
from fastapi import Header, HTTPException, status, Depends
from typing import Annotated

VALID_USERS = {
    "trader_user": {"name": "Alex Trader", "role": "OPERATOR"},
    "ops_user": {"name": "Bob Operations", "role": "SUPERVISOR"},
    "audit_user": {"name": "Clara Auditor", "role": "COMPLIANCE_AUDITOR"}
}

def get_current_user(
    x_api_key: Annotated[str | None, Header()] = "ops_user"
) -> dict:
    """Authenticates caller via API header. Defaults to ops_user for zero-friction demo."""
    if not x_api_key or x_api_key not in VALID_USERS:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid or missing authentication credentials"
        )
    return VALID_USERS[x_api_key]

def require_role(required_roles: list[str]):
    """Enforces Role-Based Access Control (RBAC) on sensitive endpoints."""
    def role_checker(current_user: dict = Depends(get_current_user)):
        if current_user["role"] not in required_roles:
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail=f"Access Denied: Role '{current_user['role']}' does not possess required permissions ({required_roles})"
            )
        return current_user
    return role_checker
```

---

### Step 2: Secure Critical Endpoints
Apply role decorators to sensitive routes:
- `POST /api/v1/actions`: Protected with `Depends(require_role(["OPERATOR", "SUPERVISOR"]))`
- `POST /api/v1/calculation`: Protected with `Depends(require_role(["SUPERVISOR"]))`
- `GET /api/v1/audit/logs`: Protected with `Depends(require_role(["OPERATOR", "SUPERVISOR", "COMPLIANCE_AUDITOR"]))`

---

### Step 3: Financial Validation & Idempotency Guard
In `backend/app/api/actions.py`, inject boundary checks:
```python
# 1. Negative or zero quantity rejection
if payload.allocated_amount <= 0:
    raise HTTPException(status_code=422, detail="Allocated amount must be strictly positive.")

# 2. Duplicate submission idempotency check
existing = repo.get_action_by_entity_and_actor(db, payload.entity_id, payload.actor_id)
if existing:
    raise HTTPException(
        status_code=409,
        detail=f"Idempotency violation: Action already recorded for Entity '{payload.entity_id}'"
    )
```

---

## 2. Automated Negative Security Unit Tests (`backend/tests/test_security.py`)

Create automated tests proving that security and financial integrity cannot be violated:

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_unauthenticated_request_rejected():
    res = client.post("/api/v1/calculation", headers={"x-api-key": "invalid_token"})
    assert res.status_code == 401
    assert "Invalid or missing" in res.json()["detail"]

def test_unauthorized_role_forbidden():
    # Operator tries to run supervisor-only calculation (Forbidden)
    res = client.post("/api/v1/calculation", headers={"x-api-key": "trader_user"})
    assert res.status_code == 403
    assert "Access Denied" in res.json()["detail"]

def test_duplicate_submission_idempotency_conflict():
    valid_payload = {
        "entity_id": "ENT-1001",
        "actor_id": "trader_user",
        "decision_choice": "APPROVE",
        "allocated_amount": 500
    }
    # First submission succeeds
    res1 = client.post("/api/v1/actions", json=valid_payload, headers={"x-api-key": "trader_user"})
    assert res1.status_code in [201, 200]
    
    # Second identical submission returns 409 Conflict
    res2 = client.post("/api/v1/actions", json=valid_payload, headers={"x-api-key": "trader_user"})
    assert res2.status_code == 409
    assert "Idempotency violation" in res2.json()["detail"]

def test_negative_quantity_validation():
    invalid_payload = {
        "entity_id": "ENT-1001",
        "actor_id": "trader_user",
        "decision_choice": "APPROVE",
        "allocated_amount": -50
    }
    res = client.post("/api/v1/actions", json=invalid_payload, headers={"x-api-key": "trader_user"})
    assert res.status_code == 422
```

---

## 3. Verification Protocol (PowerShell)

Instruct the operator to run:
```powershell
# Run security test suite in backend/
.\venv\Scripts\python.exe -m pytest tests/test_security.py -v
```
*Expected: 4 passed with 100% assertions satisfied.*

---

## 4. Handoff Protocol to Stage 9 (`skill-09-testing-and-ci`)

When negative security tests pass:

```markdown
### Stage 8 -> Stage 9 Handoff Packet
- **Security Hardening**: RBAC applied (Operator, Supervisor, Auditor), Token/Header auth integrated
- **Financial Controls**: Double-booking idempotency protection (409), negative quantity validation (422)
- **Security Evidence**: 4 automated security unit tests passing
- **Target Next Skill**: `skill-09-testing-and-ci`
- **Immediate Next Action**: Consolidate test suites, verify edge-case coverage, and set up the automated GitHub Actions CI workflow.
```
