---
name: ai-radar
version: 0.2.0
description: >
  Auto-pipeline that sweeps the AI frontier and pushes a structured digest to
  Feishu / Lark. Four stages: collect (from sources.yaml) → filter (drop
  hype) → extract (five-field prompt) → output + push. Designed to run on a
  sustainable cadence (twice a week, ~10 min review each) and to produce a
  digest that the user *actually reads* — no hype, no "key takeaways". Scores
  each item H/M/L and tracks tag trends across runs so the user sees not just
  "what's new" but "what's rising / falling". Chinese trigger examples:
  "AI 前沿扫一遍", "跑一下 AI Radar", "最新 AI 消息", "这周有什么新模型",
  "推一份 AI 周报到飞书", "大佬动态", "arXiv 新论文", "AI 信息源". Do NOT
  use for one-off factual questions about a single model (do a normal
  search), for deep literature reviews (use deep-research), or for generating
  marketing-style hype summaries.
compatibility: >
  Requires WebFetch and WebSearch. Feishu push requires `lark-cli` with user
  identity and the `im:message` + `im:message.send_as_user` scopes. File I/O
  is used for `sources.yaml` and optional run archives.
metadata:
  owner: personal
  category: intel / information-diet
  maturity: v0.2 — pipeline framing, Feishu push, scoring, pattern library
  outputs: >
    A single structured digest per run, pushed to Feishu (self DM) by default.
    Optional archived copy on disk when the user opts into persistence.
---

# AI Radar

A disciplined **pipeline** for keeping up with AI without drowning in it.

The mental model is not "a thing that lists news" — it is:

```
[ sources.yaml ]  →  fetch  →  filter  →  extract  →  score  →  digest  →  push
     (what)        (collect)   (noise)   (5-field)  (H/M/L)   (assemble) (Feishu)
```

Each stage has a clear contract, so a bad run is easy to locate: if the digest
is thin, is the collector empty, or did the filter over-drop? Is extract
hallucinating, or is scoring too strict?

## Core principles

1. **Sources over summarizers.** Always go to the first-hand entry (lab blog,
   arXiv abstract, GitHub release). Secondary commentary is only useful after
   the primary is read.
2. **Extraction over summarization.** The skill does not produce "what this
   means for the industry" prose. It extracts five fixed fields per item.
3. **Diff over recap.** A run is useful only insofar as it surfaces *new*
   information since the user last looked. Tag trends across runs are the
   higher-order signal.
4. **Taste, not coverage.** Missing a minor release is fine. Hyping a nothing
   release is not. Scores are honest: most items are `M`; `H` is rare.
5. **One place to read it.** The digest lives in Feishu (self DM). The
   user's job is to glance at it twice a week; the skill's job is to make
   that glance worth something.

## When NOT to use

- The user has a specific question about one model or one paper — just answer
  it, do not run the pipeline.
- The user wants a deep multi-source synthesis with citations — use
  `deep-research`.
- The user wants hot takes or commentary — this skill refuses to editorialize.

## What this skill produces

A structured digest pushed to Feishu, and optionally written to disk. Shape
per [extraction-template.md](references/extraction-template.md):

```
# AI Radar — <date> — <scope>

## Labs & companies
- [H] <source>: <what> | <innovation> | <data> | <limits>

## People signals
- [M] <person>: <take> | why matters | caveat

## Papers
- [H] <title> (arXiv:xxxx): <claim> | <eval> | <limits>

## Open source
- [M] <repo> <version>: <change> | <bench> | <license/hw>  #patterns: actor-model, memory-abstraction

## Trends (across last 3 runs)
- agent ↑, reasoning ↑, multimodal ↓

## Watchlist (unchanged, skip)
- <source>: no update since <date>
```

No executive summary. No "key takeaways". Empty sections are omitted.

## Active context bundle

**Always load on activation**
1. This `SKILL.md`
2. [extraction-template.md](references/extraction-template.md) — the five-field
   output contract.
3. [sources.yaml](sources.yaml) — the collector manifest.

**Load on demand**
- [signals-people.md](references/signals-people.md) — detail on the curated
  people list (only if `+people` or scope includes people).
- [signals-institutions.md](references/signals-institutions.md) — lab-specific
  fetch quirks (only if `+labs` or scope includes labs).
- [signals-academic-oss.md](references/signals-academic-oss.md) — arXiv / OSS
  extraction rules.
- [triage-heuristics.md](references/triage-heuristics.md) — what counts as a
  signal vs. noise, how to downrank hype.
- [scoring-and-trends.md](references/scoring-and-trends.md) — scoring formula
  and trend tracking (load before P4).
- [pattern-library.md](references/pattern-library.md) — accumulated design
  patterns. Load only when an OSS/paper item merits tagging (most runs don't
  need this file).

Do not load every reference on every run.

## Sub-commands

This is a **hybrid** skill: a structured manifest (`sources.yaml`) drives
the collector, and named sub-commands scope the sweep. Default: `+scan`.

