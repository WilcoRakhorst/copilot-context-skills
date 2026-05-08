# Terminal Risk Classification

Use this matrix for every terminal command, script, or installation.
Display the risk level **before** execution.

---

## Levels

| Level | Icon | When it applies |
|:------|:-----|:----------------|
| **Low** | 🟢 | Everything stays local · No files outside the project modified · Fully reversible (reading, verifying, setting a local variable) |
| **Medium** | 🟡 | Writing or overwriting files in the project · Local npm/pip installation · External API call (read-only) · Local database query (read-only) |
| **High** | 🔴 | Data leaves the system · Destructive action (delete, reset, truncate) · Global installation (system-wide) · Git commit or push · External API with write actions · Script that overwrites multiple files at once |
| **Critical** | ⛔ | Production or shared environment · Secrets or credentials involved · Irreversible without a backup · `rm -rf`, `git reset --hard`, `git push --force` |

---

## Behavior per level

| Level | Confirmation required? | Action if declined |
|:------|:-----------------------|:-------------------|
| 🟢 Low | Recommended, not blocking | Explain what was done |
| 🟡 Medium | Yes — show proposal and wait for "Yes" | Stop, offer alternative |
| 🔴 High | Yes — mandatory, no implicit assumption | Stop completely, no fallback without permission |
| ⛔ Critical | Yes — explicitly name the irreversibility | Stop completely, escalate to user |

---

## Chat display format

Use this fixed format above every command:

```
**Risk:** 🟡 Medium
**What this does:** [plain-language explanation]
**Files affected:** [which files are read or modified]
**Does data leave the system?** Yes/No (if yes: where and why)
**Reversible?** Yes/No
```

For installations, add:

```
**What's being installed:** [name + version]
**Source:** [npm / NuGet / pip / etc.]
**Global or local:** [system-wide / this project only]
```

For 🔴 High or ⛔ Critical, always add:

```
⚠️ **What could go wrong:** [concrete risk]
✅ **Safer alternative:** [alternative with lower or no risk]
```

---

## Common command examples

| Command / Action | Level | Notes |
|:-----------------|:------|:------|
| `read_file`, `grep_search`, `list_dir` | 🟢 | Read-only, local |
| `npm install` (local) | 🟡 | Modifies `node_modules/` and `package-lock.json` |
| Writing to a project file | 🟡 | Reversible via version control |
| `npm install -g` | 🔴 | System-wide, harder to undo |
| `git push` | 🔴 | Publicly visible, affects shared repo |
| `git reset --hard` | ⛔ | Irreversible without backup |
| `rm -rf` / `Remove-Item -Recurse -Force` | ⛔ | Irreversible |
| REST API write call (external service) | ⛔ | Permanent side effect on external system |
