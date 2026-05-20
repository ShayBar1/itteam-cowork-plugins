---
description: Finalize a markdown draft as a branded ITTeam .docx deliverable. Invokes the finalize-branded-docx skill. Optional argument: path to the source markdown file.
---

# /finalize

You are being asked to invoke the `finalize-branded-docx` skill.

If the user provided a path or filename after `/finalize`, treat that as the source markdown to finalize. Confirm the path, then proceed with the skill's standard plan-then-execute workflow.

If the user provided no argument, ask which markdown file in the current project's `02_Working\` they want to finalize. List candidates if useful.

Then execute the `finalize-branded-docx` skill in full:
1. Read brand-identity.md, brand-voice.md, working-preferences.md, the source markdown, and the project-brief.md.
2. Verify preconditions (approval status, master template present, codename known).
3. Present the full multi-stage plan.
4. Wait for explicit "go" approval.
5. Execute stages 1-6.
6. Report end-of-session summary.

Do not skip stages. Do not act without approval. This command is a shortcut to invoke the skill, not a shortcut around plan-then-execute discipline.