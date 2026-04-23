# Claude Skills

Personal Claude Code skills. Each skill is a directory with a `SKILL.md` entry
point and supporting `references/`, optionally `scripts/` and `assets/`.

## Install

Claude Code skills are directory-based. To install a skill:

1. Pick a directory under `skills/` in this repo.
2. Copy it into one of:
   - Personal: `~/.claude/skills/<skill-name>/`
   - Project: `.claude/skills/<skill-name>/`
3. Keep `SKILL.md` and its supporting files in the same directory.

## Current skills

| Skill | Purpose |
|---|---|
| [`ai-pulse`](skills/Geek-skills-ai-pulse/) | Low-noise sweep of the AI frontier on a sustainable cadence. Outputs a three-field (`change / data / limits`) digest, no hype, no "key takeaways". Sub-commands: `+scan`, `+labs`, `+people`, `+arxiv`, `+oss`, `+extract`, `+diff`. |

## Layout

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

## Conventions

- `SKILL.md` focuses on activation, workflow, and navigation — not on
  reference data.
- Large reference material goes under `references/` and is loaded on demand,
  not pre-loaded.
- `description` in frontmatter names both what the skill is for and what it
  is **not** for. When-NOT-to-use is as important as when-to-use.

## Inspiration

Directory convention and repo layout follow
[staruhub/ClaudeSkills](https://github.com/staruhub/ClaudeSkills).
