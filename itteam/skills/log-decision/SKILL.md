---
name: log-decision
description: Use this skill when Shay states a decision has been made AS A COMPLETED ACT and asks to log, record, or capture it in the project's decisions log. Trigger phrases include "log this decision", "record this decision", "we decided X", "we agreed on Y — log it", "capture this in the decisions log", or the /log slash command. Do NOT use for tentative, hedged, future-tense, or in-progress decisions ("probably", "thinking about", "leaning toward", "I think we're going with", "we'll probably", "tomorrow we'll decide"). The decision must be stated as already made, in past or present-perfect tense, not as a forecast or preference.
---

# Log a decision

## When this skill fires

Activate when Shay states a decision has been made AS A COMPLETED ACT and needs to be recorded.

Trigger phrases (decision already made):
- "log this decision"
- "record this"
- "add to decisions log"
- "we decided X"
- "we agreed on Y — log it"
- "capture this decision"
- The slash command `/log` (renamed from `/log-decision`)

DO NOT activate for tentative, hedged, future-tense, or in-progress phrasing. Examples that look like decisions but aren't:
- "probably", "maybe", "likely" → tentative
- "we're thinking about", "leaning toward", "considering" → in-progress
- "I think we're going with X", "I want to go with X" → preference, not commitment
- "tomorrow we'll decide", "we'll probably pick", "next week we should pick" → future
- "we were thinking maybe X" → exploration

If the user appears to be thinking out loud or pre-deciding, do not activate. Instead, the chat layer can offer to capture the item as an open question (Q-NNN in the relevant document) but log-decision must not fire.

Also do not activate for:
- Action items, open questions, risks, or assumptions — those go to different files or sections.
- Decisions that contradict an existing active decision unless Shay also says "supersede D-NNN" or "reverse D-NNN".

## This skill acts and reports — does not plan first

Logging a decision is a single append operation to a known file with a known format. Planning every entry would be theatrical friction. This skill:
1. Confirms a minimal set of fields
2. Appends the entry
3. Reports

If fields are ambiguous, asks before writing. Otherwise proceeds.

## Required reads before logging

1. The target project's `decisions-log.md` — to read the last D-NNN and increment correctly
2. `D:\Claude-Cowork\00_Context\working-preferences.md` — to confirm format conventions

## Fields required for every entry

Mandatory:
- **Title** — short, present-tense decision phrasing (e.g., "Use PostgreSQL over MongoDB for primary store", "Defer client portal to phase 2")
- **Decision** — one or two sentences: what was decided
- **Rationale** — why: reasoning, constraints, evidence

Recommended (ask if not obvious from context):
- **Alternatives considered** — at minimum one rejected alternative with reason
- **Decided by** — name(s) / role(s). Default to Shay if not specified.

Auto-populated:
- **ID** — next D-NNN after reading the last entry
- **Date** — today's date YYYY-MM-DD
- **Status** — "active" unless this is superseding/reversing an existing decision
- **Implications / follow-ups** — only if Shay mentions them; otherwise leave blank with a placeholder for future updates
- **References** — workspace paths if Shay cites source files; otherwise leave blank

## Entry format (mandatory)
- D-NNN — <Title>

- Date: YYYY-MM-DD
- Status: active | superseded by D-YYY | reversed on YYYY-MM-DD
- Decided by: <name(s) / role(s)>
- Decision: <decision statement>
- Rationale: <reasoning>
- Alternatives considered:
    - <alt 1> — <why rejected>
    - <alt 2> — <why rejected>
- Implications / follow-ups: <or "TBD">
- References: <or "—">

## Placement in the file

Insert directly below the "How to use" section header, ABOVE any existing entries. Reverse chronological order is enforced — newest at top, always.

If the file's existing entries are not in reverse chronological order, do NOT silently reorder them. Report the inconsistency and ask whether to fix it as a separate operation.

## Superseding / reversing existing decisions

If Shay says "supersede D-NNN" or "reverse D-NNN":
1. Create the new entry as normal with next D-ID.
2. Update the old entry's Status field:
   - `superseded by D-YYY on YYYY-MM-DD` — keep entry, mark relationship
   - `reversed on YYYY-MM-DD by D-YYY` — keep entry, mark relationship
3. Do NOT delete the old entry. Audit trail is sacred.
4. In the new entry's Rationale, reference the superseded/reversed ID and explain what changed.

## Voice check

Apply the AVOID-list to the entry text. If a forbidden word appears in Shay's verbatim quote, preserve it (it's a quote) but flag. If it appears in your generated phrasing (rationale, alternatives), rewrite before saving.

## After logging — report

- ID assigned (D-NNN)
- Full file path
- One-line confirmation of what was logged
- Any inconsistencies noticed (e.g., out-of-order entries, missing fields in nearby entries)
- For Command Center (only if Shay mentioned follow-ups): the implications/follow-up items as candidate tasks

## Failure modes

- **decisions-log.md doesn't exist for this project**: stop. This means the project wasn't initialized through `new-project-setup`. Ask whether to create the file with proper structure first, or whether the wrong project is being targeted.
- **Last D-NNN is malformed or unclear**: stop. Report and ask Shay how to proceed (manually specify the ID, or fix the file first).
- **Two concurrent decisions in one request**: log them as separate entries (D-NNN and D-NNN+1), not as one merged entry. One decision per entry, always.
- **Shay describes something that isn't actually a decision** (e.g., an open question framed as a decision): stop. Reflect back what was heard, ask for clarification. Don't pollute the decisions log with non-decisions.

## What this skill is NOT for

- Logging action items — those belong in meeting summaries (Section 2) or in the Command Center.
- Logging assumptions — those go in the relevant document's Assumptions section with A-NN IDs.
- Logging risks — those go in the relevant document's Risks section with RSK-NN IDs.
- Logging open questions — those go in the relevant document's Open Questions section with Q-NN IDs.
- Bulk-importing decisions from elsewhere — that's a one-time migration, not this skill's job.

