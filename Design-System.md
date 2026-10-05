# Design System Specification: AURA-Wealth
## Institutional-Grade Personal Finance & Corporate Actions Copilot
**Document Version:** 1.0.0 (FROZEN BASELINE)  
**Target Persona:** High-Net-Worth Individuals, Active Retail Investors & Self-Directed Fiduciaries  
**Aesthetic Theme:** Dark Institutional Terminal ("Obsidian Slate & Emerald Alpha")  
**Status:** Approved & Frozen for Development  

---

## 1. Design Philosophy & Visual Language

### 1.1 Core Philosophy: High Signal-to-Noise Institutional Precision
Traditional personal finance interfaces adopt playful, pastel, consumer-oriented gamification that trivializes serious capital allocation. **AURA-Wealth** is designed for the modern wealth builder managing high-stakes capital. The visual language takes inspiration from premier institutional trading stations (Bloomberg Terminal, BlackRock Aladdin, BNP Paribas CIB Portal), while delivering the fluidity, responsiveness, and polished aesthetics of modern web applications.

### 1.2 Key Design Principles
1. **High Information Density with Clear Hierarchy:**
   - Maximizes data throughput per square inch without visual clutter.
   - Clean tabular layouts, compact row padding, explicit column alignments, and standardized monospace formatting for all financial values.
2. **Deterministic Status Signalling:**
   - Colors are strictly semantic and never decorative.
   - Emerald denotes settled funds, positive yields, and verified reconciliations.
   - Amber indicates impending election deadlines, active what-if simulations, and warning thresholds.
   - Crimson highlights reconciliation breaks, cash deficits, and imminent deadline expirations ($<24\text{ hours}$).
   - Cyan represents active AI agent analysis, simulation modes, and ISO 20022 message tokens.
3. **Tactile Maker-Checker Feedback:**
   - Critical financial actions (e.g., submitting a voluntary tender offer election or accepting a corporate cash-out) require physical, deliberate interaction—such as a sliding confirmation lever or two-step review—preventing accidental clicks.
4. **Tabular Monospace Numerical Integrity:**
   - Every price, share quantity, currency amount, and cryptographic hash uses `font-variant-numeric: tabular-nums` to ensure perfect vertical alignment across multi-row blotters.

---

## 2. Color Palette & Design Tokens (CSS Variables)

The design system is built entirely on native CSS custom properties for instant load time and zero build-time overhead.

```css
:root {
  /* ==========================================================================
     Surface & Background Colors (Dark Obsidian Architecture)
     ========================================================================== */
  --bg-primary: #0a0d14;          /* Deepest obsidian canvas background */
  --bg-surface: #111622;          /* Primary card and blotter container surface */
  --bg-surface-elevated: #182030; /* Elevated modals, dropdowns, and active rows */
  --bg-surface-hover: #1e283d;    /* Row hover and button interactive states */
  --bg-surface-active: #25334e;   /* Active selection and tab highlight */

  /* ==========================================================================
     Border & Divider Tokens
     ========================================================================== */
  --border-subtle: #1f293d;        /* Subtle divider lines between rows */
  --border-medium: #2e3d5b;        /* Card boundaries and blotter borders */
  --border-prominent: #3e527a;     /* Focused inputs and active card boundaries */
  --border-glow-accent: rgba(0, 230, 153, 0.25); /* Subtle emerald edge highlight */

  /* ==========================================================================
     Typography & Text Hierarchy
     ========================================================================== */
  --text-primary: #f0f4fc;         /* Crisp near-white for headings and primary metrics */
  --text-secondary: #94a3b8;       /* Muted slate for metadata, column headers, and labels */
  --text-tertiary: #64748b;        /* Deep muted for timestamps, inactive states, and tags */
  --text-inverse: #0a0d14;         /* High-contrast dark text on bright accent badges */

  /* ==========================================================================
     Semantic Financial Accents
     ========================================================================== */
  /* Alpha Emerald: Settled, In-The-Money, Reconciled, Matched */
  --color-success: #00e699;
  --color-success-bg: rgba(0, 230, 153, 0.10);
  --color-success-border: rgba(0, 230, 153, 0.30);

  /* Alert Crimson: Reconciliation Breaks, Mismatches, Deficits, Deadline Expired */
  --color-danger: #ff4d6d;
  --color-danger-bg: rgba(255, 77, 109, 0.12);
  --color-danger-border: rgba(255, 77, 109, 0.35);

  /* Warning Amber: Imminent Election Deadlines (<72h), Unverified Break, Pending */
  --color-warning: #ffb703;
  --color-warning-bg: rgba(255, 183, 3, 0.12);
  --color-warning-border: rgba(255, 183, 3, 0.35);

  /* Agent Cyan: AI Recommendation, Simulation Mode, ISO 20022 Data Token */
  --color-accent: #00d2ff;
  --color-accent-bg: rgba(0, 210, 255, 0.10);
  --color-accent-border: rgba(0, 210, 255, 0.30);

  /* Institutional Indigo: Custody Feeds, Maker-Checker Controls, Neutral Badges */
  --color-indigo: #6366f1;
  --color-indigo-bg: rgba(99, 102, 241, 0.12);
  --color-indigo-border: rgba(99, 102, 241, 0.35);

  /* ==========================================================================
     Elevation & Shadows
     ========================================================================== */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.3);
  --shadow-md: 0 4px 12px rgba(0, 0, 0, 0.45);
  --shadow-lg: 0 12px 28px rgba(0, 0, 0, 0.65);
  --shadow-modal: 0 20px 48px -8px rgba(0, 0, 0, 0.85), 0 0 1px 1px var(--border-medium);
  --shadow-glow-emerald: 0 0 20px -3px rgba(0, 230, 153, 0.35);
  --shadow-glow-cyan: 0 0 20px -3px rgba(0, 210, 255, 0.35);

  /* ==========================================================================
     Layout, Spacing & Border Radii
     ========================================================================== */
  --radius-xs: 3px;
  --radius-sm: 6px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-full: 9999px;

  --space-2xs: 4px;
  --space-xs: 8px;
  --space-sm: 12px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  --space-2xl: 48px;

  /* ==========================================================================
     Transitions & Easing
     ========================================================================== */
  --transition-fast: 120ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-normal: 200ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-smooth: 300ms cubic-bezier(0.16, 1, 0.3, 1);
}
```

