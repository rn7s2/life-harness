---
name: rl-philosophy
description: >-
  Add or refine the user's personal philosophy and values in the life-harness
  workspace — the lens that every goal and review is checked against. Use when
  the user runs /rl-philosophy, states a principle or value in their own words,
  names a topic to think through (e.g. "how I want to spend my mornings"), or
  asks to capture/refine what they believe or how they want to decide. Writes to
  philosophy/.
metadata:
  loop: life-harness
  step: 1-philosophy
---

# rl-philosophy — capture and refine what you value

Philosophy is the **lens over the whole life-harness loop**. Goals must be
consistent with it, and reviews check not just whether the user hit a number but
whether they acted in line with what they value. This skill turns a principle,
value, or half-formed thought into a clear entry under `philosophy/`.

## Input

Whatever the user typed after `/rl-philosophy` is a **seed, not a strict
argument**:
- A principle or value in their own words → refine and record it.
- A topic to think through (e.g. "how I want to spend my mornings") → explore it
  with them, then record what they conclude.
- **Nothing** → interview them to surface a value worth writing down (see below).

Take the seed as a starting point and ask for anything else you need. Do not
invent values the user did not express.

## Steps

1. **Locate the workspace.** Discovery order: the `LIFE_HARNESS_WORKSPACE`
   environment variable → the path in `~/.life-harness/workspace` → an
   ancestor/child directory containing `philosophy/`, `goals/`, `reviews/`. If
   none is found, tell the user to run `/rl-init` first, and stop.

2. **Read the existing philosophy.** Load the files already in `philosophy/` so
   you build on them and catch contradictions rather than duplicating or
   conflicting with what's there.

3. **Draw out the principle.**
   - If the user gave a clear value, reflect it back in sharper words and probe
     the edges: When does this hold? When would it *not*? What would violating it
     look like? What does it ask you to say no to?
   - If they gave a topic, or nothing, **interview** them — a few focused
     questions at a time, not a survey. Good angles: what a good day looks like,
     what they admire in others, decisions they're proud of, what they resent
     spending time on, what they'd defend under pressure.
   - Push gently for something **decidable**: a principle you could later use to
     judge whether an action honoured it. Vague aspirations ("be my best self")
     become useful when made concrete ("mornings are for deep work before any
     screen that talks back").

4. **Check consistency.** If the new principle tensions with an existing one,
   name the tension plainly and help the user resolve it — sharpen one, scope
   both, or decide which wins when they collide. Don't quietly paper over it.

5. **Write it down.** One file per principle or theme, kebab-case
   (`philosophy/mornings.md`). If refining an existing theme, edit that file
   rather than adding a near-duplicate. Suggested shape:

   ```markdown
   # <Principle title>

   <The principle, in the user's voice, one or two sentences.>

   ## Why it matters
   Why this is true for *this* person — the reasoning, not a platitude.

   ## How to decide with it
   How to tell, in a real moment, whether an action honours or violates this.
   Include the trade-offs it asks you to accept.
   ```

6. **Confirm and connect.** Show what you wrote, and note how it should shape
   existing or future goals. If it implies an open goal is now off-lens, say so.

## Stance

You are not a cheerleader and not a ghostwriter of comforting slogans. Help the
user articulate what they actually believe, keep it honest and decidable, and
surface tensions instead of smoothing them over.
