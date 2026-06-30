# Example conventions file (format reference)

This file is a shipped, read-only **format example** — not real conventions. Your
actual entries live in writable locations (see the skill overview): personal ones
in `$CLAUDE_PLUGIN_DATA/conventions/`, project ones in
`$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/`.

Name real files by language/framework/concern, e.g. `kotlin-kotest.md`, `react.md`,
`terraform.md`, `python.md`. Each entry follows this shape:

## <Short pattern name>
- **Rule:** one line stating the convention.
- **Why:** one line on the payoff.
- **Example →** `path/to/Exemplar.ext#symbol @<short-sha>` — a live pointer to the canonical exemplar (not pasted code, so it tracks the current shape), anchored with the short commit SHA where it was last confirmed. The path resolves to HEAD; the SHA is provenance for cheap drift checks (`git diff --quiet <sha> HEAD -- <path>`), not a pin.

---

### Illustrative entry

## Prefer a code enum over a free string for an evolving set
- **Rule:** model a small, growing set of named values as an enum (or code-enum backed by a varchar column), not a bare string.
- **Why:** the type checker enforces the domain; adding a value is a code change with no schema/string-literal hunting.
- **Example →** `src/domain/OwnerType.kt#OwnerType @a1b2c3d` — a canonical enum read through a `from(value)` parser (illustrative path/SHA).
