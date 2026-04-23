# Institution Signals — first-hand lab and company entries

The rule: always go to the first-hand URL. News aggregators, Twitter
summaries, and "10 things from the keynote" blog posts are not first-hand.

If an entry below returns a stale or redirected page, fall back to the
research-index variant before giving up.

## Top-tier labs / companies (first-hand releases)

| Source | What it's good for | Entry URLs |
|---|---|---|
| OpenAI | New models, API changes, safety posts. | [openai.com/blog](https://openai.com/blog/) · [openai.com/research](https://openai.com/research/) · [platform.openai.com/docs](https://platform.openai.com/docs) |
| Google DeepMind | Research breakthroughs, Gemini updates. | [deepmind.google/discover/blog](https://deepmind.google/discover/blog/) · [deepmind.google/research](https://deepmind.google/research/) |
| Anthropic | Claude releases, alignment research, usage policies. | [anthropic.com/news](https://www.anthropic.com/news) · [anthropic.com/research](https://www.anthropic.com/research) · [docs.anthropic.com](https://docs.anthropic.com/) |
| Meta AI (FAIR) | Open-source model releases, research. | [ai.meta.com/blog](https://ai.meta.com/blog/) · [ai.meta.com/research](https://ai.meta.com/research/) |
| NVIDIA | Compute, inference optimization, ecosystem. | [developer.nvidia.com/blog](https://developer.nvidia.com/blog) · GTC keynote recordings |
| Microsoft | Platform/enterprise AI, Azure AI services. | Microsoft Build announcements · [learn.microsoft.com/azure/ai](https://learn.microsoft.com/azure/ai/) |
| Apple | On-device, privacy-oriented, interaction patterns. | [machinelearning.apple.com](https://machinelearning.apple.com/) · WWDC sessions |

## Chinese AI labs / companies

Add or prune per what you actually use. First-hand only.

| Source | What it's good for | Entry URLs |
|---|---|---|
| DeepSeek | Open models, training/post-training techniques. | [deepseek.com/blog](https://www.deepseek.com/blog) · HF org page |
| Moonshot (Kimi) | Long-context, agent features. | Official release notes, HF org page |
| Zhipu (GLM) | Open-weight models. | Official release notes, HF org page |
| Qwen (Alibaba) | Open-weight general + multimodal. | [qwen.ai](https://qwen.ai/) · HF org page |

> Do not rely on news aggregators or WeChat reposts for the above. Always
> pull from the lab's own release note or its Hugging Face org page.

## Extraction rules for this section

- The `change` sentence names the model or feature and the version — not the
  marketing tagline. "Anthropic released Claude Opus 4.7" beats "Anthropic
  announces next-generation frontier AI".
- `data` must be a number or benchmark the lab itself published. Ignore
  cherry-picked leaderboard scores from third parties on the first pass.
- `limits`: labs frequently bury these in the system card or model card.
  Read it. "Context window 200k" is a spec, not a limit — a real limit is
  something like "degraded tool-use performance on non-English prompts".

## When a lab publishes multiple things the same day

Treat each release as one row. Do not consolidate a model release plus a
pricing change plus a research paper into a single row; they have different
data and different limits.

## Known WebFetch quirks

- DeepMind blog occasionally returns an intermediate redirect page; retry
  with the research-index URL if the fetched content is thin.
- Anthropic `news` page sometimes lists items without dates in the fetched
  markdown — verify the date by opening the item's own page.
