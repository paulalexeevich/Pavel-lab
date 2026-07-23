# Pavel-lab

Personal wiki of all AI-assisted projects. One page per project.  
Open in [Obsidian](https://obsidian.md) for graph view and linked navigation.

## Active (2)

| Project | Description | Stack | Repo |
|---------|-------------|-------|------|
| [RecTar — Store Location Target](projects/rectar.md) | Facing-target calculation pipeline for FMCG clients (Beiersdorf RO) + Streamlit app + Excel export | Python, Pandas, Streamlit, MySQL | [↗](https://github.com/paulalexeevich/RecTar) |
| [UniqEqAgent](projects/uniqeqagent.md) | Bay/shelf duplicate-capture detection and cross-visit fixture-identity matching for Trax retail scene data | Python, pandas, scikit-learn, MySQL | [↗](https://github.com/paulalexeevich/UniqEqAgent) |

## Shipped (0)

*None yet.*

## Ideas (0)

*None yet.*

## Abandoned (0)

*None yet.*

---

*2 entries · Updated 2026-07-20*

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
