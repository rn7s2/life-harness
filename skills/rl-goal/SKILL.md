---
name: rl-goal
description: >-
  Discuss a goal with the user and write it down in the life-harness workspace,
  with clear checkable success criteria, consistent with their philosophy and
  existing goals. Use when the user runs /rl-goal, states a goal they want to set
  (e.g. "spend two focused hours a day building my side project"), names an
  existing goal to refine, or asks what to pursue next. Reads philosophy/ and
  goals/; writes to goals/open/ and goals/closed/.
metadata:
  loop: life-harness
  step: 2-goal
---

# rl-goal — set a goal with criteria you can actually check

A goal is useful only if it (a) follows from the user's **philosophy**, (b) is
consistent with their **other goals**, and (c) carries **clear, checkable
criteria** for what "accomplished" means. This skill turns an intention into such
a goal file, or refines an existing one. Your job is not to make the user feel
productive for having a goal — it is to make the goal honest, aligned, and
checkable, so a later review can tell the truth about whether it was reached.

## Input

Whatever the user typed after `/rl-goal` is a **seed, not a strict argument**: a
goal to shape, an existing goal to refine, or **nothing** → review their open
goals and ask what to pursue. Ask for whatever else you need.

## Steps

1. **Locate the workspace** (see rl-init conventions); if none is found, tell the
   user to run `/rl-init` and stop.

2. **Load the lens and context.** Read everything in `philosophy/` and the live
   goals under `goals/open/` (glance at `goals/closed/` when consistency needs it).
   You can't check a goal for consistency without them.

3. **Grill the intent first.** Before shaping anything, invoke the `grilling`
   skill (model-invocable; not the human-only `grill-me`) to interrogate the user
   turn by turn — *why this, why now, is it genuinely yours, what would
   "accomplished" really look like*. Let it run to completion, then carry what
   surfaced into steps 4–5. If `grilling` isn't installed, say so and run that
   interrogation yourself. (Empty seed → summarise open goals, ask which to
   advance/refine/retire, then grill that.)

4. **Check against philosophy and other goals — honestly.** Does the goal honour
   the stated principles or quietly cut against one? Does it collide with or crowd
   out an open goal (two goals demanding the user's best hours are in tension)?
   If it conflicts, say so and work it out before writing — don't launder an
   off-lens goal into an acceptable-sounding one.

5. **Forge checkable success criteria** — the core of the skill. Drive toward
   criteria a later review can mark met / partial / not met from evidence, not
   vibes: prefer observable outcomes; make quantity, cadence, and window explicit
   ("two focused hours a day on ≥5 days/week", not "more focus"); fold *what counts
   / what doesn't* and *how progress is observed* into the criterion line, not
   separate essays. If a goal can't be made checkable, reshape it until it can, or
   record honestly that it's a direction, not a goal.

6. **Write it down.** One file per goal at `goals/open/<slug>.md` (kebab-case).
   When refining, edit that file in place — **rewrite `target` and the criteria
   clean to the current truth** (no "(re-baselined X→Y)" asides in a live line;
   that story goes in one history entry). Timestamps per the workspace convention
   (to the minute, offset + IANA zone, from the system clock). In `## Notes /
   history`, keep the **last ~3 entries**; move older ones verbatim to
   `goals/history/<slug>.md` (append-only, not auto-loaded) with a one-line
   pointer. Cite philosophy only where load-bearing. When the goal becomes
   **accomplished** or **abandoned**, set `status` and **move** the file to
   `goals/closed/<slug>.md`; move its action file `actions/open/<slug>.md →
   actions/closed/<slug>.md` too, and repoint the cross-reference lines in both.
   Shape:

   ```markdown
   # <Goal title>

   - status: active                    # active | accomplished | abandoned
   - created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)
   - target: <one line — current truth only>
   - actions: actions/open/<slug>.md

   ## Why this goal
   How it follows from the philosophy and fits the other goals.

   ## Success criteria
   - [ ] Concrete, checkable criterion (quantity + cadence + window; what counts / how observed, in a line)

   ## Notes / history
   Last ~3 refinements, each a timestamped one-liner. Older → goals/history/<slug>.md.
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — created / refined: <what changed and why>
   ```

7. **Confirm and point onward.** Show the file, restate the criteria in one line,
   and point the user to `/rl-plan` to turn the goal into concrete, timed steps.
