# AI Engineering Toolkit

A working collection of AI agent skills, prompts, and reference notes I build and maintain for analytics and data engineering work.

The focus is on agents that do real work reliably — clear scope, explicit process, and verification steps that keep the output trustworthy rather than plausible-sounding.

## What's here

```
skills/     Claude Code / Claude agent skills (SKILL.md + supporting files)
doc/        Reference docs and style guides
```

More directories will land as the collection grows (prompt patterns, reference docs, evaluation notes).

## Docs

| Doc | What it covers |
| --- | --- |
| [`python_best_practices.md`](doc/python_best_practices.md) | Style guide for Python snippets embedded in skills — `pathlib` over `os.path`, explicit encoding, LBYL over try/except, idempotency, and other conventions for scaffolding scripts and inline heredocs. |

## Skills

| Skill | What it does |
| --- | --- |
| [`analytics-engineering-digest`](skills/analytics-engineering-digest/SKILL.md) | Builds a curated monthly digest of blog posts and podcast episodes across ~20 data, analytics, and AI sources. Ranks a top 3 with rationale, adds a supporting reading list, and picks podcast episodes through a staff-level lens. |

### `analytics-engineering-digest`

Staying current across the modern data stack is a real cost — dozens of company blogs and a half-dozen podcasts, most of it product marketing. This skill does the sweep and returns a short, ranked, justified list instead of a firehose.

Design decisions worth calling out:

- **Ranked across sources, not per source.** A top 3 drawn from the whole field, with a written rationale for each pick. A source contributing nothing is a valid outcome.
- **Three relevance lenses** — knowledge (technical depth), strategy (how teams organize data work), and work (applicable how-tos) — so selection is a judgment against stated criteria rather than a vibe.
- **A signal source.** The skill checks dbt Labs' Analytics Engineering Roundup on every run, since community discussion often surfaces the best post of the cycle before a per-blog search would.
- **Link validation as a hard gate.** The known failure mode of a research agent like this is emitting a URL that opens a blog index instead of the post. Every finalist link must be fetched and confirmed to land on the item itself, with explicit tells for index pages, redirect handling, and canonical-URL rules. If a link can't be verified, the item is dropped and the next candidate is promoted.
- **No padding.** A verified list of two beats a padded list of three, and the skill says so explicitly.

## Using these skills

Skills are directories containing a `SKILL.md` with YAML frontmatter (`name`, `description`) and instructions in the body. The `description` is what an agent matches against to decide when to invoke the skill.

**Claude Code** — copy or symlink a skill into your skills directory:

```bash
# Personal (available in every project)
ln -s "$PWD/skills/analytics-engineering-digest" ~/.claude/skills/

# Or project-scoped
ln -s "$PWD/skills/analytics-engineering-digest" /path/to/project/.claude/skills/
```

Then invoke it by name (`/analytics-engineering-digest`) or just describe the task — "what should I read this month?" — and let the agent match on the description.

**Claude.ai / API** — skills are plain Markdown, so the contents of a `SKILL.md` can be pasted into a project's custom instructions or supplied as a system prompt.

## Conventions

Each skill follows the same shape:

- **Frontmatter description written for retrieval.** It names the trigger phrases a user would actually say, not just what the skill does — an agent can only invoke a skill it recognizes as relevant.
- **An explicit process section.** Numbered steps the agent follows, so behavior is reproducible across runs.
- **Verification before output.** Any skill that gathers external information states how claims and links get checked, and what to do when verification fails.
- **A specified output format**, so results are consistent enough to skim.
- **Graceful degradation.** Sources go dead, feeds move, windows come up empty. Each skill says what to do in those cases rather than leaving the agent to improvise.

## Contributing

Personal repo, but issues and suggestions are welcome — particularly additional sources for the digest skill.
