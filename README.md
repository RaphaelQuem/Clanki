# Clanki: a Claude Code study coach

Flashcards and exam-style quiz questions generated from your own course notes.

## Install

In Claude Code:

```
/plugin marketplace add RaphaelQuem/Clanki
/plugin install clanki@clanki-marketplace
```

Then restart Claude Code (or run `/reload-plugins`).

## Set up your notes

Create a folder (for example `C:\Users\<you>\ClaudeCert`) with one subfolder per subject,
and paste course content into `.md` or `.txt` files:

```
ClaudeCert\
├── API Foundations\module_1.md
└── Prompting\module_2.md
```

Optional markers: `!!` or `[HIGH]` on a line shows it more often, `[LOW]` less often.

## Use

- `/clanki:clanki` — all subjects (or just say "let's study")
- `/clanki:clanki prompting` — one subject
- `/clanki:clanki 8` — a quiz question every 8 cards

The first run asks for your notes folder and saves it to `~/.claude/clanki/config.json`.

Commands during a session (type via "Other"): `subject <name>`, `subject all`, `subjects`,
`refresh`, `reset`, `again`, `easy`, `topic <name>`, `topic all`, `x <number>`, `folder <path>`.

Progress is saved in `.clanki-progress.json` inside your notes folder.
