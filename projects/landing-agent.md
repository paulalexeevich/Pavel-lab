---
slug: landing-agent
name: Landing Agent
status: active
type: landing-page generator
stack:
  - Node.js / npm
  - React + Vite (for Stageshow demo bundles)
  - vanilla HTML/CSS/JS (the generated landings themselves)
  - benjitaylor/skills — Stageshow (animated product-UI demo kit for Claude Code agents)
infra:
  - none — static HTML output, no backend, no deploy target yet
repo: https://github.com/paulalexeevich/landing-agent
url:
added: 2026-11-16
updated: 2026-11-16
---

# Landing Agent

> Agent that generates marketing landing pages for new products, with animated
> demo sections built via the [Stageshow](https://github.com/benjitaylor/skills) skill.

## What It Does

- Generates self-contained static HTML landing pages for new products — no
  build step or server required to view the result.
- Each landing's demo section is built with Stageshow: a React/CSS kit for
  believable animated product-UI mockups (cursor walkthroughs, alert →
  resolution flows), compiled down to a classic IIFE `<script>` + `<link>`
  bundle (React included) that drops straight into the raw HTML page.
- First worked example: a reference landing for [[plan-audit-agent]]'s
  "Автономный агент для проверки данных о продажах" page, combining (a) a
  Stageshow scene showing the agent checking a schema, raising an alert and
  resolving it, and (b) a hand-built vanilla-JS/SVG forecast chart with
  toggleable factors (no Stageshow/React — a plain product chart), styled
  after the interactive chart on mistral.ai/news/timestral.
- The generation pipeline itself (brief → sections → demo script → build →
  finished HTML) is not automated yet — this first landing was hand-assembled
  by the agent, and now serves as the template/reference for the next one.

## Stack & Infra

- **Code**: `embed/` (Vite entry + config that bundles one Stageshow scene into
  a standalone IIFE script), `examples/stageshow/<scene>/` (scene sources,
  our own content), `examples/<landing-slug>/` (finished reference landings)
- **Deploy**: none — static HTML, viewed via `file://` or any static host
- **Repo**: https://github.com/paulalexeevich/landing-agent (private)
- **Live**: not deployed yet

## Status

**ACTIVE** — scaffolded 2026-11-16 with one complete worked example (source +
built output) committed; the actual brief-to-landing generation pipeline is
still to be built.

## Key Decisions

- Stageshow bundles compile to a classic IIFE, not an ES module — a raw page
  opened via `file://` can't load `<script type="module">` cross-file (Chrome
  blocks it under CORS), so every demo bundle targets `formats: ['iife']` in
  Vite's lib mode instead of a normal app build.
- The Stageshow skill itself (`.agents/skills/stageshow`, ~1600 files including
  its full example gallery) is gitignored and fetched on demand via
  `npx skills add benjitaylor/skills --skill stageshow` — only our own scene
  sources under `examples/stageshow/` are committed, not the vendored kit.
- Not every demo needs Stageshow: the interactive forecast-with-factors chart
  in the reference example is plain SVG/JS, since it's a real data chart (needs
  hover tooltips, a legend, accessible color per the dataviz skill) rather than
  a "faux product UI" — Stageshow is for the latter specifically.

## Sessions

| Date | What happened |
|------|---------------|
| 2026-11-16 | Project created: scaffolded repo, pushed to GitHub (private), installed the Stageshow skill, ported over the reference landing (Stageshow demo scene + the vanilla-JS forecast chart) from an earlier one-off test as the first worked example. Desktop launcher added (`Открыть landing-agent.command` → opens the project in Claude Code). |

## Links

- GitHub: https://github.com/paulalexeevich/landing-agent
- Live: —
- Handoff: —
