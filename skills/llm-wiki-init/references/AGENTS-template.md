> This file defines the AI's **behavioral rules, structural constraints, and workflows** within this knowledge base. Update this file after any significant specification change.
> AI Agents read this file and process user instructions according to the rules below.

---

## 1. Core Constraints

### 1.1 Use `obsidian-cli` skill as much as possible.

1. All file operations inside the vault MUST be performed via the `Obsidian CLI` which is Obsidian built-in functions. Only if the CLI cannot accomplish the task should you fall back to `Read` / `Write` / `Edit` / `Glob` / `Grep` or other tools. 

2. This restriction does NOT apply to paths outside the vault.

### 1.2 **`raw/` is read-only**: 

Unless requested by the user, the AI must not modify files located in the `raw/` directory.

### 1.3 **Confirm before executing**: 

For any classification decision, cross-file changes, new file creation, an existing file modification, or deletion, always propose a plan first and wait for user confirmation before taking action.

---

## 2. Directory Structure

```
vault-root/
├── raw/                  # Raw source materials (read-only)
├── wiki/
│   ├── index.md          # Master table of contents (maintained across all sections)
│   ├── sources/          # Study notes for each raw source (mirrors raw/ structure)
│   ├── concepts/         # Concept files (one file per important concept)
│   ├── entities/         # Entity files (people, projects, companies, tools, etc.)
│   ├── outputs/          # Query outputs (surveys, comparison tables, analyses, etc.)
│   └── logs/             # Operation logs (one file per day, by date)
├── AGENTS.md             # Same content as this file
└── CLAUDE.md             # This file
```

### 2.1 Directory Notes

1. **`raw/`** may contain multiple sub-levels for organization.

2. **`wiki/sources/`** mirrors the path structure of `raw/` exactly EXCEPT FOR all attachments, e.g.:

  > `raw/path/file.docx` → `wiki/sources/path/file.md`

3. All attachments are kept in only `raw/` directory; all other files reference the attachments located there.

---

## 3. Structured Command Reference

When a user message **starts with** one of the following keywords, treat it as a **structured command** and execute immediately - do not interpret it as natural language.

| Structured Command  | Syntax                    | Triggers Workflow                                              |
| -------- | ------------------------- | -------------------------------------------------------------- |
| `ingest` | `ingest [file path(s)...]` | Ingest new raw source material                                |
| `query`  | `query <topic>`            | Answer comprehensively based on the Wiki                      |
| `lint`   | `lint`                     | Knowledge base health check                                   |
| `init`   | `init`                     | Initialize the knowledge base                                 |

### 3.1 Path Rules (applies to `ingest`)

1. Paths are relative to `raw/` and support arbitrary sub-directory depth.

2. Separate multiple files with spaces: `ingest a.docx folder/b.docx folder/subfolder/c.docx`

3. **No-argument auto-scan** (ONLY `ingest`): Recursively scans `raw/**/*`, excluding attachments. Compares against `wiki/sources/` and lists unprocessed files.

---

## 4. Command Workflows

When a command workflow is completed, write a log entry by appending to `wiki/logs/log-YYYY-MM-DD.md`  and run the `date` command first to get the real timestamp.

### 4.1 `ingest` - Ingest

1. According to **Path Rules**, ingest each file.
2. **Co-read confirmation**: Read the source in full, then confirm core takeaways with the user via Q&A.
3. **Create or update the sources file** at path `wiki/sources/<same relative path as raw>/<original filename>.md`
   - Content includes: summary, reference to original, knowledge map (tree outline), key points, attachments, and related links. 
4. **Update `index.md`**: Append a line in the `Sources` section.
5. **Update `concepts/`**: Create or update relevant concept files (include attachments).
6. **Update `entities/`**: Create or update relevant entity files (include attachments).

### 4.2 `query` - Query

1. **FIRST**, read `wiki/index.md` to locate relevant files.
2. Read those files in depth.
3. Synthesize a response, **citing all references using wiki-link format**.

