---
name: skill-01-ideation-and-scoping
description: >-
  Stage 1 Dynamic Ideation, Research & Scoping skill for the BNP Paribas Innoversite Hackathon.
  Takes ANY provided problem statement, use case brief, or dataset documentation.
  Conducts deep fintech domain research, extracts the institutional persona and operational pain point,
  dynamically synthesizes 3 hackathon-winning concepts backed by research (eliminating generic budgeting cliches),
  formulates a tight 1-day MoSCoW scope, and hands off to Environment Setup.
---

# Stage 1: Dynamic Problem Intake, Fintech Research & Scoping

## Purpose & Activation
Activate this skill at the very start of the hackathon the moment the official Problem Statement (PS), use-case brief, or dataset documentation is released by BNP Paribas organizers.

As BNP Paribas is a premier global corporate and institutional bank, the problem statement can be in **any real-world fintech or capital markets domain**, including:
- **Corporate & Institutional Banking (CIB)**: Trade execution, liquidity management, cross-border payments, syndicated loans, corporate actions, settlement reconciliation.
- **Risk, Compliance & Security**: Trade surveillance, AML/KYC anomaly detection, fraud mitigation, regulatory reporting (Basel III/IV, MiFID II, Dodd-Frank, DORA).
- **Wealth & Asset Management**: Portfolio optimization, risk attribution, ESG/sustainable finance screening, client reporting.
- **Operations & Custody**: Cash/security reconciliation, exception resolution, automated SWIFT/ISO 20022 message processing.

This skill dynamically digests **any** assigned problem statement, performs rigorous domain research, establishes a clear competitive differentiator (USP), defines a realistic 1-day MoSCoW scope, and generates the Stage 1 -> Stage 2 Handoff Packet.

> **CORE POSTURE (From AGENTS.md & Event Rules):**
> 1. **Zero Premature Assumptions**: Do not pre-judge the problem statement. Base all analysis on the actual uploaded PS text, use-case briefs, and dataset schemas.
> 2. **AI-Assisted Research Allowed**: Fully leverage AI to analyze industry standards, regulatory context, existing baselines, and mathematical algorithms.
> 3. **Anti-Generic Filter**: Strictly reject generic consumer cliches (no basic personal budgeting apps, expense trackers, or simple income-spending dashboards).
> 4. **Karpathy's Simplicity First**: Deliver a complete, robust, verifiable vertical slice (Input ➔ Engine ➔ Action ➔ Persistence ➔ Audit) rather than an unfinished maze of microservices.

---

## 1. Step-by-Step Dynamic Research & Ideation Protocol

When the operator pastes or uploads the assigned problem statement, execute this 4-step sequence:

### Step 1: Intake & Problem Decomposition
Extract and categorize from the provided materials:
- **Assigned Problem Title & Track**: Exact title, theme, and stated goals.
- **Target Institutional Persona**: Who actually uses this? (e.g., Custody Ops Specialist, Compliance Officer, Risk Manager, Treasury Trader, Portfolio Manager).
- **Operational Pain Point & Cost of Failure**: Where does the bottleneck, financial loss, reconciliation break, compliance penalty, or manual delay happen today?
- **Supplied Data & Schemas**: Documented columns, types, file formats (CSV, JSON, SQL, or API feeds), volume, and sample rows.
- **Judging Criteria & Constraints**: Rubric weights (Innovation, Technical Execution, Business Value, Feasibility), deadline, and pitch duration.

*(If critical constraints are missing from the prompt, state the assumption clearly or provide a realistic banking default).*

---

### Step 2: Fintech Domain Research & Competitive Differentiation
Conduct targeted domain research around the assigned problem:
1. **Industry Baseline**: How is this problem traditionally solved in tier-1 investment banks (e.g. manual Excel reconciliations, legacy batch COBOL systems, rule-based alerts with 90% false positive rates)?
2. **Regulatory & Market Standards**: Identify relevant financial standards (e.g., ISO 20022, FIX protocol, BCBS 239 risk data aggregation, ESG SFDR regulations, MiFIR transaction reporting).
3. **The Differentiator (USP)**: What makes our solution "hackathon-winning"?
   - Is it deterministic decimal accuracy instead of naive approximations?
   - Is it automated anomaly root-cause diagnosis instead of generic alert dumping?
   - Is it what-if simulation before committing consequential transactions?
   - Is it an immutable, tamper-evident audit trail for regulatory compliance?

---

### Step 3: Dynamic Generation of 3 Solution Concepts
Formulate 3 distinct enterprise concepts tailored specifically to the *actual* assigned problem statement:

