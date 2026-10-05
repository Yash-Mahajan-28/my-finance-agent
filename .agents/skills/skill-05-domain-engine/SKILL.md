---
name: skill-05-domain-engine
description: >-
  Stage 5 Domain Engine skill for institutional fintech calculations and business logic.
  Implements deterministic processing algorithms using Python Decimal: financial math,
  risk scoring, exception/reconciliation detection, and tamper-evident audit logging.
  Enforces zero-float-leak guarantees and automated edge-case pytest suites.
---

# Stage 5: Domain Engine & Institutional Business Logic

## Purpose & Activation
Activate this skill after Stage 4 backend routing is established.
This skill implements the core intellectual property of the hackathon project: **the Domain Processing Engine** (`backend/app/engine/`).
Ensures zero floating-point errors, mathematically rigorous domain algorithms, state transitions, exception/break diagnostics, and immutable audit tracking.

> **CRITICAL FINTECH RULES:**
> 1. **Zero Floating Point Math**: Strictly use `from decimal import Decimal, ROUND_HALF_UP` for all monetary amounts, prices, fees, and rates. Never use Python `float`.
> 2. **Deterministic Baseline**: The core domain calculation must be deterministic, transparent, and explainable to judges.
> 3. **Automated Anomaly / Break Detection**: Discrepancies between external feeds and internal records must be automatically isolated and categorized by severity (`CRITICAL`, `WARNING`, `INFO`).
> 4. **Tamper-Evident Audit Logging**: Every consequential calculation or state change generates an immutable audit record with SHA256 integrity check.

---

## 1. Engine Modules Implementation Sequence

Provide these modular processing files tailored to the assigned problem:

### Module 1: Core Domain Processor (`backend/app/engine/processor.py`)
Provide complete code implementing the primary calculation algorithms for the problem statement:
- **Calculation Function**: Accepts validated inputs, performs precision arithmetic using `Decimal`, and returns a structured calculation result with audit metadata.
- **Decision / Rule Engine**: Evaluates business thresholds, compliance boundaries, or risk limits.
- **State Transition Function**: Takes current entity state, applies calculations, and computes the new state with verifiable delta receipts.

---

### Module 2: Anomaly, Variance & Reconciliation Detector (`backend/app/engine/reconciler.py`)
Provide complete code implementing:
- **`detect_exceptions(internal_records: list[dict], external_feed: list[dict], tolerance: Decimal = Decimal("0.01")) -> dict`**:
  - Compares internal book of records against external market feeds or custodian reports.
  - Flags mismatches: quantity breaks, monetary variance, missing entries, or unauthorized status changes.
  - Generates human-readable diagnostic explanations for judges (e.g., *"Variance of €150.00 detected due to unapplied 15% withholding tax"*).
- **`create_audit_entry(actor: str, event_type: str, entity_id: str, old_state: dict, new_state: dict, rationale: str) -> dict`**:
  - Generates immutable ISO 8601 audit records with SHA256 integrity hash.

---

## 2. Comprehensive Engine Unit Tests (`backend/tests/test_engine.py`)

Create unit tests covering critical financial boundary conditions:

```python
from decimal import Decimal
import pytest
from app.engine.processor import execute_core_calculation
from app.engine.reconciler import detect_exceptions

def test_decimal_precision_no_float_drift():
    # Proves exact decimal math without binary float rounding errors
    d1 = Decimal("1000.05")
    d2 = Decimal("0.05")
    assert d1 - d2 == Decimal("1000.00")

def test_core_calculation_happy_path():
    res = execute_core_calculation(quantity=100, unit_price=Decimal("45.50"), rate=Decimal("0.10"))
    assert res["calculated_total"] == Decimal("455.00")
    assert res["status"] == "SUCCESS"

def test_zero_and_boundary_handling():
    res = execute_core_calculation(quantity=0, unit_price=Decimal("10.00"), rate=Decimal("0.05"))
    assert res["calculated_total"] == Decimal("0.00")

def test_exception_detector_finds_discrepancy():
    internal = [{"id": "REC-01", "amount": Decimal("100.00")}]
    external = [{"id": "REC-01", "amount": Decimal("90.00")}]
    report = detect_exceptions(internal, external)
    assert report["exceptions_found"] == 1
    assert report["status"] == "BREAKS_DETECTED"
```

---

## 3. Verification Protocol (PowerShell)

Instruct the operator to run:
```powershell
# Run engine test suite in backend/
.\venv\Scripts\python.exe -m pytest tests/test_engine.py -v
```
*Expected: 100% assertions satisfied in < 0.5s.*

---

## 4. Handoff Protocol to Stage 6 (`skill-06-database-persistence`)

When engine unit tests pass:

```markdown
### Stage 5 -> Stage 6 Handoff Packet
- **Engine Verified**: `processor.py` and `reconciler.py` complete
- **Math Verifications**: Pydantic Decimal precision, boundary conditions, and exception detectors verified
- **Test Evidence**: All engine unit tests passing
- **Target Next Skill**: `skill-06-database-persistence`
- **Immediate Next Action**: Implement the relational database schema (SQLite/PostgreSQL via SQLAlchemy 2.0), ACID transactions, idempotency keys, and seed fixtures.
```
