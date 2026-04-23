# Scoring & Trends

How `worth-watching` (H/M/L) is assigned, and how `## Trends` is computed
across runs. Load this before P4 Score.

## The score formula

```
score = institution_weight + tech_breakthrough + community_heat
```

Range: 0.0 – 3.0. Each term is 0.0 – 1.0.

### institution_weight (0.0 – 1.0)

Read directly from `sources.yaml` → the source's `weight` field.
Rationale: a release from OpenAI/DeepMind/Anthropic deserves a higher base
than a random HuggingFace repo, even before looking at the content.

Adjust at runtime only when:
- The source is a person account but the content is a formal lab release
  (e.g., Sam Altman announcing a pricing change) → use the lab's weight.
- The source is a lab account but the content is personal/off-topic
  (e.g., a CEO's congratulations post) → drop to 0.3.

### tech_breakthrough (0.0 – 1.0)

Qualitative. Use this rubric, not a vibe:

| Value | Criteria |
|---:|---|
| 1.0 | New paradigm (a capability class that did not exist) OR benchmark SOTA verified by an independent eval. |
| 0.7 | Meaningful benchmark improvement WITH the benchmark name disclosed AND a modern baseline cited. |
| 0.5 | New model or feature release with credible scope but no independent verification yet. |
| 0.3 | Incremental improvement, pricing change, API addition, doc update. |
| 0.1 | Marketing post with no technical substance. |
| 0.0 | No technical content. |

**Default to 0.3 when uncertain.** If the source itself does not disclose a
benchmark name, the score cannot exceed 0.5.

### community_heat (0.0 – 1.0)

Qualitative, optional. Set to 0.0 if you cannot measure it honestly.

| Value | Criteria |
|---:|---|
| 1.0 | Two or more first-tier labs or researchers publicly engage (not just retweet) within 24 h. |
| 0.7 | One first-tier engagement, or HF trending with substantive discussion. |
| 0.5 | GitHub stars rising visibly in a single week (≥1000/week) with organic issues/PRs. |
| 0.3 | Present on trending lists but low engagement depth. |
| 0.0 | No measurable heat, or not measurable without fabricating. |

**Honesty clause:** if you did not actually check engagement, set this to 0.0.
Do not guess — the `institution_weight` + `tech_breakthrough` terms are
sufficient in most cases.

## Score → worth-watching bucket

| Score | Bucket |
|---:|---|
| ≥ 2.0 | `H` |
| 1.0 – 1.99 | `M` |
| < 1.0 | `L` |

## Calibration check (run at P4 end)

After scoring a run, count buckets. Sanity rules:

- `H` should be **rare**: at most ~20% of included items. If more, recheck
  `tech_breakthrough` — you're probably inflating.
- `L` items should usually be dropped entirely. Keep an `L` item only if it
  has a specific downstream use: a watchlist anchor, a pattern-library
  contribution, or a concrete user ask.
- If a run has **zero** `H` items, that is fine. Most weeks don't.

If the bucket distribution violates these rules, adjust scores and rerun the
bucketing step — do not override the rubric per item.

## Tag vocabulary (for trends)

Tag each item with 1–3 labels from this fixed set. Adding a new tag requires
editing this file (the vocabulary is the backbone of trend detection).

```
agent            tool-use       multimodal     reasoning
retrieval        rlhf           training       inference
efficiency       safety         alignment      eval
robotics         vision         audio          code
```

If an item fits none, ask whether it's really an AI-radar item — many edge
cases (generic DevOps, hardware rollouts) should just be dropped.

## Trend detection

Trends are computed only when there are **≥2 archived prior runs** (i.e.,
at least 3 runs including current). Rule:

1. For each tag, count occurrences in the current run and in the two
   previous archived runs.
2. `↑` when current > max(previous). `↓` when current < min(previous). `→`
   otherwise.
3. Do not include a tag in the `## Trends` section unless it was mentioned
   at least once in the current run.
4. Prefix the line with the tag name, then the arrow, then counts in
   parentheses: `- agent ↑ (5 vs. 2, 3)`.

### Anti-patterns for trends

- Do not invent a baseline when no archive exists. Skip the section.
- Do not cherry-pick tags to tell a narrative ("reasoning is rising") —
  emit all tags that cross a threshold honestly.
- Do not re-score past runs; the archived bucket counts are frozen.

## When `community_heat` is worth measuring

Honest measurement needs a specific signal:

- **HF model card views / downloads** — visible on the card if enabled.
- **GitHub stars delta** — compare the repo's current star count to the
  last archived run (requires you to record the number each time).
- **First-tier endorsement** — a named researcher from the people list
  publicly engaging (not a retweet).

When none of these is available without guessing, set `community_heat = 0.0`
and move on.
