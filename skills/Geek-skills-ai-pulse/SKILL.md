---
name: ai-pulse
version: 0.1.0
description: >
  Use this skill when the user wants a disciplined, low-noise sweep of the AI
  frontier: new models, papers, product launches, open-source releases, and
  opinion signals from leading researchers. Designed for a sustainable cadence
  (e.g. twice a week, 10 minutes each) rather than doom-scrolling. Produces a
  compact digest that answers three questions only: what changed, is there data
  or benchmarks, what are the explicit limitations. Chinese trigger examples:
  "AI 前沿扫一遍", "最新 AI 消息", "这周有什么新模型", "AI 雷达", "大佬动态",
  "arXiv 新论文", "AI 信息源", "跟一下 AI 进展". Do NOT use for answering a
  specific factual question about one model (that is a normal search), for
  writing a deep literature review (use deep-research), or for generating
  marketing-style hype summaries.
compatibility: Requires WebFetch and WebSearch. File read/write is optional
  and only used when the user asks to archive runs or diff against prior ones.
metadata:
  owner: personal
  category: intel / information-diet
  maturity: v0.1 — personal use, iterate against actual weekly runs
  outputs: >
    A single structured digest per run. Optional archived copy when the user
    opts into persistence.
---

# AI Pulse

A skill for keeping up with AI **without drowning in it**.

The premise: you do not need to follow everyone. You need a small, high-signal
list of people and institutions, a fixed cadence, and an extractor that only
reports **what changed, what the data says, and what is explicitly limited**.
Everything else is noise.

## Core principles

1. **Sources over summarizers.** Always go to the first-hand entry (lab blog,
   arXiv abstract, GitHub release). Secondary commentary is only useful after
   you have read the primary.
2. **Three-field extraction.** Each item is reduced to: `change / data /
   limits`. If a source has no data and no acknowledged limits, say so — that
   is itself a signal.
3. **Diff over recap.** A run is useful only to the extent it surfaces *new*
   information since the user last looked. Stale items are suppressed.
4. **Taste, not coverage.** Missing a minor release is fine. Hyping a nothing
   release is not.

## When NOT to use

- The user has a specific question about one model or one paper — just answer
  it, do not run a full radar sweep.
- The user wants a deep, multi-source synthesis with citations — use
  `deep-research` instead.
- The user wants social-media-style commentary or hot takes — this skill
  refuses to editorialize.

## What this skill produces

A single markdown digest with this shape (see
[extraction-template.md](references/extraction-template.md) for the exact
format):

```
# AI Pulse — <date> — <scope>

## Labs & companies
- <source>: <change> | <data/bench> | <limits>

## People signals
- <person>: <take> | <why it matters> | <caveat>

## Papers
- <title> (arXiv:xxxx): <claim> | <eval/bench> | <limits>

## Open source
- <repo> <version>: <change> | <benchmark/demo> | <limits>

## Watchlist (unchanged, skip)
- <source>: no update since <date>
```

That is all. No executive summary. No "key takeaways". If a section is empty,
the header is omitted.

## Active context bundle

**Always load on activation**
1. This `SKILL.md`
2. [references/extraction-template.md](references/extraction-template.md) —
   the exact output shape and three-field rules.

**Load on demand** (pick only what the current scope needs)
- [references/signals-people.md](references/signals-people.md) — the curated
  list of people to track, grouped by vision / research / engineering.
- [references/signals-institutions.md](references/signals-institutions.md) —
  lab and company first-hand entries (OpenAI, DeepMind, Anthropic, Meta,
  NVIDIA, Microsoft, Apple).
- [references/signals-academic-oss.md](references/signals-academic-oss.md) —
  arXiv, OpenReview, Papers with Code, GitHub, Hugging Face.
- [references/triage-heuristics.md](references/triage-heuristics.md) —
  how to decide what counts as a signal vs. noise, and how to downrank hype.

Do not load every reference on every run. The whole point of the skill is
restraint.

## Sub-commands (routing)

