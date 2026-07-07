# life-harness workspace

This folder is a **life-harness workspace**: it runs your life the way a ReAct
agent runs a task — set a goal, plan concrete actions, act in real life, observe
the real evidence, let the AI push back, and refine. It is plain Markdown, so you
can read, edit, and version-control all of it by hand. There is no database and
nothing hidden. This folder *is* the workspace — the skills read and write right
here, not in some other location.

```text
philosophy ── the lens for every step below (does each action still honour it?)

set goal ──► plan actions ──► act ──► observe ──► reason ──► refine
  (goal)       (action)     (real)  (pi0 / human)       (goals + actions)
             ▲                                                  │
             └──────────────── loop until reached ─────────────┘
```

Philosophy is not a step — it sits over the whole loop. The AI's job is not to
cheer you on. It holds your actions against your own stated goals and philosophy
and tells you where they diverge.

## Layout

```text
philosophy/       principles and values — the lens for every decision
goals/
  open/           one file per live goal, each with checkable success criteria
  closed/         goals that are accomplished or abandoned
actions/
  open/           one action plan per open goal — SAME slug as the goal
  closed/         action plans for closed goals
work/
  open/           one folder per task delegated to the AI — brief + artifacts
  closed/         finished or abandoned work, kept with its artifacts
```

Each goal has exactly **one** action file sharing its slug
(`goals/open/side-project.md` ↔ `actions/open/side-project.md`). Live work lives
under `open/`; when a goal is accomplished or abandoned, its goal file **and** its
action file move to `closed/`.

`work/` is where tasks you delegate to the AI get done — the brief *and* every
artifact stay **inside this repo**, so both you and any future agent read the
whole history from one place. Nothing is scattered in scratch folders.

## File conventions

**Timestamps.** Record moments (a goal's `created`, history entries, an action's
`updated`, log entries, and review window endpoints) **to the minute, with a UTC
offset and the IANA timezone name** — like `2026-07-06 14:30 +08:00 (Asia/Shanghai)`.
Record both: the offset pins the instant, and the zone name keeps every record
unambiguous across DST and even if you move across timezones.

### philosophy/
One file per principle or theme, kebab-case (`mornings.md`, `deep-work.md`).
Each file states the principle, why it matters to you, and how to decide with it.

### goals/
One file per goal, `goals/open/<slug>.md` while live (kebab-case slug, e.g.
`goals/open/side-project.md`), moved to `goals/closed/<slug>.md` when accomplished
or abandoned. Refining edits the file in place. Suggested shape:

```markdown
# <Goal title>

- status: active                    # active | accomplished | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)   # to the minute, offset + timezone name
- target: <date or cadence, if any>
- actions: actions/open/<slug>.md

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

### actions/
One action file per goal, sharing the goal's slug: `actions/open/<slug>.md`
(moved to `actions/closed/<slug>.md` when the goal closes). It holds the **plan**
— doable, timed steps toward the goal — and a running **log** of observations that
the review skills append. Suggested shape:

```markdown
# Actions — <goal title>

- goal: goals/open/<slug>.md
- status: active                    # active | done | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)   # to the minute, offset + timezone name
- updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)

## Plan
Doable, timed steps toward the goal — each with a time constraint and checkable.
- [ ] Step — what exactly / when / how long / by when
- [ ] Step — ...

## How progress is observed
Which steps are visible in pi0 computer activity; which need a human report.

## Log
YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [pi0|human] — what the evidence showed,
which steps done/partial/not, adjustments made.
```

The **log** is where reviews land: `/rl-pi-review` appends `[pi0]` entries from
computer activity, `/rl-human-review` appends `[human]` entries from what you
report about the physical world. There is no separate reviews folder — the action
file is the single record of both the plan and how it actually went.

### work/
One folder per task you delegate to the AI, sharing a kebab-case slug:
`work/open/<slug>/` while active, moved to `work/closed/<slug>/` when finished or
abandoned. Inside each folder is a `task.md` brief — what was delegated, which
goal it serves, and a running log of what got done — **plus the artifacts the AI
produced** (code, drafts, research notes, data). Keeping the brief and its outputs
together, in the repo, is the point: every agent inherits the full context.
Suggested `task.md` shape:

```markdown
# Work — <task title>

- status: active                 # active | done | abandoned
- created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)   # to the minute, offset + timezone name
- updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
- goal: goals/open/<slug>.md      # the goal this serves — or "none (ad-hoc)"
- action: actions/open/<slug>.md  # the step it advances, if any

## Task
What was delegated, and the concrete deliverable / definition of done.

## Artifacts
Files produced in this folder (and any real-project paths touched), one line each.

## Log
YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — what got done, done/partial/blocked, decisions.
```

`/rl-work` writes here. Because the AI's own actions don't show up in pi0, this
record is the evidence a later `/rl-pi-review` or `/rl-human-review` reads to judge
whether the work moved the goal.

## Managing the workspace with skills

The seven `rl-` skills all operate on this workspace:

1. `/rl-init` — prepare the workspace (this file).
2. `/rl-philosophy` — write down what you value.
3. `/rl-goal` — set a goal with clear criteria.
4. `/rl-action` — turn the goal into concrete, doable, timed steps.
5. _Act — on the computer or in the physical world…_
6. `/rl-work` — …or delegate computer-side tasks to the AI; done in `work/`.
7. `/rl-pi-review` — observe computer activity via pi0; assess and refine.
8. `/rl-human-review` — report real-world outcomes; assess and refine.
9. Return to step 5 and keep looping until the goal is reached.
