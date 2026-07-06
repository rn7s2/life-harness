# Reading evidence from pi0

pi0 is a personal intelligence workbench that runs on the user's Mac. It records
which app is frontmost, periodically screenshots each display and OCRs it
on-device into text lines with normalised `[0,1]` screen coordinates (the image
is then deleted — only text survives), and records the keystrokes typed in each
app. It is exposed to agents as an **MCP server** named `pi0`. Any agent with the
pi0 MCP server connected can call its tools; the tool names below are the pi0
capabilities regardless of which agent is running.

## Call the tools in this order

1. **`apps`** with a time range → which apps were used, when, and how much data
   each has. **Always start here** to scope the analysis and pick the apps worth
   reading.
2. **`app-guidance`** for each app you intend to analyse → app-specific advice on
   how to read that app's screen text (what to focus on or ignore — e.g. an IM
   app like Feishu/Lark), plus the general rules for interpreting OCR items and
   raw keystroke text.
3. **`contexts`** with a time range (optionally narrowed to one app via its
   parameter) → a single time-ordered timeline interleaving the OCR'd screen text
   (what the user **saw**) and the keystrokes they **wrote**. Records are tagged
   `kind: "ocr"` or `kind: "keys"`. It is **paginated** — fetch page by page, and
   start with a small page size to gauge volume before reading everything.

## Time and coordinates

- Every time pi0 returns is in the user's **local** timezone. `time` fields are
  local wall-clock (no offset); each record also carries the IANA `timeZone` it
  was captured in and a raw `ts` epoch-ms (UTC instant) for precise ordering, so
  times stay correct even across trips where the zone changed.
- Inputs accept epoch-ms or ISO-8601; a bare ISO datetime with no offset is
  interpreted in the user's local timezone.
- **Present all times to the user in local time.** But when you *record* a time
  in the action file's log entry (the window endpoints, the entry timestamp),
  append both the UTC offset and the IANA timezone name — take the name straight
  from the record's `timeZone` and derive the offset from `timeZone`/`ts` — e.g.
  `2026-07-06 09:00 +08:00 (Asia/Shanghai)`, so the written log stays unambiguous
  across DST and even if the user later moves timezones.
- Each OCR item carries `(x, y, w, h)` normalised to `[0,1]` per display; `x,y`
  is the text box's top-left. Keystroke records carry a raw `text` token string
  whose format is described by `app-guidance`.

## Privacy — this is sensitive data

Keystrokes may include passwords and other secrets. When reviewing:
- Use the data **only** to assess the goal under review.
- **Never** surface credentials, passwords, or private content in the written
  review. Summarise activity ("~2h in the editor on the project repo") rather
  than quoting raw keystrokes.
- Prefer aggregate/behavioural signals (time in app, what was worked on) over
  verbatim content.

## Turning evidence into a verdict

- Use `apps` time-in-app and the `contexts` timeline to estimate how the window
  was actually spent relative to the goal's criteria (e.g. focused hours on the
  side project vs. distraction apps).
- Cite specific, checkable evidence for each criterion's met/partial/not-met
  verdict.
- Be explicit about what the evidence **cannot** show (offline work, thinking
  time) instead of assuming it either way.