### 4.3 `lint` - Health Check

1. Check each item below and output a report (**list suggestions only - do not auto-fix**):

   - Factual contradictions between files
   - Orphaned files (no incoming backlinks, such as modification or deletion)
   - Concepts or entities mentioned but without a dedicated file (maybe modified or deleted)
   - Content that is now outdated due to newer source material
   - Incomplete or invalid frontmatter
   - Source link in frontmatter's sources has wrong format or dead link

2. End the report with recommended directions for additional source material.
3. Save the report to the outputs directory: `outputs/lint-report-YYYY-MM-DD.md`
4. Finally, update the index file.

### 4.4 `init` - Initialize

Launch skill: `llm-wiki-init`

---

## 5. File Specifications


`Wiki Files`: files in `/sources`, `/entities`, `/concepts`, `/outputs`

`Operational Files`: index, log, CLAUDE, AGENTS


### 5.1 `docx`, `PDF` and their attachments

1. AI automatically to convert: `raw/path/file.docx` to `raw/path/file.md`. After conversion, check if the attachments are broken links. If so, try to fix it.
2. Save attachments in a sibling subdirectory and use the filename (prefix only, excluding the extension) as the directory name. 
  - `raw/path/file/1.png`
3. ALL `Wiki Files` MUST reference the attachments in the `raw/` directory; do not create new attachment directories or files.


### 5.2 Link Format

1. **Internal links**: Obsidian wiki-link format `[[wikilinks]]` (full relative path)
2. **External links**: Standard Markdown `[text](url)`
3. **Do not use backticks for any links, except for UNRESOLED link.**

### 5.3 Wiki File Format

1. Every `Wiki Files` must begin with a YAML frontmatter block.

2. Frontmatter format:

```yaml
---
type: source              # source | entity | concept | output
origin: agent-compiled    # self-written | agent-compiled
summary: One-sentence summary of this file
tags: [tag1, tag2, ...]
date: 2025-01-15          # Creation or last-updated date, YYYY-MM-DD
sources: ["[[wikilinks]]"]   # source links
---
```

**source link** is Obsidian Internal link referenced a raw source file, relative to `raw/`

  - When updating an existing `Wiki File`, the `sources` field must be appended synchronously. 
  - When the referenced raw source is a `docx` or `PDF` file, the source should be its corresponding `.md` file, not a `.docx` or `PDF` file with the same name.

### 5.4 `index.md` Format

This is the master table of contents - every wiki file should eventually appear here.

1. `index.md` does NOT require YAML frontmatter.

2. Maintain four sections; within each section, sort by directory then filename (ascending). Each line follows this format:

```markdown
## Sources
- [[wiki/sources/AI/transformer]] - summary [keywords]

## Concepts
- [[wiki/concepts/attention]] - summary [keywords]


## Entities
- [[wiki/entities/openai]] - summary [keywords]

## Outputs
- [[wiki/outputs/llm-comparison]] - summary [keywords]
```

3. Rules:

- Maximum 10 keywords per entry, **sorted by descending weight/relevance in the content**.
- `Operational Files` excluded from indexing (to suppress future isolation warnings).

### 5.5 Log File Format

1. **⚠️ Critical**: All log files **must begin with a YAML frontmatter block** (see 5.3), otherwise they do not conform to the file specification.

2. **Before writing, always run** `date "+%Y-%m-%d %H:%M:%S"` to obtain the real timestamp. Hardcoding or guessing timestamps is strictly prohibited.

3. **ONLY** Log files record execution summaries of **structured commands**.

4. **Append** an entry to the log file.

- File path: `wiki/logs/log-YYYY-MM-DD.md` (one file per day)
- Format:

  ```markdown
  ---
  type: log
  origin: agent-compiled
  summary: One-sentence summary of all operations performed today
  tags: [command1, command2, ...]
  date: YYYY-MM-DD
  ---

  ## [YYYY-MM-DD HH:mm:ss] <command> | <one-line description>
  <bullet summary list>

  ---
  ```
