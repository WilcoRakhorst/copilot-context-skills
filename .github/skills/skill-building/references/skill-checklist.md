# Skill Validation Checklist

Run this checklist before publishing a new or updated skill.
A skill must score **✅ Pass on every item**.

> **Starting point:** Use the `skill-building` SKILL.md as your blueprint, then validate with this checklist.

---

## 1. Structure & naming

| # | Check | Pass? |
|:--|:------|:------|
| 1.1 | **Folder match:** Folder name exactly matches the `name` field in YAML frontmatter (kebab-case) | ☐ |
| 1.2 | **File name:** Main file is named exactly `SKILL.md` (uppercase) | ☐ |
| 1.3 | **Reference names:** Files in `references/` have descriptive names (e.g. `api-specs.md`, not `specs.md`) | ☐ |
| 1.4 | **Scripts:** Scripts in `scripts/` include a shebang (e.g. `#!/usr/bin/env python3`) or encoding declaration | ☐ |

---

## 2. YAML frontmatter (Level 1)

| # | Check | Pass? |
|:--|:------|:------|
| 2.1 | **Description = WHAT + WHEN:** Contains both what the skill does and specific trigger phrases | ☐ |
| 2.2 | **Trigger phrases are specific:** No everyday words like `"check"` or `"help"` on their own — use compound terms | ☐ |
| 2.3 | **Character limit:** Description is under 1024 characters and contains no `<` or `>` tags | ☐ |
| 2.4 | **Metadata complete:** `version` and `author` are filled in | ☐ |

---

## 3. Knowledge quality (Level 2)

| # | Check | Pass? |
|:--|:------|:------|
| 3.1 | **Body under 300 lines:** Detail content lives in `references/` | ☐ |
| 3.2 | **Actionable text:** No vague instructions — every sentence describes a concrete action | ☐ |
| 3.3 | **No duplication (DRY):** Content is not described in two places | ☐ |
| 3.4 | **References linked:** Every file in `references/` is mentioned by name at least once in `SKILL.md` | ☐ |

---

## 4. Behavior

| # | Check | Pass? |
|:--|:------|:------|
| 4.1 | **Hard constraints:** At least 3 explicit "never do this" rules | ☐ |
| 4.2 | **No hardcoded secrets:** No credentials, tokens, or internal URLs in any skill file | ☐ |
| 4.3 | **Cross-skill links:** If this skill chains with others, those are named explicitly | ☐ |

---

## 5. Test

| # | Test | Pass? |
|:--|:-----|:------|
| 5.1 | **Trigger test:** Skill loads reliably when using the chosen trigger phrases | ☐ |
| 5.2 | **False positive test:** Skill stays inactive for similar queries outside its scope | ☐ |
| 5.3 | **Functional test:** Skill has successfully completed its intended workflow end-to-end | ☐ |

---

## Common failure modes

| Symptom | Likely cause | Fix |
|:--------|:-------------|:----|
| Skill never invokes | Trigger phrases too generic or too few | Add 5+ specific compound phrases to `description` |
| Skill invokes for the wrong queries | Description overlaps with another skill | Make trigger phrases more specific; add "not for: X" guidance |
| Output quality drops mid-session | Skill body too long, reloaded repeatedly | Move detail to `references/`, keep body under 300 lines |
| Agent ignores rules from the skill | Rules are buried in prose | Use tables and bullet lists; put critical rules near the top |
| Agent uses stale information | Hardcoded facts in skill body | Replace with `"check [source-file] for current values"` |
