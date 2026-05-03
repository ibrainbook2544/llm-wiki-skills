# llm-wiki-skills

A collection of **skills** that turn any Obsidian vault into an AI-powered, LLM-driven wiki knowledge base.

---

## Overview

`llm-wiki-skills` provides plug-and-play skills that give AI a deep understanding of your Obsidian vault structure and workflows. Inspired by the idea of an **LLM Wiki** (as popularized by Andrej Karpathy), this project enables a structured, self-evolving knowledge system built on top of large language models.

Once installed, AI can ingest documents, build structured knowledge graphs, answer queries with citations, and keep the entire knowledge base healthy — all from natural language commands inside Obsidian.

---

## Skills Included

### 1. `llm-wiki-init`

Bootstraps a brand-new LLM-driven wiki in the current Obsidian vault.

**What it does:**
- Creates the full directory scaffold (`raw/`, `wiki/sources/`, `wiki/concepts/`, `wiki/entities/`, `wiki/outputs/`, `wiki/logs/`, `wiki/conversations/`)
- Writes `CLAUDE.md` — the behavioral specification that governs all future AI activity
- Writes `AGENTS.md` — the agent-facing mirror of the same specification
- Initializes `wiki/index.md` as the master table of contents

**Trigger phrases:**
> "init wiki", "setup wiki", "create wiki structure", "build llm wiki"

---

### 2. `llm-wiki-conversation-logger`

Automatically records complete conversation logs after every AI operation.

**What it does:**
- Captures user messages verbatim, tool call sequences, and full AI replies
- Appends structured entries to `wiki/conversations/YYYY-MM-DD.md`
- Only activates when **Full Conversation Log** is enabled in `CLAUDE.md`

**Guarantees:** no truncation, no paraphrasing, real timestamps (always runs `date`), append-only (never modifies past entries).

---

## Wiki Workflow (after `init`)

Once the wiki is initialized, AI responds to the following **structured commands**:

| Command | Syntax | Action |
|---------|--------|--------|
| `ingest` | `ingest [file path(s)]` | Ingest new raw source material |
| `update` | `update [file path(s)]` | Sync a modified raw source file |
| `query`  | `query <topic>` | Answer comprehensively using the wiki |
| `lint`   | `lint` | Run a health check on the knowledge base |
| `init`   | `init` | Re-initialize the knowledge base |

### Directory Layout

```
vault-root/
├── raw/                  # Raw source materials — read-only for AI
├── wiki/
│   ├── index.md          # Master table of contents
│   ├── sources/          # Study notes (mirrors raw/ structure)
│   ├── concepts/         # One file per key concept
│   ├── entities/         # People, projects, companies, tools, etc.
│   ├── outputs/          # Queries, analyses, comparison tables
│   ├── logs/             # Daily operation logs
│   └── conversations/    # Full conversation records (daily)
├── AGENTS.md
└── CLAUDE.md
```

---

## Installation

1. **Clone this repository** (or copy skills into your vault):

   ```bash
   git clone https://github.com/your-username/llm-wiki-skills.git
   ```

2. **Copy the `skills/` folder** into your AI (Claude / GPT) project directory (typically your vault root or alongside your `.claude/` config).

3. **Open Claude Code / Codex** inside your Obsidian vault and run:

   ```
   init wiki
   ```

   AI will scaffold the directory structure and write the governing `CLAUDE.md`.

---

## Repository Structure

```
llm-wiki-skills/
├── skills/
│   ├── llm-wiki-init/
│   │   ├── SKILL.md                   # Skill definition & workflow steps
│   │   └── references/
│   │       └── CLAUDE-template.md     # Template written as CLAUDE.md on init
│   └── llm-wiki-conversation-logger/
│       └── SKILL.md                   # Skill definition & logging workflow
├── .gitignore
├── .gitattributes
└── README.md
```

---

## Key Design Principles

- **Obsidian-native**: All file operations use Obsidian CLI; wikilinks, frontmatter, and callouts are first-class.
- **AI-governed, human-confirmed**: Every destructive action (create, modify, delete) requires user confirmation before execution.
- **Append-only logs**: Operation logs and conversation logs are strictly append-only — past entries are never modified.
- **`raw/` is sacred**: AI agents will never modify files under `raw/`. Only humans write there.
- **Real timestamps**: All log entries run the `date` command to get accurate timestamps. Hardcoded timestamps are prohibited.
- **Full citation**: Every `query` response cites wiki files using Obsidian wikilink format.

---

## Requirements

- [Obsidian](https://obsidian.md/) with the **Obsidian CLI** plugin installed and operational


---

## License

MIT
