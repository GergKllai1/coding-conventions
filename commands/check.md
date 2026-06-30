---
description: Enforce the coding-conventions catalog on your code — find violations, propose fixes, apply across similar files. Diff-scoped by default; pass --all for a full-repo sweep.
---

Enforce the **convention catalog on your code** — find code that violates recorded conventions (no matter who wrote it) and fix it. Use the **coding-conventions** skill's mechanics. **Manual only** — this is a deliberate, heavy operation; it never runs automatically.

**Scope:**
- Default: **diff-scoped** — check only what changed (`git diff --name-only` against HEAD, plus staged/unstaged). Cheap, bounded, catches newly introduced violations (including ones written without Claude).
- `--all`: full-repo sweep. Expensive; reserve for initial adoption or an occasional audit.

**Run order:**
1. **Run `/refresh` first** so you never enforce stale rules. (Skip with `--no-refresh`.)
2. Load the relevant conventions for the languages/areas in scope.
3. **Lean on existing lint.** If a convention is mechanically checkable and already covered by a linter, skip it — let lint handle it. Spend effort only on **structural/semantic** conventions a linter can't express.

**Finding & triage:**
4. Scan the in-scope files for violations. Report up front: **how many files have violations, against which conventions**, and let the user choose which to walk through (a subset, or all).
5. Walk each violation: show the offending `path:line`, the convention it breaks, and the **proposed fix**. Options per violation: **Approve · Shape (discuss/adjust) · Skip**.
6. **Skip-memory:** on skip, record it in a skip ledger so the next run doesn't re-nag — `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/.check-skips.md`, one line per skip: `- <path> · <convention name> · <short-sha>`. On later runs, suppress a skipped violation **unless the file changed since that sha** (`git diff --quiet <sha> HEAD -- <path>`) — a changed file is worth re-surfacing.
7. After an approved fix, **offer to apply the same fix to all similar files** in scope (same violation of the same convention), as one batch.

**Commit:** **one commit per convention**, spanning every file that convention touched — e.g. `Apply Either-return convention across 6 services`. Not per-file (noise), not one bulk commit (unrevertable blob). Always approval-gated: show diff summary + proposed message, then approve / edit message / skip; offer to bundle the rest into one commit for anyone who doesn't want per-convention gates. Refresh commit(s) land first, then the per-convention code commits.
