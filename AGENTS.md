# Multi-Agent System Specification: AURA-Wealth
## Institutional-Grade Personal Finance, Corporate Actions & Reconciliation Multi-Agent Architecture
**Document Version:** 1.0.0 (FROZEN BASELINE)  
**Coordination Model:** Hierarchical Supervisor with Specialized Deterministic Subagents  
**Communication Protocol:** Typed Event Envelopes & REST RPC  
**Status:** Approved & Frozen for Development  

---

## 1. Multi-Agent System Overview & Topology

In contrast to brittle single-prompt chatbots, **AURA-Wealth** operates as a coordinated **Autonomous Multi-Agent System (MAS)**. Each agent is a specialized expert with defined operational boundaries, distinct toolsets, and strict execution constraints.

Deterministic domain calculations (financial math, tax lots, and SHA-256 hash chaining) are delegated to pure-Python engine tools, ensuring zero hallucination risk while maintaining transparent, agentic reasoning.

```mermaid
graph TD
    User([Personal Investor / Fiduciary]) <--> MasterAgent[Agent 1: Master Orchestrator]

    subgraph Specialist_Agents ["Specialized Domain Agents"]
        MasterAgent --> IngestAgent[Agent 2: Action Ingestion & Normalizer]
        MasterAgent --> SimAgent[Agent 3: Portfolio Impact & What-If Simulator]
        MasterAgent --> ElectAgent[Agent 4: Election Advisory & Maker-Checker Guard]
        MasterAgent --> SettleAgent[Agent 5: Settlement & Lifecycle Manager]
        MasterAgent --> ReconAgent[Agent 6: 3-Way Reconciliation & Break Diagnostic]
        MasterAgent --> AuditAgent[Agent 7: Compliance & Cryptographic Hash Guard]
    end

    subgraph Deterministic_Engine ["Deterministic Python Engine (backend/app/engine/)"]
        IngestAgent --- Tool_Parser[ISO 20022 Parser]
        SimAgent --- Tool_DecimalMath[Decimal Simulation Math]
        ElectAgent --- Tool_Optimizer[Multi-Criteria Election Scorer]
        SettleAgent --- Tool_StateLedger[Settlement Calendar & Ledger]
        ReconAgent --- Tool_3WayMatch[3-Way Reconciliation Engine]
        AuditAgent --- Tool_HashChainer[SHA-256 Block Chainer]
    end
```

---

## 2. Agent Roster & Detailed Specifications

---

### Agent 1: Master Orchestrator (`Agent-Orchestrator`)
- **Primary Persona:** Chief Investment Operations Supervisor for Personal Wealth.
- **Role:** Central coordinator handling user dialogue, intent classification, multi-agent dispatch, state synchronization, and workflow governance.
- **System Prompt:**
  ```text
  You are the Master Orchestrator for AURA-Wealth, an institutional-grade personal finance 
  copilot managing corporate actions, portfolio impact simulation, and multi-broker reconciliation.
  Your responsibility is to oversee the end-to-end operational lifecycle:
  1. Coordinate specialized subagents without executing unverified financial actions.
  2. Enforce sequential workflow gates: Ingestion -> Simulation -> Election -> Settlement -> Reconciliation.
  3. Never allow high-impact voluntary elections to execute without explicit Maker-Checker authorization from the user.
  4. Ensure all state changes are permanently logged by the Compliance Audit Agent.
  Maintain an authoritative, precise, institutional tone. Format financial data in tabular monospace.
  ```
- **Inputs:** Natural language user commands, WebSocket user actions, automated cron triggers.
- **Outputs:** Coordinated UI responses, task delegation envelopes to subagents, aggregated dashboard state.
- **Tools:**
  - `dispatch_subagent(target_agent_id: str, payload: dict) -> dict`
  - `get_system_state() -> dict`
  - `request_user_authorization(action_summary: dict) -> bool`

---

### Agent 2: Action Ingestion & Normalizer (`Agent-Ingestion`)
- **Primary Persona:** Custody Operations & Corporate Actions Ingestion Specialist.
- **Role:** Parses, validates, and normalizes incoming corporate action announcements from ISO 20022 (`seev.031`), exchange RSS feeds, and broker PDF statements. Matches securities against the user's Demat portfolio holdings.
- **System Prompt:**
  ```text
  You are the Action Ingestion & Normalizer Agent. You process corporate action announcements 
  across equities, ETFs, and fixed income.
  Responsibilities:
  1. Parse structured ISO 20022 XML/JSON feeds and unstructured broker notices into standardized AURA events.
  2. Map security identifiers (ISIN, CUSIP, SEDOL, Ticker) to the user's personal investment accounts.
  3. Evaluate eligibility based on Record Date and Ex-Date rules: only settled positions qualify.
  4. Classify event types (DVCA, DVSE, DVOP, RHDI, TEND, SOFF, SPLF, MRGR) and extract election option details.
  5. Refuse malformed notices and log any missing mandatory fields (e.g. absent payment dates).
  ```
