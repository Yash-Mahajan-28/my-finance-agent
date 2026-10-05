---
name: skill-11-docker-and-pitch
description: >-
  Stage 11 Production Packaging, Scalability Defense & Pitch skill.
  Provides optional Docker containerization (Dockerfile, docker-compose.yml),
  an enterprise scalability architecture blueprint for bonus points, a 3-minute
  live demo script backed by research, and a defense guide for BNP Paribas judges.
---

# Stage 11: Production Packaging, Scalability & Pitch Defense

## Purpose & Activation
Activate this skill in the final stage of the hackathon when core functionality is verified and committed.
Per hackathon directives: **"(docker) - containerisation (do only at end stage)... scalability - bonus rather than pitching... prepare demo and explanation for judges."**
This skill delivers optional containerization assets, formulates an enterprise scalability roadmap, scripts a high-impact 3-minute live demonstration, and prepares bulletproof answers for technical judges.

---

## 1. Optional Containerization (Docker Bonus)

> **SAFETY NOTICE**: Docker is strictly an optional bonus. If Docker Desktop is not running or takes more than 10 minutes to configure, skip containerization and demo the live local build. Never jeopardize presentation readiness for Docker.

Provide these lightweight, reproducible container configs:

### 1. `backend/Dockerfile`
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 2. `frontend/Dockerfile`
```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 3. `docker-compose.yml` (Root)
```yaml
version: "3.8"
services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=sqlite:///./app.db
    volumes:
      - ./backend/app.db:/app/app.db

  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend
```

**Verification Command (PowerShell)**:
```powershell
docker compose up --build -d
```

---

## 2. Enterprise Scalability Architecture (Bonus Presentation)

To win bonus points on scalability without writing unneeded complex infrastructure during the hackathon, present this **Institutional Scale Architecture**:

```
[External Feeds / Market Ingestion] ──► [Apache Kafka Event Stream (100k msg/sec)]
                                                  │
                                                  ▼
                                    [FastAPI Calculation Workers]
                                    (Stateless, Horizontal Auto-Scale)
                                                  │
                                                  ▼
                 ┌────────────────────────────────┴──────────────────────────────┐
                 ▼                                                               ▼
     [Distributed Redis Cache]                                     [PostgreSQL Partitioned DB]
  (Active Master Data & Rate Tables)                              (Partitioned by Account / Entity ID)
                                                                                 │
                                                                                 ▼
                                                                     [Read Replicas for Audit]
                                                                     (Immutable Compliance Stream)
```

### Key Scalability Talking Points for Judges:
1. **Stateless Financial Workers**: Calculation engine is pure functional code, enabling auto-scaling across Kubernetes pods during peak operational volumes.
2. **Account / Entity Partitioning**: Ledgers and balances can be partitioned horizontally by `account_id` or entity group, eliminating database lock contention.
3. **Idempotent Ingestion**: Natural business keys serve as idempotency identifiers, guaranteeing zero double-booking even under network retries.

---

## 3. High-Impact 3-Minute Hackathon Demo Script

Follow this structured, confident script:

### Minute 0:00 - 0:45: The Problem & Research Hook
*"Good morning judges. In global institutions like BNP Paribas, operational teams process millions of complex transactions every day. Manual reconciliations, disconnected spreadsheets, and legacy processing bottlenecks result in costly operational exceptions, delayed settlements, and severe regulatory scrutiny.*
*We built a solution engineered specifically to solve [Assigned Problem Statement]: an automated, institutional-grade platform designed for operational precision, auditability, and speed."*

### Minute 0:45 - 2:00: Live Product Walkthrough
1. **Primary Operational Ingestion**:
   *"Here in our main dashboard, we see incoming market and transaction records loaded in real-time with comprehensive status tracking."*
2. **Deterministic Processing Engine**:
   *"Our calculation engine processes the data with 100% decimal precision—eliminating floating-point errors and automatically flagging discrepancies, variance breaks, and risk threshold breaches."*
3. **Operator Workflow & Execution**:
   *"As an operator, I execute the decision workflow. Watch the instant calculation: the system validates the action, updates ledger state, and appends an immutable compliance audit record."*
4. **Automated Audit & Compliance Verification**:
   *"Compliance auditors can inspect our immutable event log: every state change is timestamped, attributed to an actor, and cryptographically verified with zero discrepancies."*

### Minute 2:00 - 3:00: Architecture, Security & Q&A
*"Our platform is built on FastAPI, Pydantic v2 with strict Decimal arithmetic, SQLAlchemy ORM, and React. We implemented Role-Based Access Control protecting sensitive endpoints, automated GitHub Actions CI, and 100% passing test suites including negative security tests.*
*Thank you, and we welcome your questions!"*

---

## 4. BNP Paribas Judge Defense Q&A Cheatsheet

| Anticipated Judge Question | Bulletproof Defense Answer |
| :--- | :--- |
| **"How do you ensure financial mathematical precision?"** | *"Our calculation engine strictly uses Python `Decimal` with explicit rounding modes (`ROUND_HALF_UP`). We never coerce financial quantities or rates into binary floating-point numbers."* |
| **"How do you prevent duplicate transaction submissions or double-booking?"** | *"We enforce defense-in-depth: database composite unique constraints coupled with application-level idempotency checks that return an HTTP 409 Conflict if an action is re-submitted."* |
| **"How does this integrate with enterprise banking standards?"** | *"Our schemas are designed to mirror institutional financial standards (e.g. ISO 20022 message schemas, FIX protocols), allowing straightforward API or message-queue adaptation."* |
| **"Why did you build the UI shell before the backend?"** | *"To de-risk the hackathon delivery. Establishing the visual contract and JSON schemas first prevented rework, gave our team an agreed specification, and ensured our end-to-end journey was complete on time."* |
| **"Is this production ready?"** | *"This prototype delivers a fully functional core with verified math, tests, and RBAC. To move to production, we would deploy our Kafka ingestion pipeline, integrate with core banking ledgers, and attach enterprise secrets management."* |

---

## 5. Hackathon Completion Checklist

- [ ] All code committed with conventional commit messages (`git log`).
- [ ] Pytest test suite runs 100% green (`pytest tests/`).
- [ ] Frontend build succeeds with zero errors (`npm run build`).
- [ ] README.md contains clear, reproducible setup steps for judges.
- [ ] Browser tabs prepared for demo:
  1. Frontend dashboard (`http://localhost:5173`)
  2. Swagger API Documentation (`http://127.0.0.1:8000/docs`)
  3. GitHub repository showing passing CI badge.
