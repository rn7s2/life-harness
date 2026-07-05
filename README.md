# life-harness

A set of skills for running your life like an agent runs a task:
set a goal, act, observe the real evidence, let the AI push back, and iterate.

## Background

I built [pi0](https://github.com/rn7s2/pi0) to capture what actually happens on my
machine (frontmost app, OCR'd screenshots, keystrokes). **life-harness** is the layer
on top: it turns that raw evidence — plus my own philosophy and goals — into a loop
that helps me optimise and train myself with the help of AI agents.

The idea is to run life the way [ReAct](https://react-lm.github.io/) runs an agent:

```text
philosophy ── the lens for every step below (does each action still honour it?)

set goal ──► act (in real life) ──► observe ──► reason ──► refine goal
             ▲                      (pi0 data)                │
             └────────────── loop until reached ──────────────┘
```

Philosophy is not a step — it sits over the whole loop. At **observe**, for
instance, the AI checks not only whether you moved toward the goal but whether the
way you acted stayed true to what you value.

The AI's job is not to cheer you on. It is to hold your actions against your own
stated goals and philosophy, and tell you where they diverge.

## Workspace

Every skill reads from and writes to one shared workspace of plain Markdown files,
organised as a filesystem hierarchy so both you and the AI can browse it:

```text
workspace/
  philosophy/      # principles and values — the lens for every decision
  goals/           # one file per goal, each with clear success criteria
  reviews/         # dated progress reviews built from pi0 evidence
```

Plain Markdown is the only source of truth. No database, nothing hidden — you can
read, edit, and version-control the whole thing by hand.

## Install

The skills are packaged to the [Agent Skills](https://github.com/vercel-labs/skills)
spec — one `SKILL.md` per skill — so they install into **any** supported agent
(Claude Code, Cursor, Cline, OpenCode, and 70+ others) with one command:

```bash
# add all four skills to the current project (or -g for your user directory)
npx skills add rn7s2/life-harness

# or pick specific skills / target specific agents
npx skills add rn7s2/life-harness --skill rl-init --agent '*'
```

Each skill lives under [`skills/`](skills/) as a self-contained directory. Once
installed, run `/rl-init` first, then follow the loop below. The skills are
portable: they carry their own instructions and depend only on the shared Markdown
workspace (and, for reviews, a connected `pi0` MCP server).

## Skills

All four skills operate on the same workspace, so they compose into one loop. Each
runs as a slash command. Whatever you type after it is a **seed, not a strict
argument** — the AI takes it as a starting point and asks for anything else it needs.

### `rl-init`

Sets up the workspace. Creates the `philosophy/`, `goals/`, and `reviews/`
directories and the conventions the other skills rely on. Run this once, first.

- **Input:** none (optionally a path for where the workspace should live)
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
- **Writes:** `goals/`
- Every goal must be consistent with your philosophy and existing goals, and must
  carry **clear, checkable criteria** for what "accomplished" means.

### `rl-pi-review`

Reviews progress against a goal using pi0.

- **Input:** which goal to review and over what time window —
  e.g. `/rl-pi-review the side-project goal, this past week`. Leave it blank and the
  AI reviews all active goals since the last review.
- **Reads:** `goals/`, `philosophy/`; pulls activity data from pi0
- **Writes:** `reviews/`
- The AI asks for the data and clarifications it needs, assesses progress
  **objectively** against the goal's criteria, and proposes refinements to the goal
  when the evidence calls for it.

## The loop, in skills

1. `/rl-init` — prepare the workspace.
2. `/rl-philosophy how I want to spend my time` — write down what you value.
3. `/rl-goal spend two focused hours a day building my side project` — set a goal with clear criteria.
4. _Act in real life._
5. `/rl-pi-review the side-project goal, this past week` — observe via pi0, get
   honest feedback, refine the goal.
6. Return to step 4 and keep acting — refining the goal (and philosophy) whenever
   the evidence calls for it — until the goal is reached.
