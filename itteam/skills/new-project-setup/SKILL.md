---
name: new-project-setup
description: Use this skill when Shay asks to set up, create, initialize, or scaffold a new ITTeam project. Trigger phrases include "new project for [client]", "set up a project for [client]", "create the [client] project", "scaffold [client]", "initialize [client] workspace". This skill creates the standard 6-folder project structure under D:\Claude-Cowork\Projects\<client>\ and seeds the three 00_Project-Context files (project-brief, decisions-log, glossary) using ITTeam's project template structure. Do NOT use to modify existing projects or to create folders outside the Projects\ tree.
---

# New project setup

## When this skill fires

Activate when Shay indicates a new client engagement is starting and needs a workspace. Trigger phrases: "new project for [client]", "set up a project for [client]", "initialize the [client] project", "scaffold [client]", "create [client] workspace".

Do NOT activate for:
- Existing projects (changes to projects already in `Projects\` are a different workflow)
- Sub-projects or sub-folders within an existing project
- Anything outside `D:\Claude-Cowork\Projects\`

## This skill plans before executing

Per Global Instructions, plan-then-execute. Creating a new project = 14 new folders + 3 new files. That's enough surface area to warrant a plan and approval before writing.

## Required reads before planning

1. `D:\Claude-Cowork\00_Context\working-preferences.md` — naming conventions
2. `D:\Claude-Cowork\Projects\Clalit\` (reference structure — current canonical example)
3. Existing `Projects\` directory listing — to confirm the client folder does NOT already exist (avoid clobbering)

## Preconditions — confirm before planning

- **Client name**: known, confirmed spelling. If ambiguous (e.g., "Bank Hapoalim" vs "BankHapoalim" vs "BHP"), ask Shay how to name the folder.
- **Folder doesn't already exist**: check `D:\Claude-Cowork\Projects\<client>\`. If it exists, stop and ask whether to use the existing folder or pick a different name.
- **Folder name format**: short, PascalCase or simple word, no spaces (e.g., `Clalit`, `BankHapoalim`, `Wolt`). If Shay supplies a name with spaces or special characters, propose a clean version and confirm.

## Plan structure

### Stage 1 — Confirm inputs
- Client folder name (final, confirmed)
- Full path: `D:\Claude-Cowork\Projects\<ClientName>\`
- Whether to seed the three context files immediately, or just create the folder structure (default: seed all three)
- Whether to log a D-001 entry immediately for "project initialized" (default: yes, matches Clalit precedent)

### Stage 2 — Folder creation
List every folder to be created:
- `Projects\<ClientName>\`
- `Projects\<ClientName>\00_Project-Context\`
- `Projects\<ClientName>\01_Inputs\`
- `Projects\<ClientName>\01_Inputs\meetings\`
- `Projects\<ClientName>\01_Inputs\client-docs\`
- `Projects\<ClientName>\01_Inputs\research\`
- `Projects\<ClientName>\02_Working\`
- `Projects\<ClientName>\03_Deliverables\`
- `Projects\<ClientName>\03_Deliverables\PRD\`
- `Projects\<ClientName>\03_Deliverables\SOW\`
- `Projects\<ClientName>\03_Deliverables\Proposals\`
- `Projects\<ClientName>\03_Deliverables\Decks\`
- `Projects\<ClientName>\03_Deliverables\Spreadsheets\`
- `Projects\<ClientName>\04_Knowledge-Base\`
- `Projects\<ClientName>\05_Meetings\`

### Stage 3 — File seeding (in 00_Project-Context)

**`project-brief.md`**: 13 numbered sections per the structure validated for Clalit. All TBD-fillable sections marked `TBD — to be filled at kickoff`. Section 1 metadata pre-filled:
- Client: <ClientName>
- Project codename: TBD — to be filled at kickoff
- Status: active
- Start date: today's date (YYYY-MM-DD)
- Last updated: today's date
- Owner: Shay Bar

Two seed assumptions:
- A-01 — Project status defaulted to "active" upon workspace creation. Falsification trigger: client cancels before kickoff.
- A-02 — Folder structure follows ITTeam's standard 6-folder template. Falsification trigger: project needs deviate (research-heavy, technical-heavy, etc.).

**`decisions-log.md`**: standard structure — title, "Source of record" line, "How to use" section, repeating block template. Seed entry D-001:
- Title: "Project workspace and context files initialized"
- Date: today
- Status: active
- Decided by: Shay Bar
- Decision: "Initialize project workspace for <ClientName> using ITTeam's standard 6-folder project template."
- Rationale: "Standard structure ensures consistency across the 10 parallel ITTeam projects, supports decision/assumption/risk tracking with stable IDs, and aligns with PPT methodology and infosec-aware staging."
- Alternatives considered: flat folder (rejected — doesn't scale); per-deliverable folder tree only (rejected — no place for inputs/context/KB); using <other client>'s structure (rejected — that one is itself derived from this template)
- Implications: project ready for kickoff; brief and glossary to be populated during discovery
- References: `01_Templates\` (if templates exist), the canonical Clalit project as reference

**`glossary.md`**: two sections (Clalit-specific terms / wait — apply the right pattern: section 1 is `<ClientName>-specific terms`, section 2 is `General terms used in this engagement`). Both stubbed `TBD — to be filled as terms surface`. Standard entry format and cross-reference footer.

### Stage 4 — Verification
- List the 14 created folders, confirm all exist
- List the 3 created files, confirm content matches what was specified in the plan
- Confirm no folder or file was created outside `Projects\<ClientName>\`

### Stage 5 — End-of-session summary
- Files and folders created (full paths)
- Decisions made: D-001 logged in the new project
- Pending: every TBD in the brief and glossary
- For Command Center:
  - "Schedule kickoff meeting for <ClientName>"
  - "Decide project codename before any external-facing deliverable"
  - "Populate glossary and brief sections at kickoff"

## Failure modes — what to do

- **Folder already exists**: stop. Do not clobber. Ask: use existing, or pick different name?
- **Client name has spaces or odd characters**: propose a clean version (`Bank-Hapoalim` → `BankHapoalim`), confirm before creating.
- **Cannot write to `D:\Claude-Cowork\Projects\`**: stop. Diagnose (permissions, drive letter, disk full). Report.
- **Partial creation succeeds, then fails midway**: report exactly what was created and what wasn't. Offer to roll back (delete partial) or continue (complete the remaining).

## What this skill is NOT for

- Modifying existing projects (renaming a project folder, adding new subfolders to an existing project, etc.) — different workflow
- Creating templates in `01_Templates\` — that's a separate operation
- Creating folders outside `Projects\` — never. Workspace structure is fixed at the root level.
- Bulk-creating multiple projects at once — this skill is one project per invocation. Bulk-creation = use the rollout PowerShell script (Phase 3, separate from this skill).