---

## 3. Typography Hierarchy

### 3.1 Type Families
- **Primary Body & Headings:** `'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`
  - High legibility, crisp geometry, multi-language support.
- **Financial Monospace & Tickers:** `'JetBrains Mono', 'Roboto Mono', 'SF Mono', Consolas, monospace`
  - Used strictly for ISINs, currency amounts, percentages, quantities, hash digests, and countdown timers.
  - All tabular numbers must enforce `font-variant-numeric: tabular-nums lining-nums`.

### 3.2 Typography Scale

| Class / Role | Font Family | Size | Weight | Line Height | Letter Spacing | Usage |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `display-1` | Inter | 28px (1.75rem) | 700 | 1.2 | -0.02em | Main Executive Dashboard Title |
| `heading-1` | Inter | 20px (1.25rem) | 600 | 1.3 | -0.01em | Card & Module Headers |
| `heading-2` | Inter | 16px (1.00rem) | 600 | 1.4 | 0 | Section Subheaders & Modal Titles |
| `body-md` | Inter | 14px (0.875rem)| 400 | 1.5 | 0 | Default body text & descriptions |
| `body-sm` | Inter | 12px (0.75rem) | 400 | 1.5 | +0.01em| Table cells, labels, helper texts |
| `metric-xl`| JetBrains Mono| 24px (1.50rem) | 700 | 1.2 | -0.02em| Portfolio Net Value, Cash Totals |
| `metric-lg`| JetBrains Mono| 18px (1.125rem)| 600 | 1.3 | -0.01em| Card KPI metrics, Election totals |
| `metric-md`| JetBrains Mono| 14px (0.875rem)| 500 | 1.4 | 0 | Table numerical cells, deltas |
| `code-sm` | JetBrains Mono| 11px (0.687rem)| 400 | 1.4 | +0.02em| SHA-256 Hashes, ISINs, ISO codes |

---

## 4. Institutional Component Specifications

### 4.1 Master Corporate Action Blotter Table
The primary command center view displaying all active, pending, and settled corporate actions across the user's personal investment accounts.

```
+-------------------------------------------------------------------------------------------------------------------------------+
| ACTION BLOTTER                     [All Brokers v] [Filter Action Type v] [Search ISIN/Ticker...]  [+ Ingest ISO 20022 Notice] |
+-----------+--------+---------------+------------+------------+--------------------+----------------+-----------+--------------+
| EVENT ID  | TICKER | SECURITY NAME | ACTION     | HOLDING    | RECORD / EX-DATE   | DEADLINE       | STATUS    | ACTION       |
+-----------+--------+---------------+------------+------------+--------------------+----------------+-----------+--------------+
| CA-2025-01| AAPL   | Apple Inc.    | TEND (Buy) | 150 shares | 2025-03-25 (T+1)   | 14h 22m [CRIT] | PENDING   | [Simulate >] |
| CA-2025-02| MSFT   | Microsoft Corp| DVOP (DRIP)| 280 shares | 2025-03-28 (T+1)   | 4d 18m  [WARN] | PENDING   | [Simulate >] |
| CA-2025-03| NVDA   | NVIDIA Corp   | SPLF (10:1)| 100 shares | 2025-03-20 (T+1)   | Auto / Mandat  | SETTLED   | [View Audit] |
+-----------+--------+---------------+------------+------------+--------------------+----------------+-----------+--------------+
```

