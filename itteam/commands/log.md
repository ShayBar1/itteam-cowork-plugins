---
description: Log a decision to the current project's decisions-log.md with the next stable ID. Invokes the log-decision skill. Optional argument: the decision statement itself. Only for decisions already made — not for tentative or future decisions.
---

# /log

You are being asked to invoke the `log-decision` skill.

First, verify the request is actually for a decision that has been made — not a tentative thought, preference, or future plan. If the statement contains hedging words ("probably", "thinking about", "leaning toward", "I think", "tomorrow we'll") or is phrased in future tense, stop and reflect that back: "This sounds like a pending question rather than a decision. Should I add it to the project's open questions instead?"

If the user typed `/log` followed by a statement, treat that statement as the decision to log. Parse it to extract:
- Title (short present-tense phrasing — derive from the statement)
- Decision (the statement itself, cleaned up if needed)
- Rationale (ask if not provided — required field, cannot be skipped)

If the user typed `/log` with no argument, ask:
1. What was decided? (Title + decision statement)
2. Why? (Rationale)
3. What alternatives were considered? (At least one, with reason rejected)

Then execute the `log-decision` skill:
1. Identify the current project. If ambiguous, ask which project's decisions-log to update.
2. Read the project's `decisions-log.md` to determine the next D-NNN.
3. Apply the voice check to your generated phrasing (not the user's verbatim words).
4. Append the entry above any existing entries (reverse chronological — newest at top).
5. Report ID assigned, file path, and a one-line confirmation.

If the user's statement appears to be an action item, open question, risk, or assumption (not a decision), stop and reflect that back — do not silently coerce it into a decision entry.