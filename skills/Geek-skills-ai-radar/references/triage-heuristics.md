# Triage Heuristics

How to decide what is a signal vs. noise. The test a run passes is not "did
it return many rows" — it is "did every row earn its place".

## The inclusion test

For each candidate item, ask in order:

1. **Does it come from a first-hand source?** If no, try to find the
   first-hand source. If there is none, drop it.
2. **Can I write the `change` field in one concrete sentence?** If the best
   I can do is "announced updates" or "made progress on", drop it.
3. **Is there either a number, a benchmark, or a stated limit?** If neither
   — and the source is not a major release from a top lab — drop it. For
   top labs, keep it but write `data: not disclosed` honestly.
4. **Is it a repeat of something covered in a prior run?** If yes and the
   new source adds nothing, drop it.

Four drops means four seats saved for items that matter.

## The downrank test (hype detection)

The following patterns mean downrank or drop, not upranking:

- The source uses the words "revolutionary", "unprecedented",
  "game-changing" about itself.
- Benchmark numbers are given without the benchmark's name.
- Comparisons are drawn only against older or weaker baselines — no modern
  comparison.
- The release is announced without a model card, paper, or demo that can be
  inspected.
- The post is a thread summarizing someone else's post.

If three or more patterns hit, drop the item.

## The "small release from a top lab" case

When OpenAI / DeepMind / Anthropic ship a small thing — a pricing change, a
docs update, a minor model tier — it can still be worth including because
the cadence itself is a signal. Keep the row, but write the `change` as
narrowly as the source does. Do not inflate "minor" to "major".

## The "major release from an unknown actor" case

When an unfamiliar org claims a new SOTA model, the bar is *higher*, not
lower:

- The model weights must be downloadable or the API must be usable.
- The evaluation must name the benchmark, split, and a comparison against a
  modern baseline.
- The model card must disclose at least one limitation.

If any of these is missing, the item goes under a separate `## Unverified`
section with a one-line note. Do not launder it into `Labs & companies`.

## The "person's take" case

Person signals are the easiest section to pollute with noise. Extra filters:

- If the post is a reply to another post, link to the thread root, not just
  the reply.
- If the post makes a claim about a model the person works on, flag it as a
  vendor claim in `caveat`.
- If the post is a link with no original commentary, it is not a person
  signal — the linked thing is the signal, include *that* instead.

## What "unchanged" means

A source is `unchanged` when, within the scope window, it published nothing
that passes the inclusion test. This is different from "published nothing
at all" — a lab might publish three pricing-FAQ updates that all drop.

The `Watchlist (unchanged)` section is only useful when the user has an
archive of prior runs. Without that, it is noise.

## What to do when the whole run is thin

Sometimes a week really does have little to report. The correct response is
a short digest, not a padded one. An honest `Labs & companies` section with
two rows beats eight rows of filler. The skill's value comes from trust in
its restraint.
