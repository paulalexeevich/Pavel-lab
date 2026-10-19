# Pavel-lab

Personal wiki of all AI-assisted projects. One page per project.  
Open in [Obsidian](https://obsidian.md) for graph view and linked navigation.

## Active (7)

| Project | Description | Stack | Repo |
|---------|-------------|-------|------|
| [RecTar — Store Location Target](projects/rectar.md) | Facing-target calculation pipeline for FMCG clients (Beiersdorf RO) + Streamlit app + Excel export | Python, Pandas, Streamlit, MySQL | [↗](https://github.com/paulalexeevich/RecTar) |
| [UniqEqAgent](projects/uniqeqagent.md) | Bay/shelf duplicate-capture detection and cross-visit fixture-identity matching for Trax retail scene data | Python, pandas, scikit-learn, MySQL | [↗](https://github.com/paulalexeevich/UniqEqAgent) |
| [Ailet Discovery KB & Governed-Write Portal](projects/ailet-kb.md) | CPG/retail discovery content KB (Opportunities/Personas/JTBD/Use Cases) + Next.js/Supabase governed-write portal prototype, shared MCP tools | Markdown, Next.js, Supabase, MCP | internal GitLab |
| [Pavel-lab Catalogue](projects/catalogue.md) | The frontmatter-driven markdown wiki (this repo) that catalogues every other vibe-coded project and idea | Markdown, Claude Code skills | [↗](https://github.com/paulalexeevich/Pavel-lab) |
| [lab-hub (Ideas & Tasks System)](projects/lab-hub.md) | Status overview, cross-project task browser, idea capture, and idea→task promotion system, plus an MCP server and Telegram bot | Next.js, Vercel, Octokit, MCP, Telegram Bot API | [↗](https://github.com/paulalexeevich/lab-hub) |
| [Retail Analytics](projects/retail-analytics.md) | Cross-source retail data analysis + talk-to-your-data Claude skill (Ailet Data Assistant) + Next.js phantom-stock dashboard (Magnit-analytics) | Python, Claude skill + MCP servers, Next.js | [↗](https://github.com/paulalexeevich/retail-analytics) |
| [Scraper Agent (Retail Product Scraper)](projects/scraper-agent.md) | Autonomous VPS agent scraping retail product data across countries; core phases migrating onto self-hosted Temporal | Python, Temporal, Supabase, Playwright/Camoufox, Gemini | [↗](https://github.com/paulalexeevich/Scraper-agent) |

## Shipped (0)

*None yet.*

## Ideas (1)

| Project | Description | Stack | Repo |
|---------|-------------|-------|------|
| [Sku Analyzer](projects/sku-analyzer.md) | — | — | [↗](https://github.com/paulalexeevich/sku-analyzer) |

## Abandoned (0)

*None yet.*

---

*8 entries · Updated 2026-10-19*

---

## Opening in Obsidian

1. Clone: `git clone https://github.com/paulalexeevich/Pavel-lab`
2. Open Obsidian → "Open folder as vault" → select the cloned folder
3. Graph view shows project relationships via `[[wikilinks]]`
4. Install [Dataview plugin](https://github.com/blacksmithgu/obsidian-dataview) for querying by status/stack

**Useful Dataview queries:**
```dataview
TABLE status, stack, added FROM "projects" SORT added DESC
```
```dataview
LIST FROM "projects" WHERE status = "active"
```
