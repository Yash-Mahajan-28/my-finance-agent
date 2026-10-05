---
name: skill-00-evidence-debugger
description: >-
  Universal browser-chat surgical debugging skill for fintech hackathons.
  Activate when facing any error, stack trace, failed test, or broken integration.
  Enforces Andrej Karpathy's surgical change principles: zero guessing, root-cause
  isolation, minimal line-level diffs, and PowerShell verification commands.
---

# Universal Evidence-Based Debugger (Surgical Root-Cause Resolver)

## Purpose & Activation
Activate this skill whenever:
1. A terminal command fails with an error or stack trace in PowerShell.
2. An API endpoint returns an unexpected status code (4xx, 5xx) or corrupt JSON.
3. A test fails or an assertion is violated.
4. A frontend component fails to render or throws a console/network error.
5. A database migration, query, or transaction fails.

> **CRITICAL POSTURE (Andrej Karpathy's 4 Rules):**
> 1. **Think Before Coding**: Analyze the *first* meaningful root error, not the cascading aftermath. State the failure hypothesis explicitly.
> 2. **Simplicity First**: Do NOT rewrite files or refactor adjacent architecture to fix a localized bug.
> 3. **Surgical Changes**: Provide precise line-level replacement anchors. Never delete lockfiles, wipe virtual environments, or execute mass rewrites.
> 4. **Goal-Driven Execution**: Every suggested fix must be paired with an exact verification command that proves the bug is gone.

---

## 1. Input Required from Human Operator

When reporting an error, the operator provides:
```text
[COMMAND RUN]: <exact command run in PowerShell>
[WORKING DIRECTORY]: <e.g., C:\Users\...\backend>
[OBSERVED ERROR / TRACEBACK]: <pasted raw terminal output or console logs>
[FILE CONTENT / ANCHOR]: <content of the file around the failure line>
[EXPECTED BEHAVIOR]: <what was supposed to happen>
```

If any critical piece is missing, the LLM must prompt for the missing line before guessing the fix.

---

## 2. Step-by-Step Diagnostic Protocol

Follow this deterministic sequence:

### Step 1: Error Classification & Root Isolation
1. Identify the **Primary Error Type**:
   - `Syntax / Import / ModuleNotFoundError`: Dependency missing or relative import broken.
   - `Type / Attribute / ValidationError`: Pydantic/TypeScript schema mismatch, undefined property, or null pointer.
   - `Network / Connection / CORS`: Port conflict, server not running, origin mismatch, or wrong endpoint path.
   - `Database / Constraint / Decimal`: Type conversion error, duplicate key, foreign key violation, or float precision bug.
   - `Async / Event Loop / Concurrency`: Unawaited coroutine, blocking call in async path, or connection pool exhaustion.
2. Locate the innermost frame in the stack trace belonging to project code (ignore third-party library internals).
3. State the exact root cause in 1–2 plain English sentences.

### Step 2: Formulate the Surgical Fix
- Identify the single file and exact lines responsible.
- Restrict the edit to the minimum required lines (typically 1–10 lines).
- Ensure no working interfaces, types, or dependencies are disturbed.
- Verify that financial calculations (e.g. monetary decimals) remain strictly preserved.

### Step 3: Emit Code-Delivery Contract
Present the fix strictly in this format:

```markdown
### A. Diagnosis & Root Cause
- **Observed Failure**: [Exact error message]
- **Root Cause**: [Why it happened in the code]
- **Confidence Level**: [High / Medium - reason]

### B. Surgical Code Replacement
- **File**: `[repository-relative path]`
- **Action**: Replace Anchor
- **Search Target (Original Code)**:
```[language]
// Exact lines currently in the file
```
- **Replacement (Fixed Code)**:
```[language]
// Replacement lines with imports/fixes
```

### C. Manual Verification Command (PowerShell)
- **Directory**: `[Exact working directory]`
- **Command**:
```powershell
[Exact PowerShell command to run]
```
- **Expected Output**: [Exact success message or status code expected]
- **Fallback if Still Failing**: [Single diagnostic check if error persists]
```

---

## 3. Common Fintech & Python/Node Gotchas Checklist

Before proposing a fix, cross-check against these frequent pitfalls:
- [ ] **PowerShell vs Bash**: Ensure commands use PowerShell syntax (e.g., `$env:VAR="value"` instead of `EXPORT VAR="value"`, `.\venv\Scripts\Activate.ps1` or direct interpreter calls `.\venv\Scripts\python.exe`).
- [ ] **Decimal Float Corruption**: Ensure monetary amounts are never coerced into `float`. Must use Python `Decimal` or string/integer minor units.
- [ ] **Pydantic V1 vs V2**: Verify whether `model_dump()` (v2) or `dict()` (v1) is used; do not mix schemas.
- [ ] **FastAPI CORS**: Ensure frontend origin (e.g. `http://localhost:5173`) is explicitly in `allow_origins`, not `*` with credentials.
- [ ] **React State / Async**: Ensure API fetch states handle empty arrays `[]` without throwing `TypeError: cannot read property of undefined`.
- [ ] **Database Session Leaks**: Ensure DB sessions are closed using context managers (`with Session() as db:` or FastAPI `Depends(get_db)`).

---

## 4. Handoff Protocol (Return to Stage)

Once the fix is verified by the operator pasting the successful terminal output:
```markdown
### Debugger Handoff Packet
- **Resolved Bug**: [Summary of error fixed]
- **Modified File(s)**: [Exact file path]
- **Verification Proof**: [Human reported success output]
- **Resuming Stage**: [Return to the active development skill: e.g. skill-04-backend-api]
- **Next Planned Action**: [Resume the interrupted workflow step]
```
