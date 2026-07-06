---
name: rl-init
description: >-
  Set up (or repair) the life-harness workspace in the current folder — the
  philosophy/, goals/, and actions/ directories plus the conventions the other
  rl- skills rely on. Run this once, first, before rl-philosophy, rl-goal,
  rl-action, rl-pi-review, or rl-human-review. Use when the user runs /rl-init,
  or asks to initialize, set up, bootstrap, or scaffold life-harness / a life
  workspace.
metadata:
  loop: life-harness
  step: 0-init
---

# rl-init — prepare the life-harness workspace

life-harness runs a person's life the way a ReAct agent runs a task:
**set a goal → plan concrete actions → act in real life → observe the evidence →
reason → refine**, with **philosophy as the lens over the whole loop**. Every
skill reads and writes one shared workspace of plain Markdown files. This skill
creates that workspace and the conventions the other skills depend on.

The workspace **is the folder life-harness is installed into** — the current
working directory. You do not put it somewhere else; you scaffold it right here.

Run this **once, first**. Running it again is safe — it repairs missing pieces
without overwriting existing content.

## Input

Whatever the user typed after `/rl-init` is a **seed, not a strict argument**.
There is usually nothing to supply — the workspace root is the current directory.
Only if the user explicitly names a different directory should you scaffold there
instead; otherwise use the current working directory without asking for a path.

## What to do

1. **Use the current directory as the workspace root.** The folder life-harness
   was installed into is the workspace. Do not prompt for a path or default to a
   home-directory location. (Only override if the user explicitly named a
   different directory in the seed.)

2. **Create the directory skeleton** under the workspace root:
   ```text
   <root>/
     philosophy/      # principles and values — the lens for every decision
     goals/
       open/          # one file per live goal, each with checkable criteria
       closed/        # goals that are accomplished or abandoned
     actions/
       open/          # one action plan per open goal — same slug as the goal
       closed/        # action plans for closed goals
   ```
   Create any that are missing. Never delete or overwrite files that already
   exist.

3. **Write the browsable conventions file** at `<root>/README.md` (only if it
   does not already exist) using the template in
   [references/workspace-readme.md](references/workspace-readme.md). This
   documents the layout and file conventions so both the human and any agent can
   browse and follow them. Plain Markdown is the only source of truth — no
   database, nothing hidden.

4. **Offer version control (optional).** Suggest running `git init` inside the
   workspace so the whole thing can be versioned by hand. Only do it if the user
   agrees.

5. **Report** the created tree and tell the user the next steps in the loop:
   - `/rl-philosophy` — write down what you value (the lens).
   - `/rl-goal` — set a goal with clear, checkable criteria.
   - `/rl-action` — turn a goal into concrete, doable, timed steps.
   - _Act — on the computer or in the physical world._
   - `/rl-pi-review` — observe computer activity via pi0 and assess.
   - `/rl-human-review` — report real-world outcomes and assess.

## Conventions the other skills rely on

Establish these here so `rl-philosophy`, `rl-goal`, `rl-action`, `rl-pi-review`,
and `rl-human-review` can assume them:

- **Locating the workspace.** The workspace is the current working directory (the
  folder life-harness is installed into). If the skill is invoked from a
  subdirectory, walk up to the nearest ancestor that contains `philosophy/`,
  `goals/`, and `actions/`. If none is found, tell the user to run `/rl-init`
  first, and stop. There is no environment variable and no pointer file — the
  workspace never lives outside its own folder.
- **philosophy/** — one file per principle or theme, kebab-case
  (`mornings.md`, `deep-work.md`). Each states the principle, why it matters,
  and how to decide with it.
- **goals/** — one file per goal, `goals/open/<slug>.md` while live and
  `goals/closed/<slug>.md` once accomplished or abandoned (kebab-case slug). Each
  carries a status, timestamps, and an explicit **Success criteria** section with
  checkable criteria, and links to its action file. Closing a goal **moves** the
  file from `open/` to `closed/`.
- **actions/** — one action file per goal, sharing the goal's slug:
  `actions/open/<slug>.md` (and `actions/closed/<slug>.md` when the goal closes).
  It holds the **plan** (doable, timed, checkable steps toward the goal) and a
  running **log** of observations appended by the review skills. It moves to
  `closed/` alongside its goal.

Keep the workspace human-browsable at every step: clear filenames, plain
Markdown, no hidden state inside it.
