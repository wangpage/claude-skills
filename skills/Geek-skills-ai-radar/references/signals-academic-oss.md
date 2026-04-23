# Academic & Open-Source Signals

Two distinct source types, grouped here because both require topic scoping —
you cannot meaningfully "scan all of arXiv" or "all of GitHub". Always run
these with a keyword or topic filter from the user.

## Academic first-hand

| Source | What it's good for | Entry URL |
|---|---|---|
| arXiv | First appearance of papers. | [arxiv.org/list/cs.AI/recent](https://arxiv.org/list/cs.AI/recent) · `cs.LG` · `cs.CL` |
| OpenReview | Peer-review discussion, contested claims, reviewer skepticism. | [openreview.net](https://openreview.net/) |
| Papers with Code | Paper + code + benchmark triangulated. | [paperswithcode.com](https://paperswithcode.com/) · [`sota`](https://paperswithcode.com/sota) |

### How to scope arXiv in a run

- Require a keyword list from the user (`+arxiv keywords:"reasoning, rlhf"`).
- Scan only the last N days (default 7). A run with no date filter will
  return thousands of results and is useless.
- Read the **abstract**, not a summary of the abstract. If the abstract
  doesn't name a benchmark or a numeric result, `data: not disclosed`.

### How to use OpenReview

- Useful specifically when a paper is contested. If the paper has unanimous
  positive reviews, OpenReview adds little over the arXiv abstract.
- Quote the reviewer criticism, do not paraphrase.

### How to use Papers with Code

- Best signal: the `SOTA` page for a specific benchmark — you can see who
  overtook whom and when.
- Do not use it as a ranking of paper "importance"; leaderboard position and
  scientific contribution are different things.

## Open-source / community

| Source | What it's good for | Entry URL |
|---|---|---|
| GitHub (releases) | Truthful change history — releases, changelogs, issues. | Per-repo: `/releases`, `/CHANGELOG.md`, `/issues` |
| Hugging Face | Model releases, dataset releases, trending. | [huggingface.co/models](https://huggingface.co/models?sort=trending) · org pages |

### GitHub extraction rules

- Read the release notes, not README. A README describes the project; a
  release note describes the change.
- If there is no release but there is a recent merged PR that clearly
  changes behavior, that is acceptable as a source — link to the PR.
- Prefer the repo's own changelog over third-party release-tracker bots.

### Hugging Face extraction rules

- The model card is the source of truth. The discussion tab is not.
- For a model-release row, `data` must come from the card's evaluation
  section, not from community benchmarks posted in comments.
- License and intended-use restrictions count as `limits` and are worth
  surfacing when non-obvious (e.g., non-commercial license, region lock).

## What not to do

- Do not treat a Twitter thread claiming "new SOTA" as a paper source.
  Either there is an arXiv abstract, or there is not.
- Do not generate a list of "trending papers this week" by summarizing
  aggregator feeds. Either the user gave keywords and you scoped to them, or
  skip the section.
- Do not include GitHub repos with no release and no recent commit activity
  — those are not signals.
