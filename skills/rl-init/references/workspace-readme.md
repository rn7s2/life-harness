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

### philosophy/
One file per principle or theme, kebab-case (`mornings.md`, `deep-work.md`).
Each file states the principle, why it matters to you, and how to decide with it.

### goals/
One file per goal, kebab-case slug (`side-project.md`). Suggested shape:

```markdown
# <Goal title>

- status: active            # active | accomplished | abandoned
- created: YYYY-MM-DD
- target: <date or cadence, if any>

## Why this goal
How it follows from your philosophy and fits your other goals.

## Success criteria
- [ ] Concrete, checkable criterion
- [ ] Another concrete, checkable criterion

## Notes / history
Refinements over time, with dates.
```

Every goal must be consistent with your philosophy and your other goals, and
must carry **clear, checkable criteria** for what "accomplished" means.

### reviews/
One file per review, named `YYYY-MM-DD-<goal-slug>.md`. Suggested shape:

```markdown
# Review — <goal title> — YYYY-MM-DD

- window: <start> to <end>
- goal: goals/<slug>.md

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
