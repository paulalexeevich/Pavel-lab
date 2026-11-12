---
slug: dedup-demo
name: Dedup Demo
status: active
type: demo / agent + human review UI
stack:
  - TypeScript / Node.js
  - "@earendil-works/pi-ai (provider-agnostic model calls)"
  - "@earendil-works/pi-durable (crash-safe agent runs)"
  - OpenSearch (candidate retrieval)
  - Next.js (React) — review UI
  - SQLite (decisions/reasoning/cost trace, human labels)
infra:
  - Local only — docker-compose for OpenSearch, no deployment yet
repo: paulalexeevich/dedup-demo
url:
added: 2026-11-12
updated: 2026-11-12
---

# Dedup Demo

> Open-source demo of a product de-duplication agent: OpenSearch candidate retrieval + a Pi Durable LLM agent that decides matches, plus a human-review UI to validate and eval its decisions.

## What It Does

- `src/catalog/` — OpenSearch index + ingest; `searchCandidates()` retrieves up to 30 candidates per product
- `src/agent/` — Pi Durable agent with `search_catalog` + `record_decision` tools; every decision, its reasoning, searches, tokens, and cost are saved to `data/demo.sqlite`. Crash-safe: killing the agent mid-run and rerunning skips finished products and resumes in-flight ones (idempotent `requestId`)
- `web/` — Next.js human-validation UI: a review queue, a per-product review screen (candidates, agent reasoning, search attempts, with the dataset label hidden until revealed), and an Evals page comparing agent runs. Reads packaged runs exported via `npm run export:run`; human labels persist locally in `data/reviews.sqlite`
- Demo dataset currently built from existing BrandBank exports (`scripts/build_demo_dataset.py`), flagged as temporary pending real scraped data

## Stack & Infra

- **Code**: TypeScript/Node agent + Next.js web UI, run locally
- **Deploy**: not deployed — OpenSearch via `docker-compose up -d` or `scripts/opensearch.sh`, everything else local
- **Repo**: [dedup-demo](https://github.com/paulalexeevich/dedup-demo) (public)
- **Live**: not deployed yet

## Status

**ACTIVE** — functional locally: ingest/search/agent/eval pipeline plus a working review UI (`npm run demo` on :3100, no OpenSearch needed since it reads packaged runs). Demo dataset is a placeholder (BrandBank exports), not yet the intended scraped data.

## Key Decisions

- `@earendil-works/pi-durable` for the agent run, specifically for crash-safety/idempotent resume rather than a plain script — matches the pattern already used in [[plan-audit-agent]]
- Review UI reads static exported run JSON rather than hitting OpenSearch/the live agent, so reviewers can evaluate without any backend running

## Sessions

| Date | What happened |
|------|---------------|
| 2026-11-12 | Repo discovered on GitHub (public, created today) and catalogued here from repo contents — no local session yet in this clone. |

## Links

- GitHub: https://github.com/paulalexeevich/dedup-demo
- Live: — (not deployed)
- Handoff: —
