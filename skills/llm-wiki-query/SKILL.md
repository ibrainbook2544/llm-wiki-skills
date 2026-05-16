---
name: llm-wiki-query
description: Use this skill when the user runs or implies a `query <topic>` command in an Obsidian LLM wiki. It reads the wiki index first, locates relevant source, concept, entity, and synthesis notes, answers comprehensively with Wiki-link citations, and optionally saves the result to wiki/outputs after user confirmation. Always combine it with llm-wiki-format when saving output notes.
---

# LLM Wiki Query

Use this skill to answer questions from an Obsidian LLM wiki.

## Trigger patterns

Use this skill when the user says things like:

- `query <topic>`
- "answer this from my wiki"
- "summarize what the knowledge base says about ..."
- "search the wiki for ..."
- "based on the wiki, explain ..."

## Core rule

Always read `wiki/index.md` first to locate relevant files.

## Workflow

1. Read vault-local instructions, especially `AGENTS.md` or `CLAUDE.md`.
2. Read `wiki/index.md`.
3. Identify likely relevant files from:
   - `wiki/sources/`
   - `wiki/concepts/`
   - `wiki/entities/`
   - `wiki/synthesis/`
4. Read relevant files in depth.
5. Follow important Wiki-links when they are clearly relevant.
6. Synthesize an answer.
7. Cite evidence using Wiki-links.
8. If the user wants to save the answer, propose an output file path.
9. After confirmation, save to `wiki/outputs/query-result-YYYY-MM-DD.md` or the vault-local equivalent.
10. Use `llm-wiki-format` for saved output notes.

## Answer format

Prefer this structure:

```markdown
## Answer

...

## Evidence

- [[wiki/sources/example]] - Why this file supports the answer.
- [[wiki/concepts/example]] - Why this concept matters.

## Open Questions

- ...
```

Adapt the format when the user asks for a different output style.

## Citation rules

- Use Wiki-links for all internal references.
- Do not cite files that were not read.
- If evidence is weak, say so clearly.
- Distinguish source-backed facts from interpretation.
- Prefer precise citations to relevant source, concept, entity, or synthesis notes.

## Output note requirements

When saving an output note, include:

- YAML frontmatter with `type: output`
- knowledge map
- summary
- key points
- full answer or report body
- Relevant Links block with reasons

## Handling uncertainty

If the wiki does not contain enough evidence, say so directly and recommend additional source material that would improve the answer.
