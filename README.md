# ai-skills

Agent skills for Claude, in the `SKILL.md` format. Each skill lives in its own folder under `skills/` with its own README.

| Skill | What it does |
|---|---|
| [ub-mode-pragmatic](skills/ub-mode-pragmatic) | Code-change discipline based on Uncle Bob's principles, scaled to the size of the task |

## Install

Replace `<skill>` with the folder name from the table.

**Claude Code, all projects:**

```bash
mkdir -p ~/.claude/skills/<skill>
curl -fsSL https://raw.githubusercontent.com/mirzaaliyev/ai-skills/main/skills/<skill>/SKILL.md \
  -o ~/.claude/skills/<skill>/SKILL.md
```

**Claude Code, one project:** copy the skill folder to `.claude/skills/` in the project and commit it.

**Claude apps (claude.ai, desktop):** zip the skill folder and upload it as a custom skill in Settings.

Usage and details are in each skill's README.
