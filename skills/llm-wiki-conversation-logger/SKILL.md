---
name: llm-wiki-conversation-logger
description: Automatically logs complete conversation records. After each conversation, saves user messages, tool calls, full replies, and file changes as structured log entries. Use this skill after the user initiates a conversation and the AI has provided a complete response.
---

## Constraints and Rules

### Must Follow
- ✅ **Verbatim** — All conversations must be preserved word-for-word. No truncation, paraphrasing, or summarizing. Ensure that each log entry is a complete, unmodified original.
- ✅ **Append every turn** — Log every conversation turn.
- ✅ **Obtain real timestamps** — Always run the `date` command. Hardcoding timestamps is strictly prohibited.

### Prohibited
- ❌ **Do not delete** any existing content.
- ❌ **Do not modify** any existing conversation entries.
- ❌ **Do not summarize** user messages or AI replies.

## Workflow

### Pre-check

1. **Get timestamp** — Run `date "+%Y-%m-%d %H:%M:%S"` to obtain the exact current time.

### Core Steps

1. Read **full verbatim conversations** from the current context

2. Check or Create the directory `wiki/conversations` if not exist

3. Determine File Path

```
wiki/conversations/YYYY-MM-DD.md
```

- Use the current date to determine the filename.
- Example: `wiki/conversations/2026-04-29.md`

4. Check or Create the File

**If the file does not exist:**
- Create a new file.

5. Append to the Log File

- Leave all preceding conversation entries unchanged.
- Append **full verbatim conversations** entry at the **END** of the file.
- Add a closing separator `---` at the end.

```markdown
---
type: conversation
origin: agent-compiled
summary: One-sentence summary of the log entry (covering all operations in the session)
tags: [tag1, tag2]
date: 2026-04-29
---

## [YYYY-MM-DD HH:mm:ss] Short Title

[Full verbatim conversations]

---
```
