# Product Requirements Document (PRD)

## Project: AURA-Wealth (Autonomous Universal Reconciliation & Action Agent for Personal Wealth)
### Subtitle: Institutional-Grade Corporate Actions, Voluntary Elections, Portfolio Impact Simulation & Reconciliation Copilot for Personal Finance
**Document Version:** 1.0.0 (FROZEN BASELINE)  
**Track:** BNP Paribas Innoversite FinTech / Personal Finance Track  
**Date:** March 2025  
**Status:** Approved & Frozen for Development  

---

## 1. Executive Summary & Problem Space

### 1.1 The Anti-Budgeting Rationale
Traditional personal finance applications focus almost exclusively on consumer budgeting: categorizing grocery expenses, tracking coffee purchases, and generating static monthly expense charts. While helpful for basic personal bookkeeping, this approach completely fails the modern investor, affluent professional, and High Net Worth Individual (HNWI).

For retail investors and wealth builders, their primary financial risk and capital growth reside inside their **investment portfolios** across equities, exchange-traded funds (ETFs), fixed-income bonds, REITs, and corporate equity plans (ESOPs/RSUs). In this domain, public corporations frequently issue **Corporate Actions**—events initiated by a public company that bring material changes to its securities and financial structure.

### 1.2 The Real-World Personal Finance Crisis in Corporate Actions
1. **Information Asymmetry & Deadline Risk in Voluntary Corporate Actions (VCAs):**
   - When companies announce voluntary events (e.g., Cash vs. Stock Dividend DRIP, Rights Issues, Share Buybacks/Tender Offers, Mergers with cash/stock consideration), brokers send lengthy, complex PDF notices or cryptic emails with tight election windows (typically 5 to 10 business days).
   - If an individual investor misses an election deadline, the custodian applies a default election (often non-participation or default cash payout with high dividend withholding tax). Missing a lucrative buyback premium or rights entitlement results in permanent capital dilution or missed cash realization.
2. **Portfolio Impact Blindness:**
   - Retail investors have no tools to answer critical what-if questions before making an election:
     - *"If I elect cash in this buyback at $180/share, what is my net capital gains tax liability?"*
     - *"How will accepting this tender offer affect my technology sector weighting and overall dividend yield on cost?"*
     - *"If I subscribe to this 1:4 rights issue at a 20% discount, do I have sufficient unallocated cash in my settlement account, or will I trigger margin interest?"*
3. **Multi-Broker Entitlement Breaks & Lack of Reconciliation:**
   - Active investors hold assets across multiple brokers/custodians (e.g., BNP Paribas Wealth, Zerodha, Charles Schwab, Interactive Brokers).
   - In custody operations, **entitlement breaks** happen frequently: brokers miscalculate withholding tax (TDS), delay crediting bonus shares past the settlement date ($T+1 / T+2$), or fail to credit fractional shares in cash (Cash-in-Lieu).
   - While institutional investment banks have dedicated reconciliation desks, individual investors have **zero** automated tools to reconcile broker statements against official exchange announcements.
4. **Lack of Auditability & Compliance for Personal Portfolios:**
   - Self-directed investors have no tamper-evident audit record of election decisions, instructions submitted, tax-lot changes, and discrepancy claims filed with brokers.

### 1.3 Solution Vision: AURA-Wealth
**AURA-Wealth** is an autonomous, institutional-grade personal wealth agent that bridges institutional custody operations with personal portfolio management. It empowers personal investors with:
- **Autonomous Action Processing:** Ingesting structured ISO 20022 (`seev.031`) announcements and broker notices, matching them against Demat/custodial holdings.
- **Dynamic Portfolio Impact Simulation:** Simulating pre- and post-action portfolio states (asset allocation, cash balance, sector weights, dividend yields, tax lots).
- **Intelligent Election Workflow:** Recommending optimal election choices based on tax brackets, liquidity targets, and portfolio goals with Maker-Checker personal authorization.
- **Automated Settlement Tracking:** Tracking expected entitlement cash and security movements against market settlement calendars ($T+1 / T+2$).
- **3-Way Multi-Broker Reconciliation:** Autonomously matching Exchange/Issuer Data vs. Broker Account Statements vs. Internal Personal Ledger to flag breaks.
- **Cryptographic Audit Trail:** Immutable, SHA-256 hash-chained event logs ensuring complete traceability for tax filing and broker disputes.

