---
slug: rectar
name: RecTar — Store Location Target
status: active
type: internal tool
stack:
  - Python
  - Pandas / NumPy
  - Streamlit
  - MySQL (Trax)
  - openpyxl
infra:
  - local (VPN to trax-cloud.com for MySQL)
repo: paulalexeevich/RecTar
url: http://localhost:8501
added: 2026-07-01
updated: 2026-07-01
---

# RecTar — Store Location Target

> Pipeline that calculates per-product per-store facing targets for FMCG clients (Beiersdorf RO). Includes shelf grouping explorer, Streamlit validation app, and Excel export.

## What It Does

- Runs a "second-max" redistribution algorithm to compute recommended SKU facing targets per store
- Supports primary and secondary shelf (separate pipelines, separate data)
- Streamlit app (2 pages + export): Summary view, Store Detail view (SKU × visit facings + probe images), Excel Export
- Excel export: multi-sheet workbook with SKU facings by visit, current/recommended/segment-median targets, single-scene filter

## Stack & Infra

- **Code**: Python — pandas, NumPy (target_calculator_v5), Streamlit, openpyxl
- **Deploy**: local only (Streamlit on port 8501); production entry point via Trax framework
- **Repo**: paulalexeevich/RecTar (local: ~/Desktop/ClaudeVibeCode/RecTar)
- **MySQL**: mysql.beiersdorfro-prod.us-east-1.trax-cloud.com (VPN required)

## Status

**ACTIVE** — Streamlit app running, pipeline working, Excel export page just added (needs testing)

## Key Decisions

- Category derived from `att3__att8` pair (beiersdorfro), `att4` (mondelezde) — see CLAUDE.md
- `require_both=True` on pair keys: never fallback to broad single-attribute categories
- Second-max target: uses second-largest observed daily total, falls back to max if only one observation
- Long format for Excel export Data sheet: correct source for user-created pivot tables with slicers

## Sessions

| Date | What happened |
|------|---------------|
| 2026-07-01 | Added Excel export page (`app/pages/export.py`) — multi-sheet workbook with SKU facings, targets, single-scene filter; page not yet tested end-to-end |

## Links

- GitHub: paulalexeevich/RecTar
- Live: http://localhost:8501
- Handoff: StoreLocationTarget/HANDOFF.md
