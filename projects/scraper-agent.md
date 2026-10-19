---
slug: scraper-agent
name: Scraper Agent (Retail Product Scraper)
status: active
type: data pipeline / autonomous scraping agent
stack:
  - Python 3.12
  - Temporal (temporalio) — durable workflows/activities, self-hosted
  - Supabase (Postgres) — task queue, raw/product tables, skills, error KB, spend ledger
  - FastAPI (internal HTTP endpoints, X-Api-Key auth)
  - Playwright / Camoufox / Chrome CDP / BrightData — tiered anti-bot browser pool
  - Gemini (site analysis, generic extraction) + LLM-powered category finder/crawl-plan sub-agents
  - Next.js (separate dashboard repo, Vercel)
infra:
  - Self-hosted Hetzner VPS (Docker Compose: scraper-agent, chrome-cdp, Temporal server+Postgres+UI, p1/p2 workers)
  - Supabase (hosted Postgres)
  - Dashboard on Vercel (separate repo: scraper-dashboard)
repo: paulalexeevich/Scraper-agent
taskSource: speckit-registry
url:
added: 2026-10-19
updated: 2026-10-19
---

# Scraper Agent (Retail Product Scraper)

> Autonomous system for scraping product data from retail e-commerce websites across multiple countries. A VPS agent polls a Supabase task queue, runs tiered two-phase scraping (URL collection, then detail enrichment), and learns from experience via a skill system and error knowledge base. Core pipeline phases are being migrated onto self-hosted Temporal for durability; a Next.js dashboard (separate repo) provides monitoring.

## What It Does

- Polls `scraping_tasks` in Supabase and runs a two-phase scrape per retailer: fast URL collection, then detail extraction for new URLs only
- Tiered browser pool with escalating anti-bot strength: HTTP → Chrome CDP (real profile) → Camoufox headless → Camoufox virtual+humanize → MCP browser + LLM → Browserbase cloud
- Skill system: per-retailer scraping skills stored in Supabase, updated after each run, auto-compacted; an error knowledge base built from past failures
- Phase 1 (URL collection) and Phase 2 (detail enrichment) are being re-expressed as per-retailer Temporal workflows (chunked, resumable, with canary/circuit-breaker/budget gates) running alongside the legacy poller during cutover
- Later specs (024-027) add an agent harness, browser/screenshot tools, category-crawl sub-agents, and a crawl-plan runner (prove → plan → run, with resumable chunked crawls) driven by LLM sub-agents
- Manual per-country scraper scripts (`brazil/`, `spain/`, `turkey/`, `hungary/`, `portugal/`, `poland/`) for initial data collection via Cursor skills
- Operator console, notifier/case system, product search/graph, and field-gate validation for data quality (specs 006-023)

## Stack & Infra

- **Code**: Python 3.12 monorepo (`agent/`), spec-kit driven (`specs/001`...`027`, each with `plan.md`/`tasks.md`); no GitHub Issues used as a task backend — work is tracked entirely via per-spec `tasks.md` checklists and merged via per-spec PRs
- **Deploy**: Docker Compose on a single Hetzner VPS (`/deploy` skill only — never `docker compose up` from a staging checkout); Temporal server + Postgres + UI self-hosted on the same VPS alongside scraper workers
- **Repo**: [Scraper-agent](https://github.com/paulalexeevich/Scraper-agent) (private)
- **Live**: no public URL — internal VPS service; dashboard lives in a separate `scraper-dashboard` repo on Vercel

## Status

**ACTIVE** — most active repo in the catalogue (pushed 2026-10-19). 27 specs total; 10 fully complete (004, 006, 007, 008, 011, 013, 014, 015, 016, 024). Heaviest open work is in **018-agent1-harness** (45/78 tasks done, 33 open). Spec 027 (crawl-plan-runner) is code-complete (45/53 tasks) with 8 remaining items blocked on VPS access, live trials, or an operator decision (see `specs/027-crawl-plan-runner/remaining-tasks.md`) rather than on more code.

## Key Decisions

- No GitHub Issues as task backend here — spec-kit's `tasks.md` per feature is the source of truth for "what's left"; this differs from lab-hub's model (GitHub Issues) and from this catalogue's general assumption that every project has an Issues-based task list
- Temporal (self-hosted on the same VPS) chosen for durable execution of long-running, resumable scraping workflows — chunked activities with the database as cursor, so a killed worker loses at most one chunk
- Legacy and Temporal orchestration coexist per-retailer during migration (`p1_orchestrator`/`p2_orchestrator` columns) — no big-bang cutover
- Single-VPS, self-hosted constitution (no new infra per feature); spend caps and attribution are a first-class constitutional principle (IX); failures become recorded "cases" rather than silent retries (X)

## Sessions

| Date | What happened |
|------|---------------|
| 2026-10-19 | Catalogued into Pavel-lab (previously untracked). Manually surveyed its task state via GitHub (zero Issues/PRs open — PRs are all merged, no Issues at all) and found the real task backend is per-spec `tasks.md` under `specs/`; computed completion ratios across all 27 specs as a first pass at "what tasks exist for this project" ahead of designing a daily Temporal-based task-aggregation agent. |

## Links

- GitHub: https://github.com/paulalexeevich/Scraper-agent
- Live: — (internal VPS)
- Handoff: `Scraper-agent/docs/PLAN.md`, `Scraper-agent/docs/wiki/`
