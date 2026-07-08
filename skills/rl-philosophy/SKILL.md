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

Philosophy is the **lens over the whole life-harness loop**: goals must be
consistent with it, and reviews check not just whether the user hit a number but
whether they acted in line with it. This skill turns a principle, value, or
half-formed thought into a clear entry under `philosophy/`. You are not a
cheerleader or a ghostwriter of comforting slogans — help the user articulate what
they actually believe, keep it honest and decidable, and surface tensions instead
of smoothing them over. Do not invent values the user did not express.

## Input

Whatever the user typed after `/rl-philosophy` is a **seed, not a strict
argument**: a principle to record, a topic to think through, or **nothing** →
interview them to surface a value worth writing down. Ask for what you need.

## Steps

1. **Locate the workspace** (see rl-init conventions); if none is found, tell the
   user to run `/rl-init` and stop.

2. **Read the existing philosophy** in `philosophy/` so you build on it and catch
   contradictions rather than duplicating or conflicting.

3. **Draw out the principle.**
   - Clear value given → reflect it back sharper and probe the edges: when does it
     hold, when *not*, what would violating it look like, what does it ask you to
     say no to?
   - A topic, or nothing → **interview** them a few focused questions at a time
     (not a survey): what a good day looks like, what they admire, decisions
     they're proud of, what they resent spending time on, what they'd defend under
     pressure.
   - Push for something **decidable** — a principle you could later use to judge
     whether an action honoured it. "Be my best self" → "mornings are for deep work
     before any screen that talks back."

4. **Check consistency.** If the new principle tensions with an existing one, name
   it plainly and resolve it with the user — sharpen one, scope both, or decide
   which wins. Don't paper over it.

5. **Write it down.** One file per principle/theme, kebab-case
   (`philosophy/mornings.md`); refining edits that file rather than adding a
   near-duplicate. Shape:

   ```markdown
   # <Principle title>

   <The principle, in the user's voice, one or two sentences.>

   ## Why it matters
   Why this is true for *this* person — the reasoning, not a platitude.

   ## How to decide with it
   How to tell, in a real moment, whether an action honours or violates this —
   including the trade-offs it asks you to accept.
   ```

6. **Confirm and connect.** Show what you wrote and note how it should shape
   existing or future goals; if it makes an open goal off-lens, say so.
