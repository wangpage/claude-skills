---
name: ai-radar
version: 0.2.1
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
  maturity: v0.2.1 — bilingual readme, pipeline framing, Feishu push
  outputs: >
    A single structured digest per run, pushed to Feishu (self DM) by default.
    Optional archived copy on disk when the user opts into persistence.
---

# AI Radar

[English](#english) | [中文](#中文)

> **Note for Claude / 面向 Claude 的说明**: This file is bilingual for human
> review. When executing, follow **either** language section — they are
> semantically identical. Code blocks, sub-command names (`+scan`), field
> names (`what` / `innovation` / `data` / `limits` / `worth-watching`), and
> scoring variables (`institution_weight` etc.) are intentionally **not**
> translated — they are structural identifiers.
>
> 本文件为便于人类阅读做了双语。执行时任选一语言节即可，两节语义一致。代码块、
> 子命令名（`+scan`）、字段名（`what` / `innovation` / `data` / `limits` /
> `worth-watching`）和打分变量（`institution_weight` 等）**刻意不翻译**，它
> 们是结构标识符。

---

## English

A disciplined **pipeline** for keeping up with AI without drowning in it.

The mental model is not "a thing that lists news" — it is:

```
[ sources.yaml ]  →  fetch  →  filter  →  extract  →  score  →  digest  →  push
     (what)        (collect)   (noise)   (5-field)  (H/M/L)   (assemble) (Feishu)
```

Each stage has a clear contract, so a bad run is easy to locate: if the digest
is thin, is the collector empty, or did the filter over-drop? Is extract
hallucinating, or is scoring too strict?

### Core principles

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

### When NOT to use

- The user has a specific question about one model or one paper — just answer
  it, do not run the pipeline.
- The user wants a deep multi-source synthesis with citations — use
  `deep-research`.
- The user wants hot takes or commentary — this skill refuses to editorialize.

### What this skill produces

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

### Active context bundle

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

### Sub-commands

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

### Workflow

#### P0 — Scope (5 seconds)

Resolve: which sub-command, since when (default 7 days), what exclusions.
State it back in one line, then proceed.

#### P1 — Collect

Read `sources.yaml`. For each source whose `enabled: true` and whose tags
match the scope, fetch. Parallelize independent fetches. Rules:

- First-hand URL only. No aggregators.
- Respect known network constraints (e.g., `eastmoney.*` blocked on this
  network — irrelevant to AI but a general rule).
- If a fetch returns stale/redirect content (common with DeepMind blog),
  try the entry's `fallback_url` before giving up.
- Failures go under `## Unreachable` at assembly time, never silently
  dropped.

#### P2 — Filter

Apply [triage-heuristics.md](references/triage-heuristics.md):

- Inclusion test: first-hand? concrete `what`? has data OR stated limit OR
  top-lab exception? not a repeat of prior runs?
- Downrank test: self-praising adjectives, bench without bench name, no
  modern baseline, no inspectable artifact, thread summarizing others.
- An honest thin week beats a padded fat one. If filter drops everything,
  say so.

#### P3 — Extract

For each surviving item, fill five fields per
[extraction-template.md](references/extraction-template.md):

1. `what` — what this is (model / paper / product / post) in one sentence.
2. `innovation` — 1–3 bullet-sized claims of what's new. No adjectives of
   impact.
3. `data` — benchmark name + score, or `not disclosed`. Never invent.
4. `limits` — what the source itself admits, or `none disclosed`.
5. `worth-watching` — `H` / `M` / `L` per scoring rubric. Default is `M`;
   `H` requires justification.

#### P4 — Score & tag

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

#### P5 — Trends (if archive exists)

Compare current-run tag counts to the previous 2–3 archived runs. Produce a
short `## Trends` section: `<tag> ↑/↓/→`. Without archives, skip this phase
silently — do not fabricate a baseline.

#### P6 — Assemble

Produce the markdown digest exactly per the template. Omit empty sections.
No preamble, no trailing summary.

#### P7 — Push to Feishu

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

#### P8 — Archive (only if asked)

If the user says "存档" / "save this", let Claude propose a path based on cwd
(default `~/ai-radar/YYYY-MM-DD.md`), confirm, then write. Record the date
range for future `+diff`.

Do not archive silently.

### Red lines

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

### Evolving the skill

- New source to track? Add an entry to `sources.yaml` (one block).
- New pattern you keep noticing? Add to `pattern-library.md` with one
  example project and one example paper.
- The scoring rubric feels off? Edit `scoring-and-trends.md` — don't
  hack around it in workflow.

---

## 中文

一条有纪律的**管线**，帮你跟上 AI 又不被信息量淹没。

心智模型不是「一个罗列新闻的东西」，而是：

```
[ sources.yaml ]  →  fetch  →  filter  →  extract  →  score  →  digest  →  push
     (源头)        (采集)     (去噪)    (五字段)    (H/M/L)  (组装)     (飞书)
```

每一阶段都有清晰契约：某次 digest 太薄时，能立刻定位问题——是采集层空、过滤层
过度筛除、提取层幻觉、还是打分太严。

### 核心原则

1. **先看源头，再看解读。**永远优先打开一手入口（实验室官博、arXiv 摘要、
   GitHub release）。二手解读只有在读过原文之后才有意义。
2. **提取 > 总结。**本 skill 不生成「这对行业意味着什么」的议论，只按固定的
   五字段提取每条目。
3. **增量 > 回顾。**一次运行的价值来自自上次之后的**新增**内容。跨运行的标签
   趋势是更高阶的信号。
4. **品味 > 覆盖。**漏掉一个小发布没关系，吹捧一个没内容的发布不行。打分必须
   诚实：大部分条目是 `M`，`H` 应当稀有。
5. **一个固定的阅读场所。**digest 推送到飞书（自己 DM）。你的工作是每周瞥两
   眼；skill 的工作是让这一瞥值回时间。

### 何时不用

- 用户问某个具体模型或论文的事实性问题——直接回答，不要跑管线。
- 用户要带引用的深度综述——用 `deep-research`。
- 用户要观点评论或热评——本 skill 拒绝下判断。

### 产出形态

推送到飞书的结构化 digest，可选本地存档。格式见
[extraction-template.md](references/extraction-template.md)：

```
# AI Radar — <日期> — <范围>

## Labs & companies
- [H] <来源>: <what> | <innovation> | <data> | <limits>

## People signals
- [M] <人物>: <观点> | why matters | caveat

## Papers
- [H] <标题> (arXiv:xxxx): <主张> | <eval> | <limits>

## Open source
- [M] <repo> <版本>: <变化> | <bench> | <license/hw>  #patterns: actor-model, memory-abstraction

## Trends (最近 3 次运行)
- agent ↑, reasoning ↑, multimodal ↓

## Watchlist (未更新，跳过)
- <来源>: no update since <日期>
```

没有 executive summary，没有「关键要点」。空段落直接省略。

### 激活时加载

**一激活就加载**
1. 本 `SKILL.md`
2. [extraction-template.md](references/extraction-template.md)——五字段输出契约
3. [sources.yaml](sources.yaml)——采集清单

**按需加载**
- [signals-people.md](references/signals-people.md)——大佬清单细节（仅 `+people`
  或范围含 people 时加载）
- [signals-institutions.md](references/signals-institutions.md)——各实验室抓取
  的坑位（仅 `+labs` 或范围含 labs 时加载）
- [signals-academic-oss.md](references/signals-academic-oss.md)——arXiv/OSS
  提取规则
- [triage-heuristics.md](references/triage-heuristics.md)——信号 vs 噪音、hype
  降权规则
- [scoring-and-trends.md](references/scoring-and-trends.md)——打分公式与趋势检
  测（P4 之前加载）
- [pattern-library.md](references/pattern-library.md)——累积的设计模式库。**仅
  当**某条 OSS/论文值得打 pattern 标签时加载，大多数运行不需要。

不要每次运行都全部加载。

### 子命令

这是个**混合型** skill：结构化清单（`sources.yaml`）驱动采集，具名子命令决定
本次扫描的范围。默认：`+scan`。

| 子命令 | 范围 | 大致耗时 |
|---|---|---:|
| `+scan` | 所有 enabled 源，全管线运行。默认。 | 8-12 分钟 |
| `+labs` | 只扫 `type: blog` 且 tags 含 `lab` 的源。 | 3-5 分钟 |
| `+people` | 只扫 `type: x` / `type: blog` 且 tags 含 `person` 的源。 | 3-5 分钟 |
| `+arxiv <关键词>` | 只扫 arXiv + OpenReview + Papers with Code。 | 4-6 分钟 |
| `+oss` | 只扫 GitHub releases + Hugging Face trending。 | 3-5 分钟 |
| `+extract <url>` | 对单个链接套五字段模板。 | <1 分钟 |
| `+trend` | 只产出 `## Trends` 段，不重新抓取。 | <1 分钟 |
| `+diff` | 对比上一次存档运行，仅显示变化。 | 看情况 |
| `+push` | 把上次产出的 digest 再推一次飞书。 | <10 秒 |

范围可叠加：`+scan since:2026-04-15`、`+arxiv keywords:"reasoning"`、
`+scan exclude:oss`。

### 工作流

#### P0 — 范围界定（5 秒）

明确三件事：用哪个子命令？起点时间（默认最近 7 天）？排除什么？用一句话把
结果复述给用户，然后进入下一步。

#### P1 — 采集（Collect）

读 `sources.yaml`。对所有 `enabled: true` 且 tags 匹配当前范围的源执行抓取，
独立请求并行化。规则：

- 只走一手 URL，不走聚合站。
- 尊重已知网络限制（例如 `eastmoney.*` 在本网络被整体封锁——和 AI 无关，但是
  通用约束）。
- 若抓取返回过期/跳转内容（DeepMind 官博常见），先尝试条目里的 `fallback_url`
  再放弃。
- 失败的源进入 `## Unreachable` 段，不静默丢弃。

#### P2 — 过滤（Filter）

套用 [triage-heuristics.md](references/triage-heuristics.md)：

- **入选测试**：是否一手来源？`what` 能否用一句具体话说清？有 data 或明述
  limits，或命中「顶级实验室例外」？是否为之前运行已覆盖的重复？
- **降权测试**：自夸形容词、匿名 benchmark 数字、没现代基线对比、无可检视产
  物、转述他人帖子的"汇总"帖。
- 一个诚实的淡周胜过一个注水的丰周。若过滤后清空，就如实说。

#### P3 — 提取（Extract）

对通过过滤的每条目按 [extraction-template.md](references/extraction-template.md)
填五个字段：

1. `what`——这是什么（模型 / 论文 / 产品 / 帖子），一句话。
2. `innovation`——1–3 条"新在哪"。每条一小段，不带影响力形容词。
3. `data`——benchmark 名称 + 分数，或 `not disclosed`。**永不编造数字**。
4. `limits`——源头自己承认的边界，或 `none disclosed`。
5. `worth-watching`——`H` / `M` / `L`，按打分表。默认 `M`；`H` 必须有依据。

#### P4 — 打分与打标签（Score & tag）

加载 [scoring-and-trends.md](references/scoring-and-trends.md)。对每条：

- 计算 `score = institution_weight + tech_breakthrough + community_heat`。
- 按桶映射到 `worth-watching`。
- 从固定词汇表中打 1–3 个话题标签（`agent`、`reasoning`、`multimodal`、
  `rlhf`、`efficiency`、`safety`、`tool-use`、`retrieval`、`inference`、
  `training`）。

若某条 OSS/论文条目命中 [pattern-library.md](references/pattern-library.md)
里的一个或多个模式，就在行内追加 `#patterns: <name>, <name>`。**Pattern 是
富化，不是硬塞**——凑不上就不写。

#### P5 — 趋势（仅有历史存档时）

将当前运行的标签频次与前 2–3 次存档做对比，产出简短的 `## Trends` 段：
`<标签> ↑/↓/→`。没有存档时**直接跳过本阶段**——不编造基线。

#### P6 — 组装（Assemble）

严格按模板产出 markdown digest。空段落省略。无开场白，无结尾总结。

#### P7 — 推送飞书（Push）

除非用户说「别推」，将 digest 发到自己：

```bash
# 1. 一次会话内查一次自己的 open_id
lark-cli contact +get-user --as user

# 2. 以 markdown 形式发送（会转成飞书 post）
lark-cli im +messages-send --as user --user-id <self-open-id> \
  --markdown "$(cat /tmp/ai-radar-YYYY-MM-DD.md)"
```

已知 `--markdown` 坑：飞书会把 H1 改写成 H4。对长 digest 可以接受。若
`sources.yaml` 里设置了 `push.destination`（群 `chat_id` 或 wiki doc token），
就推到那里而不是自己 DM。

#### P8 — 存档（仅当用户要求）

用户说「存档」/ "save this" 时，Claude 基于当前 cwd 提议一个路径（默认
`~/ai-radar/YYYY-MM-DD.md`），确认后写入。记录日期范围以便将来的 `+diff`
使用。

不要静默存档。

### 红线

- **永不编造 benchmark 数字。**源头没写，就是 `data: not disclosed`。
- **永不在不满足 `scoring-and-trends.md` 条件时给 `H`。**打分膨胀会摧毁整个
  评分系统。
- **永不把产品推荐为"最好"。**本 skill 只报告，不背书。
- **永不自作主张扩大范围。**`+labs` 不会悄悄去扫 arXiv。
- **永不推送到用户从未确认过的飞书目的地。**默认自己 DM 是最安全的回退。
- **永不生成「执行摘要」段。**行本身就是摘要。

### 演进这个 skill

- 要加新的信息源？在 `sources.yaml` 加一个 block。
- 发现一个反复出现的设计模式？加进 `pattern-library.md`，附一个项目例子和一
  篇论文例子。
- 打分规则感觉不对？直接改 `scoring-and-trends.md`——不要在 workflow 里打
  补丁。
