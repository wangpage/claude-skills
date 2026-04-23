# Extraction Template

The output format for AI Pulse. Load this before any run.

## The three fields

Every item, regardless of source, is reduced to exactly three fields.

| Field | Content | Failure mode to avoid |
|---|---|---|
| `change` | One sentence naming the concrete thing that happened. Active voice, specific subject and verb. | Vague verbs like "announced", "made progress", "pushed forward". |
| `data` | The numeric claim, benchmark name + score, or the literal string `not disclosed`. | Inventing a number. Paraphrasing "strong performance" as if it were data. |
| `limits` | The limitation the source itself states, or `none disclosed`. | Inferring limitations the source did not mention. |

If you cannot fill `change` in one sentence, the item is probably not worth
including.

## Row shapes by section

### Labs & companies

```
- <lab/company> — <change>
  data: <bench name: score> | not disclosed
  limits: <explicit limit> | none disclosed
  link: <first-hand URL>
```

### People signals

```
- <person> — <take in one sentence>
  why it matters: <why this person's word moves the frontier here>
  caveat: <known bias / context the reader should hold>
  link: <post/video/paper URL>
```

`why it matters` and `caveat` are intentionally short. If you cannot state
them in a clause each, the item does not belong in this section.

### Papers

```
- <title> (arXiv:<id>) — <claim in one sentence>
  eval: <bench used, split, score> | not disclosed
  limits: <what the authors admit is out of scope>
  link: <arXiv abstract URL>
```

Prefer the arXiv abstract URL over any summarizer. If the paper is on
OpenReview, add a second line `review: <URL>`.

### Open source

```
- <repo> <version> — <change>
  data: <benchmark/demo result> | not disclosed
  limits: <stated caveats, hardware requirements, license notes>
  link: <GitHub release or Hugging Face model URL>
```

For Hugging Face models, the link is the model card, not the discussion tab.

### Watchlist (unchanged)

```
- <source> — no update since <YYYY-MM-DD>
```

Only include watchlist rows if the user has an archived prior run. Without
that anchor, `since` is meaningless.

## Hard rules

1. **No "key takeaways" section.** The rows are the takeaways.
2. **No ranking.** Do not mark any item as "most important" or "must read".
   The reader ranks; the skill reports.
3. **No adjectives of impact.** Forbidden: "groundbreaking", "massive",
   "huge", "game-changing", "state-of-the-art" (unless the source itself
   uses the phrase and you are quoting it).
4. **Quote, don't paraphrase, for claims.** If the source says "reduces
   latency by 37%", write exactly that. Do not round to "about 40%".
5. **Empty sections are omitted.** Do not write "No new releases this week"
   as a section body. Just leave the section out.

## When a source cannot be fetched

Do not silently drop it. Add an entry under a `## Unreachable` section:

```
- <source>: <reason> (last attempted <timestamp>)
```

This keeps the user aware that their picture is incomplete.
