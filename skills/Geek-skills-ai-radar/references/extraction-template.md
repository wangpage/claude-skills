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

1. **No "key takeaways" section.** The rows are the takeaways.
2. **No editorial ranking beyond `worth-watching`.** Do not add
   "must read" / "most important" markers. The H/M/L is the ranking signal.
3. **No adjectives of impact.** Forbidden: "groundbreaking", "massive",
   "huge", "game-changing", "state-of-the-art" (unless the source itself
   uses the phrase and you are quoting it).
4. **Quote, don't paraphrase, for numeric claims.** If the source says
   "reduces latency by 37%", write exactly that. Do not round to "about
   40%".
5. **Empty sections are omitted.** Do not write "No new releases this week"
   as a section body. Just leave the section out.
6. **`H` is rare.** If more than ~20% of items in a run are marked `H`, the
   scoring is miscalibrated — recheck before pushing.

## Worth-watching buckets (short version; full rubric in scoring-and-trends.md)

- `H` — top-lab major release OR independently verifiable benchmark gain OR
  confirmed paradigm shift. Reader should click the link now.
- `M` — default. Worth knowing about, may or may not click.
- `L` — included for completeness (e.g., watchlist anchor, pattern-library
  contribution) but not actionable this week.
