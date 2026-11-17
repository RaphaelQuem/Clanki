<div align="center">

# 🃏 Clanki

**A flashcard and quiz coach for [Claude Code](https://claude.com/claude-code), built for Claude Developer Certification prep.**

Clanki turns course notes into flashcards, shows the important ones more often,
and quizzes you with exam-style scenario questions as you go.

![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-D97757)
![Version](https://img.shields.io/badge/version-1.1.0-blue)
![Content](https://img.shields.io/badge/content-Claude%20Developer%20Certification-6B4FBB)

</div>

---

## ✨ Features

- **Ready to study out of the box.** Ships with **Claude Developer Certification** course material as its default content.
- **Cards weighted by importance.** Limits, defaults, decision rules and common pitfalls come up more often than background material.
- **Exam-style quizzes.** Every few cards, Clanki asks a scenario-based multiple-choice question about a card you've already seen, then tells you whether you were right and explains why.
- **Learns from your results.** Cards you miss or mark `again` come back more often; cards you know well fade into the background.
- **Organized by subject.** Study everything mixed, or focus on a single subject.
- **Bring your own notes.** Point Clanki at any folder of `.md` / `.txt` files and it builds a deck from them.
- **Always up to date.** Clanki re-reads the notes every session, and `refresh` picks up new files mid-session.

## 📚 Default content: Claude Developer Certification

Clanki includes **Claude Developer Certification** study material, split into subjects:

| Subject | Covers |
|---|---|
| **API Foundations** | Tokens, the context window, sampling and non-determinism, the Claude model family, reasoning modes, zero/one/multi-shot prompting, SDKs vs. REST, streaming, async clients, Message Batches API |
| **Prompting Craft** | System prompts, XML tags, few-shot examples, output constraints, diagnosing failed prompts, structured outputs (JSON outputs, strict tool use) and their tradeoffs |

> More modules will be added as the course progresses.

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
| `/clanki:clanki prompting` | Study one subject (partial names work) |
| `/clanki:clanki api, prompting` | Study several subjects |
| `/clanki:clanki 8` | Ask a quiz question every 8 cards (default: 5) |
| `/clanki:clanki C:\path\to\notes` | Use your own notes folder |

You can also just say **"let's study"** or **"quiz me"**.

### During a session

Each card comes with three buttons: **Next**, **Show me a question** and **Stop**.
Type any of these through **Other**:

| Command | What it does |
|---|---|
| `again` | Show this card more often |
| `easy` | Undo `again` for this card |
| `subject <name>` / `subject all` | Focus on a subject / back to all subjects |
| `subjects` | List subjects with their card counts |
| `topic <name>` / `topic all` | Focus on a section or topic / back to all |
| `x <number>` | Change how many cards come before each quiz question |
| `folder <path>` / `folder default` | Switch to your own notes / back to the built-in content |
| `refresh` | Re-read the notes without ending the session |
| `reset` | Clear all progress (asks for confirmation first) |

When you stop, Clanki shows your quiz score per subject, the cards you missed and the topics to review next.

## 📝 Using your own notes

Create a folder with **one subfolder per subject** and paste your content into `.md` or `.txt` files:

```
MyNotes/
├── API Foundations/
│   └── module_1.md
└── Prompting Craft/
    └── module_2.md
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
    └── content/                       Built-in Claude Developer Certification material
        ├── API Foundations/
        └── Prompting Craft/
```

---

<div align="center">
<sub>Clanki is a community study tool and is not affiliated with or endorsed by Anthropic.</sub>
</div>
