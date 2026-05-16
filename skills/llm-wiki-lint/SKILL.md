---
name: llm-wiki-lint
description: Use this skill when the user runs or implies a `lint` command in an Obsidian LLM wiki. It performs a health check over wiki files only, reporting dead links, orphan files, missing concept/entity notes, outdated content, frontmatter problems, and source-link issues. It should report suggestions only and not auto-fix unless the user explicitly confirms a separate fix plan.
---

# LLM Wiki Lint

Use this skill to check the health of an Obsidian LLM wiki.

## Trigger patterns

Use this skill when the user says things like:

- `lint`
- "check the wiki health"
- "find dead links"
- "check for orphan files"
- "look for outdated synthesis notes"
- "validate frontmatter and source links"

## Scope

Only lint files under `wiki/` unless the vault-local instructions say otherwise.

Do not lint unrelated folders.

Do not auto-fix during lint.

## Checks

Check for:

1. Dead Wiki-links.
2. Orphaned files with no incoming backlinks.
3. Mentioned concepts without dedicated concept files.
4. Mentioned entities without dedicated entity files.
5. Synthesis files that appear outdated relative to newer source notes.
6. Factual contradictions between wiki files.
7. Incomplete or invalid frontmatter.
8. Invalid `type` or `origin` values.
9. Missing `sources` fields.
10. Dead or malformed source links.
11. Source links pointing to binary raw files when they should point to converted Markdown.
12. Files missing a `Relevant Links` block.
13. `wiki/index.md` entries that are missing, stale, duplicated, or unsorted.

## Report format

Generate a report like:

```markdown
# Lint Report - YYYY-MM-DD

## Summary

## Dead Links

## Orphaned Files

## Missing Concepts

## Missing Entities

## Outdated or Conflicting Content

## Frontmatter Issues

## Source Link Issues

## Index Issues

## Recommended Additional Source Material

## Suggested Fix Plan
```

Use Wiki-links when referencing files.

## Save location

If the vault-local rules require saving lint reports, save to:

```text
logs/lint-YYYY-MM-DD.md
```

Use the actual current date from the environment or system clock.

## Important

Lint reports are advisory.

Do not modify, delete, rename, or create wiki files during lint unless the user separately confirms a fix plan.
