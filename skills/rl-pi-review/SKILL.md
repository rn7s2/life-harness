---
name: rl-pi-review
description: >-
  Review real progress against a life-harness goal using pi0 activity evidence,
  assess it objectively against the goal's criteria and the user's philosophy,
  and propose refinements when the evidence calls for it. Use when the user runs
  /rl-pi-review, asks to review progress / how they're doing on a goal, or asks
  for an honest check over a time window (e.g. "the side-project goal, this past
  week"). Reads goals/ and philosophy/, pulls activity data from pi0, writes to
  reviews/.
metadata:
  loop: life-harness
  step: 3-review
---

# rl-pi-review — observe the evidence, then tell the truth

This is the **observe → reason** turn of the loop. It compares what the user
actually did — from real activity evidence — against the goal's success criteria
and their philosophy, and writes a dated, honest review. It is the step where the
AI is explicitly **not** a cheerleader.

## Input

Whatever the user typed after `/rl-pi-review` is a **seed, not a strict
argument**:
- A goal and/or a time window (e.g. "the side-project goal, this past week").
- **Nothing** → review all active goals since the last review in `reviews/`.

Take the seed as a starting point and ask for any missing piece (which goal,
what window) rather than guessing.

## Steps

1. **Locate the workspace.** Discovery order: the `LIFE_HARNESS_WORKSPACE`
   environment variable → the path in `~/.life-harness/workspace` → an
   ancestor/child directory containing `philosophy/`, `goals/`, `reviews/`. If
   none is found, tell the user to run `/rl-init` first, and stop.

2. **Load the standard to judge against.** Read the target goal file(s) in
   `goals/` and all of `philosophy/`. Pull out the **success criteria** verbatim
   — these are what the evidence will be measured against — and note how the goal
   said progress would be observed. Check `reviews/` for the last review to set
   the default window's start.

3. **Fix the time window.** Confirm the exact start and end (local time). If the
   user said "this past week", state the concrete dates you'll use.

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
     review, and summarise rather than quoting raw keystrokes.
   - **If pi0 is not connected**, say so plainly. Ask the user for the evidence
     the goal named (or another concrete source). Do not fabricate activity, and
     do not assess from assumption — an honest review needs real evidence.

5. **Assess objectively, criterion by criterion.** For each success criterion,
   judge **met / partial / not met** and cite the specific evidence for that
   verdict. Distinguish what the evidence shows from what it can't show; note
   gaps instead of filling them with optimism. Resist both flattery and undue
   harshness — report what happened.

6. **Check philosophy, not just the number.** Ask whether the *way* the user
   acted honoured their stated values, even where the metric moved. Moving toward
   a goal by violating a principle is a finding worth naming. Likewise, honouring
   the philosophy while missing the number is worth crediting.

7. **Propose refinements when the evidence calls for it.** Based on what actually
   happened, suggest concrete changes: sharpen a vague criterion, adjust an
   unrealistic or too-easy target, split or retire a goal, or flag a
   philosophy/goal tension the week exposed. Recommend, with reasons — the user
   decides. If a goal's criteria are all met, say so and propose marking it
   `accomplished`.

8. **Write the review.** One file, `reviews/YYYY-MM-DD-<goal-slug>.md`, using
   today's real date. Suggested shape:

   ```markdown
   # Review — <goal title> — YYYY-MM-DD

   - window: <start> to <end>
   - goal: goals/<slug>.md

   ## Evidence
   What pi0 (and any other source) actually shows for this window.

   ## Against the criteria
   - Criterion 1 — met / partial / not met — <evidence>
   - Criterion 2 — ...

   ## Philosophy check
   Did the user act in line with their values, not just move the numbers?

   ## Verdict & refinements
   Honest bottom line, and any proposed change to the goal or philosophy.
   ```

9. **Offer to apply refinements.** If the user accepts a proposed change, hand
   off to `/rl-goal` (or edit the goal file) and update its status/history. Then
   the loop returns to acting.

## Stance

The point of this step is an honest mirror. Hold the user's real behaviour
against their own stated goals and philosophy, and tell them where they diverge —
kindly, specifically, and without spin.