| Concept Archetype | Architectural Strategy | Primary Differentiator (USP) | Hackathon Feasibility |
| :--- | :--- | :--- | :--- |
| **Concept A: End-to-End Operational Workflow Engine** | Complete operational lifecycle (Ingestion ➔ Engine Processing ➔ User Action ➔ Settlement ➔ Audit). | Full workflow completeness, high demo reliability, zero broken links. | **Highest (Recommended for 1-day MVP)**. |
| **Concept B: Intelligent Anomaly & Reconciliation Resolver** | Focuses heavily on exception detection, discrepancy diagnostics, and root-cause analysis. | Automated explanation of breaks/anomalies; appeals strongly to risk/compliance judges. | **High (Excellent hybrid with Concept A)**. |
| **Concept C: Predictive Simulation & What-If Optimizer** | Scenario modeling allowing operators to simulate portfolio/risk impact before taking action. | Interactive visual impact sliders and decision optimization. | **Medium (High visual wow-factor)**. |

**Evaluation & Recommendation**:
- Present all 3 concepts with concrete user journeys.
- Select the best concept (typically Concept A or a hybrid of A + B) that solves the core problem and guarantees a working demo within the available time.

---

### Step 4: Dynamic MoSCoW Scope Formulation
Enforce strict scope boundaries to guarantee delivery:

#### Must-Have (Core 1-Day Demo Journey - 100% Mandatory)
1. **Primary Operational Ingestion/Blotter View**: Interactive high-density dashboard displaying incoming records, market feeds, or transactions.
2. **Deterministic Processing/Calculation Engine**: Domain logic module (`engine/`) using exact precision (Python `Decimal` for money, strict rules/math).
3. **Operator Decision/Action Workflow**: Interactive modal/form allowing the user to take a meaningful action (e.g. submit election, approve trade, resolve exception, flag break).
4. **Persistence & State Transition**: ACID database update (e.g. updating positions, statuses, or ledger balances).
5. **Immutable Compliance Audit Trail**: Event log recording timestamp, actor, action type, old state, new state, and rationale.

#### Should-Have (Differentiator / USP - Build if Core Gates Pass Early)
- Visual impact comparison (e.g. before-action vs after-action portfolio or risk state).
- Automated break/discrepancy diagnostic badge.

#### Could-Have / Out-of-Scope (Strictly Deferred to Pitch Defense)
- Distributed event streaming (Apache Kafka), multi-region active-active clusters, full microservices.
- Multi-agent autonomous swarms (keep the core system deterministic and explainable).

---

### Step 5: Dynamic Requirement-to-Test Acceptance Matrix
Create a verification matrix mapping every Must-Have feature to observable tests:

| Req ID | Feature Name | Implementation Deliverable | Observable Acceptance Check (Terminal or UI) |
| :--- | :--- | :--- | :--- |
| `REQ-01` | Data Ingestion & Master View | Data Schema & Dashboard Table | User sees seeded institutional records loaded dynamically. |
| `REQ-02` | Core Domain Calculation | Processing Module (`engine/`) | Automated algorithm passes unit tests with 100% precision. |
| `REQ-03` | Operator Workflow / Action | Action Router & Modal | Submitting action updates status from `PENDING` to `CONFIRMED`. |
| `REQ-04` | Persistence & Settlement | Database Repository & Table | State change persists across server and page restarts. |
| `REQ-05` | Compliance Audit Trail | Audit Logger & Audit View | Chronological audit entry generated with timestamp and actor ID. |

---

## 2. Technical Stack Decision Record

Based on the problem statement requirements, recommend the minimal viable stack:
- **Frontend**: Responsive Single-Page Application (Vite + React / Modern CSS) with dark institutional styling, status pills, and high-density tabular data.
- **Backend**: Python FastAPI (recommended for financial math, Pydantic validation, and Swagger UI `/docs`) or Node.js (if event-driven JS is required).
- **Database**: SQLite for zero-setup local ACID transactions during hackathon (with clean SQLAlchemy abstraction to point to PostgreSQL).
- **Processing Engine**: Pure Python domain module (`backend/app/engine/`) ensuring clean separation from HTTP routes.

---

## 3. Dynamic Handoff Protocol to Stage 2 (`skill-02-environment-setup`)

At the conclusion of Stage 1, output this standardized Handoff Packet:

```markdown
### Stage 1 -> Stage 2 Handoff Packet
- **Assigned Problem Title**: [Extracted from actual PS]
- **Target Persona & User Journey**:
  1. [Step 1: Ingestion & Master View]
  2. [Step 2: Core Processing / Calculation]
  3. [Step 3: Operator Action / Decision]
  4. [Step 4: Persistence & State Update]
  5. [Step 5: Audit & Compliance Verification]
- **Selected Solution Concept & USP**: [Concept Name & Winning Differentiator]
- **Frozen Tech Architecture**:
  - Frontend: `frontend/` (Vite + React)
  - Backend: `backend/` (FastAPI + Pydantic v2 + SQLite)
  - Engine: `backend/app/engine/` (Deterministic calculations)
- **Must-Have Scope**: REQ-01 through REQ-05 defined.
- **Target Next Skill**: `skill-02-environment-setup`
- **Immediate Next Action**: Initialize project directory structure and isolate Python & Node dependencies on Windows PowerShell.
```
