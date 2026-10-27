---
name: ub-mode-pragmatic
description: Code-change discipline based on Uncle Bob's principles (make it work, make it right, follow practice), scaled to the size of the task. Use on /ub-mode-pragmatic or /ub-mode, when asked to "use ub-mode", "write clean code" or "do TDD", and for any change to logic in a project that contains this skill.
---

# ub-mode (pragmatic)

Every change goes through three stages: **Make it work → Make it right → Practice**.
How deep each stage goes depends on the size of the task. Size the task first.

## 0. Size the task

| Size | Examples | What to apply |
|---|---|---|
| **S** | typo, copy, styles, color, config, rename | "Make it work" only (no test), then commit. Skip the rest. |
| **M** | logic bug, new function, new endpoint, new bot command | All three stages. Quality metrics only if the tools already exist in the project. |
| **L** | new subsystem, data model change, architecture rework | All three stages in full. Before writing code, write a short plan and get the user's approval. |

If you're unsure between two sizes, pick the smaller one and say so in the report.

## Priorities when rules conflict

1. Don't break what works, and don't lose user data.
2. Solve the task you were given.
3. Keep the change as small as possible.
4. Clean structure and quality metrics.

Example: a minimal fix beats a rewrite from scratch if the fix solves the task without adding hacks. Propose the rewrite separately. Don't do it silently.

---

# Stage 1. Make it work

## Minimum
- Each decision (rule, constant, formula) lives in one place.
- Make the smallest change that solves the task. Don't add functions, abstractions or settings "while you're at it".
- Before adding anything, look for what you can remove. Removing is also a solution.

## Data first
- Define the shape of the data (types, schema, state structure) before writing logic.
- Make illegal states impossible to write: enums instead of strings, required fields instead of "may be empty", separate types for separate entities.
- If the project has more than 5–7 domain terms, keep a short glossary (`GLOSSARY.md` or a comment in the types module).

## Boundaries
- Validate and parse external data once, where it enters (request, file, API response, user message).
- Inside the system, trust data that has already been validated. Don't repeat checks.
- Keep business logic in pure functions with no side effects. Push I/O to the edges.
- Don't silence the compiler or linter (`any`, `@ts-ignore`, `# type: ignore`, `eslint-disable`). If there's no way around it, explain why in a comment.

## Reliability
- Make operations that can repeat (webhooks, retries, cron jobs, migrations) idempotent: running twice, or running again after a crash halfway through, ends in the same state.
- If two parts of the system don't need the same mutable state, give each its own. If they do, allow only one writer, and enforce it in the structure, not by convention.

## TDD (M and L)
1. Write a failing test.
2. Run it and confirm it fails **for the reason you expect**, not because of a typo or a bad import.
3. Make the smallest change that makes it pass.
4. If an honest test isn't possible (UI layout, external service with no sandbox, one-off script), don't write a fake one. Describe how you checked it by hand.

If the project has no test framework, don't install one silently. Tell the user and suggest one (Vitest for JS/TS, pytest for Python).

## A new requirement doesn't fit the old design
- Don't bolt it on the side.
- Ask: "How would I build this if the requirement had been there from the start?"
- If the answer is very different from the current code, it's an L task: write a step-by-step plan, get approval, then deliver it in parts. Each part must leave the system working.

## Two failed attempts: stop
If two different fixes in a row fail the same check:
1. Stop. Don't make a third attempt of the same kind.
2. State in one sentence the assumption both attempts shared ("I assume X arrives already sorted").
3. Check that assumption directly: with a log, a test, or by reading the code.
4. If it's false, fix the assumption, not the symptom. If you can't check it, ask the user.

## Routine work: build a tool
If the same mechanical operation has to be done more than 5 times (renaming across many files, data migration, generating similar components):
1. Do one unit by hand and check it.
2. Write a script that repeats exactly that unit.
3. Compare the script's output on the first unit with the manual result, then run it on the rest.

---

# Stage 2. Make it right

## Root cause
- Reproduce the bug first (with a test or exact steps).
- Fix the cause, not the place where it crashes.
- A `try/catch`, a `null` check or a default value that only hides the error is not a fix. If you need a temporary workaround, mark it with a `TODO` and the reason, and mention it in the report.

## Small verifiable steps
- Finish one small part, check it (tests, build, run), and only then start the next.
- Breaking things in the middle is allowed only if you planned it and can roll it back.

## Tests check behavior
- A test calls the code the way a user or another module calls it, and checks the visible result.
- Don't test internals: which private functions were called, or in what order.
- Empty-implementation check: if the test would still pass with every function returning `undefined` or nothing, the test is useless. Rewrite it.

## Refactoring
1. Pin the current behavior with tests (if there are none, write them first).
2. Change the structure without changing the behavior.
3. Run the tests and prove the behavior is the same.

Keep refactoring and behavior changes in separate commits.

## Quality metrics (M and L only, only if the tool is available)

| What | Goal | Tools (examples) |
|---|---|---|
| Complexity × lack of coverage (CRAP) | functions with CRAP ≥ 10: split them, cover them with tests, or both | Clojure: crap4clj. Other stacks: use cyclomatic complexity (ESLint `complexity`, radon for Python) plus coverage |
| Duplication | remove obvious copies of logic | jscpd, PMD CPD, pylint `duplicate-code` |
| Mutation testing | surviving mutants mean weak tests | Stryker (JS/TS), mutmut (Python), PIT (Java) |

Rules:
- If the tool isn't in the project, **don't install it** without approval. Skip the item and say in the report what was skipped.
- Long `switch/case` statements by entity type don't always need splitting: if the branches are simple and the list is stable, leave them.
- Delete dead code you find along the way, if you're sure it isn't used.

---

# Stage 3. Practice

## Report
When the work is done, give a short report (not a log of every step):
- what changed and why;
- how it was checked (tests, manual check, build);
- what was deliberately skipped and why (size S, no tool, can't be tested);
- open questions and temporary workarounds.

## Context
- Keep the main conversation compact: don't paste long command output, logs or large files in full. Give the gist.
- For broad code searches, use a subagent or search tools, and keep only the conclusions in the main thread.

## Commits
- Commit often, in small, logical commits.
- Commit message: what changed, why, how it was checked. Example:
  `fix(reminders): don't send duplicate reminder on repeated webhook — add idempotency key; test test_duplicate_webhook`
- Keep refactoring, fixes and new features in separate commits.

## Risky actions: only with approval
Without an explicit instruction from the user, **don't**:
- `git push` to main/master (work in a branch; offer a push or PR at the end);
- `git push --force`, `reset --hard`, or rewrite history;
- delete branches, worktrees, or files outside the task;
- run migrations that delete or change data;
- install new dependencies or tools.

Don't touch uncommitted changes you didn't make. Ask the user.
