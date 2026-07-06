# life-harness

A set of skills for running your life like an agent runs a task:
set a goal, plan concrete actions, act, observe the real evidence, let the AI push
back, and iterate.

## Background

I built [pi0](https://github.com/rn7s2/pi0) to capture what actually happens on my
machine (frontmost app, OCR'd screenshots, keystrokes). **life-harness** is the layer
on top: it turns that raw evidence — plus my own philosophy and goals — into a loop
that helps me optimise and train myself with the help of AI agents.

The idea is to run life the way [ReAct](https://react-lm.github.io/) runs an agent:

```text
philosophy ── the lens for every step below (does each action still honour it?)

set goal ──► plan actions ──► act ──► observe ──► reason ──► refine
             ▲               (real)  (pi0 / human)              │
             └───────────────── loop until reached ─────────────┘
```

Philosophy is not a step — it sits over the whole loop. At **observe**, for
instance, the AI checks not only whether you moved toward the goal but whether the
way you acted stayed true to what you value.

The AI's job is not to cheer you on. It is to hold your actions against your own
stated goals, action plans, and philosophy, and tell you where they diverge.

## Workspace

Every skill reads from and writes to one shared workspace of plain Markdown files.
**The workspace is the folder you install life-harness into** — the skills scaffold
and operate on it right there, not in some separate location. It is organised as a
filesystem hierarchy so both you and the AI can browse it:

```text
<the folder you installed into>/
  philosophy/      # principles and values — the lens for every decision
  goals/
    open/          # one file per live goal, each with clear success criteria
    closed/        # goals that are accomplished or abandoned
  actions/
    open/          # one action plan per open goal — same slug as the goal
    closed/        # action plans for closed goals
```

Each goal has exactly **one** action file sharing its slug
(`goals/open/side-project.md` ↔ `actions/open/side-project.md`): the plan of
concrete, doable, timed steps, plus a running log of what the reviews observed.
When a goal is accomplished or abandoned, its goal file and action file move to
`closed/`.

Plain Markdown is the only source of truth. No database, nothing hidden — you can
read, edit, and version-control the whole thing by hand.

## Install

The skills are packaged to the [Agent Skills](https://github.com/vercel-labs/skills)
spec — one `SKILL.md` per skill — so they install into **any** supported agent
(Claude Code, Cursor, Cline, OpenCode, and 70+ others) with one command:

```bash
# from inside the folder you want to be your life-harness workspace:
npx skills add rn7s2/life-harness

# or pick specific skills / target specific agents
npx skills add rn7s2/life-harness --skill rl-init --agent '*'
```

Install into the folder you want to keep your life-harness data in — that folder
becomes the workspace. Each skill lives under [`skills/`](skills/) as a
self-contained directory. Once installed, run `/rl-init` there first, then follow
the loop below. The skills are portable: they carry their own instructions and
depend only on the shared Markdown workspace (and, for pi0 reviews, a connected
`pi0` MCP server).

## Skills

All six skills operate on the same workspace, so they compose into one loop. Each
runs as a slash command. Whatever you type after it is a **seed, not a strict
argument** — the AI takes it as a starting point and asks for anything else it needs.

### `rl-init`

Sets up the workspace in the current folder. Creates the `philosophy/`,
`goals/open`, `goals/closed`, `actions/open`, and `actions/closed` directories and
the conventions the other skills rely on. Run this once, first.

- **Input:** none (the workspace is the folder you run it in)
- **Writes:** the workspace skeleton

### `rl-philosophy`

Adds or refines your personal philosophy and values.

- **Input:** a principle or value in your own words, or a topic to think through —
  e.g. `/rl-philosophy how I want to spend my mornings`. Leave it blank and the AI
  interviews you to surface it.
- **Writes:** `philosophy/`
- Captures what you believe and how you want to decide, so goals and reviews can be
  checked against it.

### `rl-goal`

Discusses a goal with the AI and writes it down.

- **Input:** the goal you want to set, or the name of an existing goal to refine —
  e.g. `/rl-goal spend two focused hours a day building my side project`. Leave it blank and the AI reviews
  your open goals and asks what to pursue next.
- **Reads:** `philosophy/`, existing `goals/`
- **Writes:** `goals/open/` (moves to `goals/closed/` when accomplished/abandoned)
- Every goal must be consistent with your philosophy and existing goals, and must
  carry **clear, checkable criteria** for what "accomplished" means.

### `rl-action`

Turns a goal into concrete, doable, timed steps you and the AI agree on.

- **Input:** which goal to plan for, or an existing action plan to refine —
  e.g. `/rl-action the side-project goal`. Leave it blank and the AI finds open
  goals that lack a plan.
- **Reads:** `goals/`, `philosophy/`
- **Writes:** `actions/open/<slug>.md` (one per goal, same slug)
- Each step must be **doable** and **timed** (a real action with a time
  constraint), and checkable — so a later review can tell whether it happened. The
  action file is the material the review skills observe against.

### `rl-pi-review`

Observes your **computer activity** with pi0 and reviews it against a goal.

- **Input:** which goal to review and over what time window —
  e.g. `/rl-pi-review the side-project goal, this past week`. Leave it blank and the
  AI reviews open goals with computer-visible steps since their last log entry.
- **Reads:** `goals/`, `actions/`, `philosophy/`; pulls activity data from pi0
- **Writes:** appends a `[pi0]` entry to the goal's action file and updates
  `goals/`
- The AI assesses progress **objectively** against the action plan's timed steps
  and the goal's criteria, logs what the evidence shows, and proposes refinements
  when the evidence calls for it.

### `rl-human-review`

Observes what you did **in the physical world** — the steps pi0 can't see.

- **Input:** which goal and what happened —
  e.g. `/rl-human-review the fitness goal — ran 3× this week`. Leave it blank and
  the AI asks which goal and what happened.
- **Reads:** `goals/`, `actions/`, `philosophy/`; evidence comes from your report
- **Writes:** appends a `[human]` entry to the goal's action file and updates
  `goals/`
- Same honest mirror as `rl-pi-review`, for actions done off the computer: you
  report what got done and achieved, the AI holds it against your plan and
  philosophy.

## The loop, in skills

1. `/rl-init` — prepare the workspace (in the folder you installed into).
2. `/rl-philosophy how I want to spend my time` — write down what you value.
3. `/rl-goal spend two focused hours a day building my side project` — set a goal with clear criteria.
4. `/rl-action the side-project goal` — agree on concrete, doable, timed steps.
5. _Act — on the computer or in the physical world._
6. `/rl-pi-review the side-project goal, this past week` — observe your computer
   activity via pi0; or `/rl-human-review` — report real-world outcomes. Either way,
   get honest feedback and refine the goal and plan.
7. Return to step 5 and keep acting — refining the goal, plan, and philosophy
   whenever the evidence calls for it — until the goal is reached.
