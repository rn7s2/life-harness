---
name: rl-init
description: >-
  Set up (or repair) the life-harness workspace — the philosophy/, goals/, and
  reviews/ folders plus the conventions the other rl- skills rely on. Run this
  once, first, before rl-philosophy, rl-goal, or rl-pi-review. Use when the user
  runs /rl-init, or asks to initialize, set up, bootstrap, or scaffold
  life-harness / a life workspace.
metadata:
  loop: life-harness
  step: 0-init
---

# rl-init — prepare the life-harness workspace

life-harness runs a person's life the way a ReAct agent runs a task:
**set a goal → act in real life → observe the evidence → reason → refine the
goal**, with **philosophy as the lens over the whole loop**. Every skill reads
and writes one shared workspace of plain Markdown files. This skill creates that
workspace and the conventions the other three skills depend on.

Run this **once, first**. Running it again is safe — it repairs missing pieces
without overwriting existing content.

## Input

Whatever the user typed after `/rl-init` is a **seed, not a strict argument** —
usually an optional path for where the workspace should live. If they gave a
path, propose it. If they gave nothing, propose the default and confirm before
writing.

## What to do

1. **Choose the workspace root.**
   - If the user gave a path, use it (expand `~`, make it absolute).
   - Otherwise the default is `~/life-harness`. State the default and ask the
     user to confirm or override. Do not silently write to an unexpected place.

2. **Create the directory skeleton** under the chosen root:
   ```text
   <root>/
     philosophy/      # principles and values — the lens for every decision
     goals/           # one file per goal, each with clear success criteria
     reviews/         # dated progress reviews built from real evidence
   ```
   Create any that are missing. Never delete or overwrite files that already
   exist.

3. **Write the browsable conventions file** at `<root>/README.md` (only if it
   does not already exist) using the template in
   [references/workspace-readme.md](references/workspace-readme.md). This
   documents the layout and file conventions so both the human and any agent can
   browse and follow them. Plain Markdown is the only source of truth — no
   database, nothing hidden.

4. **Record where the workspace lives** so the other skills can find it from any
   working directory. Write the absolute path of `<root>` as a single line to
   the pointer file `~/.life-harness/workspace` (create the `~/.life-harness/`
   directory if needed). This pointer is the only file kept outside the
   workspace; it holds a path, never any of your life data.

5. **Offer version control (optional).** Suggest running `git init` inside the
   workspace so the whole thing can be versioned by hand. Only do it if the user
   agrees.

6. **Report** the created tree and tell the user the next steps in the loop:
   - `/rl-philosophy` — write down what you value (the lens).
   - `/rl-goal` — set a goal with clear, checkable criteria.
   - _Act in real life._
   - `/rl-pi-review` — observe the evidence, get honest feedback, refine.

## Conventions the other skills rely on

Establish these here so `rl-philosophy`, `rl-goal`, and `rl-pi-review` can assume
them:

- **Locating the workspace** (discovery order): the `LIFE_HARNESS_WORKSPACE`
  environment variable, else the path in `~/.life-harness/workspace`, else an
  ancestor/child directory that already contains `philosophy/`, `goals/`, and
  `reviews/`.
- **philosophy/** — one file per principle or theme, kebab-case
  (`mornings.md`, `deep-work.md`). Each states the principle, why it matters,
  and how to decide with it.
- **goals/** — one file per goal, filed under a subfolder named for its creation
  local date: `YYYY-MM-DD/<slug>.md` (kebab-case slug). Each carries a status,
  timestamps, and an explicit **Success criteria** section with checkable
  criteria. Refining edits the file in place, so it stays in its creation-date
  folder.
- **reviews/** — one file per review, filed under a natural-local-date subfolder:
  `YYYY-MM-DD/<goal-slug>.md`, where the date is the user's local calendar date.
  Each records the window reviewed, the evidence, an objective assessment against
  the goal's criteria, a philosophy-alignment check, and any proposed refinements.

Keep the workspace human-browsable at every step: clear filenames, plain
Markdown, no hidden state inside it.
