---
name: local-review
description: Use when the user asks for a code review of uncommitted local changes (staged or unstaged) before commit. Runs a multi-agent review for bugs, security (OWASP), CLAUDE.md compliance, type safety, and simplification, audits every finding with independent subagents, reports all confirmed issues, then fixes them. Review and audit agents are read-only; any experiments run in a throwaway copy under a temp folder.
allowed-tools: Bash(git diff:*), Bash(git status:*), Bash(git log:*), Bash(git blame:*), Bash(git show:*), Bash(git ls-files:*), Bash(git worktree:*), Bash(mktemp:*), Bash(rsync:*)
---

Provide a code review for uncommitted local changes (both staged and unstaged).

**Optional Focus**: If the user's request includes a focus area or extra context (e.g., "focus on auth logic", "check error handling in the new API", "I refactored the payment flow"), treat it as the primary focus for the review. The focus might be:
- A specific area to focus on (e.g., "focus on the payment flow")
- Context about what changed (e.g., "I refactored the auth module")
- Specific concerns to check (e.g., "make sure the error handling is correct")
- A method or file to prioritize (e.g., "check the handleSubmit function")

When a focus is provided, all review agents should:
1. Prioritize issues related to the focus area
2. Provide more detailed analysis of the focused area
3. Still check for critical issues elsewhere, but weight the focus area higher

**Read-only rule (applies to every review and audit agent)**: Agents must never modify the user's working tree, index, branches, or stash — no edits, no `git add`/`checkout`/`reset`/`stash`/`commit`, no running formatters or fixers in place. If an agent wants to verify a suspected issue by running code, tests, or a quick patch, it must do so in a throwaway copy:

```bash
tmp=$(mktemp -d)
git worktree add --detach "$tmp" HEAD            # clean checkout of HEAD, new detached worktree
git diff HEAD --binary | git -C "$tmp" apply     # replay staged + unstaged changes
git ls-files --others --exclude-standard -z \
  | rsync -a --from0 --files-from=- ./ "$tmp"/   # copy untracked (non-ignored) files
# ... experiment inside "$tmp" only ...
git worktree remove --force "$tmp"               # clean up when done
```

A plain `git clone` into a temp dir is also fine, but remember it will not include uncommitted changes unless they are replayed as above. Always clean up the temp copy afterwards.

**Context rule (applies to every review and audit agent)**: Review the change as a whole, not as isolated lines. Before flagging anything, understand what the change is trying to do and how it fits the surrounding system — callers, related modules, existing conventions, and recent history (`git log`, `git blame`). Judge each finding against that intent: a line that looks wrong in isolation may be correct for the goal, and a line that looks fine may break the bigger picture.

Treat the context brief you are given as a starting point, not as truth. Every claim in it that was inferred (by an agent or from the diff) must be checked against the actual code before you rely on it; if the brief is wrong or incomplete, say so in your output and review against what the code really does. The only exception is **decisions the user made explicitly** (listed separately in the brief): accept those as settled — do not re-argue or flag them — but still report a concrete bug in how they were implemented.

Agents use whatever model the session defaults to; do not pin a specific model.

To do this, follow these steps precisely:

1. Use an agent to check the current state of the working directory:
   - Run `git status` to see what files have changes
   - Run `git diff` for unstaged changes and `git diff --staged` for staged changes
   - If there are no changes, do not proceed and inform the user
   - Read enough surrounding code and recent history (`git log`, `git blame`) to understand why the change exists
   - Return a **context brief** with these sections:
     - **Intent**: what the change is trying to accomplish
     - **Bigger picture**: how it fits the surrounding system — affected callers, related modules, conventions it touches
     - **Changed files**: what changed in each file and the nature of the change
     - **Explicit user decisions**: only things the user actually stated in this conversation (e.g. "I'm intentionally dropping v1 support"), quoted or closely paraphrased. Never put inferences here.
     - **Assumptions / open questions**: anything inferred that reviewers should verify
   - If a focus was provided, note which files/changes are most relevant to that focus

2. Use another agent to find any relevant CLAUDE.md files: the root CLAUDE.md file (if one exists), as well as any CLAUDE.md files in the directories containing modified files

