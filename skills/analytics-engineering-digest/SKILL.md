---
name: analytics-engineering-digest
description: Gathers recent blog posts (roughly last month) from well-known technology and data companies to build a curated digest for improving analytics engineering knowledge, strategy, and day-to-day work. Use this whenever the user asks for a "blog roundup," "digest," "reading list," "what should I read," or similar for analytics engineering, data engineering, data strategy, or the modern data stack — even if they don't name specific companies. Always surface a ranked top 3 with rationale, plus a longer supporting list.
---

# Analytics Engineering Digest

Builds a curated reading list from top data/analytics company blogs, surfaces the 3 most valuable posts for someone doing analytics engineering work, and lists the rest as supporting reading.

## Source list (check all, past ~30 days)

Core analytics engineering / modern data stack:
- dbt Labs — https://www.getdbt.com/blog
- Hex — https://hex.tech/blog
- Snowflake — https://www.snowflake.com/blog
- Databricks — https://www.databricks.com/blog
- Fivetran — https://www.fivetran.com/blog
- Airbyte — https://airbyte.com/blog

Data quality / observability / governance:
- Monte Carlo — https://www.montecarlodata.com/blog
- Metaplane — https://www.metaplane.dev/blog
- Atlan — https://atlan.com/blog
- Secoda — https://www.secoda.co/blog

BI / analytics / activation:
- Mode Analytics — https://mode.com/blog
- Sigma Computing — https://www.sigmacomputing.com/blog
- Census — https://www.getcensus.com/blog

AI / applied engineering (for AI-in-analytics angle):
- Anthropic — https://www.anthropic.com/news and https://www.anthropic.com/engineering

Community/opinion (include if relevant posts found):
- Locally Optimistic — https://locallyoptimistic.com

Signal source (check every run — often the fastest way to find what's actually worth reading):
- dbt Labs' "Analytics Engineering Roundup" newsletter/podcast — https://roundup.getdbt.com — a biweekly-ish roundtable (Tristan Handy + guests) that discusses and links the AI/data posts the community is actually talking about that week. Treat posts it highlights as strong candidates for the top 3 even if you wouldn't have surfaced them from the source blogs alone — search for the latest issue (e.g. `roundup.getdbt.com analytics engineering roundup <month> <year>`) and check what it references.

This list isn't exhaustive — if the user names other companies, add them using the same process.

## Process

1. For each source, search for posts published in roughly the last 30 days (use `web_search` with queries like `"<company> blog" analytics engineering 2026` or `site:<domain> blog <month> <year>`, then `web_fetch` promising results to confirm dates and read enough to judge relevance). Also check the signal source above — it often surfaces the single best post of the cycle before it would otherwise turn up in a per-blog search.
2. Skip pure product-announcement/marketing posts unless they contain genuine technical or strategic insight (e.g., a new feature is fine to skip; a deep-dive on *why* they built it, or a benchmark, or a framework for thinking about a problem, is worth including).
3. Judge relevance to analytics engineering using three lenses:
   - **Knowledge**: technical depth (SQL/dbt patterns, data modeling, pipeline design, testing, orchestration).
   - **Strategy**: how teams should organize data work, build vs. buy, metrics layers, data culture, AI-assisted analytics workflows.
   - **Work**: practical, applicable how-tos and lessons learned from real implementations.
4. Rank and select the **top 3** across all sources — not per source. A source contributing zero posts to the top 3 is fine. Prioritize for genuine substance and applicability over hype.
5. List remaining relevant finds below the top 3 as a shorter "also worth a look" list (title, source, one-line why).

## Output format (chat, not a file unless the user asks)

```
## Top 3 This Month

1. **[Title](url)** — Source, date
   Why it matters: 1-2 sentences on the specific knowledge/strategy/work takeaway.

2. ...
3. ...

## Also Worth a Look
- [Title](url) — Source — one-line reason
- ...
```

Keep the "why it matters" concrete — name the specific idea or technique, not generic praise like "great read."

## Notes

- If a source's blog has nothing substantive in the window, skip it silently rather than padding the list with weak posts.
- Prefer original engineering/strategy posts over aggregated news roundups.
- If the user wants a different time window (e.g. "last quarter") or a different set of companies, adapt the source list and window accordingly rather than rigidly following the defaults above.
