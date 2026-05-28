---
name: claude-it
description: Create a CLAUDE.md in the repo root from a bundled behavioral-guidelines template, localized to the current project. Use when the user says "claude-it", "/claude-it", "add CLAUDE.md", or asks to scaffold project guidelines for Claude.
argument-hint: "Optional: extra project-specific notes to append"
---

Create a `CLAUDE.md` at the **repository root** using the bundled template as the base. Localize it to this project. Do not fetch anything from the network; do not include links to external GitHub repos or other remote URLs in the output.

## Steps

1. **Locate repo root.** Run `git rev-parse --show-toplevel`. If not a git repo, use the current working directory and warn the user.
2. **Check for existing file.** If `CLAUDE.md` already exists at the root:
   - Show the user the existing file.
   - Ask whether to overwrite, append a project-specific section, or abort.
   - Do not silently overwrite.
3. **Read template.** Load the bundled file `CLAUDE.template.md` from this skill's directory. Treat it as the canonical source - do not fetch from any URL.
4. **Localize.** Skim the repo (top-level layout, `README.md`, `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod`, framework configs) to identify:
   - Primary language(s) and frameworks
   - Test command, lint command, build command (if discoverable)
   - Notable conventions visible in the codebase
   Append a short **"Project-Specific Notes"** section at the end of the template with only what you actually verified. Do not invent rules.
5. **Strip remote references.** The output must not contain external GitHub URLs, source-attribution links, or remote fetch instructions. The template itself is clean; keep it that way when adding project notes.
6. **Write** to `<repo-root>/CLAUDE.md`.
7. **Report** the path written and a one-line summary of what project-specific notes were added (or "none added - template only" if nothing was verifiable).

## Arguments

If the user passed arguments, treat them as additional project-specific guidance to include in the "Project-Specific Notes" section verbatim (after your discovered notes).

## Constraints

- No network calls. The template is bundled.
- No emojis unless the user asks.
- Do not modify any file other than the new `CLAUDE.md`.
- Keep the four numbered sections from the template intact and in order.
