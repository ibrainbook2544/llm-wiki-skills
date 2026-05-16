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
- Creates the full directory scaffold (`asset/`, `raw/`, `wiki/sources/`, `wiki/concepts/`, `wiki/entities/`, `wiki/outputs/`, `logs/`)
- Writes `CLAUDE.md` — the behavioral specification that governs all future AI activity
- Writes `AGENTS.md` — the agent-facing mirror of the same specification
- Initializes `wiki/index.md` as the master table of contents

**Trigger phrases:**
> "init wiki", "setup wiki", "create wiki structure", "build llm wiki"

---

### 2. `llm-wiki-ingest`

Processes and imports raw source material into the wiki structure.

**What it does:**
- Ingests raw source files from the `raw/` directory
- Converts non-Markdown files to Markdown when supported
- Extracts, preserves, or links attachments according to vault rules
- Creates or updates notes under `wiki/sources/` that mirror the `raw/` structure
- Coordinates updates to index, concepts, entities, and synthesis files

**Trigger phrases:**
> "ingest", "ingest file.docx", "import these raw files", "turn this source material into wiki notes", "process the unprocessed files"

**Works best with:** `llm-wiki-format`

---

### 3. `llm-wiki-format`

Maintains consistency, linkability, and maintainability across all wiki files.

**What it does:**
- Defines and enforces required frontmatter structure (type, origin, summary, tags, sources)
- Establishes Wiki-link conventions for internal cross-references
- Specifies the `Relevant Links` block structure
- Validates `index.md` organization
- Ensures proper source-reference formatting
- Manages attachment-link conventions

**Trigger phrases:**
> Used in combination with other skills (ingest, query, lint) or when directly editing wiki notes

**Allowed frontmatter types:**
- `source`, `entity`, `concept`, `synthesis`, `output`

**Allowed origin values:**
- `self-written`, `agent-compiled`

---

### 4. `llm-wiki-lint`

Performs comprehensive health checks on the wiki knowledge base.

**What it does:**
- Identifies dead Wiki-links and validates references
- Detects orphaned files with no incoming backlinks
- Finds mentioned concepts without dedicated concept files
- Finds mentioned entities without dedicated entity files
- Detects outdated synthesis notes relative to newer sources
- Checks for factual contradictions between files
- Validates frontmatter completeness and correctness
- Verifies source link integrity and format
- Ensures all files have `Relevant Links` blocks
- Validates `wiki/index.md` entries (missing, stale, duplicated, or unsorted)

**Trigger phrases:**
> "lint", "check the wiki health", "find dead links", "check for orphan files", "validate frontmatter"

**Scope:** Only lints files under `wiki/` (excludes `raw/` and other directories)

---

### 5. `llm-wiki-query`

Answers questions by searching and synthesizing information from the wiki.

**What it does:**
- Reads `wiki/index.md` to locate relevant files
- Searches across `wiki/sources/`, `wiki/concepts/`, `wiki/entities/`, and `wiki/synthesis/`
- Synthesizes comprehensive answers with Wiki-link citations
- Optionally saves results to `wiki/outputs/` for future reference
- Follows relevant wikilinks to provide complete context

**Trigger phrases:**
> "query <topic>", "answer this from my wiki", "summarize what the knowledge base says about...", "search the wiki for...", "based on the wiki, explain..."

**Output format:**
- All answers include structured evidence section
- Every claim is cited using Obsidian wikilink format
- Results can be saved as query output notes

**Works best with:** `llm-wiki-format` (when saving output notes)

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
├── asset/                # Static assets (images, attachments, etc.)
├── raw/                  # Raw source materials — read-only for AI
├── wiki/
│   ├── index.md          # Master table of contents
│   ├── sources/          # Study notes (mirrors raw/ structure)
│   ├── concepts/         # One file per key concept
│   ├── entities/         # People, projects, companies, tools, etc.
│   ├── synthesis/        # Composite analyses combining multiple sources
│   └── outputs/          # Queries, analyses, comparison tables
├── logs/                 # Daily operation logs
├── AGENTS.md
└── CLAUDE.md
```

---

## Conversation Logging (Optional)

The `logs/` directory stores lint reports generated by the `lint` command. For full **conversation logging** — capturing every user message, tool call, and AI reply verbatim — install the companion script:

**[script-conversation-logger](https://github.com/ibrainbook2544/script-conversation-logger)**

It automatically appends complete conversation records to `logs/YYYY-MM-DD.md` after each session, with real timestamps and no truncation.

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
│   │   ├── SKILL.md                   # Initialize new wiki structure
│   │   └── references/
│   │       └── AGENTS-template.md     # Template written as AGENTS.md on init
│   ├── llm-wiki-ingest/
│   │   └── SKILL.md                   # Import raw source material
│   ├── llm-wiki-format/
│   │   └── SKILL.md                   # Format and consistency rules
│   ├── llm-wiki-lint/
│   │   └── SKILL.md                   # Health check and validation
│   └── llm-wiki-query/
│       └── SKILL.md                   # Query and synthesis
├── .gitignore
├── .gitattributes
└── README.md
```

---

## Key Design Principles

- **Obsidian-native**: All file operations use Obsidian CLI; wikilinks, frontmatter, and callouts are first-class.
- **AI-governed, human-confirmed**: Every destructive action (create, modify, delete) requires user confirmation before execution.
- **Append-only logs**: Operation logs are strictly append-only — past entries are never modified.
- **`raw/` is sacred**: AI agents will never modify files under `raw/`. Only humans write there.
- **Real timestamps**: All log entries run the `date` command to get accurate timestamps. Hardcoded timestamps are prohibited.
- **Full citation**: Every `query` response cites wiki files using Obsidian wikilink format.

---

## Requirements

- [Obsidian](https://obsidian.md/) with the **Obsidian CLI** plugin installed and operational


---

## License

MIT
