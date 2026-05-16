---
name: llm-wiki-ingest
description: Use this skill when the user runs or implies an `ingest` command in an Obsidian LLM wiki. It handles ingesting raw source files, converting non-Markdown files to Markdown, extracting or preserving attachments, creating or updating wiki/sources notes, and coordinating updates to index, concepts, entities, and synthesis notes. Always combine it with llm-wiki-format.
---

# LLM Wiki Ingest

Use this skill to ingest raw source material into an Obsidian LLM wiki.

## Trigger patterns

Use this skill when the user says things like:

- `ingest`
- `ingest file.docx`
- `ingest folder/file.pdf`
- `ingest folder/subfolder/file.md`
- "import these raw files into the wiki"
- "turn this source material into wiki notes"
- "process the unprocessed files under raw"

## Safety

Before creating or modifying files, propose a plan and wait for user confirmation if the vault-local rules require confirmation.

Do not modify `raw/` files unless explicitly requested, except when the local specification allows generating Markdown conversions beside source files.

## Path handling

- Ingest arguments are relative to `raw/`.
- A no-argument `ingest` means recursively scan `raw/`.
- Compare `raw/` with `wiki/sources/` to find unprocessed files.
- `wiki/sources/` should mirror the directory structure of `raw/`.

Example:

```text
raw/FOLDER/FILE.docx
→ raw/FOLDER/FILE.md
→ wiki/sources/FOLDER/FILE.md
```

## Workflow

1. Read vault-local instructions, especially `AGENTS.md` or `CLAUDE.md`.
2. Resolve the requested raw files.
3. If no file is specified, scan `raw/` recursively.
4. Identify already-ingested and unprocessed files.
5. For non-Markdown files, convert to Markdown when supported and allowed by local rules.
6. Extract, preserve, or link attachments according to vault-local rules.
7. Read the source in full.
8. Summarize core takeaways and ask the user to confirm the interpretation.
9. After confirmation, create or update the corresponding `wiki/sources/` note.
10. Update `wiki/index.md`.
11. Identify concepts, entities, and synthesis notes that should be created or updated.
12. Propose cross-file changes before applying them if local rules require confirmation.
13. Use `llm-wiki-format` for all generated or updated wiki files.

## Source note requirements

Each `wiki/sources/` file should include:

- YAML frontmatter
- knowledge map
- summary
- structured outline
- preserved key details
- attachment embeds at relevant points
- Relevant Links block with reasons

## Co-reading confirmation checkpoint

After reading a source, ask concise confirmation questions before broad wiki updates.

Example:

```markdown
My understanding of this source is:

1. ...
2. ...
3. ...

Is this accurate? Is there anything that should be added, removed, or emphasized before I update the wiki?
```

Do not proceed to broad wiki updates until the user confirms, when confirmation is required by the local rules.

## Concept, entity, and synthesis updates

When ingesting a source, identify:

- entities that deserve dedicated entity notes
- concepts that deserve dedicated concept notes
- synthesis notes that should incorporate the new source
- index entries that should be added or updated

Prefer a small, justified set of updates instead of creating many thin notes.
