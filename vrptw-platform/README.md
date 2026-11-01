# VRPTW Delivery Planning Platform

Web layer around the VRPTW synthetic-data generator (seed 20261022, 500 sites, 2,000 deliveries, 100 vehicles, 30 days).

- `backend/` FastAPI + SQLAlchemy 2 + Celery. `vrptw_core/` is the framework-free generator/validator (also a CLI).
- `frontend/` React 18 + Vite + TypeScript, TanStack Query, Zustand, Tailwind.

## Run locally

```bash
cp backend/.env.example backend/.env
docker compose up -d db redis
cd backend && pip install -e ".[dev]" && alembic revision --autogenerate -m init && alembic upgrade head
uvicorn app.main:app --reload            # http://localhost:8000/docs
celery -A app.workers.celery_app worker -l info --concurrency=1
cd ../frontend && cp .env.example .env && npm install && npm run dev   # http://localhost:5173
```

CLI (no web stack needed): `python -m vrptw_core.cli generate --seed 20261022 --scale starter`

## State of the scaffold

Working: auth (JWT + refresh cookie), RBAC (6 roles, permission-based), all 67 routes registered with schemas,
paginated/filtered list endpoints, supplier row-level scoping, run locking, order lifecycle transitions,
21 ORM tables with composite run-scoped keys, geo + rule helpers with tests. Frontend: every screen routed and
permission-guarded, typed API layer for every endpoint, token refresh, job polling.

`NotImplementedError` / `TODO` marks the business logic still to write: generator steps, planner, simulator,
anomaly injector, the 8 validation checks, KPI queries, Celery task bodies, and a few detail endpoints.

Tests: `cd backend && pytest` · `cd frontend && npm run typecheck`