| Command | Scope | Typical duration |
|---|---|---:|
| `+scan` | Full pipeline over all enabled sources. Default. | 8-12 min |
| `+labs` | Only `type: blog` sources tagged `lab`. | 3-5 min |
| `+people` | Only `type: x` / `type: blog` tagged `person`. | 3-5 min |
| `+arxiv <keywords>` | arXiv + OpenReview + Papers with Code only. | 4-6 min |
| `+oss` | GitHub releases + Hugging Face trending only. | 3-5 min |
| `+extract <url>` | Apply the five-field template to one link. | <1 min |
| `+trend` | Only the `## Trends` section, no new fetch. | <1 min |
| `+diff` | Compare this run to the most recent archived run. | varies |
| `+push` | Push the last produced digest to Feishu again. | <10 s |

Scope hints compose: `+scan since:2026-04-15`, `+arxiv keywords:"reasoning"`,
`+scan exclude:oss`.

## Workflow

### P0 — Scope (5 seconds)

Resolve: which sub-command, since when (default 7 days), what exclusions.
State it back in one line, then proceed.

### P1 — Collect

Read `sources.yaml`. For each source whose `enabled: true` and whose tags
match the scope, fetch. Parallelize independent fetches. Rules:

- First-hand URL only. No aggregators.
- Respect known network constraints (e.g., `eastmoney.*` blocked on this
  network — irrelevant to AI but a general rule).
- If a fetch returns stale/redirect content (common with DeepMind blog),
  try the entry's `fallback_url` before giving up.
- Failures go under `## Unreachable` at assembly time, never silently
  dropped.

### P2 — Filter

Apply [triage-heuristics.md](references/triage-heuristics.md):

- Inclusion test: first-hand? concrete `what`? has data OR stated limit OR
  top-lab exception? not a repeat of prior runs?
- Downrank test: self-praising adjectives, bench without bench name, no
  modern baseline, no inspectable artifact, thread summarizing others.
- An honest thin week beats a padded fat one. If filter drops everything,
  say so.

### P3 — Extract

For each surviving item, fill five fields per
[extraction-template.md](references/extraction-template.md):

1. `what` — what this is (model / paper / product / post) in one sentence.
2. `innovation` — 1–3 bullet-sized claims of what's new. No adjectives of
   impact.
3. `data` — benchmark name + score, or `not disclosed`. Never invent.
4. `limits` — what the source itself admits, or `none disclosed`.
5. `worth-watching` — `H` / `M` / `L` per scoring rubric. Default is `M`;
   `H` requires justification.

### P4 — Score & tag

Load [scoring-and-trends.md](references/scoring-and-trends.md). For each
item:

- Compute `score = institution_weight + tech_breakthrough + community_heat`
  per the rubric.
- Assign `worth-watching` from the score bucket.
- Tag with topic labels from a fixed vocabulary (`agent`, `reasoning`,
  `multimodal`, `rlhf`, `efficiency`, `safety`, `tool-use`, `retrieval`,
  `inference`, `training`).

If an OSS/paper item maps to one or more entries in
[pattern-library.md](references/pattern-library.md), append `#patterns:
<name>, <name>` inline. Patterns are *optional enrichment* — do not
stretch.

### P5 — Trends (if archive exists)

Compare current-run tag counts to the previous 2–3 archived runs. Produce a
short `## Trends` section: `<tag> ↑/↓/→`. Without archives, skip this phase
silently — do not fabricate a baseline.

### P6 — Assemble

Produce the markdown digest exactly per the template. Omit empty sections.
No preamble, no trailing summary.

### P7 — Push to Feishu

Unless the user said "don't push", send the digest to self:

```bash
# 1. Resolve self open_id once per session
lark-cli contact +get-user --as user

# 2. Send digest as markdown (Feishu post)
lark-cli im +messages-send --as user --user-id <self-open-id> \
  --markdown "$(cat /tmp/ai-radar-YYYY-MM-DD.md)"
```

Known `--markdown` caveats: Feishu rewrites H1 → H4 during post conversion.
For long digests this is acceptable. If `sources.yaml` has `push.destination`
set (a `chat_id` or wiki doc token), use that instead of self DM.

### P8 — Archive (only if asked)

If the user says "存档" / "save this", let Claude propose a path based on cwd
(default `~/ai-radar/YYYY-MM-DD.md`), confirm, then write. Record the date
range for future `+diff`.

Do not archive silently.

## Red lines

- **Never fabricate a benchmark number.** If not in the source, `data: not
  disclosed`.
- **Never mark an item `H` without meeting the criteria in
  `scoring-and-trends.md`.** Inflation defeats the scoring system.
- **Never recommend a product as "best".** This skill reports, it does not
  endorse.
- **Never expand scope on the user's behalf.** `+labs` does not quietly
  sweep arXiv.
- **Never push to a Feishu destination the user hasn't confirmed at least
  once.** Self DM is the safe default.
- **Never generate an "executive summary" section.** The rows are the
  summary.

## Evolving the skill

- New source to track? Add an entry to `sources.yaml` (one block).
- New pattern you keep noticing? Add to `pattern-library.md` with one
  example project and one example paper.
- The scoring rubric feels off? Edit `scoring-and-trends.md` — don't
  hack around it in workflow.
