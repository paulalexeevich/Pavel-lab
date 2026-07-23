---
slug: uniqeqagent
name: UniqEqAgent
status: active
type: Python data-science/ML pipeline
stack:
  - Python
  - Pandas / NumPy
  - scikit-learn
  - PyMySQL
  - requests / Pillow
infra:
  - local (VPN to trax-cloud.com for MySQL; public S3 for probe photos)
repo: paulalexeevich/UniqEqAgent
url: null
added: 2026-07-20
updated: 2026-07-20
---

# UniqEqAgent

> Bay/shelf duplicate-capture detection and cross-visit fixture-identity matching for Trax retail scene data.

## What It Does

- Detects when two bay records in the same store visit are actually the same physical shelf photographed twice (a duplicate-capture / "wrong sticking" bug), using a dual cosine + weighted-Jaccard similarity gate plus per-product shelf-position concordance features and a trained GradientBoostingClassifier.
- Expanding scope: identifying when the *same physical fixture* is legitimately re-observed across *different* store visits (not a bug — a fixture-identity-tracking capability), building on the same feature set.
- Proven out end-to-end on beiersdorfro (Beiersdorf Romania), Primary Shelf.

## Stack & Infra

- **Code**: Python — pandas, scikit-learn (GradientBoostingClassifier), pymysql, requests, Pillow
- **Deploy**: local only; data access via VPN to Trax MySQL + public S3 probe-photo URLs
- **Repo**: [UniqEqAgent](https://github.com/paulalexeevich/UniqEqAgent) (private, local: ~/Desktop/ClaudeVibeCode/DupBay)
- **Live**: not deployed — analysis/notebook-style pipeline

## Status

**ACTIVE** — same-visit duplicate detection proven out (one open edge case: intentional twin displays, never stress-tested). Cross-visit fixture matching scoped and validated on one worked example, not yet a standing pipeline.

## Key Decisions

- Dual-threshold fix: gate candidate pairs on BOTH cosine similarity AND weighted Jaccard, not cosine alone — cosine-only cut 74,255 candidates down to 1,733 real ones on beiersdorfro (97% were the scale-invariance false-positive pattern).
- Position-concordance features (from `probedata.match_product_in_scene_recognition`, per-shelf relative order with a 10% tolerance for recognition noise) are the strongest signal — dominate model feature importance (77%), stronger and cheaper than LLM-judge or single-photo vision review.
- Honest model accuracy is 81%/61% recall (on independently-labeled pairs), not the inflated 98% CV number (most CV labels were derived from the model's own input features).
- Single-probe multi-bay scenes bias position features low — confirmed by direct visual inspection that a genuine same-fixture match scored NOT_DUPLICATE purely due to asymmetric photo framing across visits.

## Sessions

| Date | What happened |
|------|---------------|
| 2026-07-20 | Project scaffolded: reorganized flat `send_to_pavel_similarities/` dump into src/data/models/outputs/legacy layout, extracted hardcoded DB password into env-var config, wrote CLAUDE.md covering both capabilities, git init + GitHub repo + push, desktop launcher created. Ad-hoc cross-visit test (scenes 1904567 vs 1945629, store 329) uncovered the single-probe framing blind spot above. |

## Links

- GitHub: [UniqEqAgent](https://github.com/paulalexeevich/UniqEqAgent)
- Live: —
- Handoff: HANDOFF.md (in repo)
