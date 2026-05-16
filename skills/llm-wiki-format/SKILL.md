---
name: llm-wiki-format
description: Use this skill whenever creating, updating, validating, or formatting files in an Obsidian LLM wiki. It defines the required frontmatter, Wiki-link conventions, Relevant Links block, index.md structure, source references, and attachment-link formatting. Use it together with ingest, query, lint, or any direct wiki note editing task in an LLM wiki.
---

# LLM Wiki Format

Use this skill to keep generated or modified Obsidian LLM wiki notes consistent, linkable, and maintainable.

## Core principles

- Use Obsidian-compatible Markdown.
- Prefer Wiki-links for internal references.
- Preserve existing frontmatter fields unless the task explicitly requires changes.
- Append new source references instead of replacing existing ones.
- Do not modify raw source files unless the user explicitly requests it or the vault-local rules allow a specific conversion step.
- When operating inside an Obsidian vault, follow the vault-local `AGENTS.md` or `CLAUDE.md` rules first.

## Wiki file frontmatter

Every wiki file except `wiki/index.md` should begin with YAML frontmatter:

```yaml
---
type: source
origin: agent-compiled
summary: One-sentence summary of this file
tags: [tag1, tag2]
sources:
  - "[[raw/example.md]]"
---
```

Allowed `type` values:

- `source`
- `entity`
- `concept`
- `synthesis`
- `output`

Allowed `origin` values:

- `self-written`
- `agent-compiled`

## Source-link rules

For files in `wiki/sources/`:

- The `sources` field should reference the corresponding file under `raw/`.
- If the original raw file was `.docx`, `.pdf`, or another converted format, reference the generated `.md` version, not the binary original.

For files in:

- `wiki/entities/`
- `wiki/concepts/`
- `wiki/synthesis/`
- `wiki/outputs/`

The `sources` field should reference relevant files under `wiki/sources/`.

## Required note structure

For `wiki/sources/` files:

```markdown
---
frontmatter
---

# Title

## Knowledge Map

## Summary

## Outline

## Relevant Links

---
- [[wiki/concepts/example]] - Reason this concept is relevant.
---
```

For `wiki/entities/`, `wiki/concepts/`, `wiki/synthesis/`, and `wiki/outputs/` files:

```markdown
---
frontmatter
---

# Title

## Knowledge Map

## Summary

## Key Points

## Relevant Links

---
- [[wiki/sources/example]] - Reason this source is relevant.
---
```

## `wiki/index.md` format

`wiki/index.md` does not require frontmatter.

Use this section order:

```markdown
## Outputs

## Synthesis

## Concepts

## Entities

## Sources
```

Each entry should follow this format:

```markdown
- [[wiki/concepts/example]] - concise summary [keyword1, keyword2]
```

Rules:

- Sort alphabetically within each section.
- Keep summaries short.
- Use no more than 10 keywords per entry.
- Use Wiki-link paths without `.md` unless the vault-local convention requires otherwise.

## Attachment rules

- Use Obsidian embeds for attachments: `![[asset/path/image.png]]`.
- Prefer storing attachments under `asset/`.
- Preserve the raw-source-relative structure when possible.
- Place image embeds near the related text instead of collecting all images at the end.

## Relevant Links block

Every wiki file should end with a `Relevant Links` block.

Use this format:

```markdown
## Relevant Links

---
- [[wiki/entities/example]] - One-sentence reason for the relationship.
- [[wiki/concepts/example]] - One-sentence reason for the relationship.
---
```

Each link should include a short reason. Avoid bare link lists.
