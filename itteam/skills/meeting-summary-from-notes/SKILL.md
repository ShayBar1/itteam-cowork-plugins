---
name: meeting-summary-from-notes
description: Use this skill when Shay provides raw meeting notes, a transcript, or a recording-derived text and asks for a structured ITTeam meeting summary. Trigger phrases include "summarize this meeting", "meeting summary", "write up these notes", "format this transcript", "make this into a meeting summary", or when Shay pastes/uploads notes from a Clalit / client meeting. The skill produces a markdown file in Projects\<client>\05_Meetings\ named YYYY-MM-DD_topic.md, structured per ITTeam's Meeting-summary template: decisions and actions first, narrative second. Do NOT use for non-meeting content, single-person dictation, or notes that are already in summary form.
---

# Meeting summary from notes

## When this skill fires

Activate when Shay provides raw meeting material — handwritten notes, typed notes, Zoom/Teams transcript, voice-memo transcription, or pasted bullets — and asks for a properly structured ITTeam meeting summary.

Do NOT activate for:
- Notes already in summary form (just save them, don't re-summarize).
- Non-meeting content (1:1 brainstorm dump, personal notes, research notes — those go to `04_Knowledge-Base\` via a different workflow).
- Material from a meeting Shay didn't attend or didn't authorize summarizing.

## This skill acts and reports — does not plan first

Meeting summaries are low-stakes (a single new file in a known location) and high-frequency. Planning every one would be friction. This skill:
1. Confirms a small set of inputs (date, project, output filename)
2. Produces the summary
3. Writes it to disk
4. Reports

If anything material is ambiguous, the skill stops and asks BEFORE writing. Otherwise it proceeds.

## Required reads before producing

1. `D:\Claude-Cowork\00_Context\working-preferences.md` — naming conventions, output format defaults
2. `D:\Claude-Cowork\00_Context\brand-voice.md` — AVOID-list for voice check
3. `D:\Claude-Cowork\01_Templates\Meeting-summary-template.md` — the structural template
4. Target project's `00_Project-Context\glossary.md` — to recognize client-specific terms in the notes

## Inputs to confirm before producing

Minimum required:
- **Project** — which client folder under `Projects\`? If notes mention only one client clearly, infer and confirm. If unclear, ask.
- **Meeting date** — from the notes or from Shay. If absent, ask.
- **Topic / short title** — for the filename. Infer from notes; confirm if uncertain.

Inferable from notes (don't ask unless ambiguous):
- Attendees, meeting type, channel/location, materials referenced

If Shay provides only notes with no context, ask: "Project? Date? Topic in 2-3 words for the filename?"

## Filename and path

Format: `YYYY-MM-DD_<topic-slug>.md`
- Date prefix mandatory.
- Topic slug: lowercase, hyphenated, 2-4 words max (e.g., `kickoff-discovery`, `scope-review`, `tech-architecture-walkthrough`).
- Path: `D:\Claude-Cowork\Projects\<ClientName>\05_Meetings\<filename>`

If a file with the same name exists, do NOT overwrite. Append `-v2`, `-v3` and flag.

## Production rules

### Structure (mandatory)
Follow the Meeting-summary template exactly:
1. Metadata block
2. Attendees table
3. Section 1 — Decisions made (stable IDs D-NNN that will be logged separately to decisions-log)
4. Section 2 — Action items (A-01, A-02...)
5. Section 3 — Open questions (Q-01, Q-02...)
6. Section 4 — Risks flagged (RSK-NNN)
7. Section 5 — Glossary updates
8. Section 6 — Discussion notes (organized by topic, not chronology)
9. Section 7 — Materials referenced
10. Section 8 — Next meeting (if mentioned)
11. For the Command Center block at the end

### Content rules
- **Decisions and actions first, narrative second.** Always.
- **Attribution only when it matters.** "Shay said X" only if the source of the statement is decision-relevant; otherwise just state X.
- **Skip pleasantries.** No "the meeting opened with introductions". Start at substance.
- **Voice check.** Scan the produced summary for AVOID-list words from `brand-voice.md`. If any appear in YOUR generated content (not in quoted speech), rewrite before saving. If they appear in quoted speech from attendees, preserve and flag in the report.
- **Flag inference.** If the notes are ambiguous and you're inferring a decision or action, mark it with `[inferred — needs confirmation]` in the summary so Shay catches it.
- **Glossary terms.** If client-specific terms appear in notes that aren't in `glossary.md`, list them in Section 5 with a tentative definition for Shay to confirm later.

### What NOT to produce
- Do not invent attendees, dates, decisions, or numbers not in the source notes.
- Do not "smooth over" gaps — if notes have a gap, the summary has a gap (marked `[notes unclear here]`).
- Do not generate a section header if there's no content for it. Use the structure as scaffolding but write `[none]` if a section is genuinely empty (e.g., "Section 4 — Risks flagged: [none surfaced in this meeting]").

## After producing — report

Report to Shay:
- Full path of the created file
- Counts: N decisions, N actions, N questions, N risks, N glossary terms surfaced
- Any inference flags (`[inferred — needs confirmation]`)
- Any AVOID-list hits in quoted speech (preserved, flagged)
- For Command Center: tasks from Section 2 that need CC entries, decisions from Section 1 that need to be logged to `decisions-log.md` (which is a separate skill — `log-decision` — invoke if Shay approves)

## Failure modes

- **Notes too sparse to summarize meaningfully**: stop. Report what's missing. Don't fabricate.
- **Multiple projects mentioned**: ask which one this summary belongs to. Don't split a single meeting across multiple project folders.
- **Notes appear to be from a meeting Shay didn't attend** (e.g., third-party meeting summary): confirm authorization before writing to ITTeam workspace.
- **File already exists at target name**: append version suffix, flag.

## What this skill is NOT for

- Logging the decisions surfaced to `decisions-log.md` — that's `log-decision`'s job. After Shay reviews the summary, Shay or the slash command triggers logging.
- Creating tasks in the Command Center — Cowork doesn't own task management.
- Producing a branded .docx of the meeting summary — that's `finalize-branded-docx`. Meeting summaries live as markdown by default; finalize only if Shay explicitly wants a branded version (rare; usually meeting summaries are internal).