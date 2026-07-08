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

A goal in life-harness is only useful if it (a) follows from the user's
**philosophy**, (b) is consistent with their **other goals**, and (c) carries
**clear, checkable criteria** for what "accomplished" means. This skill turns an
intention into such a goal file, or refines an existing one.

## Input

Whatever the user typed after `/rl-goal` is a **seed, not a strict argument**:
- A goal to set → shape it into a checkable goal.
- The name of an existing goal → refine it.
- **Nothing** → review their open goals and ask what to pursue next.

Take the seed as a starting point and ask for whatever else you need.

## Steps

1. **Locate the workspace.** The workspace is the current working directory (the
   folder life-harness is installed into); if invoked from a subdirectory, walk up
   to the nearest ancestor containing `philosophy/`, `goals/`, `actions/`. If none
   is found, tell the user to run `/rl-init` first, and stop.

2. **Load the lens and the context.** Read everything in `philosophy/` and the
   live goal files under `goals/open/` (glance at `goals/closed/` too when it
   matters for consistency). You cannot check a goal for consistency without them.

3. **Grill the intent — before you shape anything.** Once the context is loaded,
   your *first* move on a goal seed is to hand the conversation to grilling — not to
   start reasoning about the goal yourself. Invoke the **Skill** tool with the skill
   named `grilling` (this is the `/grilling` skill — the model-invocable one, *not*
   the human-only `grill-me`). It runs an interactive interrogation: it asks the
   user, turn by turn, *why this, why now*, whether the goal is genuinely theirs, and
   what "accomplished" would really look like. **Let it run** — ask its questions,
   wait for the user's real answers over as many turns as it takes, and don't answer
   for them or cut it short. When it wraps, **come back to this skill** and carry
   what surfaced into the philosophy check (step 4) and the criteria (step 5). Do
   this every time there's a goal to set or refine, before step 4 and before writing
   anything. Skip **only** if `grilling` is not in your available skills; if you
   skip, say so out loud ("grilling not installed — I'll interrogate you myself")
   and run the same interrogation yourself before continuing. If the seed is empty,
   there's nothing to grill yet — summarise the open goals, ask which to advance,
   refine, or retire, then grill whatever they pick.

4. **Check it against the philosophy and other goals — honestly.**
   - Does this goal honour the stated principles, or quietly cut against one?
     If it conflicts, say so and work it out with the user before writing.
   - Does it collide with or crowd out an existing open goal? Name the
     trade-off. Two goals that both demand the user's best hours are in tension.
   - If the goal is off-lens, don't launder it into an acceptable-sounding
     version — tell the user plainly.

5. **Forge checkable success criteria.** This is the core of the skill. Drive
   toward criteria that a later review could mark met / partial / not met from
   evidence, not vibes:
   - Prefer observable behaviour and outcomes over feelings.
   - Make the quantity, cadence, and time window explicit ("two focused hours a
     day, on ≥5 days each week", not "more focus").
   - Define what counts and what doesn't (what is a "focused hour"? does
     meeting-heavy time count?).
   - Note how progress will be observed — what evidence a later `/rl-review`
     could check it against (a report, an export, a screenshot, a log, or another
     source the user names).
   - If a goal can't be made checkable, that's a signal: reshape it until it can,
     or record honestly that it's a direction rather than a goal.

6. **Write it down.** One file per goal at `goals/open/<slug>.md` (kebab-case
   slug, e.g. `goals/open/side-project.md`). When refining, edit the existing file
   in place — find it by slug under `goals/open/` — appending to its history with a
   timestamp. Don't create a duplicate. When the goal becomes **accomplished** or
   **abandoned**, set its `status` and **move** the file to
   `goals/closed/<slug>.md`; also move its action file from `actions/open/<slug>.md`
   to `actions/closed/<slug>.md`, and rewrite the cross-reference paths in both (the
   goal's `actions:` line and the action's `goal:` line) to point at `closed/`.
   Suggested shape:

   ```markdown
   # <Goal title>

   - status: active                    # active | accomplished | abandoned
   - created: YYYY-MM-DD HH:MM ±HH:MM (Zone/Name)   # to the minute, UTC offset + timezone name
   - target: <date or cadence, if any>
   - actions: actions/open/<slug>.md

   ## Why this goal
   How it follows from the philosophy and fits the other goals.

   ## Success criteria
   - [ ] Concrete, checkable criterion (quantity + cadence + window)
   - [ ] What counts / what doesn't
   - [ ] How progress will be observed

   ## Notes / history
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — created / refined: <what changed and why>
   ```

   Read the current date, time, and timezone from the system clock (do not guess),
   and record `created` **to the minute with both the UTC offset and the IANA
   timezone name** — e.g. `2026-07-06 14:30 +08:00 (Asia/Shanghai)`. Include both
   on every timestamp: the offset pins the instant, and the zone name keeps it
   unambiguous across DST and even if the user later moves timezones. Read the IANA
   zone from the system (e.g. the `TZ` env var or `/etc/localtime`); only if you
   truly can't determine the name, record the offset alone rather than guessing a
   zone.

7. **Confirm and point onward.** Show the file, restate the criteria in one line,
   and point the user to `/rl-plan` to turn the goal into concrete, doable, timed
   steps — then `/rl-review` once they've acted for a while.

## Stance

Your job is not to make the user feel productive for having a goal. It is to
make the goal honest, aligned, and checkable — so a later review can tell the
truth about whether it was reached.
