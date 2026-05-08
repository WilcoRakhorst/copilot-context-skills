# Copilot Context Skills

**Reusable GitHub Copilot agent skills for sustainable, high-quality sessions.**

This repository contains skills that tackle a problem most developers hit but rarely think about: **context degradation**. The longer an agent session runs, the more the context window fills up — and the more quietly the output quality drops.

These skills teach Copilot how to work efficiently, how to communicate transparently before running terminal commands, and how to build reliable skills of its own.

---

## Skills included

### `token-hygiene`
Context management for GitHub Copilot agent sessions.

Covers the five root causes of token loss, a pre-task checklist the agent can consult before any complex job, and VS Code workspace settings that prevent silent re-indexing of generated output folders.

**Use when:** a session is slowing down, the agent is repeating questions, context is filling up, or you're starting a long multi-step task.

---

### `skill-building`
A structured guide for building high-quality Copilot skills.

Covers YAML frontmatter best practices (the `description` field is your trigger), the three-level progressive disclosure pattern (SKILL.md → references/ → assets/), and a validation checklist to run before publishing a skill.

**Use when:** you want to create a new skill, improve an existing one, or understand why a skill isn't being invoked reliably.

---

## Installation

Clone directly into your workspace:

```bash
git clone https://github.com/WilcoRakhorst/copilot-context-skills.git .github/skills/context
```

Or as a Git submodule (stays updatable):

```bash
git submodule add https://github.com/WilcoRakhorst/copilot-context-skills.git .github/skills/context
```

Then open VS Code with Copilot in agent mode. The skills are auto-discovered.

---

## How skills work

A skill is a Markdown file with YAML frontmatter in `.github/skills/skill-name/SKILL.md`. Copilot reads the `description` field to decide when to invoke the skill. The body contains the structured knowledge it applies.

```
.github/skills/your-skill/
  SKILL.md          # Required: YAML frontmatter + knowledge body
  references/       # Optional: detailed docs loaded on demand
  assets/           # Optional: templates, examples
```

The `description` field is the most important part. Include specific trigger phrases — the more precise, the more reliably Copilot activates the skill:

```yaml
---
name: your-skill
description: "What this skill does and when to use it. Triggers: 'specific phrase',
  'another trigger', 'exact term the user would say'."
---
```

---

## What's inside the references/

Each skill loads lightweight knowledge in its main `SKILL.md` and defers detailed content to `references/` files that are only loaded when needed:

| Skill | Reference files |
|:------|:----------------|
| `token-hygiene` | `terminal-protocol.md` — required transparency rules for terminal calls |
| | `terminal-risk-classification.md` — 🟢/🟡/🔴/⛔ risk matrix per command type |
| `skill-building` | `skill-checklist.md` — validation checklist before publishing a skill |

---

## Related

If you're looking for SEO, GEO, content creation, and WordPress publishing skills, see:
[helder-ai-marketing-skills](https://github.com/WilcoRakhorst/helder-ai-marketing-skills)

---

## License

CC BY-NC 4.0 — free to use, adapt, and learn from.

Built by [Wilco Rakhorst](https://helder-ai.nl) — exploring how AI makes websites and small organizations work smarter.

⭐ If you find it useful, star the repo. PRs welcome.
