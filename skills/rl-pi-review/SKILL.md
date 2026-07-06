---
name: rl-pi-review
description: >-
  Observe real computer activity with pi0 and judge it against a life-harness
  goal's action plan and success criteria and the user's philosophy — honestly,
  not as a cheerleader — then log what happened into the action file and refine.
  Use when the user runs /rl-pi-review, asks to review progress / how they're
  doing on a goal from their computer activity, or asks for an honest check over a
  time window (e.g. "the side-project goal, this past week"). Reads goals/,
  actions/, philosophy/; pulls activity data from pi0; writes to actions/ and
  goals/.
metadata:
  loop: life-harness
  step: 4-pi-review
---

# rl-pi-review — observe the computer evidence, then tell the truth

This is the **observe → reason** turn of the loop for work done **on the
computer**. It compares what the user actually did — from real pi0 activity
evidence — against the goal's action plan (its timed steps) and success criteria
and their philosophy, and logs an honest verdict straight into the action file. It
is the step where the AI is explicitly **not** a cheerleader.

For actions done in the physical world, use `/rl-human-review` instead — it does
the same job from what the user reports rather than from pi0.

## Input

Whatever the user typed after `/rl-pi-review` is a **seed, not a strict
argument**:
- A goal and/or a time window (e.g. "the side-project goal, this past week").
- **Nothing** → review the open goals whose action plans have computer-visible
  steps, since each one's last log entry.

Take the seed as a starting point and ask for any missing piece (which goal,
what window) rather than guessing.

## Steps

1. **Locate the workspace.** The workspace is the current working directory (the
   folder life-harness is installed into); if invoked from a subdirectory, walk up
   to the nearest ancestor containing `philosophy/`, `goals/`, `actions/`. If none
   is found, tell the user to run `/rl-init` first, and stop.

2. **Load the standard to judge against.** Read the target goal file under
   `goals/open/`, **its matching action file** `actions/open/<slug>.md`, and all
   of `philosophy/`. Pull out the goal's **success criteria** and the action
   plan's **timed steps** verbatim — these are what the evidence will be measured
   against — and note, from the action's "How progress is observed" section, which
   steps are computer-visible. Read the action's `## Log`; the last entry's
   timestamp is the default start of this review's window.

3. **Fix the time window.** Confirm the exact start and end **to the minute, each
   with its UTC offset and IANA timezone name** — pi0 timestamps are local
   wall-clock, so record the offset and zone (e.g.
   `2026-07-06 09:00 +08:00 (Asia/Shanghai)`), not a bare date. If the user said
   "this past week", state the concrete start/end you'll use. If the user changed
   timezones during the window, note it so the offsets aren't misread.

4. **Gather the evidence from pi0.** pi0 is the intended evidence source — a
   personal activity store exposed as an MCP server. Use its tools **in this
   order** (see [references/pi0-usage.md](references/pi0-usage.md) for detail):
   1. `apps` for the window → which apps were used, when, how much data each has.
      Always start here to scope the analysis.
   2. `app-guidance` for each app you'll analyse → how to read that app's screen
      text and keystrokes.
   3. `contexts` for the window (optionally narrowed to one app) → the
      time-ordered timeline of what the user *saw* (OCR) and *wrote* (keys).
      Paginate: start with a small page size to gauge volume before reading more.
   - All pi0 times are the user's **local** wall-clock; present times in local
     time.
   - This data is **personal and sensitive** — keystrokes can include passwords.
     Use it only to assess the goal, never surface secrets or credentials in the
     log, and summarise rather than quoting raw keystrokes.
   - **If pi0 is not connected**, say so plainly. Ask the user for the evidence
     the action plan named (or another concrete source), or suggest
     `/rl-human-review` if the steps were done off the computer. Do not fabricate
     activity, and do not assess from assumption — an honest review needs real
     evidence.

5. **Assess objectively, step and criterion by criterion.** For each timed step in
   the action plan and each success criterion, judge **done / partial / not done**
   (or **met / partial / not met**) and cite the specific evidence for that
   verdict. Hold steps to their time constraint — a step "Mon–Fri, 2h before noon"
   that happened twice, late, is partial, and say why. Distinguish what the
   evidence shows from what it can't show; note gaps instead of filling them with
   optimism. Resist both flattery and undue harshness — report what happened.

6. **Check philosophy, not just the number.** Ask whether the *way* the user
   acted honoured their stated values, even where the metric moved. Moving toward
   a goal by violating a principle is a finding worth naming. Likewise, honouring
   the philosophy while missing the number is worth crediting.

7. **Propose refinements when the evidence calls for it.** Based on what actually
   happened, suggest concrete changes: sharpen a vague criterion, adjust an
   unrealistic or too-easy target, re-plan a step that isn't working, split or
   retire a goal, or flag a philosophy/goal tension the window exposed. Recommend,
   with reasons — the user decides. Route criterion changes to `/rl-goal` and step
   changes to `/rl-action`. If a goal's criteria are all met, say so and propose
   marking it `accomplished`.

8. **Log the verdict into the action file.** Do **not** write a separate review
   file. Append one entry to the `## Log` section of `actions/open/<slug>.md`,
   tick or adjust the affected Plan checkboxes, and bump the action's `updated`.
   Read the current date, time, and timezone from the system clock (do not guess);
   record the timestamp and window endpoints **to the minute with the UTC offset
   and IANA timezone name**. Entry shape:

   ```markdown
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [pi0] — window <start> to <end>
   - Step / criterion — done | partial | not done — <specific evidence>
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

The point of this step is an honest mirror. Hold the user's real behaviour
against their own action plan, goals, and philosophy, and tell them where they
diverge — kindly, specifically, and without spin.