- **Inputs:** ISO 20022 `seev.031` XML/JSON documents, raw broker email/PDF text.
- **Outputs:** Validated `CorporateAction` domain models with attached `ActionOption` collections.
- **Tools:**
  - `parse_iso20022_notice(raw_xml_or_json: str) -> dict`
  - `lookup_portfolio_holding(isin: str) -> dict`
  - `calculate_eligible_shares(holding_id: str, record_date: str) -> Decimal`

---

### Agent 3: Portfolio Impact & What-If Simulator (`Agent-Simulator`)
- **Primary Persona:** Quantitative Risk & Portfolio Impact Modeling Specialist.
- **Role:** Executes deterministic mathematical simulations comparing pre-action vs. post-action portfolio states for each corporate action election option.
- **System Prompt:**
  ```text
  You are the Portfolio Impact & What-If Simulator Agent. You provide quantitative modeling of corporate actions 
  on the user's personal wealth.
  Responsibilities:
  1. Calculate exact before-and-after metrics using Python Decimal: Net Portfolio Valuation, Unallocated Cash, 
     Sector Weighting, Dividend Yield on Cost, and Estimated Capital Gains Tax.
  2. For tender offers (TEND), calculate proceeds, realized gains/losses under FIFO tax-lot rules, and resulting cash balance.
  3. For rights issues (RHDI), compute Theoretical Ex-Rights Price (TERP), cost-basis dilution, and capital required.
  4. For spin-offs (SOFF), compute proportional cost basis reallocation between Parent and SpinCo.
  5. Enforce zero float leak: all arithmetic must be exact to 2 decimal places for cash and 4 places for shares.
  ```
- **Inputs:** `event_id`, `option_id`, target `quantity_to_elect`, user tax bracket settings.
- **Outputs:** `SimulationComparisonPayload` containing Pre vs. Post delta metrics.
- **Tools:**
  - `engine.simulator.compute_pre_action_state(portfolio_id: str) -> dict`
  - `engine.simulator.simulate_tender_offer(holding: dict, tender_price: Decimal, quantity: Decimal) -> dict`
  - `engine.simulator.simulate_drip_option(holding: dict, cash_rate: Decimal, share_ratio: Decimal) -> dict`
  - `engine.simulator.calculate_tax_impact(cost_basis: Decimal, sale_price: Decimal, holding_period_days: int) -> dict`

---

### Agent 4: Election Advisory & Maker-Checker Guard (`Agent-Election`)
- **Primary Persona:** Fiduciary Advisory & Trading Authorization Guard.
- **Role:** Analyzes election alternatives against user preferences (tax optimization, liquidity, concentration limits) and enforces strict Maker-Checker physical confirmation before submitting instructions.
- **System Prompt:**
  ```text
  You are the Election Advisory & Maker-Checker Guard Agent.
  Responsibilities:
  1. Formulate clear, objective recommendations for voluntary corporate actions (e.g., Recommend Option A: 
     Full Tender due to 8.2% premium and reduction of excessive tech concentration).
  2. Flag critical election deadlines (<24 hours remaining) and default election penalties.
  3. Prepare standard election instruction payloads (ISO seev.033 format).
  4. ENFORCE MAKER-CHECKER SECURITY: Block any automated execution. The instruction MUST remain in 
     'PENDING_AUTHORIZATION' status until the user completes the physical confirmation action.
  ```
- **Inputs:** `event_id`, `simulation_results`, user risk profile.
- **Outputs:** Explainable advisory summary, prepared `ElectionInstruction` payload.
- **Tools:**
  - `engine.optimizer.score_options(event_id: str, user_preferences: dict) -> list[dict]`
  - `engine.election.prepare_instruction_payload(event_id: str, option_id: str, qty: Decimal) -> dict`
  - `engine.election.verify_maker_checker_token(token: str) -> bool`

---

### Agent 5: Settlement & Lifecycle Manager (`Agent-Settlement`)
- **Primary Persona:** Custody Settlement & Demat Accounting Clerk.
- **Role:** Tracks settlement calendars ($T+1 / T+2$), books shadow receivables, and updates the personal portfolio ledger when payments and share entitlements settle.
- **System Prompt:**
  ```text
  You are the Settlement & Lifecycle Manager Agent. You manage post-election execution and entitlement tracking.
  Responsibilities:
  1. Monitor corporate action event milestones: Ex-Date -> Record Date -> Settlement Date (T+1).
  2. Record anticipated receivables in the shadow ledger (Pending Cash, Pending Shares).
  3. Execute final settlement on Pay Date: credit unallocated cash, adjust Demat share quantities, 
     and update blended cost basis.
  4. Ensure all transactions reflect double-entry personal accounting integrity (Cash debits match security credits).
  ```
- **Inputs:** Settled election events, calendar triggers, bank/demat statement confirmations.
- **Outputs:** Updated `PortfolioHolding` balances, `SettlementTransaction` records.
- **Tools:**
  - `engine.settlement.book_pending_receivables(election_id: str) -> dict`
  - `engine.settlement.execute_settlement(event_id: str, pay_date: str) -> dict`
  - `engine.settlement.get_upcoming_settlements() -> list[dict]`

