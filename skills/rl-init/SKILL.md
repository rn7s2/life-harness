---
name: rl-init
description: >-
  Set up (or repair) the life-harness workspace in the current folder — the
  philosophy/, goals/, actions/, and work/ directories plus the conventions the
  other rl- skills rely on. Run this once, first, before rl-philosophy, rl-goal,
  rl-plan, rl-work, or rl-review. Use when the user runs /rl-init, or asks to
  initialize, set up, bootstrap, or scaffold life-harness / a life workspace.
metadata:
  loop: life-harness
  step: 0-init
---

# rl-init — prepare the life-harness workspace

life-harness runs a person's life the way a ReAct agent runs a task:
**set a goal → plan actions → act → observe → reason → refine**, with
**philosophy as the lens over the whole loop**. Every skill reads and writes one
shared workspace of plain Markdown files. This skill creates that workspace.

The workspace **is the folder life-harness is installed into** — the current
working directory. Scaffold it right here, not elsewhere. Running this again is
safe: it repairs missing pieces without overwriting existing content.

## Input

Usually nothing to supply — the workspace root is the current directory. Only if
the user explicitly names a different directory should you scaffold there.

## What to do

1. **Create the directory skeleton** under the workspace root:
   ```text
   <root>/
     philosophy/      # principles and values — the lens for every decision
     goals/
       open/          # one file per live goal, each with checkable criteria
       closed/        # goals that are accomplished or abandoned
       history/       # archived overflow of goals' Notes/history — not auto-loaded
     actions/
       open/          # one action plan per open goal — same slug as the goal
       closed/        # action plans for closed goals
       history/       # archived overflow of actions' Log — not auto-loaded
     work/
       open/          # one folder per task delegated to the AI (rl-work)
       closed/        # finished or abandoned work, with its artifacts
   ```
   Create any that are missing, each leaf holding an empty `.gitkeep` so the
   skeleton survives a fresh clone. Never delete or overwrite existing files.

2. **Write the conventions file** at `<root>/README.md` (only if absent) from the
   template in [references/workspace-readme.md](references/workspace-readme.md).
   Plain Markdown is the only source of truth — no database, nothing hidden.

3. **Offer version control (optional).** Suggest `git init` inside the workspace;
   only run it if the user agrees.

4. **Report** the created tree and point to the next steps: `/rl-philosophy` (the
   lens) → `/rl-goal` → `/rl-plan` → act, or `/rl-work` → `/rl-review`.

## Conventions the other skills rely on

Establish these here so the other skills can assume them:

- **Locating the workspace.** It is the current working directory; from a
  subdirectory, walk up to the nearest ancestor containing `philosophy/`,
  `goals/`, `actions/`. If none is found, tell the user to run `/rl-init` and
  stop. No env var, no pointer file — the workspace never lives outside its folder.
- **Timestamps.** Record moments to the minute with UTC offset + IANA zone from
  the system clock, e.g. `2026-07-06 14:30 +08:00 (Asia/Shanghai)`.
- **philosophy/** — one file per principle/theme, kebab-case; each states the
  principle, why it matters, and how to decide with it.
- **goals/** — one file per goal, `goals/open/<slug>.md` while live,
  `goals/closed/<slug>.md` once accomplished/abandoned. Carries status,
  timestamps, checkable **Success criteria**, and a link to its action file.
- **actions/** — one action file per goal, same slug: `actions/open/<slug>.md`.
  Holds the **Plan** (doable, timed, checkable steps) and a running **Log**.
  Moves to `closed/` alongside its goal.
- **work/** — one folder per delegated task, `work/open/<slug>/` then
  `work/closed/<slug>/`, holding a `task.md` brief **and its artifacts**. Written
  by `rl-work`; the `task.md` `## Log` rolls overflow to `task.log.md` beside it
  (append-only, not auto-loaded).
- **Closing a goal (accomplished or abandoned) — one sweep over all three artifact
  types; leave nothing dangling in `open/`.** When a review shows a goal is reached,
  or the user drops it, do the *whole* sweep, not just the goal file:
  1. **Goal** — set `status: accomplished | abandoned`; move `goals/open/<slug>.md`
     → `goals/closed/<slug>.md`.
  2. **Action** — set the action file's own `status: done | abandoned`
     (`accomplished` maps to **done**); move `actions/open/<slug>.md` →
     `actions/closed/<slug>.md`.
  3. **Work** — for each `work/open/<task>/task.md` whose `goal:`/`action:` names
     this goal, set `status: done | abandoned` and move the folder to
     `work/closed/<task>/`. Keep one open only if the user wants that work to
     outlive the goal — then say so.
  4. **Repoint every cross-reference from `open/` to `closed/`:** the goal's
     `actions:` line, the action's `goal:` line, and each moved task's
     `goal:`/`action:` lines. No live link may point into `open/` at a file now in
     `closed/`. `/rl-review` (criteria met) and `/rl-goal` (abandon) run this whole
     sweep.
- **Context economy — these files reload into context every loop, so keep them
  lean.** Live sections (`target`, criteria, plan steps, the work brief) state
  only the *current* truth: on refine, rewrite them clean rather than accreting
  "(re-baselined X→Y)" asides, and collapse a finished slice to a one-line pointer
  rather than leaving its full narrative. Budget: `target`/each step = 1 line,
  each criterion = 1–2, each artifact = 1, each history/log entry = a heading +
  ≤5 one-sentence bullets (delta-only). Cite philosophy only where load-bearing.
  `## Notes / history` (goals), `## Log` (actions), and `## Log` (work `task.md`)
  keep only the last ~3 entries; move older ones verbatim to
  `goals/history/<slug>.md`, `actions/history/<slug>.md`, or the task's own
  `task.log.md` (beside `task.md`, append-only, never auto-loaded) and leave a
  one-line pointer.

Keep the workspace human-browsable: clear filenames, plain Markdown, no hidden
state.