- **Row Styling:**
  - Standard row height: `44px`.
  - Border bottom: `1px solid var(--border-subtle)`.
  - Hover state: Background shifts to `var(--bg-surface-hover)` with a subtle left indicator bar (`2px solid var(--color-accent)`).
- **Status Pills (Micro-Badges):**
  - Padding: `2px 8px`, border-radius: `var(--radius-full)`, font size: `11px`, font-weight: `600`, font-family: `'JetBrains Mono'`.
  - *Pending Election:* Background `var(--color-warning-bg)`, border `1px solid var(--color-warning-border)`, text `var(--color-warning)`.
  - *Settled:* Background `var(--color-success-bg)`, border `1px solid var(--color-success-border)`, text `var(--color-success)`.
  - *Break Detected:* Background `var(--color-danger-bg)`, border `1px solid var(--color-danger-border)`, text `var(--color-danger)`.
  - *Simulation Active:* Background `var(--color-accent-bg)`, border `1px solid var(--color-accent-border)`, text `var(--color-accent)`.

### 4.2 Split-Screen What-If Impact Simulator
The decision sandbox allowing users to visually inspect before-and-after states prior to committing an election.

```
+-------------------------------------------------------------------------------------------------------+
| SIMULATION: AAPL Voluntary Tender Offer (Event ID: CA-2025-01)                       [Close Modal [X]] |
+---------------------------------------------------+---------------------------------------------------+
| PRE-ACTION BASELINE                               | POST-ACTION SIMULATED STATE (Option A: Full Tender)|
+---------------------------------------------------+---------------------------------------------------+
| Portfolio Net Value:   $754,200.00                | Portfolio Net Value:   $757,800.00  [+ $3,600.00] |
| Unallocated Cash:      $32,450.00                 | Unallocated Cash:      $59,450.00   [+ $27,000.00]|
| AAPL Position:         150 shares ($33,000.00)    | AAPL Position:         0 shares     [- 150 shares]|
| Technology Sector:     34.2% of Portfolio         | Technology Sector:     29.8%        [- 4.4%]      |
| Estimated Tax Impact:  $0.00 (Unrealized)         | Net Capital Gains Tax: $1,420.00    [LTCG @ 15%]  |
| Portfolio Div Yield:   2.14% ($16,140/yr)         | Portfolio Div Yield:   1.98%        [- 0.16%]     |
+---------------------------------------------------+---------------------------------------------------+
| AI ELECTION ADVISORY & OPTIMIZATION:                                                                  |
| * Tender Price: $180.00/share represents an 8.2% premium over current market price ($166.35).         |
| * Tech concentration drops from 34.2% to 29.8%, satisfying your <30% portfolio diversification rule.  |
| * Recommendation: ACCEPT TENDER OFFER (Option A: Full Tender 150 shares).                             |
+-------------------------------------------------------------------------------------------------------+
| MAKER-CHECKER AUTHORIZATION:                                                                          |
| [ >>>>>>>>>>>>>>>>>>>>> SLIDE TO CONFIRM ELECTION INSTRUCTION >>>>>>>>>>>>>>>>>>>>> ]                 |
+-------------------------------------------------------------------------------------------------------+
```

- **Delta Badges:**
  - Positive capital change: Green pill `[+ $3,600.00]`.
  - Tax or dilution deduction: Amber/Red pill `[- $1,420.00]`.
- **Dual-Pane Grid:**
  - 50/50 CSS Grid layout (`display: grid; grid-template-columns: 1fr 1fr; gap: var(--space-md);`).
  - Pre-action card has subtle muted border `var(--border-medium)`.
  - Post-action card features active cyan border with glowing shadow `var(--shadow-glow-cyan)`.

### 4.3 Maker-Checker Authorization Slider
A physical, non-accidental confirmation component for executing financial instructions.
- **Track:** Width `100%`, height `48px`, background `var(--bg-surface-elevated)`, border `1px solid var(--border-medium)`, border-radius: `var(--radius-full)`.
- **Thumb:** Width `44px`, height `44px`, background `var(--color-success)`, border-radius: `var(--radius-full)`, display flex, center-aligned checkmark icon.
- **Behavior:** Dragging thumb from $0\%$ to $100\%$ triggers state verification and submits the election instruction payload to the backend API.

### 4.4 3-Way Reconciliation Matrix View
Displays discrepancies between Issuer declarations, Custody/Broker statement, and Internal Portfolio Ledger.

