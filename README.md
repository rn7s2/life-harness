# life-harness

A set of skills for running your life like an agent runs a task:
set a goal, plan concrete actions, act, observe the real evidence, let the AI push
back, and iterate.

## Install

The skills follow the [Agent Skills](https://github.com/vercel-labs/skills) spec —
one `SKILL.md` per skill — so they install into **any** supported agent (Claude
Code, Cursor, Cline, OpenCode, and 70+ others). Run the commands below from inside
the folder you want as your life-harness workspace: **that folder becomes the
workspace**, holding all your philosophy, goals, actions, and work.

```bash
# Claude Code users: create the skills directory first
mkdir -p .claude/skills

# 1. grilling — companion skill that pressure-tests your goals,
#    plans, and tasks before they're written down
npx skills add mattpocock/skills --skill=grilling -y

# 2. life-harness — all skills
npx skills add rn7s2/life-harness --skill '*' -y
```

Then run `/rl-init` in that folder to scaffold the workspace, and follow
[the loop](#the-loop-in-skills) from there.

`grilling` is optional but recommended: `rl-goal`, `rl-plan`, and `rl-work` use
it to grill a goal, plan, or task definition before writing it down, and skip that
step cleanly when it isn't installed. (`grilling` is the model-invocable skill; the
`grill-me` command is only a human-typed alias for it, so the skills call `grilling`
directly.) For the skills to actually invoke it, `grilling` must appear in Claude's
skill list under exactly that name — after installing, type `/` and confirm you see
`grilling` (not, say, a plugin-namespaced `some-plugin:grilling`). The command name
comes from the install **directory**, not the skill's frontmatter `name`; if yours
differs, update the `grilling` references in `rl-goal`, `rl-plan`, and `rl-work` to
match. Otherwise the skills are self-contained —
they depend only on the shared Markdown workspace (and, for `rl-pi-review`, a
connected [`pi0`](https://github.com/rn7s2/pi0) MCP server).

## Background

I built [pi0](https://github.com/rn7s2/pi0) to capture what actually happens on my
machine (frontmost app, OCR'd screenshots, keystrokes). **life-harness** is the layer
on top: it turns that raw evidence — plus my own philosophy and goals — into a loop
that helps me optimise and train myself with the help of AI agents.

The idea is to run life the way [ReAct](https://react-lm.github.io/) runs an agent:

```text
philosophy ── the lens for every step below (does each action still honour it?)

set goal ──► plan actions ──► act ──────────► observe ──► reason ──► refine
             ▲             (you, or the AI    (pi0 / human)            │
             │              via rl-work)                              │
             └────────────────────── loop until reached ─────────────┘
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
  work/
    open/          # one folder per task delegated to the AI — brief + artifacts
    closed/        # finished or abandoned work, kept with its artifacts
```

Each goal has exactly **one** action file sharing its slug
(`goals/open/side-project.md` ↔ `actions/open/side-project.md`): the plan of
concrete, doable, timed steps, plus a running log of what the reviews observed.
When a goal is accomplished or abandoned, its goal file and action file move to
`closed/`.

`work/` is where tasks you delegate to the AI get carried out — the brief and
every artifact live **in the same repo**, one folder per task, so that this and
every future agent inherits the full context instead of digging through scratch
space. When a task is finished or dropped, its folder moves to `closed/`.

Plain Markdown is the only source of truth. No database, nothing hidden — you can
read, edit, and version-control the whole thing by hand.

## Skills

All seven skills operate on the same workspace, so they compose into one loop. Each
runs as a slash command. Whatever you type after it is a **seed, not a strict
argument** — the AI takes it as a starting point and asks for anything else it needs.

### `rl-init`

Sets up the workspace in the current folder. Creates the `philosophy/`,
`goals/open`, `goals/closed`, `actions/open`, `actions/closed`, `work/open`, and
`work/closed` directories (with a `.gitkeep` in each) and the conventions the
other skills rely on. Run this once, first.

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

### `rl-plan`

Turns a goal into concrete, doable, timed steps you and the AI agree on.

- **Input:** which goal to plan for, or an existing action plan to refine —
  e.g. `/rl-plan the side-project goal`. Leave it blank and the AI finds open
  goals that lack a plan.
- **Reads:** `goals/`, `philosophy/`
- **Writes:** `actions/open/<slug>.md` (one per goal, same slug)
- Each step must be **doable** and **timed** (a real action with a time
  constraint), and checkable — so a later review can tell whether it happened. The
  action file is the material the review skills observe against.

### `rl-work`

Executes concrete tasks you delegate to the AI — the "act" arm of the loop done on
the computer. It grounds each task in your philosophy and goals, does the work, and
keeps the brief **and every artifact** in the repo under `work/`.

- **Input:** the task(s) to do, optionally tied to a goal —
  e.g. `/rl-work draft the landing-page copy for the side-project goal`. Leave it
  blank and the AI scans `work/open/` and delegatable action steps and asks what
  to take on.
- **Reads:** `philosophy/`, `goals/`, `actions/`
- **Writes:** `work/open/<slug>/` — a `task.md` brief plus artifacts (moves to
  `work/closed/` when done or abandoned)
- Not a blind executor: it flags tasks that serve no goal or cut against a
  principle, records what got done honestly (partial as partial, blocked as
  blocked), and leaves the record a later review reads as evidence — since the
  AI's own work doesn't show up in pi0.

### `rl-pi-review`

Observes your **computer activity** with pi0 and reviews it against a goal.

- **Input:** which goal to review and over what time window —
  e.g. `/rl-pi-review the side-project goal, this past week`. Leave it blank and the
  AI reviews open goals with computer-visible steps since their last log entry.
- **Reads:** `goals/`, `actions/`, `philosophy/`, and the AI's work records under
  `work/`; pulls activity data from pi0
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
- **Reads:** `goals/`, `actions/`, `philosophy/`, and any `work/` records tied to
  the goal; evidence comes from your report
- **Writes:** appends a `[human]` entry to the goal's action file and updates
  `goals/`
- Same honest mirror as `rl-pi-review`, for actions done off the computer: you
  report what got done and achieved, the AI holds it against your plan and
  philosophy.

## The loop, in skills

1. `/rl-init` — prepare the workspace (in the folder you installed into).
2. `/rl-philosophy how I want to spend my time` — write down what you value.
3. `/rl-goal spend two focused hours a day building my side project` — set a goal with clear criteria.
4. `/rl-plan the side-project goal` — agree on concrete, doable, timed steps.
5. _Act — on the computer or in the physical world_ — or `/rl-work the side-project
   landing page` to delegate a computer-side task to the AI, done in `work/`.
6. `/rl-pi-review the side-project goal, this past week` — observe your computer
   activity via pi0; or `/rl-human-review` — report real-world outcomes. Either way,
   get honest feedback and refine the goal and plan.
7. Return to step 5 and keep acting — refining the goal, plan, and philosophy
   whenever the evidence calls for it — until the goal is reached.
