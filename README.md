# Claude Skills

[English](#english) | [中文](#中文)

---

## English

Personal Claude Code skills. Each skill is a directory with a `SKILL.md`
entry point and supporting `references/`, optionally `scripts/` and
`assets/`.

### Install

Claude Code skills are directory-based. To install a skill:

1. Pick a directory under `skills/` in this repo.
2. Copy it into one of:
   - Personal: `~/.claude/skills/<skill-name>/`
   - Project: `.claude/skills/<skill-name>/`
3. Keep `SKILL.md` and its supporting files in the same directory.

### Current skills

| Skill | Purpose |
|---|---|
| [`ai-radar`](skills/Geek-skills-ai-radar/) | **Auto-pipeline** that sweeps the AI frontier and pushes a structured digest to Feishu. Four stages: collect (from `sources.yaml`) → filter (drop hype) → extract (five-field prompt: `what / innovation / data / limits / worth-watching`) → output + push. Scores each item H/M/L and tracks tag trends across runs. Sub-commands: `+scan`, `+labs`, `+people`, `+arxiv`, `+oss`, `+extract`, `+trend`, `+diff`, `+push`. |

### Layout

```
.
├── skills/
│   └── Geek-skills-<name>/
│       ├── SKILL.md          # activation, workflow, navigation
│       ├── references/       # load-on-demand material
│       ├── scripts/          # optional deterministic helpers
│       └── assets/           # optional static assets
└── README.md
```

### Conventions

- `SKILL.md` focuses on activation, workflow, and navigation — not on
  reference data.
- Large reference material goes under `references/` and is loaded on
  demand, not pre-loaded.
- The `description` in frontmatter names both what the skill is for and
  what it is **not** for. "When NOT to use" is as important as "when to
  use".

### Inspiration

Directory convention and repo layout follow
[staruhub/ClaudeSkills](https://github.com/staruhub/ClaudeSkills).

---

## 中文

个人 Claude Code skills 仓库。每个 skill 是一个目录，包含 `SKILL.md` 入口
和辅助的 `references/`，可选 `scripts/` 与 `assets/`。

### 安装

Claude Code 的 skill 是基于目录的。安装步骤：

1. 在本仓库 `skills/` 下挑选一个目录。
2. 复制到以下位置之一：
   - 个人全局：`~/.claude/skills/<skill-name>/`
   - 项目级：`.claude/skills/<skill-name>/`
3. 保持 `SKILL.md` 与其辅助文件在同一目录。

### 当前 skills

| Skill | 用途 |
|---|---|
| [`ai-radar`](skills/Geek-skills-ai-radar/) | **自动管线**：采集（读 `sources.yaml`）→ 过滤（去噪/去 hype）→ 提取（五字段 prompt：`what / innovation / data / limits / worth-watching`）→ 输出并推送飞书。每条打分 H/M/L，并跨运行追踪标签趋势。不产出「关键要点」式总结。子命令：`+scan`、`+labs`、`+people`、`+arxiv`、`+oss`、`+extract`、`+trend`、`+diff`、`+push`。 |

### 目录结构

```
.
├── skills/
│   └── Geek-skills-<name>/
│       ├── SKILL.md          # 激活条件、工作流、导航
│       ├── references/       # 按需加载的参考材料
│       ├── scripts/          # 可选的确定性辅助脚本
│       └── assets/           # 可选的静态资源
└── README.md
```

### 约定

- `SKILL.md` 只写激活条件、工作流、导航——不承载参考数据。
- 体量较大的参考内容放在 `references/` 下，按需加载，而非一次性全部加载。
- frontmatter 里的 `description` 同时说明「何时使用」与「何时不使用」。
  "何时不用"和"何时用"一样重要。

### 灵感来源

目录约定与仓库布局参考自
[staruhub/ClaudeSkills](https://github.com/staruhub/ClaudeSkills)。
