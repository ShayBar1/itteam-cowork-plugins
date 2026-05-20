---
name: finalize-branded-docx
description: Use this skill when Shay says "finalize", "publish", "produce branded version", "make this a deliverable", or asks to convert a final markdown draft into a branded ITTeam Word document. Use when the user references producing a final .docx of a PRD, SOW, Proposal, Meeting summary, or any document that needs ITTeam header/footer/logo branding. Do NOT use for non-final drafts, in-chat content, or non-Word formats. This skill is the markdown-to-branded-docx workflow defined in 00_Context\brand-identity.md.
---

# Finalize as branded ITTeam .docx

## When this skill fires

Activate when Shay indicates a markdown draft is ready to become a final branded Word deliverable. Trigger phrases: "finalize", "publish", "make this final", "produce the branded version", "deliverable version", "send-ready".

Do NOT activate for:
- Drafts still in iteration (anything not explicitly approved as final).
- Non-Word formats (decks, spreadsheets, in-chat answers).
- Content that won't be delivered externally (internal notes, working files).

## This skill plans before executing

Per Global Instructions, this skill MUST show a plan and wait for explicit approval before any file write. The plan is non-negotiable — finalizing a deliverable is high-stakes and irreversible at the version level.

## Required reads before planning

1. `D:\Claude-Cowork\00_Context\brand-identity.md` — brand specs, source of truth for finalization workflow
2. `D:\Claude-Cowork\00_Context\brand-voice.md` — AVOID-list for voice check (Stage 3)
3. `D:\Claude-Cowork\00_Context\working-preferences.md` — naming conventions, drafts-vs-finals rules
4. The source markdown draft in `02_Working\`
5. The project's `00_Project-Context\project-brief.md` — to confirm project codename for Word metadata Subject field

## Preconditions — confirm before planning

Before producing any plan, verify:

- **Approval status**: Shay has explicitly said the draft is final ("this is final", "publish", "finalize"). If not, stop and ask.
- **Source markdown exists**: full path under `02_Working\`, version suffix included. If multiple drafts exist, ask which one.
- **Master template present**: `D:\Claude-Cowork\01_Templates\Brand\ITTeam_Template.docx` exists and is unmodified.
- **Project codename**: known and not "TBD". If TBD, fall back to `"<Client> <doctype> YYYY-MM-DD"` and flag this in the plan so Shay can override.

## Plan structure

The plan presented to Shay must include:

### Stage 1 — Inputs confirmation
- Source markdown: full path
- Output filename per `working-preferences.md` (e.g., `clalit-prd-final.docx`)
- Output path: `Projects\<client>\03_Deliverables\<type>\<filename>`
- Subfolder creation if needed (flag explicitly — new folders need approval)
- Word metadata to set: Author=Shay Bar, Company=ITTeam, Title=[derived], Subject=[codename or fallback]

### Stage 2 — Template handling
- Open `01_Templates\Brand\ITTeam_Template.docx` as base
- Verify header (logo + navy border), footer (contact line, confidentiality, page numbers), page size A4, body Calibri 11pt are intact
- Treat master template as read-only — never modify

### Stage 3 — Body replacement
- Strip placeholder body content from the template copy
- Insert finalized markdown content, preserving heading structure (H1 → Heading 1, H2 → Heading 2, etc.)
- Preserve tables, lists, hyperlinks
- Do NOT touch header, footer, page setup, logo, colors
- **Voice check**: scan content for AVOID-list words from `brand-voice.md` (revolutionary, seamless, leverage, synergy, unleash, supercharge, frictionless, effortless, groundbreaking, game-changing, ecosystem-as-metaphor, "AI-powered" as standalone adjective). Flag matches with location and exact phrase. DO NOT silently rewrite. Shay decides.

### Stage 4 — Metadata
- Set Word document properties per Stage 1

### Stage 5 — Save and verify
- Save to `03_Deliverables\<type>\<filename>`
- If a file with the same final name exists, do NOT overwrite — bump version (`-final-v2`, `-final-v3`) and flag in plan
- Re-open the saved file (or re-inspect its XML) and verify:
  - Header: logo present, navy bottom border
  - Footer: contact line (centered), confidentiality notice (left, italic, gray), page numbers (right)
  - Body: Calibri 11pt, hyperlinks navy
  - Page size: A4
  - Word metadata fields populated correctly

### Stage 6 — Decision log entry
- Append a new entry to `Projects\<client>\00_Project-Context\decisions-log.md`
- Next stable ID (D-NNN)
- Entry content:
  - Title: "[Doctype] finalized — [filename]"
  - Decision: "[Doctype] published as branded deliverable on YYYY-MM-DD"
  - Rationale: brief — source draft path, master template version, finalization trigger
  - References: source markdown path, output docx path, master template path

## After execution — end-of-session summary

Per Global Instructions:
- Files touched (full paths): source read, master read, output written, decisions-log appended
- Decisions made: D-NNN logged
- Pending: any items flagged during voice check that Shay deferred
- For Command Center: any follow-up tasks (e.g., "send to client", "request client review by [date]")

## Technical execution notes

When opening the .docx programmatically:
- Treat the .docx as a ZIP archive containing XML
- Replace content in `word/document.xml` body, preserving section properties, page setup, and header/footer references
- Do NOT touch `word/header1.xml`, `word/footer1.xml`, or files in `word/media/`
- Validate the resulting .docx after saving (does it open in Word? Are header/footer intact on every page?)
- If validation fails, do not deliver — report the failure and revert

## Failure modes — what to do

- **Master template missing**: stop. Do not regenerate from scratch. Tell Shay the template needs to be restored before finalization can proceed.
- **Source markdown ambiguous**: stop. Ask which version is final.
- **Voice check finds AVOID words**: flag every match with location. Do NOT silently rewrite. Wait for Shay's instruction (rewrite which words to what, or accept as-is).
- **Output file exists**: do not overwrite. Propose `-v2`, `-v3`, etc., and ask.
- **Project codename is TBD**: use fallback `"<Client> <doctype> YYYY-MM-DD"` for Subject metadata, flag this in the plan, suggest Shay decide the codename before next finalization.

## What this skill is NOT for

- Producing decks (.pptx) — different format, different template, separate skill (future)
- Producing spreadsheets (.xlsx) — different format, separate workflow
- Producing PDFs — convert from the finalized .docx after Shay approves, not directly
- Editing the master template itself — never. Master template changes are a separate, explicit workflow.