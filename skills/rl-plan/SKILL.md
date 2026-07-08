---
name: rl-plan
description: >-
  Turn a life-harness goal into concrete, doable, timed actions and write them
  down as the goal's action plan, consistent with the user's philosophy. Use when
  the user runs /rl-plan, asks how to actually reach a goal, wants to plan or
  break down a goal into steps, or names an existing action plan to refine. Reads
  goals/ and philosophy/; writes one action file per goal to actions/open/. The
  plan is later observed against by rl-review.
metadata:
  loop: life-harness
  step: 3-plan
---

# rl-plan — agree on concrete, doable, timed steps

A goal states *what* "accomplished" means; an **action plan** states *how* the
user gets there — steps that are **doable** and **timed**, each with a checkable
outcome. This is where the AI and human agree on something specific to actually do
and put it on record; `rl-review` later reads it back and logs what the evidence
shows happened against each step. Your job is not an impressive-looking plan — it
is one the user will actually do, because a review can only be honest about steps
that were real commitments.

There is **one action file per goal**, sharing the goal's slug:
`goals/open/<slug>.md` ↔ `actions/open/<slug>.md`.

## Input

Whatever the user typed after `/rl-plan` is a **seed, not a strict argument**: a
goal to plan (or re-plan), an existing plan to refine, or **nothing** → scan
`goals/open/` for goals with no/weak/stale plan and ask which to plan.

## Steps

1. **Locate the workspace** (see rl-init conventions); if none is found, tell the
   user to run `/rl-init` and stop.

2. **Load the goal and the lens.** Read the target goal under `goals/open/` — its
   **success criteria** and target especially — and all of `philosophy/`. If an
   action file already exists, read it so you refine rather than duplicate. If
   there's no matching goal, offer to create one with `/rl-goal` first, and stop.

3. **Draw out doable, timed steps — with the user.** Work out the concrete moves
   that satisfy the goal's criteria. Every step is **doable** (a real action, not
   an aspiration — break down anything too big), **timed** (explicit *when* / *how
   long* / *by when* — "Draft the landing page Sat morning, ~2h", not "work on the
   landing page"), and **checkable**. Once you have a **draft** — but before
   writing anything — hand it to the `grilling` skill (model-invocable; not the
   human-only `grill-me`) to pressure-test whether the user will *actually* do each
   move given their real time and energy. Let it run, then cut or reshape the steps
   it exposes as ones they're quietly unlikely to keep. If `grilling` isn't
   installed, say so and pressure-test it yourself. An honest plan they can keep
   beats an ambitious one they can't; surface collisions with other open goals.

4. **Check against philosophy and the goal.** Don't plan steps that reach the goal
   by cutting against a principle, and make sure the steps genuinely move the
   criteria (not adjacent busywork). If the only realistic plan conflicts with the
   philosophy or the goal looks unreachable as written, say so — reshape the goal
   (`/rl-goal`), don't write a plan you don't believe.

5. **Mark how each step is observed.** For each, note what evidence would show it
   happened and how the user will supply it to a later `/rl-review` — a report, a
   file/export, a screenshot, a link — so the review knows what to look for.

6. **Write the action file** at `actions/open/<slug>.md` (same slug as the goal).
   When refining, edit in place and bump `updated`. **Supersede = delete:** when a
   re-plan replaces earlier steps, remove the dead ones (or collapse a superseded
   block to a one-line Log pointer) — `## Plan` shows only the plan now in force,
   and a completed step stays a one-line checked entry, not a paragraph. Timestamps
   per the workspace convention. In `## Log`, keep the **last ~3 entries**; move
   older ones verbatim to `actions/history/<slug>.md` (append-only, not
   auto-loaded) with a one-line pointer. Leave the `## Log` heading for the review
   skills, and make sure the goal's `actions:` line points back here. Shape:

   ```markdown
   # Actions — <goal title>

   - goal: goals/open/<slug>.md
   - status: active                 # active | done | abandoned
   - created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
   - updated: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)

   ## Plan
   Only the plan in force — each step timed and checkable.
   - [ ] Step — what exactly / when / how long / by when

   ## How progress is observed
   Per step, what evidence would show it happened and how the user supplies it.

   ## Log
   Last ~3 entries; older → actions/history/<slug>.md.
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [review] — source: <evidence> — what it showed, steps done/partial/not.
   ```

7. **Confirm.** Show the plan, restate in one line what the user is committing to
   and by when, and point them to `/rl-review` once they've acted.
