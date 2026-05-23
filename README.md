# RunwayOS

Multi-tenant workspace license auditor and runway impact engine.

RunwayOS helps teams detect inactive SaaS seats after offboarding events, route assets through a controlled validation workflow, and quantify the resulting monthly/annual cash recovery.

---

## Why RunwayOS

Modern startups and portfolio companies often lose budget to orphaned licenses across tools like Slack, Google Workspace, GitHub, Jira, Notion, and Figma.  
RunwayOS closes that loop by connecting HR offboarding signals to IT/finance controls.

Core outcomes:
- Detect potentially reclaimable seats quickly
- Prevent premature financial assumptions during verification
- Export clean, audit-friendly artifacts for finance review

---

## Core Workflow

Each tracked asset follows a strict lifecycle:

1. `ACTIVE`  
   Baseline state; included in standard runway assumptions.

2. `PENDING_REMOVAL`  
   Triggered by an offboarding event webhook (for example, HRIS).  
   In this state, impacted projections are frozen to avoid contamination from unverified assumptions.

3. `DEACTIVATED`  
   Confirmed via provider API checks and/or admin validation.  
   Projection engine is unfrozen and realized savings are applied.

---

## Security Model

RunwayOS is designed with webhook integrity and tenant isolation in mind.

- **Timing-safe secret verification**  
  Uses constant-time comparison primitives (`hmac.compare_digest`, `crypto.timingSafeEqual`) to reduce timing side-channel risk.

- **Replay-window enforcement**  
  Validates signed timestamps against a strict acceptance window (default: 5 minutes).

- **Raw payload verification path**  
  Preserves request body bytes prior to JSON/body mutation so signatures are validated against canonical payload input.

- **Tenant-aware boundaries**  
  Data and operations are scoped by tenant/workspace IDs to avoid cross-tenant access.

> Note: implementers should pair application-level controls with network controls (WAF/rate-limits), key rotation, and centralized audit logging.

---

## Product Surface

- Mobile-first React + Tailwind UI for fast operator actions
- Admin reconciliation actions (e.g., mark removal complete)
- CSV export for downstream finance workflows
- Predictive runway visualization against baseline vs optimized scenarios

---

## Quick Start

### 1) Clone
```bash
git clone https://github.com/Full-Stack-Assets-Enterprise/RunwayOS.git
cd RunwayOS
```

### 2) Configure environment
Create `.env` in the project root:

```env
PORT=8000
DATABASE_URL=your_postgresql_connection_string
SLACK_SIGNING_SECRET=your_workspace_secret_key
GUSTO_WEBHOOK_SECRET=your_hr_secret_key
```

Optional (recommended in production):
```env
NODE_ENV=production
WEBHOOK_TOLERANCE_SECONDS=300
LOG_LEVEL=info
```

### 3) Install dependencies
If your stack includes Node + Python services:

```bash
npm install
pip install -r requirements.txt
```

### 4) Run
Use your project’s start command(s), for example:

```bash
npm run dev
# or
npm start
```

If Python workers/webhook processors are separate, run them in a second process:

```bash
python app.py
# or your project-specific entrypoint
```

---

## Configuration Reference

| Variable | Required | Description |
|---|---:|---|
| `PORT` | Yes | HTTP port for app/webhook service |
| `DATABASE_URL` | Yes | Postgres connection string |
| `SLACK_SIGNING_SECRET` | If Slack enabled | Validates Slack webhook signatures |
| `GUSTO_WEBHOOK_SECRET` | If Gusto enabled | Validates HR offboarding webhook signatures |
| `WEBHOOK_TOLERANCE_SECONDS` | No | Allowed signature timestamp skew (default `300`) |
| `LOG_LEVEL` | No | `debug`, `info`, `warn`, `error` |

---

## Suggested Repository Layout

```text
.
├─ README.md
├─ docs/
│  ├─ outbound/
│  │  └─ linkedin.md
│  └─ sales/
│     └─ one-pager.md
├─ web/                  # React frontend (if present)
├─ server/               # Node API/webhooks (if present)
├─ workers/              # async/background jobs (if present)
└─ requirements.txt      # Python deps (if present)
```

---

## Operational Best Practices

- Rotate webhook secrets on a fixed schedule
- Store secrets in a managed secret store (not plaintext on hosts)
- Enable structured logs with request IDs and tenant IDs
- Add alerting for signature failures, replay attempts, and state-transition anomalies
- Perform periodic reconciliation against source-of-truth provider APIs

---

## Roadmap Ideas

- Provider coverage expansion (SCIM + additional SaaS APIs)
- Automatic deactivation playbooks with approval gates
- Cost anomaly detection and confidence scoring
- Scheduled executive summaries by tenant/portfolio

---

## License

Add your license here (for example, MIT, Apache-2.0, or proprietary internal use).
