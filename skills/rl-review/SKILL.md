---
name: rl-review
description: >-
  Observe what the user actually did — from whatever evidence they provide (a
  report in their words, a file or export they point to, a screenshot, a paste, a
  link) plus the AI's own work records — and judge it against a life-harness
  goal's action plan and success criteria and the user's philosophy — honestly,
  not as a cheerleader — then log what happened into the action file and refine.
  Use when the user runs /rl-review, reports what they did / achieved, points at
  activity data to assess, or wants an honest check on progress over a time
  window. Reads goals/, actions/, philosophy/, and any AI work records under
  work/; the activity evidence comes from the source the user names; writes to
  actions/ and goals/.
metadata:
  loop: life-harness
  step: 4-review
---

# rl-review — observe the evidence, then tell the truth

This is the **observe → reason** turn of the loop. The user did things — on the
computer, in the world, or both — and this skill holds them against the goal's
timed steps, its success criteria, and their philosophy, then logs an honest
verdict into the action file. It is explicitly **not** a cheerleader: take the
user's account however they show it, hold it against their own plan, goals, and
philosophy, and tell them where they diverge — kindly, specifically, no spin.

*How* the evidence arrives is the user's choice, not a fixed pipeline: they can
tell you, or point you at an export, a screenshot, a log, a file, a paste, or a
link. Take whatever they provide and measure it against the plan.

## Input

Whatever the user typed after `/rl-review` is a **seed, not a strict argument**: a
goal and/or window, a report of what happened, a pointer to evidence, or
**nothing** → ask which goal, over what window, and how they'll show what they did.

## Steps

1. **Locate the workspace** (see rl-init conventions); if none is found, tell the
   user to run `/rl-init` and stop.

2. **Load the standard to judge against.** Read the target goal under `goals/open/`,
   its action file `actions/open/<slug>.md`, and all of `philosophy/`. Pull the
   goal's **success criteria** and the plan's **timed steps** verbatim, and note
   from "How progress is observed" how each was to be evidenced. Scan `work/open/`
   and `work/closed/` for any `task.md` whose `goal:`/`action:` points here: steps
   the user delegated to the AI are evidenced by that record, not the user's report
   — read it, and don't credit the user with the AI's work. The last `## Log`
   entry's timestamp is the default start of this review's window.

3. **Fix the time window.** Confirm start and end to the minute where it matters,
   each with offset + IANA zone. If the user said "this week", state the concrete
   window; if they changed timezones mid-window, note it.

4. **Take in the evidence.** Read (or ask for) whichever source the user is giving
   — a direct report, a file/export on disk, or a paste/link (fetch it if you can;
   if not, say so and ask for the content). Combine sources if offered. Pull out
   **concrete, checkable facts**, not a vibe: what got done, how much / how often /
   when, the measurable outcome, what got in the way. Push gently past "it went
   okay" to specifics, but take a report at face value — you're not interrogating —
   and record what was actually claimed, noting where the user is unsure rather
   than rounding up. If no usable evidence is offered for a step, don't fabricate
   it — ask for a source or record the step unverified. **Sensitive data:** exports
   and screenshots may hold passwords or private messages — use them only to assess
   this goal, never surface secrets in the log, and summarise ("~2h in the editor
   on the project repo") rather than quoting raw captures.

5. **Assess objectively, step and criterion by criterion.** For each, judge **done
   / partial / not done** (or **met / partial / not met**) and cite the specific
   evidence — user-did steps from the user's evidence, AI-did steps from the
   `work/` record. Hold steps to their time constraint (a "Mon–Fri, 2h before noon"
   step done twice, late, is partial — say why). Distinguish what the evidence
   shows from what it can't; note gaps instead of filling them with optimism.

6. **Check philosophy, not just the number.** Did the *way* the user acted honour
   their values? Moving toward a goal by violating a principle is worth naming; so
   is honouring the philosophy while missing the number.

7. **Propose refinements when the evidence calls for it.** Suggest concrete
   changes — sharpen a vague criterion, adjust an unrealistic/too-easy target,
   re-plan a step that isn't working, split or retire a goal, flag a
   philosophy/goal tension. Recommend with reasons; the user decides. Route
   criterion changes to `/rl-goal`, step changes to `/rl-plan`. If all criteria are
   met, say so and propose marking the goal `accomplished`.

8. **Log the verdict into the action file.** No separate review file — append one
   entry to `## Log` of `actions/open/<slug>.md`, tick/adjust the affected Plan
   checkboxes, and bump `updated`. The entry is **delta-only** (what changed since
   the last entry — the criteria and plan already hold current truth) and
   **budgeted** (a heading line + ≤5 one-sentence bullets; no paragraphs or
   numbered sub-lists; one load-bearing "Philosophy check" line). Timestamps per
   the workspace convention. Then keep the **last ~3 entries** in `## Log`; move
   older ones verbatim to `actions/history/<slug>.md` (append-only, not
   auto-loaded) with a one-line pointer. Shape:

   ```markdown
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [review] — window <start> to <end> — source: <how evidence was provided>
   - Step / criterion — done | partial | not done — <specific evidence, one line>
   - Philosophy check: <did the way they acted honour their values?>
   - Verdict & refinements: <honest bottom line; any proposed goal/action change>
   ```

9. **Update the goal, and close if reached.** Append a one-sentence dated line to
   the goal's `## Notes / history` (rolling older entries to
   `goals/history/<slug>.md`), and **rewrite** — don't append an aside to — any
   criterion this review changes. If all criteria are met and the user agrees, set
   `status: accomplished` and run the **full close sweep** (rl-init → *Closing a
   goal*), not just the goal and action files: flip the action file (and any open
   `work/` task serving this goal) to `done`, move goal + action + those work
   folders to `closed/`, and repoint **every** cross-reference (`actions:`, `goal:`,
   work `goal:`/`action:`) from `open/` to `closed/`. Then hand back to `/rl-goal`
   or `/rl-plan` if refinements were accepted, or return to acting.
