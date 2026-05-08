---
name: token-hygiene
description: "Guidelines for efficient GitHub Copilot context management. Use when
  a session is slowing down, the agent is repeating questions, context is filling
  up, or when starting a complex multi-step task. Triggers: 'token', 'context full',
  'session cleanup', 'new chat', 'load skill', 'terminal risk', 'terminal protocol',
  'plan of action', 'skill checklist', 'validate skill', 'context efficiency',
  'token waste', 'context window', 'session degradation'."
version: "1.0"
author: "Wilco Rakhorst"
---

## The five root causes of context loss

### 1. Re-reads
The same instruction in multiple files means the agent reloads it every time it's referenced. Cross-cutting content belongs exclusively in `references/*.md`. Skills link to them — they never copy them.

**User tip:** Use the **edit button** on a mistaken prompt instead of sending a correction as a new message. This keeps the error out of context entirely.

### 2. Reading entire files instead of snippets
- Always use `startLine` + `endLine` with `read_file`
- Use `grep_search` to locate the right lines before calling `read_file`
- Never use `list_dir` recursively on the workspace root — deep structures (e.g. `node_modules`, `.git`) index thousands of paths and fill context fast
- Use `semantic_search` sparingly — broad queries load a lot of irrelevant content; use `grep_search` for exact terms
- **Never write back an entire file after a change** — use only the changed section or a diff format

### 3. Too many tools active
- Load deferred tools only via `tool_search` when actually needed
- Use the `Explore` subagent for read-only research — keeps the main context clean
- Keep `copilot-instructions.md` short; defer detail to `references/`

### 4. Working too long in one session
- Start a new chat after **15–20 messages** or after completing a distinct task
- At the end of a complex task, ask: *"Summarize our progress as a short status update."* Paste that as the first message in the new chat
- Save intermediate results to session memory (`/memories/session/`) or to files
- Use `manage_todo_list` to track progress — so you never need to scroll back

### 5. Trial-and-error tool use
Require the agent to write a **Plan of Action** before any tools are invoked on complex tasks — which steps, which tools, in which order. Use `manage_todo_list` when the plan has multiple steps.

---

## Pre-task checklist

| Check | Action |
|:------|:-------|
| Is this info already in context? | Don't reload it (→ cause 1) |
| Which skills do I actually need? | Load only those — not preemptively (→ cause 3) |
| Am I reading a large file? | Use `startLine`/`endLine` (→ cause 2) |
| Searching with a broad term? | Use `grep_search` instead of `semantic_search` (→ cause 2) |
| Complex task with multiple tools? | Write a Plan of Action first (→ cause 5) |
| Been in this session a long time? | Consider new chat + session memory (→ cause 4) |
| Agent repeating a question? | Session is full — save state and restart (→ cause 4) |
| Output getting too long? | Ask for "diff format" or only the changed section (→ cause 2) |
| Search results slow or irrelevant? | Check `files.exclude` and `search.exclude` |

---

## VS Code: prevent silent re-indexing

Add to `.vscode/settings.json`:

```json
"files.exclude": { "out/": true, "Output/": true },
"search.exclude": { "out/": true, "Output/": true }
```

VS Code indexes your entire workspace — including generated output folders with HTML reports, screenshots, and logs. These end up as irrelevant context when the agent runs a search. `.gitignore` does not prevent this. Only `files.exclude` and `search.exclude` do.

---

## References (loaded on demand)

- [terminal-protocol.md](references/terminal-protocol.md) — required transparency rules before running terminal commands
- [terminal-risk-classification.md](references/terminal-risk-classification.md) — 🟢/🟡/🔴/⛔ risk matrix per command type