This skill is a **hybrid**: it has a complete source list as reference, and a
few named sub-commands so the user can scope the sweep. The user writes one of
these at invocation; if none is given, default to `+scan`.

| Command | Scope | Typical duration |
|---|---|---:|
| `+scan` | Full sweep across labs, people, papers, OSS. Default. | 8-12 min |
| `+labs` | Only lab/company official blogs & release pages. | 3-5 min |
| `+people` | Only people signals (X/blogs/talks). | 3-5 min |
| `+arxiv <keywords>` | arXiv + OpenReview + Papers with Code only. | 4-6 min |
| `+oss` | GitHub releases + Hugging Face trending only. | 3-5 min |
| `+extract <url>` | Apply the three-field template to one link. | <1 min |
| `+diff` | Compare this run to the most recent archived run. | varies |

Sub-commands compose with scope hints: `+scan since:2026-04-15`,
`+arxiv keywords:"reasoning,rlhf"`, `+labs only:anthropic,deepmind`.

## Workflow

### P0 — Clarify scope (5 seconds)

Before doing anything, decide:
- Which sub-command? (`+scan` if unspecified.)
- Since when? (Default: last 7 days. If the user archived a prior run, use
  that date.)
- Any explicit exclusions? (e.g., "skip arXiv, I'll read papers later").

State the resolved scope back to the user in one line, then proceed.

### P1 — Fetch

Run WebFetch/WebSearch against the entries in whichever `signals-*.md` files
the scope requires. Parallelize independent fetches.

Rules:
- Prefer the **official** entry (lab blog, arXiv, GitHub releases page) over
  news aggregators and Twitter threads.
- Respect the user's network constraints — if a source is known to be
  unreachable, note it and skip rather than retry.
- If a fetch returns a stale or unrelated page (common with lab blogs that
  redirect), try the research-index URL variant before giving up.

### P2 — Extract

For each fetched item, apply [extraction-template.md](references/extraction-template.md):

- `change`: one sentence, active voice, concrete. "Anthropic released Claude
  Opus 4.7" not "Anthropic made a big announcement".
- `data`: numeric result, benchmark name, or "no data disclosed". Never
  invent a number.
- `limits`: the explicit limitation the source itself states. If the source
  does not disclose any, write `limits: none disclosed` — that is a signal
  about the source, not a defect in the skill.

Apply [triage-heuristics.md](references/triage-heuristics.md) to decide
whether an item is worth including at all. A minor blog post about the same
release covered last week is noise — drop it.

### P3 — Diff (optional)

If the user archived a prior run and asked for `+diff` (or the skill inferred
it because a prior archive exists), suppress items whose `change` matches a
prior run. Surface only the delta.

When the user has not set up archiving, skip this phase entirely — do not
invent a fake "last week" state.

### P4 — Assemble

Produce the digest exactly per the template. No preamble, no "hope this
helps", no bulleted takeaways section. The structured digest is the entire
output.

### P5 — Archive (only if asked)

If the user says "save this" / "archive" / "存档":

- Let Claude decide the concrete path based on the working directory and any
  existing convention (e.g., `~/ai-pulse/YYYY-MM-DD.md` if that folder
  exists; otherwise propose one and confirm).
- Write the digest verbatim.
- Record the resolved date range so future `+diff` runs have an anchor.

Do not archive silently. Do not pick a path the user has not seen.

## Network notes (personal)

- `eastmoney.com` and subdomains are blocked on this network — irrelevant to
  AI sources but mentioned here because it is a general constraint.
- Some lab blogs (DeepMind, Anthropic) occasionally return cached intermediate
  pages via WebFetch. Prefer their research-index URL if the blog index looks
  stale.

## Red lines

- **Never fabricate a benchmark number.** If a number is not in the source,
  the `data` field is `not disclosed`.
- **Never recommend a product or model as "best".** This skill reports, it
  does not endorse.
- **Never generate a "key takeaways" or "executive summary" section.** The
  three-field rows are the summary. Additional prose defeats the purpose.
- **Never expand scope on the user's behalf.** If the user asked for
  `+labs`, do not also quietly sweep arXiv.
