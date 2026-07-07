---
name: rl-human-review
description: >-
  Observe real-world outcomes the user reports and judge them against a
  life-harness goal's action plan and success criteria and the user's philosophy —
  honestly, not as a cheerleader — then log what happened into the action file and
  refine. Use when the user runs /rl-human-review, reports what they did / achieved
  / what happened in the physical world, or wants an honest check on a goal whose
  steps aren't visible on the computer. Reads goals/, actions/, philosophy/, and any
  AI work records under work/ tied to the goal; writes to actions/ and goals/.
metadata:
  loop: life-harness
  step: 4-human-review
---

# rl-human-review — observe the real-world evidence, then tell the truth

This is the **observe → reason** turn of the loop for actions done **in the
physical world** — the ones pi0 can't see (a workout, a conversation, sleep, time
away from screens). The user reports what was done, what was achieved, and what
happened; the AI assesses that against the goal's action plan (its timed steps)
and success criteria and their philosophy, and logs an honest verdict straight
into the action file. Same honest mirror as `/rl-pi-review` — only the evidence
comes from the human, not from activity data.

For work done on the computer, use `/rl-pi-review` instead — it pulls the evidence
from pi0.

## Input

Whatever the user typed after `/rl-human-review` is a **seed, not a strict
argument**:
- A goal plus a report of what happened (e.g. "the fitness goal — ran 3× this
  week, skipped Thursday") → assess it.
- **Nothing** → ask which goal to review and what happened over what window.

Take the seed as a starting point and ask for what you need.

## Steps

1. **Locate the workspace.** The workspace is the current working directory (the
   folder life-harness is installed into); if invoked from a subdirectory, walk up
   to the nearest ancestor containing `philosophy/`, `goals/`, `actions/`. If none
   is found, tell the user to run `/rl-init` first, and stop.

2. **Load the standard to judge against.** Read the target goal file under
   `goals/open/`, **its matching action file** `actions/open/<slug>.md`, and all
   of `philosophy/`. Pull out the goal's **success criteria** and the action
   plan's **timed steps** verbatim, and note which steps the plan expected to be
   confirmed by human report. Also scan `work/open/` and `work/closed/` for any
   `task.md` whose `goal:` points at this goal: steps the user delegated to the AI
   are evidenced by that record — not by their own report — so read it, and don't
   ask the user to account for, or credit them with, work the AI did. Read the
   action's `## Log`; the last entry's timestamp is the default start of this
   review's window.

3. **Fix the time window.** Confirm the start and end the report covers **to the
   minute where it matters, each with its UTC offset and IANA timezone name** (e.g.
   `2026-07-06 09:00 +08:00 (Asia/Shanghai)`). If the user said "this week", state
   the concrete window you'll use.

4. **Interview for concrete evidence — not a vibe.** The human is the sensor here,
   so draw out specifics rather than a feeling: what exactly got done, how much /
   how often / when, what the measurable outcome was, and what got in the way.
   Push gently past "it went okay" to checkable facts ("ran Mon/Wed/Sat, ~5km
   each; missed Thursday because of a late meeting"). Take the report at face value
   — you are not interrogating — but record what was actually claimed, and note
   where the user is unsure rather than rounding up.

5. **Assess objectively, step and criterion by criterion.** For each timed step in
   the action plan and each success criterion, judge **done / partial / not done**
   (or **met / partial / not met**), and cite the evidence for each verdict — the
   user's report for steps they did themselves, and the `work/` record and its
   artifacts for any step they delegated to the AI. Hold steps to their time
   constraint. Be explicit about what the report does and doesn't establish. Resist
   both flattery and undue harshness — reflect what happened.

6. **Check philosophy, not just the number.** Ask whether the *way* the user acted
   honoured their stated values, even where the outcome landed. Reaching a goal by
   violating a principle is a finding worth naming; honouring the philosophy while
   falling short of the number is worth crediting.

7. **Propose refinements when the evidence calls for it.** Suggest concrete
   changes based on what happened: sharpen a criterion, adjust an unrealistic or
   too-easy target, re-plan a step that isn't working, split or retire a goal, or
   flag a philosophy/goal tension the window exposed. Recommend, with reasons — the
   user decides. Route criterion changes to `/rl-goal` and step changes to
   `/rl-action`. If the criteria are all met, say so and propose marking the goal
   `accomplished`.

8. **Log the verdict into the action file.** Do **not** write a separate review
   file. Append one entry to the `## Log` section of `actions/open/<slug>.md`,
   tick or adjust the affected Plan checkboxes, and bump the action's `updated`.
   Read the current date, time, and timezone from the system clock (do not guess);
   record the timestamp and window endpoints **to the minute with the UTC offset
   and IANA timezone name**. Entry shape:

   ```markdown
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [human] — window <start> to <end>
   - Step / criterion — done | partial | not done — <what the user reported>
   - Step / criterion — ...
   - Philosophy check: <did the way they acted honour their values?>
   - Verdict & refinements: <honest bottom line; any proposed goal/action change>
   ```

9. **Update the goal, and close if reached.** Append a dated line to the goal's
   `## Notes / history` capturing the verdict in one sentence. If the criteria are
   all met and the user agrees, set the goal's `status: accomplished` and **move**
   `goals/open/<slug>.md → goals/closed/<slug>.md` and
   `actions/open/<slug>.md → actions/closed/<slug>.md`, rewriting the cross-
   reference paths in both (the goal's `actions:` line and the action's `goal:`
   line) to `closed/`. Then the loop returns to acting — or, if refinements were
   accepted, hand off to `/rl-goal` or `/rl-action`.

## Stance

The point of this step is an honest mirror. Take the user's own account of what
happened, hold it against their action plan, goals, and philosophy, and tell them
where they diverge — kindly, specifically, and without spin.
