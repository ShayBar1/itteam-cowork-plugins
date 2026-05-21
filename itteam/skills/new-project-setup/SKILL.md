---
name: new-project-setup
description: Use this skill when Shay asks to set up, create, initialize, or scaffold a new ITTeam project. Trigger phrases include "new project for [client]", "set up a project for [client]", "create the [client] project", "scaffold [client]", "initialize [client] workspace". This skill creates the standard 15-folder project structure under D:\Claude-Cowork\Projects\<name>\ following ITTeam's naming conventions (single-engagement clients use Client; multi-engagement clients use Client-Engagement) and seeds the three 00_Project-Context files (project-brief, decisions-log, glossary). Do NOT use to modify existing projects or to create folders outside the Projects\ tree.
---

# New project setup

## When this skill fires

Activate when Shay indicates a new client engagement is starting and needs a workspace. Trigger phrases: "new project for [client]", "set up a project for [client]", "initialize the [client] project", "scaffold [client]", "create [client] workspace".

Do NOT activate for:
- Existing projects (changes to projects already in `Projects\` are a different workflow)
- Sub-projects or sub-folders within an existing project
- Anything outside `D:\Claude-Cowork\Projects\`

## This skill plans before executing

Per Global Instructions, plan-then-execute. Creating a new project = 15 new folders + 3 new files. That's enough surface area to warrant a plan and approval before writing.

## Required reads before planning

1. `D:\Claude-Cowork\00_Context\working-preferences.md` — naming conventions
2. `D:\Claude-Cowork\Projects\Clalit-Maya-BPSDTracker\` — canonical reference structure
3. Existing `Projects\` directory listing — to confirm:
   - The proposed folder does NOT already exist (avoid clobbering)
   - Whether any sibling projects with the same client prefix already exist (triggers multi-engagement naming pattern)

## Naming convention — three cases

ITTeam projects follow `Client-EngagementName` format where multiple engagements share a client. The pattern adapts to the situation:

### Case 1 — Single-engagement client
The client has only one ITTeam engagement (current or planned).
- Folder name: `<ClientName>`
- Examples: `Wolt`, `Strauss`
- Use when: no sibling projects with this client prefix exist in `Projects\`

### Case 2 — Client with multiple engagements
The client has (or will have) more than one distinct engagement with ITTeam.
- Folder name: `<ClientName>-<EngagementName>`
- Examples: `Clalit-Maya-BPSDTracker`, `Clalit-CommandCenter`, `Clalit-Meshi-ConversationalSupport`
- Use when: another project with the same client prefix already exists, OR Shay indicates this is one of multiple planned engagements

### Case 3 — Engagement with internal division/sub-context
The engagement has a specific division, product, or sub-context worth encoding in the name.
- Folder name: `<ClientName>-<Division>-<Function>` or `<ClientName>-<Product>-<Function>`
- Examples: `Clalit-Meshi-ConversationalSupport`, `Clalit-Maya-BPSDTracker`
- Use when: the engagement is associated with a named division (Meshi) or named product (Maya) within the client

### Naming rules across all cases
- Use hyphens between parts (`-`), not spaces, not underscores
- PascalCase or simple words for each part (`BPSDTracker` not `bpsd-tracker`)
- No spaces, no special characters except hyphens
- Keep total length reasonable (under ~40 characters)
- Hebrew names: transliterate to readable English (e.g., מאיה → Maya, מש"י → Meshi)

### Detection logic at plan time
When Shay says "set up a new project for X":
1. If X is a single token with no engagement context (e.g., "new project for Wolt"): assume Case 1, propose `Wolt`. Confirm before creating.
2. If X mentions both client and engagement (e.g., "new project for Clalit, the conversational support thing"): propose Case 2 or 3 format. Confirm.
3. Always check: does any folder in `Projects\` start with the proposed client name? If yes, this is a multi-engagement client — confirm naming with Shay even if Case 1 was initially proposed.

## Preconditions — confirm before planning

- **Folder name format**: confirmed per the naming convention above. If ambiguous, ask Shay before proceeding.
- **Folder doesn't already exist**: check `D:\Claude-Cowork\Projects\<proposed-name>\`. If it exists, stop and ask whether to use the existing folder or pick a different name.
- **Client-prefix check**: list any existing projects starting with the same client prefix. If found, surface them to Shay — this affects naming (Case 2 not Case 1) and may affect content (shared context, related decisions).
- **Hebrew/non-English client names**: propose a transliteration and confirm before proceeding.

## Plan structure

### Stage 1 — Confirm inputs
- Proposed folder name (per naming convention) and which case applies (1, 2, or 3)
- Full path: `D:\Claude-Cowork\Projects\<FolderName>\`
- Any sibling projects with the same client prefix (for awareness, not for action)
- Whether to seed the three context files immediately, or just create the folder structure (default: seed all three)
- Whether to log a D-001 entry immediately for "project initialized" (default: yes, matches established precedent)

### Stage 2 — Folder creation
List every folder to be created:
- `Projects\<FolderName>\`
- `Projects\<FolderName>\00_Project-Context\`
- `Projects\<FolderName>\01_Inputs\`
- `Projects\<FolderName>\01_Inputs\meetings\`
- `Projects\<FolderName>\01_Inputs\client-docs\`
- `Projects\<FolderName>\01_Inputs\research\`
- `Projects\<FolderName>\02_Working\`
- `Projects\<FolderName>\03_Deliverables\`
- `Projects\<FolderName>\03_Deliverables\PRD\`
- `Projects\<FolderName>\03_Deliverables\SOW\`
- `Projects\<FolderName>\03_Deliverables\Proposals\`
- `Projects\<FolderName>\03_Deliverables\Decks\`
- `Projects\<FolderName>\03_Deliverables\Spreadsheets\`
- `Projects\<FolderName>\03_Deliverables\SecurityArtifacts\`
- `Projects\<FolderName>\04_Knowledge-Base\`
- `Projects\<FolderName>\05_Meetings\`

Total: 15 folders (project root + 14 subfolders).

### Folder purposes
- `00_Project-Context\` — brief, decisions log, glossary (project's identity files)
- `01_Inputs\` — raw source materials, organized by type
- `02_Working\` — drafts and WIP (Cowork's sandbox)
- `03_Deliverables\` — finalized outputs, organized by deliverable type
  - `PRD\` — Product Requirements Documents
  - `SOW\` — Statements of Work
  - `Proposals\` — pre-engagement proposals
  - `Decks\` — presentation files
  - `Spreadsheets\` — data tables, financial models, control matrices
  - `SecurityArtifacts\` — security-specific deliverables that don't fit other categories: threat models, security architecture documents, control matrices, security task lists, audit handouts, penetration test reports. Standard for ITTeam engagements given the agency's infosec focus.
- `04_Knowledge-Base\` — synthesized insights, patterns, reusable knowledge from the engagement
- `05_Meetings\` — meeting summaries (named YYYY-MM-DD_topic.md)

### Stage 3 — File seeding (in 00_Project-Context)

**`project-brief.md`**: 13 numbered sections per the canonical structure. All TBD-fillable sections marked `TBD — to be filled at kickoff`. Section 1 metadata pre-filled:
- Client: <ClientName> (the client name without engagement suffix)
- Project codename: TBD — to be filled at kickoff
- Status: active
- Start date: today's date (YYYY-MM-DD)
- Last updated: today's date
- Owner: Shay Bar

Two seed assumptions:
- A-01 — Project status defaulted to "active" upon workspace creation. Falsification trigger: client cancels before kickoff.
- A-02 — Folder structure follows ITTeam's standard 15-folder template. Falsification trigger: project needs deviate (research-heavy, technical-heavy, etc.).

If multi-engagement (Case 2 or 3): add A-03 noting the relationship to sibling projects under the same client prefix, e.g., "Sibling project at Projects\Clalit-Maya-BPSDTracker\ exists; engagements are distinct (separate SOW, scope) but the client is shared."

**`decisions-log.md`**: standard structure — title, "Source of record" line, "How to use" section, repeating block template. Seed entry D-001:
- Title: "Project workspace and context files initialized"
- Date: today
- Status: active
- Decided by: Shay Bar
- Decision: "Initialize project workspace for <FolderName> using ITTeam's standard 15-folder project template."
- Rationale: "Standard structure ensures consistency across the parallel ITTeam projects, supports decision/assumption/risk tracking with stable IDs, and aligns with PPT methodology and infosec-aware staging."
- Alternatives considered: flat folder (rejected — doesn't scale); per-deliverable folder tree only (rejected — no place for inputs/context/KB); copying an existing project's tree wholesale (rejected — would inherit stale content)
- Implications: project ready for kickoff; brief and glossary to be populated during discovery
- References: `Projects\Clalit-Maya-BPSDTracker\` as canonical reference structure

**`glossary.md`**: two sections — section 1 titled `<ClientName>-specific terms`, section 2 titled `General terms used in this engagement`. Both stubbed `TBD — to be filled as terms surface`. Standard entry format and cross-reference footer.

### Stage 4 — Verification
- List the 15 created folders, confirm all exist
- List the 3 created files, confirm content matches what was specified in the plan
- Confirm no folder or file was created outside `Projects\<FolderName>\`

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
- **Naming ambiguous between Case 1 and Case 2**: stop and ask. Surface any sibling projects found.
- **Client name has spaces or odd characters**: propose a clean version (`Bank Hapoalim` → `BankHapoalim`), confirm before creating.
- **Hebrew or non-English name**: propose a transliteration (e.g., מאיה → `Maya`), confirm.
- **Cannot write to `D:\Claude-Cowork\Projects\`**: stop. Diagnose (permissions, drive letter, disk full). Report.
- **Partial creation succeeds, then fails midway**: report exactly what was created and what wasn't. Offer to roll back (delete partial) or continue (complete the remaining).

## What this skill is NOT for

- Modifying existing projects (renaming a project folder, adding new subfolders to an existing project, etc.) — different workflow
- Creating templates in `01_Templates\` — that's a separate operation
- Creating folders outside `Projects\` — never. Workspace structure is fixed at the root level.
- Bulk-creating multiple projects at once — this skill is one project per invocation.
- Migrating content from existing folders into a new project structure — that requires the migration workflow (PowerShell + per-file mapping), not this skill.