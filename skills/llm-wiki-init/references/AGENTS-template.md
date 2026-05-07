> This file defines the AI's **behavioral rules, structural constraints, and workflows** within this knowledge base. Update this file after any significant specification change.
> AI Agents read this file and process user instructions according to the rules below.

---

## 1. Core Constraints

### 1.1 Use `obsidian-cli` skill for vault operations.

1. All file operations inside the vault MUST be performed via the `Obsidian CLI` which is Obsidian built-in functions. Only if the CLI cannot accomplish the task should you fall back to `Shell Command` tools. 

2. This restriction does NOT apply to paths outside the vault.

### 1.2 Using `obsidian-markdown` skill for Obsidian-safe report formatting.

### 1.3 **`raw/` is read-only**: 

Unless requested by the user, AI must not modify files located in the `raw/` directory.

### 1.4 **Confirm before executing**: 

For any classification decision, cross-file changes, new file creation, an existing file modification, or deletion, always propose a plan first and wait for user confirmation before taking action.

---

## 2. Directory Structure

```
vault-root/
├── asset/                # Attachments (mirrors raw/ or /wiki structure)
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

### 2.1 Directory Rules

1. `raw/` may contain multiple sub-levels (subdirectories, subfolders) for organization.

2. `wiki/sources/` mirrors the path structure of `raw/` exactly: `raw/FOLDER/FILE.docx` → `wiki/sources/FOLDER/FILE.md`

### 2.2 `asset/` and Attachments

1. All attachments referenced in `raw/` and `wiki/` files are ONLY located in `asset/` directory and mirror `raw/` structure, e.g.:

   - A pic in `raw/FOLDER/FILE.docx` will be saved in `asset/raw/FOLDER/<FILENAME as folder>/1.png`

2. All references to attachments use the `Wiki-link` format.

---

## 3. Structured Command Reference

When a user message **starts with** one of the following keywords, treat it as a **structured command**.

| Structured Command  | Syntax                    | Triggers Workflow                                              |
| -------- | -------------------------- | -------------------------------------------------------------- |
| `ingest` | `ingest [FILE PATH(s)...]` | Ingest new raw source material or update existing one          |
| `query`  | `query <topic>`            | Answer comprehensively based on the Wiki                       |
| `lint`   | `lint`                     | Knowledge base health check                                    |
| `init`   | `init`                     | Initialize the knowledge base                                  |

### 3.1 File Path Rules (applies to `ingest`)

1. Paths are relative to `raw/` and support arbitrary sub-directory depth.

2. Separate multiple files with spaces: `ingest FILE.docx FOLDER/FILE.docx FOLDER/SUBFOLDER/FILE.docx`

3. **No-argument auto-scan** (ONLY `ingest`): 

   - Recursively scan `raw/` directory and all its subdirectories.
   - Find all unprocessed files by comparing with `wiki/sources/`.

---

## 4. Command Workflows

When a command workflow is completed, write a log entry by appending to `wiki/logs/log-YYYY-MM-DD.md` (see 5.1.2).

### 4.1 `ingest` - Ingest

1. According to `Path Rules`, ingest each file.

2. Co-read confirmation: Read the source in full, then confirm core takeaways with the user via Q&A.

3. When file is not markdown format, such as `docx`, `PDF`, or others, automatically to convert into markdown file in the SAME directory: `raw/PATH/FILE.docx` to `raw/PATH/FILE.md`.

4. All attachments MUST follow Directory Rules (see 2.2).

5. Create or update the sources file at path `wiki/sources/<same relative path as raw>/<original filename>.md` (see 5.2.1).

6. Update `index.md`: Append a line in the `Sources` section.

7. Update `concepts/` (see 5.2.2).

8. Update `entities/` (see 5.2.2).

### 4.2 `query` - Query

1. **FIRST**, read `wiki/index.md` to locate relevant files.

2. Read those files in depth.

3. Find the 10 oldest log files in the `wiki/logs/` directory.

4. Synthesize a response, **citing all references using `Wiki-link` format**.

5. Finally, after consulting the user and obtaining confirmation, save the result to the outputs directory: `outputs/query-result-YYYY-MM-DD.md`

### 4.3 `lint` - Health Check

1. **Exclude** the directories and files outside of the wiki system (see 2).

2. Check each item below and output a report (**list suggestions only - do not auto-fix**):

   - Factual contradictions between files
   - Orphaned files (no incoming backlinks, such as modification or deletion)
   - Concepts or entities mentioned but without a dedicated file (maybe modified or deleted)
   - Content that is now outdated due to newer source material
   - Incomplete or invalid frontmatter
   - Source link in frontmatter's sources has wrong format or dead link

3. End the report with recommended directions for additional source material.

4. Save the report to the outputs directory: `outputs/lint-report-YYYY-MM-DD.md`

5. Finally, update the index file.

### 4.4 `init` - Initialize

Launch `llm-wiki-init` skill.

---

## 5. File Specifications

### 5.1 `Operational Files`: index, logs, CLAUDE, AGENTS

#### 5.1.1 `index.md`

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

#### 5.1.2 Log File

1. **Critical**: All log files **must begin with a YAML frontmatter block** (see 5.2.2), otherwise they do not conform to the file specification.

2. **Before writing, always run** `date "+%Y-%m-%d %H:%M:%S"` to obtain the real timestamp. Hardcoding or guessing timestamps is strictly prohibited.

3. **ONLY** Log files record execution summaries of **structured commands**.

4. **Append** an entry to the log file.

- File path: `wiki/logs/log-YYYY-MM-DD.md` (one file per day)
- Format:

  ```markdown
  ---
  type: log
  origin: agent-compiled
  tags: [command1, command2, ...]
  date: YYYY-MM-DD
  ---

  ## [YYYY-MM-DD HH:mm:ss] <command> | <one-line description>
  <bullet summary list>

  ---
  ```

### 5.2 `Wiki Files`

#### 5.2.1 files in `sources/`

1. knowledge map (tree outline)

2. summary

3. Generate a clear, well-structured, and easy-to-read Markdown outline document based on the original material. Keep the content concise and the language precise, while preserving all knowledge points, pictures, images and their logical relationships. Ensure that no key points or details are omitted.

4. Add attachments at appropriate locations based on the context of the original source material.

#### 5.2.2 files in `entities/`, `concepts/`, `outputs/`

1. knowledge map (tree outline)

2. summary

3. key points

#### 5.2.3 Wiki File Frontmatter

1. Every `Wiki Files` must begin with a YAML frontmatter block.

2. Frontmatter format:

```yaml
---
type: source              # source | entity | concept | output
origin: agent-compiled    # self-written | agent-compiled
summary: One-sentence summary of this file   # ONLY for source | entity | concept
tags: [tag1, tag2, ...]
date: 2025-01-15          # Creation or last-updated date, YYYY-MM-DD
sources:                  # Source links
  - "[[raw/FILE.md]]"
  - "[[wiki/sources/FILE]]"
---
```

**Source link** is Obsidian Internal `Wiki-link` referenced a raw source file, relative to `raw/`

  - When updating an existing `Wiki File`, the `sources` field must be appended synchronously if it not exsits. 
  - When the referenced raw source is a `docx` or `PDF` file, the source should be its corresponding `.md` file, not `.docx` or `PDF` file with the same filename.

#### 5.2.4 Wiki File Relevant Links

1. Every `Wiki Files` must end with `Relevant Links` block.

2. `Relevant Links` MUST attach the reasons for their association: 

```markdown
---
- [[wiki/entities/DEMO]] - One-sentence reason
---
```
