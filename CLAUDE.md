# CLAUDE.md — LearnOS

Agent quick-reference for the **AI-Powered Personalized Learning OS (LearnOS)**.
Full design lives in [`docs/SYSTEM_DESIGN.md`](docs/SYSTEM_DESIGN.md).

## What it is

A warm, gamified K-12 learning platform that adapts content, teaching style, and pacing in
real time — speech-to-speech tutoring (Gemini Live), live video sentiment analysis, and
AI-generated personalized curricula, with flashcards, projects, story mode, and a coach.

## Stack

- **Backend:** Python 3.11+, FastAPI, Pydantic v2, SQLAlchemy (async), Alembic, Redis (optional)
- **Frontend:** Next.js 14 (App Router), TypeScript, Tailwind CSS, shadcn/ui
- **Data/Auth:** Supabase (PostgreSQL, Auth/JWT, Storage, Realtime)
- **AI:** Claude API (curriculum, teaching, evaluation, Vision), Gemini Live (voice), OpenAI TTS (podcast)

## Layout

```
backend/app/   routers/ → services/ → models/ + schemas/ + core/ + utils/   (23 routers, 13 services, 18 models)
frontend/src/  app/ (App Router) · components/ · hooks/ · lib/ · types/
docs/          SYSTEM_DESIGN.md · AGENTS.md · SUPABASE_INFO.md · IMPLEMENTATION_FLOW.md
```

## Architecture rules

- Keep routes thin: HTTP only. Business logic lives in `services/`; persistence in `models/` + repositories.
- Async I/O end to end (asyncpg, httpx, FastAPI async routes); stream chat via SSE, voice/sentiment via WebSocket.
- Type everything (Pydantic schemas, TypeScript interfaces); validate at the boundary.
- Multi-student: every table is RLS-scoped and queries filter by the authenticated student (tenant isolation).
- Never hardcode secrets — all config comes from env (`backend/app/config.py`); see `.env.example` files.
- Migrations for every schema change (Alembic); no raw SQL in routes.

## Quick start

```bash
docker-compose up -d                      # Redis
cd backend && python -m venv venv && source venv/bin/activate
pip install -r requirements.txt && alembic upgrade head
uvicorn app.main:app --reload --port 8000
cd ../frontend && npm install && npm run dev   # http://localhost:3000
```

See [`QUICKSTART.md`](QUICKSTART.md) for the full 15-minute setup and [`docs/SYSTEM_DESIGN.md`](docs/SYSTEM_DESIGN.md) for architecture.
