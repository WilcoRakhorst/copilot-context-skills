# Terminal & Installation Protocol

This protocol governs all terminal interactions. The agent loads this file only when it actually intends to run a terminal command.

> **Order is mandatory:** The full explanation MUST appear as the first text block in the response. The tool call for the terminal comes only after a blank line. A tool call before the explanation is a protocol violation.

> **Edge case:** An installation command (e.g. `npm install`) falls under **both** sections — show both the command explanation (section 1) and the installation explanation (section 2).

---

## 1. Required explanation before every command

Present the following as chat text before execution:

- **Purpose:** What does this command do in plain language?
- **Impact:** Which project files are read or modified? Focus on project files. Refer to folders like `node_modules/` as a whole — never list individual libraries or transitive dependencies.
- **Data:** Does any data leave the system? (If yes: what and where?)
- **Recovery:** Is this action reversible? — If **No**: ask for explicit confirmation before running and offer a safer alternative. If **Yes**: state the specific recovery command.
- **Risk:** 🟢 Low / 🟡 Medium / 🔴 High / ⛔ Critical — per [terminal-risk-classification.md](terminal-risk-classification.md)

**Example of a correct chat explanation:**
> 🟡 **Medium risk**
> - **Purpose:** Installs the npm packages listed in `package.json`.
> - **Impact:** Writes to `node_modules/`. Modifies `package-lock.json`.
> - **Data:** Downloads packages from npmjs.com.
> - **Recovery:** Yes — remove installed packages with `npm uninstall [package-name]`.

---

## 2. Required explanation before installations (npm, pip, NuGet, etc.)

- **Tool:** Name + version
- **Why:** Why is this installation necessary for the current step?
- **Source:** Official registry (e.g. npmjs.com, nuget.org)
- **Scope:** Global (system-wide) or local (this project only)
- **Recovery:** Is the installation reversible? State the recovery command.

---

## 3. Guidelines

- Summarize scripts longer than 3 lines as a numbered list.
- Warn explicitly about side effects (⚠️) and offer a safer alternative.
- Never force an installation or external request — the user has the final say.
- For risky actions (🔴 / ⛔), always offer a local or data-safe alternative.
- **Output limit:** For installations, never list the full set of dependent packages — name only the main tool being installed.
- **Sequence:** Always present the full explanation BEFORE calling `run_in_terminal`.

> ⚠️ **Note: the `explanation` parameter is misleading**
>
> The `run_in_terminal` tool has an `explanation` parameter described as *"This will be shown to the user before the command is run."* This is **not accurate** — this parameter is **not visible** in the user's confirmation dialog.
>
> **The chat explanation is the ONLY way to inform the user.** Always write the explanation as chat text first, then call the tool.
