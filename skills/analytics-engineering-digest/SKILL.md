---
name: analytics-engineering-digest
description: Gathers recent blog posts and podcast episodes (roughly last month) from well-known technology and data companies to build a curated digest for improving analytics engineering knowledge, strategy, and day-to-day work. Use this whenever the user asks for a "blog roundup," "digest," "reading list," "what should I read," "what should I listen to," or similar for analytics engineering, data engineering, data strategy, or the modern data stack — even if they don't name specific companies. Always surface a ranked top 3 of posts with rationale, a longer supporting list, and recent podcast episodes picked for someone growing toward a staff-level analytics/data engineering role.
---

# Analytics Engineering Digest

Builds a curated reading list from top data/analytics company blogs, surfaces the 3 most valuable posts for someone doing analytics engineering work, lists the rest as supporting reading, and recommends recent podcast episodes that build staff-level range.

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
- Elementary — https://www.elementary-data.com/blog

BI / analytics / activation:
- Mode Analytics — https://mode.com/blog
- Sigma Computing — https://www.sigmacomputing.com/blog
- Census — https://www.getcensus.com/blog
- Omni Analytics — https://omni.co/blog

AI / applied engineering (for AI-in-analytics angle):
- Anthropic — https://www.anthropic.com/news and https://www.anthropic.com/engineering

Community/opinion (include if relevant posts found):
- Locally Optimistic — https://locallyoptimistic.com

