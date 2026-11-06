---
slug: plan-audit-agent
name: Plan Audit Agent
status: active
type: durable background agent
stack:
  - TypeScript / Node.js 23
  - "@earendil-works/pi-ai (provider-agnostic model calls)"
  - "@earendil-works/pi-durable (SQLite-backed durable state)"
  - Octokit
  - Telegram Bot API
infra:
  - Dedicated VPS (not the Scraper-agent VPS) — Docker Compose, single long-running container
repo: paulalexeevich/plan-audit-agent
url:
added: 2026-11-01
updated: 2026-11-06
---

# Plan Audit Agent

> One durable agent, one job, two outputs: for every catalogued Pavel-lab project, compares its last 24h of commits + self-reported `docs/session_log.jsonl` against its own `docs/PLAN.md`, writes the finding to that project's own `lab-hub-audit` branch (never `main`), and sends one daily Telegram digest in Russian covering every project with activity.

## What It Does

- Reads every project from Pavel-lab's `projects/*.md` (same catalogue lab-hub reads)
- Per project: fetches commits since last run, self-report (`docs/session_log.jsonl`, written by that project's own Claude Code sessions via `/session-log`), and a `docs/PLAN.md` excerpt if one exists
- Classifies aligned / minor_drift / major_drift via a one-shot `pi-ai` call (provider configurable, defaults to Anthropic — not locked to one provider)
- Writes the day's finding to the project's own `lab-hub-audit` branch — structurally the only branch this agent can ever write to (see repo README's "Safety boundary")
- Sends one consolidated Telegram message per day
- Records every classification call's full prompt + raw response as a durable trace, browsable in a built-in web UI (`:8787`, bearer-token gated) where each trace can be marked correct/incorrect with a note

## Stack & Infra

- **Code**: TypeScript/Node, single long-running process (not serverless — needs a persistent local SQLite file for durable state)
- **Deploy**: Docker Compose on its own dedicated VPS
- **Repo**: [plan-audit-agent](https://github.com/paulalexeevich/plan-audit-agent) (private)
- **Live**: deployed and running a daily cron — confirmed 2026-11-06 via a real Telegram digest received

## Status

**ACTIVE** — deployed and running. Confirmed live 2026-11-06: sent a real daily Telegram digest
(`📊 Что сделано за сутки`) covering catalogued-project commits, matching `src/audit.ts` exactly. Uses its
own Telegram bot, separate from lab-hub's — now being consolidated to one shared bot (see Key Decisions).
`pi-ai`/`pi-durable` are both marked experimental upstream (v1.4.0); the scheduler and trace storage
deliberately use only `pi-durable`'s simplest primitives (`defineDoc`, one conversation per trace) rather
than its task/`sleepUntil`/generation-and-tool-execution API, to minimize dependence on that experimental
surface.

## Key Decisions

- One agent, not two systems — analysis, audit-branch writes, and Telegram notification all happen in a single process/codebase, after an earlier draft mistakenly split this across lab-hub (notify) and a separate VPS service (analyze)
- Runs on its own, separate VPS rather than Scraper-agent's — a watchdog shouldn't share a failure domain with what it watches
- Writes only ever land on each monitored repo's `lab-hub-audit` branch, never `main` — merging a finding into `main` is left as a deliberate human action
- `pi-ai` (not a direct Anthropic SDK call) for the model layer, specifically so the provider isn't locked to Anthropic
- SQLite (via `pi-durable`'s official Node support) for durable state, not DuckDB — DuckDB was considered but dropped: `pi-durable`'s internal migrations use SQLite-specific DDL (e.g. `STRICT` tables) that DuckDB doesn't support, and patching an experimental dependency's internals was judged too fragile
- lab-hub's own daily digest cron ran in parallel temporarily until this agent proved reliable; disabled 2026-11-06 in favor of this agent's digest, consolidating both projects onto one shared Telegram bot
- Traces stored as one `pi-durable` conversation per classification call (custom `TraceDoc`), not routed through `pi-durable`'s generation/tool-execution flow — keeps the trace feature on the same minimal-surface footing as the scheduler
- Eval UI is deliberately simple (correct/incorrect + free-text note), not a scoring rubric — the goal is a running record of "was the agent right," not a benchmark

## Sessions

| Date | What happened |
|------|---------------|
| 2026-11-01 | Built from scratch in one session: repo created, full implementation (catalogue read, GitHub commit/branch access, self-report read, pi-ai plan-check, pi-durable schedule, Telegram send), Dockerfile/compose/README for VPS deployment. Catalogued here. Not yet deployed. |
| 2026-11-03 | Added trace UI: `src/traces.ts` durably records each plan-check call's full prompt/response as a `pi-durable` conversation; `src/server.ts` (bearer-token-gated Express app, shares the harness with the schedule) serves a plain-JS page to browse traces and mark correct/incorrect with a note. Still not yet deployed. |
| 2026-11-06 | Confirmed actually deployed and running (catalogue entry had gone stale) — received its real daily Telegram digest. Disabled lab-hub's redundant digest cron and started consolidating both projects onto this agent's Telegram bot as the single shared bot. |

## Links

- GitHub: https://github.com/paulalexeevich/plan-audit-agent
- Live: — (not deployed)
- Handoff: `plan-audit-agent/README.md`
