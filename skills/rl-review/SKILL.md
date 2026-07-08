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
computer, in the physical world, or both — and this skill holds what they did
against the goal's action plan (its timed steps), its success criteria, and their
philosophy, then logs an honest verdict straight into the action file. It is the
step where the AI is explicitly **not** a cheerleader.

The one thing this skill does **not** assume is *how* the evidence arrives. The
user decides that: they can simply tell you what happened, or point you at an
export, a screenshot, a log, a file in the repo, a paste, or a link. Your job is
to take whatever they provide, read it honestly, and measure it against the plan
— the source is a parameter, not a fixed pipeline.

## Input

Whatever the user typed after `/rl-review` is a **seed, not a strict argument**:
- A goal and/or a time window (e.g. "the side-project goal, this past week").
- A report of what happened (e.g. "the fitness goal — ran 3× this week, skipped
  Thursday").
- A pointer to evidence — a file or export path, a screenshot, a pasted blob, a
  link — the user wants you to read and assess against the goal.
- **Nothing** → ask which goal, over what window, and **how they'll show you what
  they did** (see step 4).

Take the seed as a starting point and ask for what you need rather than guessing.

## Steps

1. **Locate the workspace.** The workspace is the current working directory (the
   folder life-harness is installed into); if invoked from a subdirectory, walk up
   to the nearest ancestor containing `philosophy/`, `goals/`, `actions/`. If none
   is found, tell the user to run `/rl-init` first, and stop.

2. **Load the standard to judge against.** Read the target goal file under
   `goals/open/`, **its matching action file** `actions/open/<slug>.md`, and all
   of `philosophy/`. Pull out the goal's **success criteria** and the action
   plan's **timed steps** verbatim — these are what the evidence will be measured
   against — and note, from the action's "How progress is observed" section, how
   each step was expected to be evidenced. Also scan `work/open/` and
   `work/closed/` for any `task.md` whose `goal:` points at this goal (or whose
   `action:` points at one of its steps): steps the user delegated to the AI are
   evidenced by that record — not by the user's own report — so read it, and don't
   ask the user to account for, or credit them with, work the AI did. Read the
   action's `## Log`; the last entry's timestamp is the default start of this
   review's window.

3. **Fix the time window.** Confirm the start and end the review covers **to the
   minute where it matters, each with its UTC offset and IANA timezone name** (e.g.
   `2026-07-06 09:00 +08:00 (Asia/Shanghai)`). If the user said "this week", state
   the concrete window you'll use. If they changed timezones during the window,
   note it so the offsets aren't misread.

4. **Establish how the user is providing their activity — then take it in.** Ask
   (or read off the seed) which source they're giving you, and work with it. It's
   the user's choice; common forms:
   - **Told directly** — the user reports in their own words what they did, how
     much, how often, when, and what got in the way. The human is the sensor.
   - **A file or export they point to** — a screen-time report, a git log, a
     tracker's CSV, an app export, a document, a photo/screenshot — sitting in the
     repo or elsewhere on disk. Read it with your file tools and parse out the
     facts that bear on the goal.
   - **A paste or a link** — data pasted into the conversation, or a URL to fetch
     (fetch it if you can; if you can't reach it, say so and ask for the content).

   You can combine sources (a report *plus* an export that backs it up). Whatever
   the form, pull out **concrete, checkable facts**, not a vibe: what exactly got
   done, how much / how often / when, the measurable outcome, and what got in the
   way. Where the evidence is a report, push gently past "it went okay" to
   specifics ("ran Mon/Wed/Sat, ~5km each; missed Thursday, late meeting"); take it
   at face value — you're not interrogating — but record what was actually claimed
   and note where the user is unsure rather than rounding up. Where the evidence is
   a file the user can't fully vouch for, say what it does and doesn't establish.

   If the user offers no usable evidence for a step, don't fabricate or assume it —
   ask for a concrete source, or record the step as unverified. An honest review
   needs real evidence.

   **This data can be sensitive.** Exports and screenshots may contain passwords,
   private messages, or credentials. Use it **only** to assess the goal under
   review, never surface secrets or private content in the written log, and
   summarise ("~2h in the editor on the project repo") rather than quoting raw
   captures.

5. **Assess objectively, step and criterion by criterion.** For each timed step in
   the action plan and each success criterion, judge **done / partial / not done**
   (or **met / partial / not met**), and cite the specific evidence for each
   verdict — matching each step to its source: steps the *user* did are judged from
   the evidence they provided, and steps delegated to the *AI* from their `work/`
   record and artifacts. Hold steps to their time constraint — a step "Mon–Fri, 2h
   before noon" that happened twice, late, is partial, and say why. Distinguish
   what the evidence shows from what it can't show; note gaps instead of filling
   them with optimism. Resist both flattery and undue harshness — report what
   happened.

6. **Check philosophy, not just the number.** Ask whether the *way* the user acted
   honoured their stated values, even where the metric moved. Moving toward a goal
   by violating a principle is a finding worth naming. Likewise, honouring the
   philosophy while missing the number is worth crediting.

7. **Propose refinements when the evidence calls for it.** Based on what actually
   happened, suggest concrete changes: sharpen a vague criterion, adjust an
   unrealistic or too-easy target, re-plan a step that isn't working, split or
   retire a goal, or flag a philosophy/goal tension the window exposed. Recommend,
   with reasons — the user decides. Route criterion changes to `/rl-goal` and step
   changes to `/rl-plan`. If a goal's criteria are all met, say so and propose
   marking it `accomplished`.

8. **Log the verdict into the action file.** Do **not** write a separate review
   file. Append one entry to the `## Log` section of `actions/open/<slug>.md`,
   tick or adjust the affected Plan checkboxes, and bump the action's `updated`.
   Read the current date, time, and timezone from the system clock (do not guess);
   record the timestamp and window endpoints **to the minute with the UTC offset
   and IANA timezone name**. Name the evidence source so the record shows how it
   was observed. Entry shape:

   ```markdown
   YYYY-MM-DD HH:MM ±HH:MM (Zone/Name) — [review] — window <start> to <end> — source: <how the evidence was provided>
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
   accepted, hand off to `/rl-goal` or `/rl-plan`.

## Stance

The point of this step is an honest mirror. Take the user's account — however they
choose to show it — hold it against their own action plan, goals, and philosophy,
and tell them where they diverge — kindly, specifically, and without spin.