---

## 2. Target Persona & User Scenarios

### 2.1 Primary Persona: The Autonomous Wealth Builder & HNWI
- **Name:** Julian Vance, 38
- **Role:** Tech Executive & Active Wealth Builder
- **Portfolio Size:** $750,000 USD distributed across 3 brokerages (BNP Paribas Wealth Management, Interactive Brokers, local retail broker).
- **Holdings:** 24 equities, 4 global ETFs, corporate RSUs.
- **Pain Points:**
  - Received a 42-page PDF notice regarding a tender offer with a 5-day deadline while traveling; missed the deadline, losing a 14% premium.
  - Suffered a 30% dividend withholding tax instead of 15% treaty rate on a foreign stock holding; only noticed 9 months later when filing taxes.
  - Has no consolidated view of how corporate splits and spin-offs affected his purchase cost basis (tax lots).

### 2.2 Secondary Persona: Family Office Manager / Self-Directed Fiduciary
- **Name:** Priya Sharma, 45
- **Role:** Family Portfolio Fiduciary (Managing personal & multi-generational family accounts).
- **Needs:** Strict Maker-Checker controls (Priya drafts election decisions; the primary family member confirms via 2-factor authorization), automated break detection across multiple custodial Demat accounts, and exportable tax audit packets.

---

## 3. Product Scope & Functional Requirements

The system is architected across **Seven Functional Pillars**:

```
+-----------------------------------------------------------------------------------+
|                                  AURA-WEALTH                                      |
+---------------------+--------------------+-------------------+--------------------+
| 1. Action Processing| 2. Portfolio Impact| 3. Election       | 4. Settlement      |
|    & Master Blotter |    Simulator       |    Workflow       |    Tracking        |
+---------------------+--------------------+-------------------+--------------------+
| 5. Multi-Broker     | 6. Institutional   | 7. Tamper-Evident                      |
|    Reconciliation   |    Tax Reporting   |    Audit Trail                     |
+---------------------+--------------------+----------------------------------------+
```

### Pillar 1: Corporate Action Ingestion & Master Blotter (`REQ-01` to `REQ-03`)
- **REQ-01: Standardized Action Ingestion:**
  - The system shall ingest corporate actions from structured feeds (ISO 20022 `seev.031` XML/JSON) and semi-structured broker notifications.
  - Supported Action Types:
    - `DVCA` (Cash Dividend) & `DVSE` (Stock Dividend / Scrip)
    - `DVOP` (Dividend Option / DRIP vs. Cash)
    - `RHDI` (Rights Issue Subscription)
    - `TEND` (Tender Offer / Share Buyback)
    - `SOFF` (Spin-Off / Demerger with Cost Basis Reallocation)
    - `SPLF` (Stock Split / Forward Split) & `SPLR` (Reverse Split)
    - `MRGR` (Merger & Acquisition Consideration)
- **REQ-02: Holding Matching & Eligibility Calculation:**
  - Upon ingestion, the engine shall cross-reference the security identifier (ISIN / CUSIP / Ticker) against the user's active portfolio holdings.
  - Automatically evaluate eligibility based on the **Record Date**: only shares settled prior to the Ex-Date qualify for entitlement calculation.
- **REQ-03: Real-Time Action Blotter Dashboard:**
  - High-density institutional data table displaying: Event ID, Security Name/ISIN, Action Type, Mandatory/Voluntary Flag, Record Date, Election Deadline Countdown (with color-coded urgency: `<24h` Critical Red, `<72h` Warning Amber), and Action Status (`ANNOUNCED`, `ELIGIBLE`, `ELECTION_PENDING`, `SETTLED`).

