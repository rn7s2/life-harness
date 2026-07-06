# life-harness workspace

This folder is a **life-harness workspace**: it runs your life the way a ReAct
agent runs a task — set a goal, act in real life, observe the real evidence, let
the AI push back, and refine. It is plain Markdown, so you can read, edit, and
version-control all of it by hand. There is no database and nothing hidden.

```text
philosophy ── the lens for every step below (does each action still honour it?)

set goal ──► act (in real life) ──► observe ──► reason ──► refine goal
             ▲                      (real evidence)             │
             └────────────── loop until reached ───────────────┘
```

Philosophy is not a step — it sits over the whole loop. The AI's job is not to
cheer you on. It holds your actions against your own stated goals and philosophy
and tells you where they diverge.

## Layout

```text
philosophy/   principles and values — the lens for every decision
goals/        one file per goal, each with clear success criteria
reviews/      dated progress reviews built from real evidence
```

## File conventions

**Timestamps.** Record moments (a goal's `created`, history entries, a review's
`reviewed`, and window endpoints) **to the minute, with a UTC offset and the IANA
timezone name** — like `2026-07-06 14:30 +08:00 (Asia/Shanghai)`. Record both: the
offset pins the instant, and the zone name keeps every record unambiguous across
DST and even if you move across timezones. Both goals and reviews are filed
under a subfolder named for their **natural local date** — goals by their creation
date (`goals/YYYY-MM-DD/<slug>.md`), reviews by the review date
(`reviews/YYYY-MM-DD/<goal-slug>.md`) — so the tree browses by day.

### philosophy/
One file per principle or theme, kebab-case (`mornings.md`, `deep-work.md`).
Each file states the principle, why it matters to you, and how to decide with it.

### goals/
One file per goal, filed under a subfolder named for its **creation local date**:
`YYYY-MM-DD/<slug>.md` (kebab-case slug, e.g. `goals/2026-07-06/side-project.md`).
Refining edits the file in place, so it stays in its creation-date folder.
Suggested shape:

```markdown
# <Goal title>

- status: active                    # active | accomplished | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)   # to the minute, offset + timezone name
- target: <date or cadence, if any>

## Why this goal
How it follows from your philosophy and fits your other goals.

## Success criteria
- [ ] Concrete, checkable criterion
- [ ] Another concrete, checkable criterion

## Notes / history
Refinements over time, each with a timestamp (to the minute, offset + timezone name).
```

Every goal must be consistent with your philosophy and your other goals, and
must carry **clear, checkable criteria** for what "accomplished" means.

### reviews/
One file per review, filed under a natural-local-date subfolder:
`YYYY-MM-DD/<goal-slug>.md` (the date is your local calendar date). Suggested
shape:

```markdown
# Review — <goal title> — YYYY-MM-DD

- reviewed: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)   # to the minute, offset + timezone name
- window: <start> to <end>                        # each endpoint to the minute, offset + name
- goal: goals/YYYY-MM-DD/<slug>.md

## Evidence
What the activity data (e.g. pi0) and any other sources actually show.

## Against the criteria
Criterion-by-criterion: met / partial / not met, with the evidence for each.

## Philosophy check
Did you act in line with your values, not just move the numbers?

## Verdict & refinements
Honest assessment, and any proposed change to the goal or philosophy.
```

## Managing the workspace with skills

The four `rl-` skills all operate on this workspace:

1. `/rl-init` — prepare the workspace (this file).
2. `/rl-philosophy` — write down what you value.
3. `/rl-goal` — set a goal with clear criteria.
4. _Act in real life._
5. `/rl-pi-review` — observe via your activity data, get honest feedback,
   refine the goal.
6. Return to step 4 and keep looping until the goal is reached.
