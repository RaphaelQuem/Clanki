<div align="center">

# 🃏 Clanki

**A flashcard and quiz coach for [Claude Code](https://claude.com/claude-code), built for Claude Developer Certification prep.**

Clanki turns course notes into true-or-false flashcards, shows the important ones more often,
and quizzes you with exam-style scenario questions as you go.

![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-D97757)
![Version](https://img.shields.io/badge/version-1.3.0-blue)
![Content](https://img.shields.io/badge/content-Claude%20Developer%20Certification-6B4FBB)

</div>

---

## ✨ Features

- **Ready to study out of the box.** Ships with **Claude Developer Certification** course material as its default content.
- **True or false on every card.** Each card is a statement you judge as true or false, so you get feedback on every single card. Clanki then shows the right answer and, for false statements, the corrected version.
- **Cards weighted by importance.** Limits, defaults, decision rules and common pitfalls come up more often than background material.
- **Exam-style quizzes.** Every few cards, Clanki asks a scenario-based multiple-choice question about a card you've already seen, then tells you whether you were right and explains why.
- **Learns from your results.** Cards you get wrong, on true/false or in a quiz, come back more often; cards you know well fade into the background.
- **Organized by subject.** Study everything mixed, or focus on a single subject.
- **Bring your own notes.** Point Clanki at any folder of `.md` / `.txt` files and it builds a deck from them.
- **Always up to date.** Clanki re-reads the notes every session, and `refresh` picks up new files mid-session.

## 📚 Default content: Claude Developer Certification

Clanki includes the **Claude Developer Certification** study material as the subject
**`Certified-Developer`**, with one topic per course module:

| Topic | Covers |
|---|---|
| **Module_1** | API foundations: tokens, the context window, sampling and non-determinism, the Claude model family, reasoning modes, zero/one/multi-shot prompting, SDKs vs. REST, streaming, async and batch processing |
| **Module_2** | Building with the API: prompting craft and structured outputs, extended thinking, tool schemas, streaming without corrupting state, model selection and context budgets, production agents, memory across sessions, images, PDFs and high-volume processing |
| **Module_3** | Claude Code and integrations: the agent loop, permission modes and settings, CLAUDE.md, rules, hooks and subagents, plugins, skills and marketplaces, MCP servers, enterprise connections and authentication |
| **Module_4** | Production readiness: evals and calibrated judges, testing and tracing, tool errors and retries, model selection in production, cost, latency and reliability across agents, securing against untrusted input |
| **Module_5** | Delivery and deployment: reusable assets, contributing to shared infrastructure, requirements and the systems lifecycle, deployment platforms and version pinning, compliance and cost tradeoffs, trust boundaries across deployments |

Study one module with `/clanki:clanki module_3`, or type `topic 3` during a session.

## 🚀 Installation

In Claude Code, run:

```
/plugin marketplace add RaphaelQuem/Clanki
/plugin install clanki@clanki-marketplace
```

Then restart Claude Code (or run `/reload-plugins`). No need to clone this repository.

## 🎯 Usage

| Command | What it does |
|---|---|
| `/clanki:clanki` | Study all subjects |
| `/clanki:clanki certified` | Study one subject (partial names work) |
| `/clanki:clanki module_2` | Study one topic (module) |
| `/clanki:clanki 8` | Ask a quiz question every 8 cards (default: 5) |
| `/clanki:clanki C:\path\to\notes` | Use your own notes folder |

You can also just say **"let's study"** or **"quiz me"**.

### During a session

Each card is a statement with four buttons: **True**, **False**, **Show me a question** and **Stop**.
After you answer, Clanki tells you if you were right and explains the fact, then shows the next card.
Type any of these through **Other**:

| Command | What it does |
|---|---|
| `skip` | Skip this card without answering |
| `again` | Show this card more often |
| `easy` | Undo `again` for this card |
| `subject <name>` / `subject all` | Focus on a subject / back to all subjects |
| `subjects` | List subjects with their card counts |
| `topic <name>` / `topic all` | Focus on a topic (e.g. `topic 3` for Module_3) / back to all |
| `topics` | List the topics of the active subjects |
| `x <number>` | Change how many cards come before each quiz question |
| `folder <path>` / `folder default` | Switch to your own notes / back to the built-in content |
| `refresh` | Re-read the notes without ending the session |
| `reset` | Clear all progress (asks for confirmation first) |

When you stop, Clanki shows your true/false and quiz scores per topic, the cards you missed and the topics to review next.

## 📝 Using your own notes

Create a folder with **one subfolder per subject**, optionally with **topic subfolders** inside,
and paste your content into `.md` or `.txt` files:

```
MyNotes/
├── Certified-Developer/        ← subject
│   ├── Module_1/               ← topic
│   │   └── module_1.md
│   └── Module_2/
│       └── lesson_1.md
└── Another-Subject/
    └── notes.md                ← files directly in a subject go to topic "General"
```

Then run `/clanki:clanki <path-to-MyNotes>`. Clanki remembers the folder for next time.

**Tips**
- Use headings (`#`, `##`). They show on each card, so you know where a fact came from.
- Mark key lines with `!!` or `[HIGH]` to see them more often, or `[LOW]` to see them less.
- Files placed directly in the root folder go into a subject called `General`.

## 💾 Where things are stored

| What | Where |
|---|---|
| Settings (notes folder, quiz frequency) | `~/.claude/clanki/config.json` |
| Progress for the built-in content | `~/.claude/clanki/progress-default.json` |
| Progress for your own notes | `.clanki-progress.json` inside your notes folder |

Plugin updates never touch your settings or progress.

## 🔄 Updating

Open `/plugin` in Claude Code, select the `clanki-marketplace` marketplace and update it to get the latest version and content.

## 🗂️ Repository structure

```
.claude-plugin/marketplace.json        Marketplace definition
plugins/clanki/
├── .claude-plugin/plugin.json         Plugin manifest
└── skills/clanki/
    ├── SKILL.md                       The study coach
    └── content/
        └── Certified-Developer/       Built-in Claude Developer Certification material
            ├── Module_1/ … Module_5/
```

---

<div align="center">
<sub>Clanki is a community study tool and is not affiliated with or endorsed by Anthropic.</sub>
</div>
