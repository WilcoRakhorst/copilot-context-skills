---
title: My GitHub Copilot Sessions Kept Degrading -- So I Built a Skill to Fix It
published: false
description: Context degradation is the silent killer of long Copilot sessions. Here's how I encoded the fix as a reusable agent skill -- including a terminal transparency protocol and a risk classification matrix.
tags: githubcopilot, ai, productivity, webdev
cover_image: https://images.unsplash.com/photo-1555066931-4365d14bab8c?w=1280&auto=format&fit=crop
---

You've probably experienced this. You're 30 minutes into a productive Copilot agent session, things are flowing well -- and then something shifts. The agent asks you to clarify something it already knows. It repeats a question from earlier. The quality of the output quietly drops. You didn't change anything. What happened?

Context ran out.

Every Copilot session has a fixed token budget. Once that budget fills up, the model starts compressing or dropping older context. The result isn't an error message. It's subtle degradation -- and most developers don't even realize it's happening until they're already fighting it.

I built a skill to address this directly. Here's what I learned.

---

## The problem isn't Copilot. It's how we use it.

After tracking a few weeks of agent sessions, I found five recurring patterns that burned through context faster than the actual work:

**1. Re-reads**
When the same instruction exists in multiple files, the agent reloads it every time it's referenced. I had rules duplicated across `copilot-instructions.md` and individual skill files. Every time a skill loaded, it brought along content that was already in context. Fix: cross-cutting rules live in `references/*.md` only. Skills link to them, never copy them.

**2. Reading entire files instead of snippets**
`read_file` without `startLine`/`endLine` loads the full file -- every time. On a workspace with many skill files, this adds up fast. I also had `list_dir` calls on the workspace root, which recursively indexed hundreds of paths I never needed.

**3. Too many tools active**
Every loaded MCP tool takes up space in the system context -- even if it's never called. In agent mode, deferred tools should only be loaded via `tool_search` when genuinely needed.

**4. Working too long in one session**
After 15-20 messages, context compression begins. The agent doesn't tell you this. You notice it when output quality drops or earlier decisions get quietly reversed.

**5. Trial-and-error tool use**
When an agent dives straight into tool calls without a plan, every failed attempt costs double tokens -- once for the call, once for the error. Requiring a written Plan of Action before any tool is invoked eliminates this entirely.

---

## The skill I built

I encoded all of this into a `token-hygiene` skill:

```yaml
---
name: token-hygiene
description: "Guidelines for efficient Copilot context management. Use when a session
  is slowing down, the agent is repeating questions, context is filling up, or when
  starting a complex multi-step task. Triggers: 'token', 'context full', 'session
  cleanup', 'new chat', 'load skill', 'terminal risk', 'plan of action',
  'context efficiency'."
version: "1.0"
---
```

The skill body covers the five causes above, plus a pre-task checklist the agent can consult before starting any complex job:

| Check | Action |
|---|---|
| Is this info already in context? | Don't reload it |
| Which skills do I actually need? | Load only those -- not preemptively |
| Am I reading a large file? | Use `startLine`/`endLine` |
| Searching with a broad term? | Use `grep_search` instead of `semantic_search` |
| Complex multi-step task? | Write a Plan of Action first |
| Been working in this session long? | Consider new chat + session memory |
| Agent repeating a question? | Session is full -- save and restart |

---

## The terminal protocol: a surprise lesson

While building this skill, I discovered something that surprised me. The `run_in_terminal` tool has an `explanation` parameter described as *"This will be shown to the user before the command is run."*

That is not true.

The `explanation` parameter is not visible in the confirmation dialog. It's metadata for the tool system, not a user-facing message. This means if you rely on it to inform users about what a command does, they never see it.

So I added a terminal protocol as a reference file. It requires the agent to write the explanation as a chat message *before* the tool call -- not after, not inside the tool call. The message must cover:

- What the command does in plain language
- Which files are read or modified
- Whether data leaves the system
- Whether the action is reversible

Combined with a risk classification matrix ([LOW] low / [MEDIUM] medium / [HIGH] high / [CRITICAL] critical), this makes every terminal action visible and deliberate.

---

## VS Code indexing: a silent token drain

One thing worth adding to your `.vscode/settings.json` immediately:

```json
"files.exclude": { "out/": true, "Output/": true },
"search.exclude": { "out/": true, "Output/": true }
```

VS Code indexes your entire workspace for search. Generated output folders -- HTML reports, screenshots, log files -- get included in search results and can end up as irrelevant context when the agent runs a workspace search. `.gitignore` doesn't prevent this. Only `files.exclude` and `search.exclude` do.

---

## The skill structure

```
.github/skills/token-hygiene/
  SKILL.md                            # Core skill -- 5 causes, checklist, VS Code tip
  references/
    terminal-protocol.md              # Required transparency rules for terminal calls
    terminal-risk-classification.md   # Risk matrix per command type
```

The references are loaded on demand -- the agent only reads them when it actually needs to run a terminal command. This follows the progressive disclosure pattern I described in my previous post on reusable marketing skills.

---

## The most useful thing: session memory

The fix I use most often isn't even in the skill itself. At the end of any session longer than 15 messages, I ask:

> "Summarize our progress as a short status update."

I paste that into the first message of the new chat. The agent has full context again in one message, without reloading a single file.

`manage_todo_list` also helps here -- if the agent tracks tasks as it works, you never need to scroll back through the conversation to remember where you are.

---

## Why this matters more than you'd expect

Token hygiene sounds like an optimization problem. It's actually a reliability problem.

When context fills quietly, the agent doesn't fail loudly. It makes slightly worse decisions. It misremembers earlier choices. It starts contradicting itself in subtle ways. For short sessions, you might not notice. For longer, multi-step workflows -- publishing pipelines, multi-file refactors, anything with state -- the degradation compounds.

Encoding these rules as a skill means I never have to think about them. The agent consults the checklist before complex tasks and follows the terminal protocol automatically. I just do the work.

The repo: [copilot-context-skills on GitHub](https://github.com/WilcoRakhorst/copilot-context-skills)

The previous post: [I Built Reusable Marketing Skills for GitHub Copilot -- Here's How and Why](https://dev.to/wilcorakhorst/i-built-reusable-marketing-skills-for-github-copilot-heres-how-and-why-4l97)

What patterns have you noticed that drain context faster than they should? I'd like to expand the skill with more real-world examples.
