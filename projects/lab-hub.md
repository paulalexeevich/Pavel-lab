---
slug: lab-hub
name: lab-hub (Ideas & Tasks System)
status: active
type: internal tool
stack:
  - Next.js (App Router, TypeScript)
  - Vercel (hosting, Cron)
  - Octokit (GitHub REST + GraphQL)
  - gray-matter (frontmatter parsing)
  - "@modelcontextprotocol/sdk (MCP server)"
  - Telegram Bot API
infra:
  - Vercel (paulalexeevichs-projects scope)
  - GitHub (paulalexeevich/lab-hub)
repo: paulalexeevich/lab-hub
url: https://lab-hub-vert.vercel.app
added: 2026-10-11
updated: 2026-10-11
---

# lab-hub (Ideas & Tasks System)

> A status overview, cross-project task browser, idea capture, and idea→task promotion system for every Pavel-lab catalogued project, plus an MCP server exposing the same capabilities to agents and a Telegram bot for notifications. Implements `specs/001-ideas-tasks-system` from this repo — no database; reads/writes Pavel-lab's own `projects/*.md`/`ideas/*.md` via the GitHub Contents API, and GitHub Issues as the task backend.

## What It Does

- Project overview: every catalogued project's status plus live open/closed GitHub Issue counts and last activity, with graceful "no tasks tracked" handling for projects with no repo or a non-GitHub repo (e.g. `ailet-kb`'s GitLab repo)
- Idea capture (app, Telegram bot, or agent) writing straight into the existing `ideas/{slug}.md` catalogue format
- Explicit, idempotent idea→task promotion: one confirmed action creates exactly one GitHub Issue and records it back on the idea file — never silently, never twice
- Cross-project and per-project task browsing
- MCP server (`/api/mcp`) exposing `list_projects`, `list_ideas`, `list_tasks`, `create_idea`, `promote_idea_to_task` to agents, with the same explicit-confirmation rules as the UI
- Telegram bot: idea-capture command plus notifications (idea captured, task created/closed via GitHub webhook, periodic digest via Vercel Cron)

## Stack & Infra

- **Code**: Next.js/TypeScript, one shared `lib/` core consumed by the UI, the MCP route, and the Telegram/GitHub webhook handlers (no logic duplicated per surface)
- **Deploy**: Vercel, `paulalexeevichs-projects` scope, GitHub push-to-deploy connected (also deployable manually via `vercel --prod`)
- **Repo**: [lab-hub](https://github.com/paulalexeevich/lab-hub) (private)
- **Live**: https://lab-hub-vert.vercel.app (bearer-token + Vercel SSO gated — see README for required manual setup)

## Status

**ACTIVE** — all 7 user stories (spec `specs/001-ideas-tasks-system`) implemented, pushed, and deployed. GitHub `issues` webhooks are registered on every GitHub-hosted catalogued repo. Still needs, before it's fully usable day to day: `GITHUB_TOKEN` set manually (a broad-scope credential, deliberately not auto-provisioned) and a Telegram bot created via @BotFather. See `lab-hub/README.md` for exact steps.

## Key Decisions

- No database anywhere — Pavel-lab's markdown catalogue and each project's GitHub Issues are read/written live via their APIs (Pavel-lab Constitution I & II)
- Every catalogued project gets its own GitHub repo as task backend (no shared aggregator repo) — except `ailet-kb`, whose existing GitLab repo is treated as a permanent, legitimate "no tasks tracked" state rather than migrated
- Single shared bearer token for UI + MCP auth (no multi-user system) — a personal, single-user tool
- `GITHUB_TOKEN` was deliberately left for manual setup rather than auto-populated from the building agent's own `gh` CLI session — a broad-scope personal credential is too high-blast-radius to provision without explicit, real-time confirmation

## Sessions

| Date | What happened |
|------|---------------|
| 2026-10-11 | Full spec→plan→tasks→implement cycle via spec-kit. Repo created, Next.js app scaffolded, all 7 user stories implemented and pushed. First deploy attempt landed under the wrong personal Vercel account (`pavelpopkov-2586s-projects`, not connected to the `paulalexeevich` GitHub login) and hit Vercel's Hobby-plan commit-author verification block; re-linked and redeployed under the correct `paulalexeevichs-projects` scope, which auto-connected GitHub push-to-deploy. GitHub `issues` webhooks registered on all 5 GitHub-hosted catalogued repos. 53/54 tasks done — only the full `quickstart.md` run-through remains, pending `GITHUB_TOKEN`. |

## Links

- GitHub: https://github.com/paulalexeevich/lab-hub
- Live: https://lab-hub-vert.vercel.app
- Handoff: `lab-hub/README.md`
