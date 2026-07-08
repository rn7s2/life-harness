# life-harness workspace

This folder is a **life-harness workspace**: it runs your life the way a ReAct
agent runs a task — set a goal, plan concrete actions, act, observe the real
evidence, let the AI push back, and refine. It is plain Markdown, so you can read,
edit, and version-control all of it by hand. This folder *is* the workspace — the
skills read and write right here.

```text
philosophy ── the lens for every step below (does each action still honour it?)

set goal ──► plan actions ──► act ──► observe ──► reason ──► refine
  (goal)       (action)     (real)  (evidence you    (goals + actions)
             ▲                        provide)               │
             └──────────────── loop until reached ─────────────┘
```

Philosophy is not a step — it sits over the whole loop. The AI's job is not to
cheer you on. It holds your actions against your own goals and philosophy and
tells you where they diverge.

## Layout

```text
philosophy/       principles and values — the lens for every decision
goals/
  open/           one file per live goal, each with checkable success criteria
  closed/         goals that are accomplished or abandoned
  history/         archived overflow of goals' Notes/history — not auto-loaded
actions/
  open/           one action plan per open goal — SAME slug as the goal
  closed/         action plans for closed goals
  history/         archived overflow of actions' Log — not auto-loaded
work/
  open/           one folder per task delegated to the AI — brief + artifacts
  closed/         finished or abandoned work, kept with its artifacts
```

Each goal has exactly **one** action file sharing its slug
(`goals/open/side-project.md` ↔ `actions/open/side-project.md`). When a goal is
accomplished or abandoned, its goal file **and** action file move to `closed/`.
`work/` holds tasks you delegate to the AI — the brief *and* every artifact stay
inside this repo so any agent reads the whole history from one place.

## File conventions

**Timestamps.** Record moments (a goal's `created`, history entries, an action's
`updated`, log entries, review windows) to the minute with a UTC offset and IANA
zone name — like `2026-07-06 14:30 +08:00 (Asia/Shanghai)`.

**Context economy.** These files reload into context on every loop, so keep them
lean. Live sections state only the *current* truth (on refine, rewrite them clean
— don't accrete "(re-baselined X→Y)" asides; collapse a finished slice to a
one-line pointer). Budget: `target`/each step = 1 line, each criterion = 1–2, each
artifact = 1, each history/log entry = a heading + ≤5 one-sentence bullets
(delta-only). Cite philosophy only where load-bearing. `## Notes / history`
(goals), `## Log` (actions), and `## Log` (work `task.md`) keep the **last ~3
entries**; move older ones verbatim to `goals/history/<slug>.md`,
`actions/history/<slug>.md`, or the task's `task.log.md` (beside `task.md`,
append-only, not auto-loaded) with a one-line pointer.

### philosophy/
One file per principle/theme, kebab-case (`mornings.md`, `deep-work.md`) — the
principle, why it matters to you, and how to decide with it.

### goals/
`goals/open/<slug>.md` while live, moved to `goals/closed/<slug>.md` when
accomplished/abandoned. Refining edits in place. Shape:

```markdown
# <Goal title>

- status: active                    # active | accomplished | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
- target: <one line — current truth only>
- actions: actions/open/<slug>.md

## Why this goal
How it follows from your philosophy and fits your other goals.

## Success criteria
- [ ] Concrete, checkable criterion (what counts / how observed, in a line)
- [ ] Another

## Notes / history
Last ~3 refinements, each a timestamped one-liner. Older → goals/history/<slug>.md.
```

### actions/
One per goal, same slug: `actions/open/<slug>.md`. Holds the **Plan** (doable,
timed, checkable steps — only the plan in force) and the **Log** reviews append to.
Shape:

```markdown
# Actions — <goal title>

- goal: goals/open/<slug>.md
- status: active                    # active | done | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
- updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)

## Plan
- [ ] Step — what exactly / when / how long / by when
- [ ] Step — ...

## How progress is observed
Per step, what evidence would show it happened and how you'll supply it at review.

## Log
Last ~3 entries; older → actions/history/<slug>.md.
YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [review] — source: <evidence> — what it showed.
```

### work/
`work/open/<slug>/` then `work/closed/<slug>/`, holding a `task.md` brief **plus
the artifacts the AI produced**. Shape:

```markdown
# Work — <task title>

- status: active                 # active | done | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
- updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
- goal: goals/open/<slug>.md      # the goal this serves — or "none (ad-hoc)"
- action: actions/open/<slug>.md  # the step it advances, if any

## Task
What was delegated, and the concrete deliverable / definition of done —
current-truth only (collapse a finished slice to a one-line pointer; deep detail → notes/).

## Artifacts
Files produced here (and any real-project paths touched), one line each.

## Log
Last ~3 entries; older → task.log.md (beside this file). Each = heading + ≤5 one-sentence bullets, delta-only.
YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — what got done, done/partial/blocked, decisions.
```

Because the AI's own work isn't part of the activity you report on, this record is
the evidence a later `/rl-review` reads to judge whether the work moved the goal.

## Managing the workspace with skills

1. `/rl-init` — prepare the workspace (this file).
2. `/rl-philosophy` — write down what you value.
3. `/rl-goal` — set a goal with clear criteria.
4. `/rl-plan` — turn it into concrete, doable, timed steps.
5. _Act — on the computer or in the physical world…_
6. `/rl-work` — …or delegate computer-side tasks to the AI; done in `work/`.
7. `/rl-review` — observe what you did (from evidence you provide); assess and refine.
8. Loop from step 5 until the goal is reached.
