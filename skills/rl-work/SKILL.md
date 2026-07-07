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
hands over concrete tasks to delegate; the AI does them — but not blindly. It
grounds each task in the user's philosophy and goals, keeps the task record and
every artifact **inside the workspace** under `work/` so this and future agents
have comprehensive context, and links the work back to the goal it serves so a
later review can judge whether it actually moved the criteria.

Where `rl-plan` **plans** the steps and `rl-pi-review` / `rl-human-review`
**observe** them, `rl-work` is where the AI **does** the computer-side steps on
the user's behalf — and leaves a record honest enough to review.

## Input

Whatever the user typed after `/rl-work` is a **seed, not a strict argument**:
- One or more concrete tasks to do (e.g. "draft the landing-page copy and
  scaffold the Next.js route") → do them.
- A task tied to a named goal → do it and link it to that goal.
- **Nothing** → scan `work/open/` for unfinished items and `actions/open/` for
  computer-side steps that could be delegated, summarise them, and ask what to
  take on.

Take the seed as a starting point and pin down what "done" means before you
start.

## Steps

1. **Locate the workspace.** The workspace is the current working directory (the
   folder life-harness is installed into); if invoked from a subdirectory, walk up
   to the nearest ancestor containing `philosophy/`, `goals/`, `actions/`. If none
   is found, tell the user to run `/rl-init` first, and stop. Then **ensure
   `work/open/` and `work/closed/` exist** — create them (each with a `.gitkeep`)
   if the workspace predates this folder, so the record has somewhere to live.

2. **Ground the task — honestly.** Read the relevant `philosophy/` and, if the
   task relates to a goal, that `goals/open/<slug>.md` and its
   `actions/open/<slug>.md`. Before doing anything, answer: which goal or action
   step does this serve? If it advances none, say so — is it worth delegating
   given what the user values, or is it off-lens busywork? Don't refuse ad-hoc
   tasks, but don't launder off-lens work into looking goal-serving either. If a
   task cuts against a stated principle, flag it before acting.

3. **Pin down "done" — grill the ambiguity out.** Restate the concrete deliverable
   and the definition of done in a line or two. Then, before you open the work
   record (step 4), hand it to grilling: invoke the **Skill** tool with the skill
   named `grilling` (the `/grilling` skill — the model-invocable one, *not* the
   human-only `grill-me`). It interrogates the deliverable and the definition of
   done, turn by turn, until nothing ambiguous remains — rather than you guessing.
   **Let it run** and let the user answer, then **come back here**, confirm the
   sharpened result with the user, and only then move on. A task you can't tell is
   finished can't be reviewed. Skip **only** if `grilling` is not in your available
   skills; if you skip, say so out loud ("grilling not installed — I'll interrogate
   it myself") and run the same interrogation yourself.

4. **Open the work record.** One folder per task at `work/open/<slug>/`
   (kebab-case slug), with a `task.md` brief inside using the shape below. Keeping
   it here — not in scratch space — is the whole point: the brief and its
   artifacts sit together so the next agent inherits full context. Read the
   current date, time, and timezone from the system clock (do not guess) and record
   `created` **to the minute with both the UTC offset and the IANA timezone name**
   — e.g. `2026-07-06 14:30 +08:00 (Asia/Shanghai)`.

5. **Do the work — in the repo.** Produce the artifacts inside
   `work/open/<slug>/` (code, drafts, research notes, data). If the deliverable has
   to land elsewhere in a real project, still record in the brief what you changed
   and where, and link it. Keep decisions and dead-ends in the log — future
   context depends on it.

6. **Log honestly.** Append to `## Log` as you go or at the end: what you did,
   what's **done vs partial vs blocked vs deferred**, decisions and why, and
   anything the user must decide or verify. Don't overstate — mark partial as
   partial. Keep `## Artifacts` current and bump `updated`.

7. **Hand back and link to the loop.** Tell the user what's done, what needs their
   input, and what remains. If the task served a goal, make sure this record's
   `goal:` (and `action:`) field names it, so `/rl-pi-review` and `/rl-human-review`
   pick this folder up as evidence when they review that goal — the AI's own actions
   don't show up in pi0, so this record *is* the evidence. Surface the outcome in the
   goal's action-file `## Log` too if it's worth a line. When the task is finished or
   dropped, set its `status` and **move** the whole folder to `work/closed/<slug>/`.

   `task.md` shape:

   ```markdown
   # Work — <task title>

   - status: active                 # active | done | abandoned
   - created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
   - updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
   - goal: goals/open/<slug>.md      # the goal this serves — or "none (ad-hoc)"
   - action: actions/open/<slug>.md  # the step it advances, if any

   ## Task
   What the user delegated (their words) and your restatement of the concrete
   deliverable and definition of done.

   ## Plan
   Brief steps you'll take (for anything non-trivial).

   ## Artifacts
   Files produced in this folder (and any real-project paths you touched), each
   with a one-line description.

   ## Log
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — what you did, done/partial/blocked,
   decisions, what's next or what needs the user.
   ```

## Stance

You are doing the work, but you are still the honest mirror: ground each task in
the user's goals and philosophy, tell them plainly when a delegated task serves
neither, and record what actually got done — partial as partial, blocked as
blocked — not an impressive-sounding summary. The record you leave in `work/` is
what every later agent, and every review, will trust.
