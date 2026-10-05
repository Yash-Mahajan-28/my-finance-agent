---
name: skill-06-database-persistence
description: >-
  Stage 6 Database Persistence skill using SQLAlchemy 2.0.
  Designs relational schemas for domain entities, transactions, and audit trails.
  Implements ACID transactions, idempotency keys, automated SQLite/PostgreSQL
  initialization, and synthetic data seed fixtures.
---

# Stage 6: Database & Persistence Layer (SQLAlchemy 2.0)

## Purpose & Activation
Activate this skill after Stage 5 when the domain engine calculations are verified.
The objective is to replace in-memory stubs with a real, transactional relational database matching the frozen contract.
Provides instant, zero-configuration local persistence using SQLite (`app.db`) while keeping the code 100% compatible with PostgreSQL via an environment variable.

> **CRITICAL PERSISTENCE RULES:**
> 1. **Zero-Friction Fallback**: Default to SQLite (`sqlite:///./app.db`) so no database connection setup can fail or block the hackathon.
> 2. **Financial Precision in SQL**: Store monetary values using `String` or `Numeric(18, 4)`. Never map prices/balances to SQLite `REAL` or SQL `FLOAT`.
> 3. **ACID Transactions & Idempotency**: Consequential state changes must execute inside atomic transactions (`db.commit()` / `db.rollback()`). Protect against duplicate submissions using composite unique constraints.
> 4. **Automated Seeding**: Provide an automated seed routine so judges or teammates can populate demo data with a single command (`python -m app.db.seed`).

---

## 1. Step-by-Step Implementation Sequence

Provide these modular database files matching the domain entities:

### Step 1: Database Session & Configuration (`backend/app/db/session.py`)
```python
import os
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

DATABASE_URL = os.getenv("DATABASE_URL", "sqlite:///./app.db")

# Handle SQLite multi-threading on Windows PowerShell
connect_args = {"check_same_thread": False} if DATABASE_URL.startswith("sqlite") else {}

engine = create_engine(DATABASE_URL, connect_args=connect_args)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    """FastAPI Dependency for database session management."""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

---

### Step 2: ORM Models (`backend/app/models/entities.py`)
Provide complete models inheriting from `Base` tailored to the domain:
1. **Primary Entity Model**:
   - `id: int (PK)`
   - `entity_id: str (unique, index)`
   - Domain-specific fields (e.g. symbol, instrument, transaction ref)
   - Status, timestamps, and parameters.
2. **State / Account Ledger Model**:
   - `id: int (PK)`
   - `account_id: str (index)`
   - Balances, quantities, and calculated metrics (stored as Decimal strings).
3. **Action / Decision Model**:
   - `id: int (PK)`
   - `action_id: str (unique)`
   - `entity_id: str (index)`
   - `actor_id: str`
   - `status: str`
   - UniqueConstraint on `(entity_id, actor_id)` to enforce idempotency and prevent double-booking.
4. **AuditLog Model**:
   - `id: int (PK)`
   - `log_id: str (unique)`
   - `timestamp: str`
   - `actor: str`
   - `action_type: str`
   - `details: str`

---

### Step 3: Repository Layer (`backend/app/repositories/domain_repo.py`)
Provide clean repository functions with transactional safety:
- `get_all_entities(db: Session)`
- `get_entity_by_id(db: Session, entity_id: str)`
- `get_summary(db: Session, account_id: str)`
- `save_action(db: Session, action_data: dict)` (with rollback on duplicate)
- `record_audit(db: Session, actor: str, action_type: str, details: str)`

---

### Step 4: Seed Fixture Script (`backend/app/db/seed.py`)
Provide complete runnable seed script creating tables (`Base.metadata.create_all(bind=engine)`) and inserting realistic initial test records.

---

## 2. Verification Protocol (PowerShell)

### Step 1: Run Seed Script
Instruct operator to run in `backend/`:
```powershell
.\venv\Scripts\python.exe -m app.db.seed
```
*Expected: `Database initialized and seeded successfully. Records created.`*

### Step 2: Database Persistence Unit Test (`backend/tests/test_db.py`)
Create a fast SQLite test verifying:
1. Retrieval of seeded records.
2. Saving an action updates the database.
3. Duplicate action attempt triggers idempotency rejection without crashing.

```powershell
.\venv\Scripts\python.exe -m pytest tests/test_db.py -v
```
*Exit Condition: All database tests pass.*

---

## 3. Handoff Protocol to Stage 7 (`skill-07-system-integration`)

When database seeding and tests pass:

```markdown
### Stage 6 -> Stage 7 Handoff Packet
- **Persistence Active**: SQLite database `app.db` initialized and seeded
- **ORM Models**: SQLAlchemy 2.0 models for Entities, State, Actions, and Audit Logs
- **Transactional Safety**: Atomic commits, rollback handlers, and idempotency constraints verified
- **Target Next Skill**: `skill-07-system-integration`
- **Immediate Next Action**: Connect the frontend UI adapter to live FastAPI endpoints, replace mock toggle with live data, and verify the full end-to-end user journey.
```
