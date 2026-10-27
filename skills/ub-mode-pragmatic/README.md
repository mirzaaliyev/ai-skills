# ub-mode-pragmatic

Code-change discipline for Claude based on Uncle Bob's principles, scaled to the size of the task.

Every change goes through three stages: **make it work → make it right → practice**. The agent sizes the task first and applies only what fits:

| Size | Examples | Applied |
|---|---|---|
| S | typo, styles, config | fix + commit |
| M | logic bug, new feature | TDD, root cause, behavior tests, small commits |
| L | new subsystem, data model change | everything above + a plan approved by you before coding |

## Install

**Claude Code, all projects:**

```bash
mkdir -p ~/.claude/skills/ub-mode-pragmatic
curl -fsSL https://raw.githubusercontent.com/mirzaaliyev/ai-skills/main/skills/ub-mode-pragmatic/SKILL.md \
  -o ~/.claude/skills/ub-mode-pragmatic/SKILL.md
```

**Claude Code, one project:** put this folder in `.claude/skills/ub-mode-pragmatic/` at the project root and commit it.

**Claude apps (claude.ai, desktop):** zip this folder and upload it as a custom skill in Settings.

## Use

- Explicitly: `/ub-mode-pragmatic`, or "use ub-mode", "do TDD".
- Automatically: Claude picks it up when changing logic in a project where the skill is installed.

## What it adds to the original

- **Task sizing.** No TDD or mutation testing for a typo.
- **Priority order** for when rules conflict (don't break things → solve the task → smallest change → clean structure).
- **Concrete steps** instead of one-line principles, e.g. "two failed fixes → stop and check the shared assumption".
- **Tools for any stack.** Clojure-specific tools replaced with examples for JS/TS, Python and Java. Nothing gets installed without your approval.
- **Safe defaults.** No push to main, force-push, deletions, data migrations or new dependencies without explicit approval.
- **Short final report** instead of a log of every action.

## Credits

Based on [unclebob/ub-mode](https://github.com/unclebob/ub-mode) by Robert C. Martin.
