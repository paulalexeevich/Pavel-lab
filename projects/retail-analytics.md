---
slug: retail-analytics
name: Retail Analytics
status: active
type: data analysis / internal tool (multi-surface)
stack:
  - Python (pandas, SQLAlchemy, ClickHouse + Supabase/Postgres connectors)
  - Claude skill + MCP servers (Ailet Data Assistant)
  - Next.js (gap-dashboard)
infra:
  - GitHub (paulalexeevich/retail-analytics)
  - Supabase-hosted warehouse (synced from 4 live retail data sources)
  - Vercel (gap-dashboard; existing project link appears stale/unverified)
repo: paulalexeevich/retail-analytics
url:
added: 2026-10-11
updated: 2026-10-11
---

# Retail Analytics

> Cross-source retail data analysis spun off from `RetailAnalyst` (2026-08-28), now home to three bundled surfaces: raw cross-source analysis, a talk-to-your-data Claude skill ("Ailet Data Assistant"), and a Next.js phantom-stock dashboard ("Magnit-analytics"). Local-only until 2026-10-11 — had real code and history (2 commits back to 2026-09-08) but no GitHub remote until this session pushed it.

## What It Does

- **Core analysis** (`analyses/`, `mapping/`, `schema/`) — cross-source retail data analysis across 4 live sources (Supabase Postgres for `sales_stock`/`magnit_facings`, ClickHouse for `ehrmann_visits`/`progressfood_visits`), with crosswalk tables matching stores and products across sources that don't share IDs (text/attribute-matched)
- **Ailet Data Assistant** (`specs/001-ailet-data-assistant/`) — a talk-to-your-data Claude skill backed by two MCP servers (`ailet/mcp_admin`, `ailet/mcp_user`), with a human-approved dynamic-KPI-creation flow, computing against a Supabase-hosted synced warehouse (not the 4 live sources directly). Spec-kit-driven; as of 2026-09-17 all 195 tasks (T001–T195) implemented and passing, including a product entity-resolution tool measuring 100% (48/48) top-3 accuracy on real data.
- **Magnit-analytics** (`gap-dashboard/`) — a Next.js app turning the core analysis's phantom-stock research into a live, browsable, MCP-calibratable dashboard. Not spec-kit-driven.

## Stack & Infra

- **Code**: Python (core analysis + Ailet Data Assistant MCP servers), Next.js/TypeScript (`gap-dashboard`)
- **Deploy**: `gap-dashboard` has a `.vercel` project link (`ailet-gap-dashboard`) but it resolves to a Vercel org/team not reachable from the current `paulalexeevich` CLI session — deploy status unverified, may need re-linking
- **Repo**: [retail-analytics](https://github.com/paulalexeevich/retail-analytics) (pushed 2026-10-11; was local-only before that)
- **Live**: not verified — see Deploy note above

## Status

**ACTIVE** — substantial working code across all three surfaces. Ailet Data Assistant's spec-kit scope (T001–T195) is fully implemented per `HANDOFF.md` (as of 2026-09-17); `preview.py`'s pre-existing gap for `sales_stock` product-scoped queries (raises a raw DB error instead of a clean response) is a known, unfixed issue. Latest commit (2026-10-11) bundled further work: gap taxonomy/safety-stock pipeline, the Magnit gap-dashboard app, and an Ailet Data Assistant knowledge layer — not yet individually verified in this session.

## Key Decisions

- Kept as one repo with three surfaces (core analysis, Ailet Data Assistant, Magnit-analytics) rather than splitting, to share the same crosswalked data/mapping layer — see `README.md` for the full rationale and boundaries between them
- `.env` (read-only DB credentials for all 4 sources + the Ailet warehouse) is gitignored, never committed — confirmed before pushing to GitHub in this session
- Root-level `CLAUDE.md`/`roadmap.md`/`constitution.md` are copied verbatim from the parent `RetailAnalyst` repo for background only — their file-path references don't resolve from this repo

## Sessions

| Date | What happened |
|------|---------------|
| 2026-09-08 | Initial commit: spun off from `RetailAnalyst`, added Ailet Data Assistant feature |
| 2026-10-11 | Discovered the repo had real local code (2 commits, no remote) while the catalogue's `retail-analytics.md`/GitHub repo were empty placeholders from FR-016 work. Pushed local `main` to `paulalexeevich/retail-analytics` (confirmed `.env` and other secrets were gitignored first) and rewrote this wiki page with real content. |

## Links

- GitHub: https://github.com/paulalexeevich/retail-analytics
- Live: —
- Handoff: `retail-analytics/HANDOFF.md`