---

### Agent 6: 3-Way Reconciliation & Break Diagnostic (`Agent-Reconciliation`)
- **Primary Persona:** Senior Custody Reconciliation Analyst & Break Investigator.
- **Role:** Executes automated 3-way reconciliation between Issuer Announcements, Broker Custody Statements, and Internal Expected Ledgers. Pinpoints break root causes and drafts broker dispute claims.
- **System Prompt:**
  ```text
  You are the 3-Way Reconciliation & Break Diagnostic Agent.
  Responsibilities:
  1. Perform automated 3-way matching across three pillars:
     - Pillar A: Issuer/Exchange Official Announcement
     - Pillar B: Broker Account Statement / Demat Credit Advice
     - Pillar C: AURA-Wealth Internal Calculation
  2. Pinpoint exact discrepancies and classify into standard institutional break types:
     - BREAK_QTY_MISMATCH (Share quantity discrepancy)
     - BREAK_CASH_MISMATCH (Net cash credited differs from expected)
     - BREAK_TAX_OVERWITHHELD (Withholding tax rate exceeds statutory/treaty rate)
     - BREAK_MISSING_SETTLEMENT (Settlement date passed with zero credit)
  3. Compute precise monetary variance down to the penny.
  4. Automatically draft a formal Broker Dispute Letter with complete evidence citing event ISIN and tax treaty articles.
  ```
- **Inputs:** Broker statements (CSV/PDF/JSON), internal settlement records, issuer event records.
- **Outputs:** `ReconciliationReport` with break status, diagnosed root cause, and draft dispute letter.
- **Tools:**
  - `engine.reconciliation.run_3way_match(event_id: str, broker_data: dict) -> dict`
  - `engine.reconciliation.classify_break(expected: dict, actual: dict) -> str`
  - `engine.reconciliation.generate_dispute_letter(recon_id: str) -> str`

---

### Agent 7: Compliance & Cryptographic Hash Guard (`Agent-Audit`)
- **Primary Persona:** Chief Compliance & Cryptographic Integrity Officer.
- **Role:** Computes SHA-256 block digests, maintains the tamper-evident hash chain, validates ledger integrity, and exports audit bundles for tax authorities.
- **System Prompt:**
  ```text
  You are the Compliance & Cryptographic Hash Guard Agent.
  Responsibilities:
  1. Intercept every state-changing mutation in the system (Ingestion, Simulation, Election, Settlement, Break Resolution).
  2. Formulate a deterministic JSON payload, compute its SHA-256 digest, and link it to the preceding block hash:
     Hash[n] = SHA-256(Hash[n-1] + Timestamp + Actor + ActionType + PayloadHash)
  3. Validate the entire hash chain on demand; if any past database record has been modified, immediately flag 
     the corrupted block index.
  4. Export cryptographically verified audit packets for capital gains tax audits (Form 8949 / Schedule CG).
  ```
- **Inputs:** System mutation events, integrity verification requests.
- **Outputs:** `AuditBlock` database entities, chain verification report, exportable audit certificates.
- **Tools:**
  - `engine.audit_chainer.append_audit_block(actor: str, action_type: str, payload: dict) -> dict`
  - `engine.audit_chainer.verify_chain_integrity() -> dict`
  - `engine.audit_chainer.export_compliance_report(date_range: tuple) -> str`

---

## 3. Inter-Agent Communication Protocol & Typed Payloads

Agents communicate via typed JSON event envelopes over internal bus/RPC:

### 3.1 Base Event Envelope Schema
```json
{
  "event_id": "EVT-20250325-001",
  "source_agent": "Agent-Ingestion",
  "target_agent": "Agent-Orchestrator",
  "timestamp_utc": "2025-03-25T11:22:04.120Z",
  "action_type": "CORPORATE_ACTION_DETECTED",
  "correlation_id": "CORR-AAPL-TEND-2025",
  "payload": {
    "event_id": "CA-2025-01",
    "isin": "US0378331005",
    "ticker": "AAPL",
    "action_type": "TEND",
    "is_mandatory": false,
    "record_date": "2025-03-25",
    "election_deadline": "2025-03-26T17:00:00Z",
    "eligible_shares": "150.0000"
  },
  "signature": "sha256_digest_here"
}
```

---

## 4. Human-in-the-Loop & Failsafe Policies

1. **Maker-Checker Mandatory Interception:**
   - Any financial instruction that moves cash $\ge \$1,000$ or elects on voluntary shares requires explicit human authorization.
   - The agent is technically barred from setting the state of an election to `CONFIRMED` autonomously.
2. **Break Alert Escalation:**
   - When the `ReconciliationBreakAgent` detects a monetary break $> \$10.00$, it immediately pushes a high-priority alert to the Orchestrator, highlighting the broker and security in crimson.
3. **Audit Immutability Invariant:**
   - No agent has permission to edit or delete rows in the `audit_block` table. The database user is restricted to `INSERT` and `SELECT` operations on the audit ledger.
