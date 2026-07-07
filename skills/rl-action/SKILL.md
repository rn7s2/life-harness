---
name: rl-action
description: >-
  Turn a life-harness goal into concrete, doable, timed actions and write them
  down as the goal's action plan, consistent with the user's philosophy. Use when
  the user runs /rl-action, asks how to actually reach a goal, wants to plan or
  break down a goal into steps, or names an existing action plan to refine. Reads
  goals/ and philosophy/; writes one action file per goal to actions/open/. The
  plan is later observed against by rl-pi-review and rl-human-review.
metadata:
  loop: life-harness
  step: 3-action
---

# rl-action — agree on concrete, doable, timed steps

A goal states *what* "accomplished" means; an **action plan** states *how* the
user will get there — as steps that are **doable** and **timed** (each with a
clear time constraint and a checkable outcome). This is the step where the AI and
the human agree on something specific to actually do, and put it on record. That
record is not just a to-do list: `rl-pi-review` and `rl-human-review` read it back
and log, against each step, what the evidence shows actually happened.

There is **one action file per goal**, sharing the goal's slug:
`goals/open/<slug>.md` ↔ `actions/open/<slug>.md`.

## Input

Whatever the user typed after `/rl-action` is a **seed, not a strict argument**:
- A goal name/slug → plan (or re-plan) the actions for that goal.
- An existing action plan → refine it.
- **Nothing** → scan `goals/open/` for goals with no action file or a weak/stale
  one, summarise them, and ask which to plan.

Take the seed as a starting point and ask for whatever else you need.

## Steps

1. **Locate the workspace.** The workspace is the current working directory (the
   folder life-harness is installed into); if invoked from a subdirectory, walk up
   to the nearest ancestor containing `philosophy/`, `goals/`, `actions/`. If none
   is found, tell the user to run `/rl-init` first, and stop.

2. **Load the goal and the lens.** Read the target goal file under `goals/open/`
   — its **success criteria** and target especially — and everything in
   `philosophy/`. If an action file already exists at `actions/open/<slug>.md`,
   read it so you refine rather than duplicate. If there is no matching goal, offer
   to create one with `/rl-goal` first, and stop.

3. **Draw out doable, timed steps — with the user.** Work out the concrete moves
   that would actually satisfy the goal's criteria. Every step should be:
   - **Doable** — a real action the user can take, not an aspiration. If a step is
     too big to do, break it down until each piece is.
   - **Timed** — carry an explicit time constraint: *when* (which days / part of
     day), *how long* (duration or size), and/or *by when* (a deadline).
     "Draft the landing page Sat morning, ~2h" — not "work on the landing page".
   - **Checkable** — phrased so a later review can mark it done / partial / not
     done from evidence.
   Before committing the plan, invoke the `grilling` skill to pressure-test
   whether the user will *actually* do these steps given their real time and
   energy — surface the ones they're quietly unlikely to keep, and cut or reshape
   them until the plan is one they can hold to. (Requires the external `grilling`
   skill to be installed; skip this if it is unavailable.) Be realistic about the
   user's actual time and energy — an honest plan the user can keep beats an
   ambitious one they can't. Surface trade-offs with their other open goals if the
   hours collide.

4. **Check against the philosophy and the goal.** Don't plan steps that reach the
   goal by cutting against a stated principle, and make sure the steps genuinely
   move the goal's criteria (not busywork adjacent to it). If the only realistic
   plan conflicts with the philosophy or the goal looks unreachable as written, say
   so — that's a signal to reshape the goal (hand back to `/rl-goal`), not to write
   a plan you don't believe.

5. **Mark how each step will be observed.** For each step, note whether progress
   will show up in **pi0 computer activity** (e.g. time in the editor, on the repo)
   — reviewed later by `/rl-pi-review` — or whether it happens in the physical
   world and needs a **human report** via `/rl-human-review` (e.g. a workout, a
   conversation). This tells the review skills what to look for.

6. **Write the action file.** One file per goal at `actions/open/<slug>.md`, using
   the **same slug as the goal**. When refining, edit in place and bump `updated`.
   Read the current date, time, and timezone from the system clock (do not guess),
   and record timestamps **to the minute with both the UTC offset and the IANA
   timezone name** — e.g. `2026-07-06 14:30 +08:00 (Asia/Shanghai)`. Suggested
   shape:

   ```markdown
   # Actions — <goal title>

   - goal: goals/open/<slug>.md
   - status: active                 # active | done | abandoned
   - created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
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

   Leave the `## Log` heading in place (empty) for the review skills to append to.
   Make sure the goal file's `actions:` line points back at this file.

7. **Confirm.** Show the plan, restate in one line what the user is committing to
   do and by when, and point them to `/rl-pi-review` (for computer-visible steps)
   or `/rl-human-review` (for real-world steps) once they've acted for a while.

## Stance

Your job is not to produce an impressive-looking plan. It is to reach a plan the
user will actually do — concrete, honestly timed, and aligned with the goal and
the philosophy — because a review can only be honest about steps that were real
commitments in the first place.