### Pillar 2: Portfolio Impact & What-If Simulation Engine (`REQ-04` to `REQ-06`)
- **REQ-04: Deterministic Impact Calculation:**
  - The calculation engine shall use Python `Decimal` with strict rounding rules (`ROUND_HALF_EVEN` / Banker's rounding) to eliminate floating-point drift.
  - For any selected corporate action, the engine simulates:
    1. **Pre-Action State:** Total portfolio valuation, cash balance, holding shares, cost basis, sector allocation percentage, expected annual dividend yield.
    2. **Post-Action State per Option:**
       - *Option A (Cash/Tender):* New cash balance, reduced share count, realized capital gain/loss, updated sector weighting.
       - *Option B (Stock/DRIP/Rights):* Increased share count, diluted or blended cost basis, capital required (for rights), updated dividend yield.
       - *Option C (Default/Inaction):* Default broker outcome, potential dilution loss.
- **REQ-05: Tax-Lot Cost Basis Adjustment:**
  - In a spin-off (`SOFF`), calculate proportional cost-basis allocation (e.g., Parent 80%, SpinCo 20%) adhering to IRS / tax-authority fractional rules.
  - In a rights issue (`RHDI`), compute the Theoretical Ex-Rights Price (TERP) and adjusted cost basis.
- **REQ-06: Interactive Visual Scenario Comparator:**
  - Side-by-side UI comparison displaying delta badges: $\Delta$ Valuation, $\Delta$ Cash, $\Delta$ Sector Weight, $\Delta$ Tax Liability.

### Pillar 3: Voluntary Election Workflow & Maker-Checker (`REQ-07` to `REQ-09`)
- **REQ-07: AI Election Advisory & Optimization:**
  - The agent evaluates each election option against user-configured preferences:
    - *Liquidity Preference* (Maximize cash vs. compound growth)
    - *Tax Optimization* (Long-term vs. short-term capital gains, dividend withholding tax rates)
    - *Concentration Limit* (Cap single stock exposure at, e.g., 15% of portfolio)
  - Produces an explainable decision rationale with pros, cons, and mathematical projections.
- **REQ-08: Maker-Checker Personal Safety Protocol:**
  - High-consequence financial elections (e.g., tendering $50,000 in shares) require dual-step confirmation:
    - **Step 1 (Maker/Analysis):** User or Agent prepares election instruction payload (Option Code, Quantity, Cash Required).
    - **Step 2 (Checker/Authorization):** User reviews risk summary, types confirmation phrase or slides cryptographic approval lever to execute.
- **REQ-09: Election Instruction Generation (`seev.033` equivalent):**
  - Formats instruction payload matching custodian requirements, transitions state to `ELECTION_SUBMITTED`, locks shares from secondary elections, and records submission timestamp.

### Pillar 4: Settlement Lifecycle Management (`REQ-10` to `REQ-12`)
- **REQ-10: Settlement Calendar & Milestone Tracking:**
  - Maintains event milestone tracker:
    $$\text{Announcement Date} \longrightarrow \text{Ex-Date} \longrightarrow \text{Record Date} \longrightarrow \text{Election Deadline} \longrightarrow \text{Pay/Settlement Date ($T+1$)}$$
- **REQ-11: Anticipated Entitlement Booking:**
  - Automatically books pending entitlements into a shadow settlement ledger:
    - Pending Cash Receivables (Gross Amount - Anticipated Withholding Tax)
    - Pending Security Entitlements (Bonus shares, rights shares, new spin-off ISIN)
- **REQ-12: Settlement Execution & Ledger Update:**
  - Upon arrival of payment date, moves status from `PENDING_SETTLEMENT` to `SETTLED`, updating Demat quantities and cash balances.

### Pillar 5: Multi-Broker 3-Way Reconciliation & Break Diagnostics (`REQ-13` to `REQ-15`)
- **REQ-13: 3-Way Reconciliation Engine:**
  - Compares three independent data sources:
    1. **Issuer/Exchange Master Announcement:** What the company officially declared (e.g., $1.50 dividend per share, 1:10 bonus).
    2. **Broker Account Statement / Custody Feed:** What the custodian actually credited/debited on the settlement date.
    3. **Internal Personal Ledger:** What AURA-Wealth calculated and expected.
- **REQ-14: Automated Break Detection & Taxonomy:**
  - Flags and categorizes discrepancies into institutional break classes:
    - `BREAK_QTY_MISMATCH`: Broker credited 95 shares instead of 100 bonus shares.
    - `BREAK_CASH_MISMATCH`: Net cash credited is less than expected (e.g., unexpected broker fee).
    - `BREAK_TAX_OVERWITHHELD`: Withholding tax deducted at 30% instead of 15% DTAA treaty rate.
    - `BREAK_MISSING_SETTLEMENT`: Settlement date + 2 days elapsed with zero broker credit.
    - `BREAK_FRACTIONAL_CIL`: Fractional share cash-in-lieu omitted or rounded incorrectly.
- **REQ-15: One-Click Broker Dispute Ticket Generation:**
  - Automatically drafts a formal institutional reconciliation dispute letter addressed to the broker's custody operations desk, citing Event ISIN, Record Date holding, calculation breakdown, and exact monetary variance.

### Pillar 6: Institutional Wealth Reporting & Tax-Lot Tracking (`REQ-16` to `REQ-18`)
- **REQ-16: Comprehensive Corporate Action Ledger:**
  - Filterable audit view of all historical corporate actions, elections taken, realized gains, and adjusted cost bases.
- **REQ-17: Tax-Lot Schedule Export:**
  - Generates tax-lot adjustment reports compliant with capital gains reporting standards (Form 8949 / Indian Schedule CG equivalent), preserving original purchase dates and adjusted cost bases.
- **REQ-18: Performance Attribution Report:**
  - Quantifies the net alpha generated through active election optimization (e.g., "+$3,420 gained by selecting Stock Dividend over Cash due to tax deferral").

### Pillar 7: Cryptographic Tamper-Evident Compliance & Audit Trail (`REQ-19` to `REQ-20`)
- **REQ-19: Cryptographic Hash Chaining (`SHA-256`):**
  - Every system mutation (Action Ingestion, What-If Simulation, Election Submission, Settlement Confirmation, Break Resolution) creates an immutable audit record.
  - Each record contains: `Record_ID`, `Timestamp_UTC`, `Actor` (`USER` or `AGENT`), `Action_Type`, `Payload_Hash`, and `Previous_Record_Hash`.
  - Any unauthorized tampering with database records breaks the cryptographic hash chain and is flagged immediately on dashboard startup.
- **REQ-20: Compliance Snapshot Export:**
  - One-click export of the complete cryptographically signed audit log for tax audits, estate planning, or legal verification.

---

## 4. MoSCoW Scope Definition (1-Day Hackathon Delivery)

To adhere strictly to **Karpathy's Simplicity First** and ensure a 100% bug-free, fully integrated vertical slice, the scope is defined as follows:

| Priority | Feature / Deliverable | Included in 1-Day Baseline MVP |
| :--- | :--- | :--- |
| **Must-Have** | High-density Action Blotter with countdowns and status filters | **YES (Core Blotter View)** |
| **Must-Have** | Ingestion of seeded ISO 20022 Corporate Action notifications (`DVOP`, `TEND`, `RHDI`, `SOFF`, `SPLF`) | **YES (5 Diverse Event Types)** |
| **Must-Have** | Deterministic Python `Decimal` calculation engine (`engine/`) | **YES (Zero Float Drift)** |
| **Must-Have** | Interactive What-If Simulation Modal with Before vs. After comparison | **YES (Dual-Pane Simulator)** |
| **Must-Have** | Voluntary Election Submission workflow with Maker-Checker confirmation slider | **YES (Actionable Decision)** |
| **Must-Have** | ACID Database persistence (SQLite + SQLAlchemy 2.0) updating portfolios | **YES (State Persistence)** |
| **Must-Have** | Automated 3-Way Reconciliation Blotter with Break Badges (`QTY`, `CASH`, `TAX`) | **YES (Break Engine)** |
| **Must-Have** | Cryptographic SHA-256 Hash-Chained Audit Trail | **YES (Compliance Log)** |
| **Should-Have** | Automated Broker Dispute Letter Generator (One-click Markdown/PDF modal) | **YES (High Wow-Factor)** |
| **Should-Have** | Multi-Broker Account Filter (e.g. BNP Paribas Custody vs. Interactive Brokers) | **YES (Realistic Multi-Account)** |
| **Could-Have** | Live integration with real-time exchange FIX protocols | **DEFERRED (Pitch Architecture Only)** |
| **Could-Have** | Live automated brokerage trade execution via OAuth broker APIs | **DEFERRED (Simulated via Ledger)** |
| **Won't-Have** | Generic consumer budgeting, grocery categorization, receipt OCR | **STRICTLY EXCLUDED** |

---

## 5. Requirement-to-Test Traceability Matrix

| Req ID | Feature Deliverable | Target Module | Acceptance Verification Method |
| :--- | :--- | :--- | :--- |
| `REQ-01` | Action Ingestion | `backend/app/engine/ingestion.py` | Pytest verifies 5 distinct ISO 20022 JSON events are parsed without error. |
| `REQ-02` | Holding Eligibility | `backend/app/engine/entitlement.py` | Pytest verifies ex-date settlement rule: only positions held at record date receive entitlement. |
| `REQ-03` | Action Blotter UI | `frontend/src/components/ActionBlotter.jsx` | Browser subagent / Vitest verifies high-density table renders with status pills and deadline countdowns. |
| `REQ-04` | Deterministic Impact Sim | `backend/app/engine/simulator.py` | Pytest verifies exact Python Decimal arithmetic on tender offer: cash + tax + shares balance. |
| `REQ-07` | Election Optimization | `backend/app/engine/election.py` | Engine computes recommendation score based on user tax bracket and concentration limit. |
| `REQ-08` | Maker-Checker Workflow | `frontend/src/components/ElectionModal.jsx` | Submitting without sliding confirmation throws validation error; sliding persists election. |
| `REQ-12` | Settlement Booking | `backend/app/engine/settlement.py` | Status changes from `ELECTION_SUBMITTED` to `SETTLED`; ledger balances update. |
| `REQ-13` | 3-Way Reconciliation | `backend/app/engine/reconciliation.py` | Discrepancy detector flags deliberate tax over-deduction and missing bonus shares with break IDs. |
| `REQ-19` | SHA-256 Hash Chain | `backend/app/engine/audit.py` | Unit test validates that altering any database row invalidates the cryptographic chain verification. |

---

## 6. Success Metrics & Demonstration Walkthrough

1. **Precision Metric:** Zero float arithmetic discrepancies (`0.00000000` precision error across all financial calculations).
2. **Speed Metric:** Sub-50ms simulation response time on complex portfolio what-if calculations.
3. **Operational Clarity:** High-density Bloomberg/FactSet-grade UI that immediately communicates action urgency and financial consequences.
4. **Judging Pitch Impact:** A complete, end-to-end 3-minute live walkthrough:
   - **Minute 1:** Operator views high-density Corporate Action Blotter. Spots a critical Tender Offer ($TEND) with a 24-hour election deadline for a portfolio holding.
   - **Minute 2:** Opens the **Impact Simulator**. Compares Option 1 (Tender 100 shares for $18,000 cash) vs. Option 2 (Retain shares). Sees instant visual delta: $+\$18,000$ cash, $-\$1,200$ capital gains tax, Tech weighting drops from $34\%$ to $28\%$. Slides Maker-Checker confirmation to submit.
   - **Minute 3:** Navigates to **Reconciliation Blotter**. Discovers a `BREAK_TAX_OVERWITHHELD` break where a custodian over-deducted withholding tax on a foreign dividend. Generates a formal Broker Dispute Claim letter with one click. Shows the SHA-256 cryptographic audit trail verifying the entire sequence.
