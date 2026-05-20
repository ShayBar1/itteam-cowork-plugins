# ITTeam Cowork Plugins

Internal Cowork plugin marketplace for ITTeam.

## What's in here

One plugin: **itteam** — encodes ITTeam's working conventions for Claude Cowork.

### Skills

- **`finalize-branded-docx`** — produce a final branded ITTeam .docx from an approved markdown draft. Plans before executing.
- **`new-project-setup`** — scaffold a new client project folder with the standard ITTeam 6-folder structure and seed the three `00_Project-Context\` files. Plans before executing.
- **`meeting-summary-from-notes`** — convert raw meeting notes or transcripts into a structured ITTeam meeting summary. Acts and reports.
- **`log-decision`** — append a decision entry to a project's `decisions-log.md` with the next stable ID. Acts and reports.

### Slash commands

- **`/finalize`** — invoke `finalize-branded-docx` explicitly, optionally with a source markdown path.
- **`/log`** — invoke `log-decision` explicitly, optionally with a decision statement. Only for decisions already made — refuses hedged or future-tense statements.statement.

## Installation

In Claude Desktop → Cowork tab → Customize (left sidebar) → **+** → **Add marketplace from GitHub** → enter `<your-username>/itteam-cowork-plugins`.

Then browse the marketplace and install the `itteam` plugin.

## Design conventions

- Plan-then-execute is mandatory for high-stakes skills (finalization, project setup).
- Stable IDs everywhere: `D-NNN` for decisions, `A-NN` for assumptions, `R-NNN` for requirements, `RSK-NN` for risks, `Q-NN` for open questions.
- Voice rules enforced from `00_Context\brand-voice.md` — see the AVOID-list in any skill that produces written output.
- Brand identity (logo, colors, footer) sourced from `00_Context\brand-identity.md` and `01_Templates\Brand\ITTeam_Template.docx`. Skills never regenerate branded chrome.
- Tasks live in Shay's Command Center. Skills surface candidates at end of session; they do not maintain task state.

## Repo structure
itteam-cowork-plugins/                   ← marketplace root
├── .claude-plugin/
│   └── marketplace.json
├── itteam/                              ← the plugin
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── commands/
│   │   ├── finalize.md
│   │   └── log-decision.md
│   └── skills/
│       ├── finalize-branded-docx/
│       ├── new-project-setup/
│       ├── meeting-summary-from-notes/
│       └── log-decision/
├── README.md
└── .gitignore

## Updating

After pushing changes to this repo, in Cowork: Customize → marketplace → refresh. Updates propagate to every machine running this plugin.

## License

Proprietary — ITTeam internal use.