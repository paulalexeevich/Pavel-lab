---
slug: catalogue
name: Pavel-lab Catalogue
status: active
type: internal tool
stack:
  - Markdown (frontmatter-driven wiki)
  - Claude Code skills (/catalogue, /handoff)
repo: paulalexeevich/Pavel-lab
url: null
added: 2026-10-11
updated: 2026-10-11
---

# Pavel-lab Catalogue

> The frontmatter-driven markdown wiki (this repo) that catalogues every other vibe-coded project and idea — one page per project in `projects/`, one per idea in `ideas/`, maintained via the `/catalogue` and `/handoff` skills.

## What It Does

- `projects/{slug}.md` / `ideas/{slug}.md` — one Obsidian-compatible page per real project / idea, frontmatter as the data layer (status, stack, repo, etc.)
- `/catalogue add|status|session|idea` and `/handoff` skills keep pages and `README.md` in sync without hand-editing
- Is itself the source-of-truth catalogue read/written by the `lab-hub` Ideas & Tasks System (specs/001-ideas-tasks-system) — lab-hub treats this repo as "just another GitHub repo" via the Contents API

## Stack & Infra

- **Code**: Markdown + Claude Code skills, no application code of its own
- **Deploy**: n/a — a git repo, browsable via GitHub and Obsidian
- **Repo**: this repo, `paulalexeevich/Pavel-lab`
- **Live**: not deployed — a wiki, not an app

## Status

**ACTIVE** — in daily use as the catalogue of record; now also the read/write backend for the `lab-hub` status/task/idea system.

## Key Decisions

- Markdown + frontmatter stays the single source of truth for project/idea metadata — `lab-hub` reads/writes it via the GitHub Contents API rather than duplicating it into a database (Pavel-lab Constitution I).

## Sessions

| Date | What happened |
|------|---------------|
| 2026-10-11 | Catalogued into itself to satisfy FR-016 of the Ideas & Tasks System spec (every catalogued project needs a `repo:` field) — this project's repo is Pavel-lab itself, no new repo needed. |

## Links

- GitHub: https://github.com/paulalexeevich/Pavel-lab
- Live: —
- Handoff: —
