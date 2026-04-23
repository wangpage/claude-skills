# Extraction Template

The output format for AI Radar. Load this before any run.

## The five fields

Every item, regardless of source, is reduced to exactly five fields. This
replaces the v0.1 three-field format — the added `what` and `innovation`
fields are what let the user skim the digest without clicking every link.

| Field | Content | Failure mode to avoid |
|---|---|---|
| `what` | One sentence naming what this is — a model, paper, product, post, release. Active voice, named subject. | Vague headings like "update", "announcement". |
| `innovation` | 1–3 short claims of what is actually new. Bullet shape, no impact adjectives. | "Groundbreaking improvements" — name the improvement. |
| `data` | Benchmark name + split + score, or the literal `not disclosed`. | Inventing numbers. Paraphrasing "strong performance" as data. |
| `limits` | What the source itself states as out of scope, or `none disclosed`. | Inferring limitations the source did not mention. |
| `worth-watching` | `H` / `M` / `L`, assigned per `scoring-and-trends.md`. Default `M`. | Marking `H` on something the user will not revisit in a week. |

If you cannot fill `what` in one sentence, the item is probably not worth
including.

## Start here (reading-order block)

The digest opens with a short pointer section called `## Start here` /
`## 本周先看`. This is the ONE exception to the "no executive summary"
rule — and it is narrow on purpose:

- **At most 3 items.** Always. If 5 items qualify, pick the 3 with highest
  reader-action value (reader bias: frontier model release > major paper
  > new tool > infrastructure).
- **Only items already in the body at `[H]`.** If the run has zero H
  items, **omit the Start here section entirely**. Do not pad with M's.
- **One line per item.** Format: `- <name> — <one-line why to click
  first>`. The "why" must be factual (a specific capability, a specific
  number, a specific actor) — not hype ("groundbreaking") or vibes
  ("seems important").
- **No ranking within Start here.** The three lines are peers, not a
  top-3 list. Order by section-of-origin (Labs, Papers, OSS).

Row shape:

```
## Start here
- Claude Opus 4.7 — Anthropic's flagship version bump; the "agents + multi-step" focus matters for any tool-use work this quarter.
- RAG-Anything (arXiv:2510.12323) — unified cross-modal retrieval framework, highest-upvoted paper on HF this week.
- Kimi K2.6 — 1.1T-param open release from Moonshot; largest open model updated this cycle.
```

If there is no H item, do not emit the header — go straight to
`## Labs & companies`.

## Row shapes by section

Rows are one-line-per-item *when possible*, with the five fields separated
by ` | `. Use the expanded multi-line form only when a field genuinely needs
it (e.g., 2–3 innovation claims).

### Labs & companies

Compact form (preferred):
```
- [H] <lab> — <what> | <innovation> | <data> | <limits>
  link: <first-hand URL>
```

Expanded form (when `innovation` has 2–3 claims):
```
- [H] <lab> — <what>
  innovation:
    - <claim 1>
    - <claim 2>
  data: <bench name: score> | not disclosed
  limits: <explicit limit> | none disclosed
  link: <first-hand URL>
```

### People signals

```
- [M] <person> — <take in one sentence>
  why matters: <why this person's word moves the frontier here>
  caveat: <known bias / context the reader should hold>
  link: <post/video URL>
```

`why matters` and `caveat` are intentionally short. If you cannot state
them in a clause each, the item does not belong in this section.

### Papers

```
- [H] <title> (arXiv:<id>) — <claim in one sentence>
  innovation:
    - <method/claim 1>
    - <method/claim 2>
  eval: <bench, split, score> | not disclosed
  limits: <what the authors admit is out of scope>
  link: <arXiv abstract URL>
  review: <OpenReview URL>   # only if contested
```

Prefer the arXiv abstract URL over any summarizer.

### Open source

```
- [M] <repo> <version> — <change>
  innovation: <new capability, one line>
  data: <benchmark/demo result> | not disclosed
  limits: <stated caveats, hardware requirements, license notes>
  link: <GitHub release or Hugging Face model URL>
  #patterns: <pattern-name>, <pattern-name>   # optional — see pattern-library.md
```

For Hugging Face models, the link is the model card, not the discussion tab.

### Trends (across last 2-3 runs)

```
## Trends
- <tag> ↑   (N mentions this run vs. M previous)
- <tag> ↓   (N vs. M)
- <tag> →   (stable)
```

Only emit this section if there are ≥2 archived prior runs to compare to.

### Watchlist (unchanged)

```
- <source> — no update since <YYYY-MM-DD>
```

Only include watchlist rows if the user has an archived prior run. Without
that anchor, `since` is meaningless.

### Unreachable

```
- <source>: <reason> (last attempted <timestamp>)
```

Never silently drop a failed fetch — surface it so the user knows the
picture is incomplete.

## Hard rules

1. **No "key takeaways" paragraph.** The only allowed lead-in is the
   `## Start here` block, and it must follow all the constraints above
   (≤3 items, H-only, one line each, factual why).
2. **No editorial ranking within sections.** Inside `## Labs & companies`
   etc., items are not ordered by importance. The H/M/L marker is the
   ranking; `## Start here` is the pointer.
3. **No adjectives of impact.** Forbidden: "groundbreaking", "massive",
   "huge", "game-changing", "state-of-the-art" (unless the source itself
   uses the phrase and you are quoting it).
4. **Quote, don't paraphrase, for numeric claims.** If the source says
   "reduces latency by 37%", write exactly that. Do not round to "about
   40%".
5. **Empty sections are omitted.** Do not write "No new releases this week"
   as a section body. Just leave the section out.
6. **`H` is rare but not absent.** Expect 1–3 H items per run in an active
   week; expect 0 in a quiet week. More than ~20% of items marked H means
   drift — recheck before pushing.

## Worth-watching buckets (short version; full rubric in scoring-and-trends.md)

- `H` — top-lab major release OR independently verifiable benchmark gain OR
  confirmed paradigm shift. Reader should click the link now.
- `M` — default. Worth knowing about, may or may not click.
- `L` — included for completeness (e.g., watchlist anchor, pattern-library
  contribution) but not actionable this week.