Signal source (check every run — often the fastest way to find what's actually worth reading):
- dbt Labs' "Analytics Engineering Roundup" newsletter/podcast — https://roundup.getdbt.com — a biweekly-ish roundtable (Tristan Handy + guests) that discusses and links the AI/data posts the community is actually talking about that week. Treat posts it highlights as strong candidates for the top 3 even if you wouldn't have surfaced them from the source blogs alone — search for the latest issue (e.g. `roundup.getdbt.com analytics engineering roundup <month> <year>`) and check what it references.

This list isn't exhaustive — if the user names other companies, add them using the same process.

## Podcast sources (check all, past ~30 days)

- The Analytics Engineering Podcast — dbt Labs (Tristan Handy); find recent episodes via `"Analytics Engineering Podcast" episode <month> <year>`
- Data Engineering Podcast — https://www.dataengineeringpodcast.com
- The Analytics Power Hour — https://analyticshour.io — strongest source for the influence/stakeholder/career side of staff-level work
- The Data Stack Show — https://datastackshow.com
- Drill to Detail — https://www.drilltodetail.com
- Catalog & Cocktails — data.world; find via `"Catalog and Cocktails" podcast <month> <year>`
- Monday Morning Data Chat — Joe Reis & Matt Housley; find via `"Monday Morning Data Chat" <month> <year>`

Podcast URLs drift and shows go on hiatus — if a link doesn't resolve or has nothing recent, find the show's current feed by search rather than assuming it's dead, and skip it silently if it genuinely has no episode in the window.

## Process

1. For each source, search for posts published in roughly the last 30 days (use `web_search` with queries like `"<company> blog" analytics engineering 2026` or `site:<domain> blog <month> <year>`, then `web_fetch` promising results to confirm dates and read enough to judge relevance). Also check the signal source above — it often surfaces the single best post of the cycle before it would otherwise turn up in a per-blog search.
2. Skip pure product-announcement/marketing posts unless they contain genuine technical or strategic insight (e.g., a new feature is fine to skip; a deep-dive on *why* they built it, or a benchmark, or a framework for thinking about a problem, is worth including).
3. Judge relevance to analytics engineering using three lenses:
   - **Knowledge**: technical depth (SQL/dbt patterns, data modeling, pipeline design, testing, orchestration).
   - **Strategy**: how teams should organize data work, build vs. buy, metrics layers, data culture, AI-assisted analytics workflows.
   - **Work**: practical, applicable how-tos and lessons learned from real implementations.
4. Rank and select the **top 3** across all sources — not per source. A source contributing zero posts to the top 3 is fine. Prioritize for genuine substance and applicability over hype.
5. List remaining relevant finds below the top 3 as a shorter "also worth a look" list (title, source, one-line why).
6. Then work the podcast sources for episodes released in the same ~30-day window. Confirm the release date and read the episode description (or show notes) before recommending — never recommend an episode from the show's general reputation alone.
7. Select **2–3 episodes** through a staff-level lens. What earns a slot:
   - **Scope beyond one team**: platform and architecture decisions, migrations, build vs. buy, cost/reliability tradeoffs at scale.
   - **Judgment under ambiguity**: guests reasoning through *why* they chose an approach and what it cost them — war stories over highlight reels.
   - **Influence without authority**: setting technical direction, org design for data teams, aligning stakeholders, driving standards across teams, mentoring.
   Deprioritize beginner explainers, tool tutorials, and vendor-pitch episodes where the guest is mainly demoing a product.
8. Aim for a mix rather than three variations on one theme — e.g. one architecture/technical-depth episode and one on influence/organizational scope beats three on modeling patterns.

## Link validation (required — do this before writing any output)

Every URL in the output must be one you actually fetched and confirmed lands on the item itself. The known failure mode of this skill is emitting a link that opens the blog's index page instead of the post, so treat this as a gate, not a nicety.

- **Never construct a URL.** No guessing slugs, no extrapolating from another post's URL pattern, no URLs from memory. The only links allowed in output are ones that came back from search and were then confirmed by `web_fetch`.
- **Re-fetch each finalist URL before writing it up**, and confirm the page is the item rather than a listing. Tells that you're on an index/listing page:
  - the URL stops at the blog root (`/blog`, `/blog/`, `/news`, `/resources`) with no post slug
  - the page shows many post teasers, pagination, or a "Load more" control
  - the page title is generic ("Blog", "Blog | Company") rather than the post's own title
  - there's no single byline or publish date
- **Confirm the fetched page's title and publish date match what you're about to print.** A mismatch means you have the wrong URL, not a formatting problem.
- **Follow redirects to their destination and judge that.** If a post URL redirects to the blog index, the post moved or is gone — the link is dead regardless of where it came from.
- **Link the canonical URL.** Strip search-engine redirector wrappers (`google.com/url?…`), AMP variants, and tracking parameters (`?utm_…`).
- **Podcast links follow the same rule**: the individual episode page, never the show's homepage, feed root, or a platform search result.
- **If you can't produce a verified direct link, drop the item** and promote the next best candidate. Never substitute a homepage, and never emit a link you haven't confirmed without labeling it unverified.

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

## Listen: Staff-Level Track

1. **[Episode title](url)** — Show, date, ~runtime
   Staff-level angle: 1-2 sentences on the specific scope, judgment, or influence lesson — what it teaches that a senior-level version of you wouldn't already know.

2. ...
```

Keep the "why it matters" concrete — name the specific idea or technique, not generic praise like "great read."

For the podcast picks, name the actual argument or story in the episode ("walks through why they moved off a centralized team model and what broke"), not the topic label ("about data team org design"). Include runtime when you can find it — it's a real input into what someone will commit to.

## Notes

- If a source's blog has nothing substantive in the window, skip it silently rather than padding the list with weak posts.
- Prefer original engineering/strategy posts over aggregated news roundups.
- Podcasts publish less densely than blogs. If the window has only one episode worth recommending, give one — don't pad to three. If it has none, say so in a single line rather than dropping the section without explanation.
- If the user wants a different time window (e.g. "last quarter") or a different set of companies, adapt the source list and window accordingly rather than rigidly following the defaults above. A longer window is often the right call for podcasts specifically.
- If the user asks only for reading, skip the podcast section — and vice versa.
- A verified list of two beats a padded list of three. If link validation knocks out a candidate and nothing else in the window is strong enough to promote, ship the shorter list.
