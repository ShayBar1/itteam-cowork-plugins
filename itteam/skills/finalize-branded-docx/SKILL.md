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

1. `D:\Claude-Cowork\00_Context\brand-identity.md` — brand specs (including §11 Client-facing variant), source of truth for finalization workflow
2. `D:\Claude-Cowork\00_Context\brand-voice.md` — AVOID-list for voice check (Stage 3b)
3. `D:\Claude-Cowork\00_Context\working-preferences.md` — naming conventions, drafts-vs-finals rules, Deliverable language defaults, Hebrew docx bidi handling
4. The source markdown draft in `02_Working\`
5. The project's `00_Project-Context\project-brief.md` — to confirm project codename for Word metadata Subject field

## Preconditions — confirm before planning

Before producing any plan, verify:

- **Approval status**: Shay has explicitly said the draft is final ("this is final", "publish", "finalize"). If not, stop and ask.
- **Source markdown exists**: full path under `02_Working\`, version suffix included. If multiple drafts exist, ask which one.
- **Master template present**: `D:\Claude-Cowork\01_Templates\Brand\ITTeam_Template.docx` exists and is unmodified.
- **Project codename**: known and not "TBD". If TBD, fall back to `"<Client> <doctype> YYYY-MM-DD"` and flag this in the plan so Shay can override.
- **Audience and language**: determine which is needed before planning.
  - **Audience**: is this an **internal record** (preserves D/A/Q identifiers, working-baseline framing, sibling-project paths), a **client-facing** document (stripped of internal references), or **both** (parallel artefacts)? For doctypes that obviously go to a client (Proposal, SOW), confirm intent rather than assume.
  - **Language**: default deliverable language is **English**. For client-facing documents, ask whether **English only**, **Hebrew only**, or **both**. Never assume bilingual. If both are needed, produce separate files with `-en` and `-he` suffixes.
  - If both internal and client-facing are needed, plan them as two parallel artefacts logged as two separate decisions.

## Plan structure

The plan presented to Shay must include:

### Stage 1 — Inputs confirmation
- Source markdown: full path
- Output filename per `working-preferences.md` and the variant convention:
  - Internal record: `<client>-<doctype>-final.docx`
  - Client-facing English: `<client>-<doctype>-client-en.docx`
  - Client-facing Hebrew: `<client>-<doctype>-client-he.docx`
  - If both internal and client-facing produced, they live side by side in `03_Deliverables\<type>\` and reference each other in their respective decisions log entries.
- Output path: `Projects\<client>\03_Deliverables\<type>\<filename>`
- Subfolder creation if needed (flag explicitly — new folders need approval)
- Word metadata to set: Author=Shay Bar, Company=ITTeam, Title=[derived], Subject=[codename or fallback]

### Stage 2 — Template handling
- Open `01_Templates\Brand\ITTeam_Template.docx` as base
- Verify header (logo + navy border), footer (contact line, confidentiality, page numbers), page size A4 are intact
- Verify typography per variant:
  - **Internal record**: Calibri 11pt body (the §3 default in `brand-identity.md`).
  - **Client-facing**: Calibri 12pt body, 1.15 line spacing, 8pt paragraph spacing after (the §11 client-facing variant in `brand-identity.md`). Tables stay at 11pt.
- Treat master template as read-only — never modify

### Stage 3 — Body replacement

#### 3a — Strip internal content (client-facing variants only)
Remove from the client-facing copy (do not touch the internal record):
- All decision IDs (`D-XXX`), assumption IDs (`A-XX`), question IDs (`Q-XX`)
- Internal workspace paths (`01_Inputs\…`, `02_Working\…`, `00_Project-Context\…`)
- Sibling-project references (other `Projects\…` paths)
- Internal terminology like "working baseline" → rewrite to plain client language

#### 3b — Voice check (English only)
Scan content for AVOID-list words from `brand-voice.md` (revolutionary, seamless, leverage, synergy, unleash, supercharge, frictionless, effortless, groundbreaking, game-changing, ecosystem-as-metaphor, "AI-powered" as standalone adjective). Flag matches with location and exact phrase. DO NOT silently rewrite. Shay decides.

For Hebrew variants, flag that the translation needs Shay's review rather than auto-rewriting; the AVOID list is English-only.

#### 3c — Version / status transformation
Apply during conversion (source markdown stays unchanged):
- `**Version**: Draft vN` → `**Version**: Final vN`
- `**Status**: Internal draft …` → `**Status**: Final — ready for client send`
- Any other "Draft" / "Internal draft" headers in the file header AND in §1 Document control. Check for multiple occurrences and verify all replaced before saving.

#### 3d — Body content insertion and typography
- Strip placeholder body content from the template copy
- Insert finalized markdown content, preserving heading structure (H1 → Heading 1, H2 → Heading 2, etc.)
- Preserve tables, lists, hyperlinks
- Apply 11pt vs 12pt body + spacing per Stage 2 (apply directly to paragraphs and runs; do NOT rely on `doc.styles['Normal']` — pandoc-generated docx may omit that style name)
- Do NOT touch header, footer, page setup, logo, colors

#### 3e — Hebrew bidi passes (Hebrew variant only)
Per `working-preferences.md` "Hebrew docx production — bidi handling". Apply to the Hebrew docx only — never to the English version.
- **Pass 1 — Run-level RTL marking**: for every `<w:r>` in `word/document.xml` whose text contains any Hebrew character (U+0590–U+05FF), add `<w:rtl/>` to its `<w:rPr>`. Pure-Latin runs (ITTeam, RAG, LLM, etc.) stay unmarked.
- **Pass 2 — List numbering RTL**: for every `<w:lvl>` in `word/numbering.xml`, add `<w:bidi/>` to its `<w:pPr>`.

Without both passes, mixed-script punctuation drifts and bullets render on the wrong side.

### Stage 4 — Metadata
Set Word document properties per Stage 1. `python-docx` handles Author/Title/Subject via `core_properties`; for `Company`, edit `docProps/app.xml` directly inside the zip — `python-docx` does not expose it.

### Stage 5 — Save and verify
- Save to `03_Deliverables\<type>\<filename>`
- If a file with the same final name exists, do NOT overwrite — bump version (`-final-v2`, `-final-v3`) and flag in plan
- Re-open the saved file (or re-inspect its XML) and verify:
  - Header: logo present, navy bottom border
  - Footer: contact line (centered), confidentiality notice (left, italic, gray), page numbers (right)
  - Body: Calibri 11pt or 12pt (per variant), hyperlinks navy
  - Page size: A4
  - Word metadata fields populated correctly
- For client-facing variant: Calibri 12pt body confirmed, 1.15 line spacing confirmed
- For Hebrew variant: `<w:rtl/>` count in `word/document.xml` > 0 (most Hebrew-bearing runs marked), `<w:bidi/>` count in `word/numbering.xml` > 0
- For both variants if produced: filenames distinct, both files reference each other in their D-entries

### Stage 6 — Decision log entry
- Append a new entry to `Projects\<client>\00_Project-Context\decisions-log.md`
- Next stable ID (D-NNN)
- Entry content:
  - Title: "[Doctype] finalized — [filename]"
  - Decision: "[Doctype] published as branded deliverable on YYYY-MM-DD"
  - Rationale: brief — source draft path, master template version, finalization trigger, audience/language variant
  - References: source markdown path, output docx path, master template path
- If both internal and client-facing variants are produced in one finalization round, log them as two separate D-entries that cross-reference each other.

## After execution — end-of-session summary

Per Global Instructions:
- Files touched (full paths): source read, master read, output written, decisions-log appended
- Decisions made: D-NNN logged (one per variant produced)
- Pending: any items flagged during voice check that Shay deferred; for Hebrew, the translation review
- For Command Center: any follow-up tasks (e.g., "send to client", "request client review by [date]")

## Technical execution notes

Validated pipeline:

1. Source markdown → `pandoc --reference-doc=ITTeam_Template.docx -f markdown+pipe_tables -t docx -o output.docx`
2. `python-docx` restyle: 11pt or 12pt + line spacing + paragraph spacing per variant. Apply directly to each paragraph and run; do NOT rely on `doc.styles['Normal']` — pandoc may omit that style name.
3. Set core properties via `python-docx` (`Author`, `Title`, `Subject`). For `Company`, edit `docProps/app.xml` directly inside the zip — `python-docx` does not expose it.
4. Hebrew variant only: run the two `lxml`-based bidi passes on `word/document.xml` and `word/numbering.xml`.
5. Verify `word/header1.xml`, `word/footer1.xml`, `word/media/*` survived intact; verify the body has at least one Heading 1 style; verify metadata.

When opening the .docx programmatically:
- Treat the .docx as a ZIP archive containing XML
- Replace content in `word/document.xml` body, preserving section properties, page setup, and header/footer references
- Do NOT touch `word/header1.xml`, `word/footer1.xml`, or files in `word/media/`
- Validate the resulting .docx after saving (does it open in Word? Are header/footer intact on every page?)
- If validation fails, do not deliver — report the failure and revert

Practical note on large file writes: if the `Write` or `Edit` tool truncates a large file output (observed at the previous file's byte count), fall back to writing the file via `python3` heredoc in bash. This is reliable for files larger than a few KB.

## Failure modes — what to do

- **Master template missing**: stop. Do not regenerate from scratch. Tell Shay the template needs to be restored before finalization can proceed.
- **Source markdown ambiguous**: stop. Ask which version is final.
- **Voice check finds AVOID words**: flag every match with location. Do NOT silently rewrite. Wait for Shay's instruction (rewrite which words to what, or accept as-is).
- **Output file exists**: do not overwrite. Propose `-v2`, `-v3`, etc., and ask.
- **Project codename is TBD**: use fallback `"<Client> <doctype> YYYY-MM-DD"` for Subject metadata, flag this in the plan, suggest Shay decide the codename before next finalization.
- **Ambiguous internal-vs-client-facing**: stop and ask. Do not assume internal because that's the historical default.
- **Hebrew bidi pass missing or incomplete**: do not deliver. Re-apply Passes 1 and 2 and re-verify counts.
- **Multiple "Draft vN" / "Status" occurrences in source**: enumerate all matches before transformation; verify all replaced before saving.

## What this skill is NOT for

- Producing decks (.pptx) — different format, different template, separate skill (future)
- Producing spreadsheets (.xlsx) — different format, separate workflow
- Producing PDFs — convert from the finalized .docx after Shay approves, not directly
- Editing the master template itself — never. Master template changes are a separate, explicit workflow.