```
+-------------------------------------------------------------------------------------------------------+
| 3-WAY RECONCILIATION AUDIT: Event CA-2025-02 (MSFT Dividend Option)                   [Run Auto-Recon]|
+-------------------------+----------------------+----------------------+-------------------------------+
| METRIC                  | ISSUER DECLARATION   | INTERNAL EXPECTATION | BROKER ACTUAL STATEMENT       |
+-------------------------+----------------------+----------------------+-------------------------------+
| Record Date Holding     | 280.00 shares        | 280.00 shares        | 280.00 shares [MATCH]         |
| Gross Dividend Declared | $0.7500 / share      | $210.00 USD          | $210.00 USD   [MATCH]         |
| Applicable Tax Rate     | 15.00% (US-IN DTAA)  | $31.50 Tax           | $63.00 (30.00% Deducted)      |
| Net Cash Credited       | $178.50 USD          | $178.50 USD          | $147.00 USD   [DISCREPANCY]   |
+-------------------------+----------------------+----------------------+-------------------------------+
| DIAGNOSIS: [BREAK: TAX_OVERWITHHELD] Custodian withheld statutory 30% instead of treaty 15% rate.    |
| MONETARY VARIANCE: -$31.50 USD.                                                                       |
| ACTION: [Draft Broker Dispute Claim Letter]                                                           |
+-------------------------------------------------------------------------------------------------------+
```

- **Discrepancy Highlighting:**
  - Matching cells: subtle text `var(--color-success)` with `[MATCH]` badge.
  - Discrepant cells: bold text `var(--color-danger)` with animated pulsing red background `var(--color-danger-bg)` and `[DISCREPANCY]` badge.

### 4.5 Cryptographic Audit Trail Timeline
A tamper-evident chronological ledger tracking all state mutations with SHA-256 verification.

```
+-------------------------------------------------------------------------------------------------------+
| COMPLIANCE AUDIT TRAIL (SHA-256 HASH CHAIN)                                           [Verify Chain]  |
+------------+--------------------+-----------+--------------------------+------------------------------+
| TIMESTAMP  | EVENT TYPE         | ACTOR     | PAYLOAD SUMMARY          | SHA-256 RECORD HASH          |
+------------+--------------------+-----------+--------------------------+------------------------------+
| 11:22:04 Z | ELECTION_SUBMITTED | USER      | TEND AAPL 150 shs @ $180 | 7f83b165...e924 [VERIFIED]   |
| 11:15:30 Z | WHATIF_SIMULATION  | AGENT     | Model AAPL Tender Opt A  | 4b227777...d82a [VERIFIED]   |
| 10:45:12 Z | CA_INGESTION       | SYSTEM    | ISO seev.031 AAPL TEND   | 3a51f82c...91bc [VERIFIED]   |
+------------+--------------------+-----------+--------------------------+------------------------------+
| CHAIN STATUS: [100% VALID - 3/3 BLOCKS CRYPTOGRAPHICALLY SECURED]                                     |
+-------------------------------------------------------------------------------------------------------+
```

---

## 5. Animation, Micro-Interactions & Transitions

1. **Modal Transitions:**
   - Entering: Fade-in opacity from $0 \to 1$ and scale from $0.96 \to 1.0$ over `200ms` with easing `cubic-bezier(0.16, 1, 0.3, 1)`.
   - Exiting: Fade-out opacity from $1 \to 0$ over `150ms`.
2. **Numeric Count-Up Animation:**
   - Financial totals and delta metrics smoothly transition between numbers over `400ms` without layout shifting, powered by monospace tabular figures.
3. **Pulse Animation on Critical Deadlines:**
   - Actions with election deadlines under 24 hours feature a subtle amber/red ambient glow pulse:
     ```css
     @keyframes deadline-pulse {
       0%, 100% { box-shadow: 0 0 0 0 rgba(255, 77, 109, 0.4); }
       50% { box-shadow: 0 0 10px 3px rgba(255, 77, 109, 0.2); }
     }
     ```
4. **Button & Interactive States:**
   - Active click scale: `transform: scale(0.98)` on `:active`.
   - Hover transition: `background-color` and `border-color` transition over `120ms`.

---

## 6. Accessibility & Responsive Specifications

- **Contrast Ratios:** All text elements exceed WCAG 2.1 AA standards (minimum 4.5:1 for body text, 7:1 for headers against dark backgrounds).
- **Keyboard Navigation:**
  - `Tab` / `Shift+Tab` traverses blotter rows and interactive elements.
  - `Escape` closes any open simulation, election, or dispute modal.
  - `Space` / `Enter` activates buttons and row selection.
- **Screen Responsiveness:**
  - Designed primarily for desktop financial workstations ($\ge 1280\text{px}$ width) to maximize tabular density.
  - Fluid breakpoints down to $768\text{px}$ (tablet view) converting multi-column blotters into expandable high-density cards.
