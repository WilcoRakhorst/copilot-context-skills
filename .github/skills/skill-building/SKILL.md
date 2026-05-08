---
name: skill-building
description: "Structured guide for building high-quality GitHub Copilot agent skills.
  Use when creating a new skill, improving an existing one, diagnosing why a skill
  isn't being invoked, or reviewing a skill before publishing. Triggers: 'create skill',
  'build skill', 'new SKILL.md', 'improve skill', 'skill not triggering', 'skill
  not loading', 'validate skill', 'publish skill', 'YAML frontmatter', 'description
  field', 'trigger phrases', 'skill structure', 'references folder', 'progressive
  disclosure'."
version: "1.0"
author: "Wilco Rakhorst"
---

You are an expert in GitHub Copilot agent skill design. You help users create skills that are reliably triggered, efficiently loaded, and clearly structured.

---

## The three levels of a skill

Skills follow a progressive disclosure pattern — load only what's needed, when it's needed:

| Level | Location | When loaded |
|:------|:---------|:------------|
| **1 — Trigger** | YAML `description` field | Always — determines if skill is invoked |
| **2 — Knowledge** | `SKILL.md` body | When the skill is relevant to the task |
| **3 — Detail** | `references/*.md` files | Only when the task explicitly requires them |

Keep the `SKILL.md` body under **300 lines**. Everything beyond that belongs in `references/`.

---

## The description field is your trigger

Copilot decides which skill to invoke based on the `description` in YAML frontmatter. This is the single most important part of any skill.

**Rules:**
- State clearly **what the skill does** and **when to use it**
- Include 8–15 specific trigger phrases — the exact terms a user would say
- Avoid generic words like `"check"`, `"help"`, `"analyze"` on their own
- Keep it under 1024 characters
- No `<` or `>` tags

**Example (good):**
```yaml
description: "SEO strategy and on-page optimization for websites. Use when the user
  asks about 'meta description', 'title tag', 'H2 structure', 'keyword in first
  paragraph', 'internal linking', 'SEO audit', 'rank on Google', 'slug', or
  'Yoast SEO'."
```

**Example (too vague):**
```yaml
description: "Helps with SEO and content."
```

---

## Folder structure

```
.github/skills/your-skill-name/
  SKILL.md          # Required: YAML frontmatter + knowledge body
  references/       # Optional: detailed docs, loaded on demand
  assets/           # Optional: templates, examples, images
  scripts/          # Optional: executable helper scripts
```

The folder name must exactly match the `name` field in YAML (kebab-case).

---

## SKILL.md body guidelines

- Start with a one-sentence role definition ("You are a...")
- Use clear H2 headings per topic
- Prefer tables, checklists, and bullet lists over prose paragraphs
- Reference files in `references/` by name — never copy their content inline
- No style rules that a linter could enforce
- No code examples that go stale quickly — link to source files instead

---

## Cross-skill references

Skills that naturally chain together should reference each other explicitly:

```markdown
For keyword research, this skill assumes you've already run `seo-keyword-research`.
For content writing, see `content-creator`.
```

This helps Copilot chain skills in multi-step workflows.

---

## References (loaded on demand)

- [skill-checklist.md](references/skill-checklist.md) — full validation checklist before publishing a skill
