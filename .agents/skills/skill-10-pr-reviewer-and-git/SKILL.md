---
name: skill-10-pr-reviewer-and-git
description: >-
  Stage 10 PR Reviewer & Stage Commits skill for hackathon delivery.
  Performs automated code reviews on Git diffs (detects financial float leaks,
  security bypasses, contract mismatches), and generates stage-by-stage Git
  commits with concise conventional commit messages.
---

# Stage 10: Pull Request Reviewer & Stage-Based Git Committer

## Purpose & Activation
Activate this skill before committing code or opening/merging a Pull Request.
Per hackathon directives: **"Break commits in stage with concise message - through commits we can see our implementation stages and functionalities and error resolution... make pull requests, enable CI checks... skill for PR reviewer."**
This skill audits code diffs for financial safety, security, and cleanliness, and generates atomic, stage-by-stage Git commit commands.

> **CRITICAL CODE REVIEW DIRECTIVES:**
> 1. **Financial Float Leak Detection**: Automatically flag any introduction of Python `float` for prices, quantities, rates, or balances.
> 2. **Secret & Credential Scanner**: Block any hardcoded passwords, tokens, private keys, or absolute local machine paths.
> 3. **Architectural Separation**: Verify that routers remain thin and domain formulas stay strictly inside `backend/app/engine/`.
> 4. **Conventional Commits**: Enforce clean commit taxonomy (`feat(...)`, `fix(...)`, `test(...)`, `ci(...)`) so judges can inspect the commit history to verify steady, professional development progress.

---

## 1. Pull Request Review Protocol

When the operator pastes a `git diff` or list of changed files, evaluate against this checklist:

### PR Review Audit Matrix

| Category | Verification Item | Action if Violated |
| :--- | :--- | :--- |
| **Financial Math** | Are all rates, prices, and balances using `Decimal` or `str`? | **BLOCKER**: Reject `float` coercion. |
| **Security** | Are sensitive endpoints guarded by `Depends(require_role(...))`? | **BLOCKER**: Require RBAC dependency. |
| **Idempotency** | Is double-submission guarded by a unique constraint or conflict check? | **BLOCKER**: Enforce duplicate rejection (409). |
| **Contract** | Do request/response fields match `docs/API_CONTRACT_V1.md` exactly? | **BLOCKER**: Correct field names. |
| **Testing** | Is there a corresponding test in `backend/tests/` for new behavior? | **WARNING**: Provide test snippet. |
| **Cleanliness** | Are print statements, temp files, or `.db` files excluded from staging? | **BLOCKER**: Add to `.gitignore` / unstage. |

### Output Format for PR Review
The LLM outputs:
```markdown
### PR Review Findings
- **Status**: [APPROVED / CHANGES REQUIRED]
- **Blocking Issues**: [List of must-fix bugs or security leaks with line anchors]
- **Warnings & Edge Cases**: [Non-blocking suggestions]
- **PR Description Draft**: [Copy-pasteable markdown summary for GitHub PR]
```

---

## 2. Stage-by-Stage Git Commit Workflow (PowerShell)

To showcase clear, professional engineering stages in the Git log, instruct the operator to execute atomic stage commits:

```powershell
# Stage 1: Environment & Foundation
git add backend/requirements.txt backend/tests/test_smoke.py frontend/package.json .gitignore
git commit -m "feat(env): initialize backend and frontend environments with smoke tests"

# Stage 2: Contract Freezing & UI Shell
git add docs/API_CONTRACT_V1.md frontend/src/
git commit -m "feat(ui): implement institutional dashboard shell and freeze api contract v1"

# Stage 3: Backend API & Pydantic Schemas
git add backend/app/main.py backend/app/api/ backend/app/schemas/ backend/tests/test_api_v1.py
git commit -m "feat(api): implement fastapi routers and decimal pydantic schemas"

# Stage 4: Financial Domain Calculation Engine
git add backend/app/engine/ backend/tests/test_engine.py
git commit -m "feat(engine): add deterministic domain calculation engine and exception detection"

# Stage 5: Database Persistence & Seeding
git add backend/app/db/ backend/app/models/ backend/app/repositories/ backend/tests/test_db.py
git commit -m "feat(db): implement sqlalchemy models, sqlite persistence and seed fixtures"

# Stage 6: Live Integration
git add frontend/src/api/client.js frontend/src/App.jsx
git commit -m "feat(integration): connect react UI to live backend and verify full journey"

# Stage 7: Fintech Security & RBAC
git add backend/app/api/deps.py backend/tests/test_security.py
git commit -m "feat(security): enforce rbac role guards, idempotency and negative test suite"

# Stage 8: CI Pipeline & Edge-Case Coverage
git add .github/workflows/ci.yml backend/tests/test_edge_cases.py
git commit -m "ci(github): add automated pytest and vite build workflows"
```

---

## 3. Verification Command (PowerShell)

Instruct the operator to check the clean, chronological commit history:
```powershell
git log --oneline -n 10
```
*Expected: Clean, conventional commits telling a clear story of progressive feature delivery.*

---

## 4. Handoff Protocol to Stage 11 (`skill-11-docker-and-pitch`)

When all PR checks are approved and stage commits are cleanly committed:

```markdown
### Stage 10 -> Stage 11 Handoff Packet
- **Code Review**: PR review passed with zero blockers
- **Git State**: Clean commit history following conventional commits
- **CI Status**: GitHub Actions CI workflow verified
- **Target Next Skill**: `skill-11-docker-and-pitch`
- **Immediate Next Action**: Build optional Docker containerization (`docker-compose.yml`), formulate the Scalability Architecture Pitch, and prepare the 3-minute winning demo and judge defense script.
```
