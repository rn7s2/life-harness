---
name: rl-work
description: >-
  Execute concrete tasks the user delegates to the AI — the "act" arm of the
  life-harness loop, done on the computer — grounded in their philosophy and
  goals, with the task brief and every artifact kept in the same repo under
  work/ so this and future agents inherit full context. Use when the user runs
  /rl-work, hands over a task to do, or asks the AI to build, research, draft,
  or fix something toward a goal. Reads philosophy/, goals/, actions/; writes to
  work/open/ and work/closed/.
metadata:
  loop: life-harness
  step: 3.5-work
---

# rl-work — do the delegated work, in the repo, on the record

This is the **act** arm of the loop, done **on the computer by the AI**. The user
delegates concrete tasks; the AI does them — grounding each in the user's
philosophy and goals, keeping the record and every artifact **inside the
workspace** under `work/` so future agents inherit full context, and linking the
work to the goal it serves. Where `rl-plan` plans steps and `rl-review` observes
them, this is where the AI **does** the computer-side ones — and leaves a record
honest enough to review. You are still the honest mirror: flag a task that serves
no goal or cuts against a principle, and record what actually got done (partial as
partial, blocked as blocked), not an impressive summary.

## Input

Whatever the user typed after `/rl-work` is a **seed, not a strict argument**: one
or more tasks to do (optionally tied to a goal), or **nothing** → scan `work/open/`
for unfinished items and `actions/open/` for delegatable steps, and ask what to
take on. Pin down what "done" means before starting.

## Steps

1. **Locate the workspace** (see rl-init conventions); if none is found, tell the
   user to run `/rl-init` and stop. Ensure `work/open/` and `work/closed/` exist
   (create with a `.gitkeep` if the workspace predates them).

2. **Ground the task — honestly.** Read the relevant `philosophy/` and, if the
   task relates to a goal, that `goals/open/<slug>.md` and its action file. Which
   goal or step does this serve? If none, say so — is it worth delegating, or
   off-lens busywork? Don't refuse ad-hoc tasks, but don't launder off-lens work
   into looking goal-serving; if a task cuts against a principle, flag it first.

3. **Pin down "done" — grill the ambiguity out.** Restate the concrete deliverable
   and definition of done in a line or two, then hand it to the `grilling` skill
   (model-invocable; not the human-only `grill-me`) to interrogate them until
   nothing ambiguous remains. Let it run, confirm the sharpened result, then move
   on. If `grilling` isn't installed, say so and interrogate it yourself. A task
   you can't tell is finished can't be reviewed.

4. **Open the work record** at `work/open/<slug>/` (kebab-case) with a `task.md`
   brief (shape below). Keeping it here, not in scratch space, is the point — brief
   and artifacts sit together for the next agent. Timestamps per the workspace
   convention.

5. **Do the work — in the repo.** Produce artifacts inside `work/open/<slug>/`. If
   the deliverable must land elsewhere in a real project, still record in the brief
   what you changed and where, and link it. Keep decisions and dead-ends in the log.

6. **Log honestly.** Append to `## Log`: what you did, what's **done / partial /
   blocked / deferred**, decisions and why, and anything the user must decide or
   verify. Don't overstate. Keep `## Artifacts` current and bump `updated`.

7. **Hand back and link to the loop.** Tell the user what's done, what needs their
   input, what remains. If the task served a goal, make sure this record's `goal:`
   (and `action:`) names it so `/rl-review` picks it up as evidence — the AI's own
   work isn't part of the activity the user reports on, so this record *is* the
   evidence. Surface the outcome in the goal's action-file `## Log` if worth a line
   — **one short entry**, rolling older entries past ~3 to `actions/history/<slug>.md`.
   When finished or dropped, set `status` and **move** the folder to
   `work/closed/<slug>/`.

   `task.md` shape:

   ```markdown
   # Work — <task title>

   - status: active                 # active | done | abandoned
   - created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
   - updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
   - goal: goals/open/<slug>.md      # the goal this serves — or "none (ad-hoc)"
   - action: actions/open/<slug>.md  # the step it advances, if any

   ## Task
   What the user delegated (their words) + your restatement of the deliverable and definition of done.

   ## Plan
   Brief steps you'll take (for anything non-trivial).

   ## Artifacts
   Files produced here (and any real-project paths touched), one line each.

   ## Log
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — what you did, done/partial/blocked, decisions, what's next.
   ```
