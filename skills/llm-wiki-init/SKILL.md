---
name: llm-wiki-init
description: Initialize a new LLM-driven wiki knowledge base in the current Obsidian vault. Sets up the full directory structure, creates starter files, and writes the CLAUDE.md and the AGENTS.md specification that governs all future AI behavior in this vault. Use this skill whenever a user wants to set up a new knowledge base, start a wiki from scratch, or says things like "init wiki", "setup wiki", "create wiki structure", "build llm wiki", or opens a blank vault and wants AI-assisted note-taking.
---
# llm-wiki-init - LLM Wiki initialization

This skill sets up a complete LLM-driven wiki in the current working directory (the Obsidian vault root). The goal is to create a consistent structure and `CLAUDE.md` and `AGENTS.md` specification so that AI behavior is predictable and rules-governed from day one.

## Requirement: 

Check whether Obsidian CLI has installed and can work normally. If not, you will be required to install it before continuing.

## Step 1: Check existing state

Check whether `CLAUDE.md`, `AGENTS.md`, `wiki/`, or `raw/` already exist:

- **All absent** → proceed directly
- **Any present** → warn the user:
  > Detected existing wiki structure. Continuing will overwrite `CLAUDE.md`, `AGENTS.md`, `wiki/index.md`. Other existing files will NOT be affected. Continue?
- Wait for confirmation before proceeding.

## Step 2: Create directory structure

- `raw/`
- `wiki/concepts/`
- `wiki/entities/`
- `wiki/sources/`
- `wiki/outputs/`
- `wiki/logs/`

## Step 3: Create wiki/index.md

Use today's date (from `currentDate` in context) for the `date` field.

```markdown
---
date: YYYY-MM-DD
---

# Wiki Index

> All files are maintained by type. Each line is formatted as: `[[wikilink]] - One-sentence summary`

---

## Sources

(Source data summary file)

---

## Concepts

(Concept file)

---

## Entities

(Entity file: People, Projects, Companies, Tools)

---

## Outputs

(Output file: Overview, Comparison Table, Analysis)

---
```

## Step 4: Copy AGENTS.md from template

Use shell commands to copy the full template ffile `references/AGENTS-template.md` (in this skill's directory) into `AGENTS.md`. This file is what governs all future AI behavior - it defines workflows, naming conventions, and rules. 

## Step 5: Create CLAUDE.md

```
@AGENTS.md
```
