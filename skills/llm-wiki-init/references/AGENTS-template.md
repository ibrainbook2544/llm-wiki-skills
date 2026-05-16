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
├── logs/
├── asset/                # Attachments
├── raw/                  # Raw source materials
├── wiki/
│   ├── index.md          # Master table of contents
│   ├── sources/          # Study notes for each raw source
│   ├── entities/         # Entity files
│   ├── concepts/         # Concept files
│   ├── synthesis/        # Synthesis files
│   └── outputs/          # Output filess
├── AGENTS.md             # This file
└── CLAUDE.md             # Same content as this fileThis file
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

### 4.1 `ingest` - Ingest

1. According to `Path Rules`, ingest each file.

2. Co-read confirmation: Read the source in full, then confirm core takeaways with the user via Q&A.

3. When file is not markdown format, such as `docx`, `PDF`, or others, automatically to convert into markdown file in the SAME directory: `raw/PATH/FILE.docx` to `raw/PATH/FILE.md`.

4. All attachments MUST follow Directory Rules (see 2.2).

5. Create or update the sources file at path `wiki/sources/<same relative path as raw>/<original filename>.md` (see 5.2).

6. Update `index.md` (see 5.1).

7. Update `synthesis/` (see 5.3).

8. Update `concepts/` (see 5.3).

9. Update `entities/` (see 5.3).

### 4.2 `query` - Query

1. **FIRST**, read `wiki/index.md` to locate relevant files.

2. Read those files in depth.

3. Synthesize a response, **citing all references using `Wiki-link` format**.

4. Finally, after consulting the user and obtaining confirmation, save the result to the `outputs/` directory: `wiki/outputs/query-result-YYYY-MM-DD.md` (see 5.3).

### 4.3 `lint` - Health Check

1. **Exclude** the directories and files outside of the `wiki/` (see 2).

2. Check each item below and output a report (**list suggestions only - do not auto-fix**):

   - All dead links
   - Factual contradictions between files
   - Orphaned files (no incoming backlinks, such as modification or deletion)
   - Synthesis, Concepts or entities mentioned but without a dedicated file (maybe modified or deleted)
   - Content that is now outdated due to newer source material
   - Incomplete or invalid frontmatter
   - Source link in frontmatter's sources has wrong format or dead link

3. End the report with recommended directions for additional source material.

4. Save the report to the outputs directory: `logs/lint-YYYY-MM-DD.md`

### 4.4 `init` - Initialize

Launch `llm-wiki-init` skill.

---

## 5. `Wiki Files`

### 5.1 `index.md`

This is the master table of contents - every `Wiki Files` wiki file should eventually appear here.

1. `index.md` does NOT require YAML frontmatter.

2. Maintain four sections:

   - Section Order - Now follows spec: Outputs → Synthesis → Concepts → Entities → Sources
   - Alphabetical Sorting - Each section sorted by directory then filename
   - Keyword Format - Added keywords in brackets, sorted by relevance (max 10 per entry)
   - Cleaner Format - Summary line with relevant keywords

```markdown
## Outputs
- [[wiki/outputs/llm-comparison]] - summary [keywords]

## Synthesis
- [[wiki/synthesis/llm-analysis]] - summary [keywords]

## Concepts
- [[wiki/concepts/attention]] - summary [keywords]

## Entities
- [[wiki/entities/openai]] - summary [keywords]

## Sources
- [[wiki/sources/AI/transformer]] - summary [keywords]

```

### 5.2 files in `sources/`

1. knowledge map (tree outline)

2. summary

3. Generate a clear, well-structured, and easy-to-read Markdown outline document based on the original material. Keep the content concise and the language precise, while preserving all knowledge points, pictures, images and their logical relationships. Ensure that no key points or details are omitted.

4. Add attachments at appropriate locations based on the context of the original source material.

### 5.3 files in `entities/`, `concepts/`, `synthesis/`, `outputs/`

1. knowledge map (tree outline)

2. summary

3. key points

## 6. `Wiki Files` Specifications

### 6.1 Wiki File Frontmatter

1. Every `Wiki Files` must begin with a YAML frontmatter block.

2. Frontmatter format:

```yaml
---
type: source              # source | entity | concept | synthesis | output
origin: agent-compiled    # self-written | agent-compiled
summary: One-sentence summary of this file
tags: [tag1, tag2, ...]
sources:                  # Source links
  - "[[raw/FILE.md]]"
  - "[[wiki/sources/FILE]]"
---
```

**Source link** is Obsidian internal `Wiki-link` 

  - When files in `sources/`, the `sources` field referenced a raw source file, relative to `raw/`. All other files, the `sources` field referenced a source file in `wiki/sources/`.
  - When the referenced raw source is a `docx` or `PDF` file, the source should be its corresponding `.md` file, not `.docx` or `PDF` file with the same filename.
  - When updating an existing `Wiki File`, the `sources` field must be appended synchronously if it not exsits. 

### 6.2 Wiki File Relevant Links

1. Every `Wiki Files` must end with `Relevant Links` block.

2. `Relevant Links` MUST attach the reasons for their association: 

```markdown
---
- [[wiki/entities/DEMO]] - One-sentence reason
---
```