3. Then, launch 5 parallel agents to independently code review the changes. Each agent should read the full file context when needed. **Include the read-only rule and the context rule above verbatim in each agent's prompt, plus the full context brief from step 1.** Before sending, check the brief yourself against the diff and the conversation: correct anything wrong, and make sure the "Explicit user decisions" section holds only what the user actually said. **If a focus was provided, include it in each agent's prompt so they prioritize that area.** Each agent should return a list of issues, and for each issue: file and line, what is wrong, a concrete scenario showing how it breaks (or why it matters), a suggested fix, whether it was verified in a temp copy, and a severity of `critical` (must fix before commit — real bug, security hole, data loss) or `warning` (real problem, lower impact). Report every issue you believe is real — do not self-filter by severity; a separate audit step verifies each finding:
   a. Agent #1: Audit the changes to make sure they comply with any CLAUDE.md guidelines found. Note that CLAUDE.md is guidance for Claude as it writes code, so not all instructions will be applicable during code review.
   b. Agent #2: Understand and confirm the goal of the changes against the code, then read the file changes, then scan for bugs, logic errors, and edge cases. Focus on significant bugs, avoid nitpicks. Check for: null/undefined issues, off-by-one errors, race conditions, resource leaks, error handling gaps.
   c. Agent #3: Scan for security vulnerabilities (OWASP top 10): injection flaws, XSS, auth bypass, sensitive data exposure, insecure dependencies, etc.
   d. Agent #4: Check for TypeScript type safety issues, performance problems, and code quality concerns that would fail code review.
   e. Agent #5 (Code Simplifier): You are an expert code simplification specialist focused on enhancing code clarity, consistency, and maintainability while preserving exact functionality. Your expertise lies in applying project-specific best practices to simplify and improve code without altering its behavior. You prioritize readable, explicit code over overly compact solutions.

      Analyze the changed code and flag issues related to:

      1. **Preserve Functionality**: Flag any simplification that would change what the code does - only how it does it matters. All original features, outputs, and behaviors must remain intact.

      2. **Apply Project Standards**: Flag violations of coding standards from CLAUDE.md including:
         - Use ES modules with proper import sorting and extensions
         - Prefer `function` keyword over arrow functions
         - Use explicit return type annotations for top-level functions
         - Follow proper React component patterns with explicit Props types
         - Use proper error handling patterns (avoid try/catch when possible)
         - Maintain consistent naming conventions

      3. **Enhance Clarity**: Flag code that could be simplified by:
         - Reducing unnecessary complexity and nesting
         - Eliminating redundant code and abstractions
         - Improving readability through clear variable and function names
         - Consolidating related logic
         - Removing unnecessary comments that describe obvious code
         - IMPORTANT: Flag nested ternary operators - prefer switch statements or if/else chains for multiple conditions
         - Choose clarity over brevity - explicit code is often better than overly compact code

      4. **Maintain Balance**: Do NOT flag issues that would lead to over-simplification:
         - Avoid reducing code clarity or maintainability
         - Avoid creating overly clever solutions that are hard to understand
         - Avoid combining too many concerns into single functions or components
         - Avoid removing helpful abstractions that improve code organization
         - Avoid prioritizing "fewer lines" over readability (e.g., nested ternaries, dense one-liners)
         - Avoid making the code harder to debug or extend

      Return a list of code clarity and maintainability issues found in the changed code.

4. Deduplicate: merge findings that describe the same problem at the same location (keep the clearest description and the higher severity). Do not drop anything else here.

5. Audit every finding. Launch parallel audit agents — one per finding, or one per file when a file has several findings. Each audit agent gets the finding(s), the context brief, the CLAUDE.md files from step 2, and the read-only and context rules verbatim. Its job is to independently try to prove or disprove the finding:
   - Read the relevant code and callers; check the claimed failure scenario actually happens. Reproduce it in a temp copy when that is practical.
   - Return a verdict per finding: `CONFIRMED` (real issue introduced by this change), `REFUTED` (not a real issue — give the concrete reason), or `NEEDS-INPUT` (real, but the right fix depends on a decision only the user can make — say what the decision is).
   - Correct the severity, location, or suggested fix if the original was wrong.
   - Refute only for reasons in the list below. Severity and "seems minor" are never a reason to refute.

   Reasons to refute a finding:
   - Pre-existing issue, not introduced by the current changes
   - The claimed failure scenario cannot actually happen (show why)
   - Behavior change that is intentional per the verified intent
   - Contradicts a decision the user explicitly stated
   - Explicitly silenced by a lint-ignore or similar comment with a stated reason

   If any agent reported that the context brief was wrong, give the corrected understanding to the audit agents.

6. Report every `CONFIRMED` and `NEEDS-INPUT` finding to the user using the output format below — no severity cutoff. Also list `REFUTED` findings in one line each with the refutation reason, so the user can overrule an audit.

7. Fix all `CONFIRMED` issues (Critical, Warning, and Simplification) in the user's working tree. This step is done by you, not by the read-only agents:
   - Apply the audited fix for each issue; keep each fix minimal and in the style of the surrounding code.
   - Do not fix `NEEDS-INPUT` items — leave them for the user.
   - Do not stage, commit, or stash anything.
   - After fixing, run the project's relevant tests/typecheck/lint if they exist, and report the results faithfully.
   - Finish with a short list of what was changed per issue (file:line → fix) and any issue you could not fix and why.

Notes:

- Review and audit agents never build, test, or typecheck in the user's working directory; they do it only inside a temp copy per the read-only rule
- Make a todo list first
- For each issue, include:
  - File path and line number
  - Brief description of the problem
  - Why it matters
  - Suggested fix (with code snippet if helpful)
- If no issues are confirmed, confirm the code looks good for commit

Output format:

---

### Local Code Review

**Focus**: <user's focus, if provided, otherwise omit this line>

Found N confirmed issues in uncommitted changes (M findings refuted by audit):

**Critical** (N issues)

1. **file.ts:42** - <brief description>

   <explanation and suggested fix>

**Warning** (N issues)

1. **file.ts:15** - <brief description>

   <explanation and suggested fix>

**Simplification** (N issues)

1. **file.ts:28** - <brief description>

   <explanation and suggested simplification>

**Needs your input** (N issues)

1. **file.ts:60** - <brief description>

   <what decision is needed and the options>

**Refuted by audit** (M findings)

- **file.ts:77** - <finding> — refuted: <reason>

---

Then, after step 7:

---

### Fixes Applied

- **file.ts:42** - <what was changed>
- ...

Not fixed: <NEEDS-INPUT items or anything that could not be fixed, with reason>

Checks: <tests/typecheck/lint run and their results, or "none available">

---

Or, if no issues:

---

### Local Code Review

**Focus**: <user's focus, if provided, otherwise omit this line>

No confirmed issues. Code looks good for commit.

Checked for: bugs, security vulnerabilities, CLAUDE.md compliance, type safety, code clarity.

---
