---
slug: ailet-kb
name: Ailet Discovery KB & Governed-Write Portal
status: active
type: internal tool
stack:
  - Markdown (content KB)
  - Next.js
  - Supabase (Postgres, RLS, JWT)
  - MCP (Model Context Protocol)
infra:
  - Vercel (2 deployments, one behind SSO)
  - Supabase (hosted Postgres)
repo: gitlab.ailet.net:product-management/revgro.git
url: https://orion-ailet-knowledgebase.vercel.app
added: 2026-07-23
updated: 2026-07-23
---

# Ailet Discovery KB & Governed-Write Portal

> CPG/retail discovery research knowledge base (Opportunities → Personas → JTBD → Use Cases) plus a working Next.js + Supabase prototype that turns it into a governed-write portal: master data is only ever changed through a propose → review → apply diff flow, exposed to Product/Marketing/Sales roles via a shared MCP server (local stdio + remote HTTP, same tool implementations).

## What It Does

- `data/` — plain-Markdown source of truth: Opportunities, Personas, JTBD, competitor data, skills. No DB, no versioning, no access control at the file level yet.
- `app/` — Next.js + Supabase prototype implementing a 3-persona (Product / Marketing-Sales / Admin) slice of the TZ spec: Opportunities/Personas/JTBD as first-class entities, inline edit, archive/restore/revert, evidence logs with computed metric suggestions.
- `app/mcp-server/` — one set of MCP tool implementations shared by a local stdio server (3 seeded test roles) and a remote HTTP endpoint at `/api/mcp` (personal-access-token auth, RLS enforced end-to-end via short-lived per-user JWT).
- `app/skills/` — 8 packaged Claude Code skills, one per functional discovery role, rendered in-app via `/process/<role>` pages.
- 5-stage opportunity validation pipeline (Define → Pain confirmed → Interest → Economic value → Prioritization) with TAV (`Reach × Economic value`) scoring, tracked per-country.

## Stack & Infra

- **Code**: Markdown content KB + Next.js/TypeScript app, forked from OB1's dashboard for UI conventions
- **Deploy**: Vercel — primary `orion-ailet-knowledgebase.vercel.app`, original `app-seven-iota-35.vercel.app` (behind Vercel SSO)
- **Data**: Supabase Postgres, RLS + governed-write (`record_updates`) pattern modeled on the OB1 `agent_memories` project
- **Repo**: internal GitLab, `product-management/revgro` (not GitHub)

## Status

**ACTIVE** — content KB is mature and in daily use; `app/` prototype has personas/opportunities/JTBD as first-class entities, archive/restore/revert, evidence logging, and double-submit guards on create forms. Admin role has no dedicated skill yet (deliberately deferred). See `HANDOFF.md`/`app/HANDOFF.md` in-repo for the current pick-up list.

## Key Decisions

- Governed writes (propose → confirm → apply) instead of direct edits to master data — mirrors the file-based `OPPORTUNITY-UPDATE-TOOL-SPEC.md` bridge proposal and the Supabase TZ spec
- `validation_stage` stays one flat value per opportunity; per-country signal/economic-value/reach live on separate `opportunity_country_validation` / `persona_country_reach` tables; TAV sums across markets rather than being one computed number
- Granular opportunity layer (opportunity × JTBD pair) added 2026-07-12 to collect signal per specific persona's specific job, accepting cross-persona reach double-counting risk in exchange for a real rollup number (flagged via `spans_multiple_personas`)
- MCP access kept to one tool implementation shared by local/remote transports so the two surfaces can't drift apart

## Sessions

| Date | What happened |
|------|---------------|
| 2026-07-23 | Catalogued into Pavel-lab; audited `/init`-style project settings (found `.claude/settings.json` missing project-side, secrets correctly gitignored via `.mcp.json`) |

## Links

- Repo: gitlab.ailet.net:product-management/revgro.git (internal, not GitHub)
- Live: https://orion-ailet-knowledgebase.vercel.app
- Handoff: `app/HANDOFF.md`, `HANDOFF.md`
