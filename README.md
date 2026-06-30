# Coding Conventions (Claude Code plugin)

Teach [Claude Code](https://code.claude.com) your team's coding conventions once, and have it
apply them automatically — without pasting the same style notes into every conversation.

It's language- and framework-agnostic: Kotlin, React, Terraform, Python, anything.

## What it does

- **Captures patterns, not one-off edits.** When you correct a choice — *or when Claude adopts an
  existing pattern in your code as a template for new work* — it notes the convention worth keeping.
  Nothing is saved behind your back.
- **Follows the *latest* version of a pattern.** When Claude adopts an existing pattern as a
  template, it steers to the most-evolved instance in your code (`LatestXyzRepoFilter`, not an
  early `XyzRepoFilter`) before copying it — so new code is born in the current shape, and the
  exemplar it records is the good one.
- **Batched review, never nagging.** Candidates are collected quietly and presented once at a
  natural checkpoint as a single list (with clickable links to the real code), so you approve them
  all, pick a subset, or skip — not one interrupting prompt per pattern.
- **Two scopes, and a rule can be both.** *Global* (your personal cross-project style) and
  *project* (how a specific codebase does things). It suggests which; you decide. When a rule is
  general but you captured it with a concrete example (say a Kotest `runTest` style), it offers to
  record **both in one step** — the portable principle globally and the concrete, enforceable
  instantiation in the project — no double prompt, and the two aren't treated as duplicates.
- **Bootstraps each repo.** Start work in a project with no catalog and it offers to scaffold a
  project-local one, checked into the repo so your teammates get it too.
- **Stays lean.** Entries are terse — rule · why · a pointer to a real example in your code — never
  pasted snippets. They load on demand, so they don't bloat every conversation.

## Why this, and not just native memory?

Claude Code already has memory, and there are other "let Claude learn from your sessions" plugins.
The primitives here aren't unprecedented — what's different is the **discipline and ergonomics**:

- **Approval-gated.** Conventions are only recorded when you say so. No silent accumulation of
  guesses about how you like to work.
- **Git-SHA-anchored pointers, not snippets.** Each entry points at a canonical example already in
  your codebase — anchored with the commit where it was last confirmed — instead of duplicating
  code. That anchor powers a cheap drift check (`git diff` against HEAD), so Claude trusts an
  unchanged exemplar without re-reading it and re-verifies only when it actually moved.
- **No duplicate sprawl.** New candidates are checked against what's already captured — overlaps
  are merged into the existing entry and contradictions are surfaced for you to resolve, so the
  catalog doesn't fill up with near-identical rules.
- **Scoped and shareable.** Project conventions live in the repo and travel with it, so a whole
  team shares one source of truth — not one person's private memory file.

If that "deliberate, reviewable, team-shared" flavor is what you want, this is a nicer layer than
rolling it yourself. If you just need ad-hoc personal memory, native memory may be enough.

## Install

```
/plugin marketplace add gergkllai1/coding-conventions-plugin
/plugin install coding-conventions@gergo-claude-plugins
```

Updates arrive automatically on session start, or run `/plugin marketplace update`.

### Team auto-enable (optional)

Pin it in a project's `.claude/settings.json` so everyone on the repo gets it:

```json
{
  "extraKnownMarketplaces": {
    "gergo-claude-plugins": {
      "source": { "source": "github", "repo": "gergkllai1/coding-conventions-plugin" }
    }
  },
  "enabledPlugins": { "coding-conventions@gergo-claude-plugins": true }
}
```

## Commands

All commands are **manual and opt-in** — nothing scans or commits on its own.

- **`/seed`** — one deliberate harvest pass over the current repo: Claude scans for the dominant
  conventions and proposes them in a single batched review (deduped against anything already
  captured). Useful to bootstrap a catalog up front instead of letting it accrete during real work.
  Run it once per repo; ongoing capture handles the rest.
- **`/refresh`** — maintain the catalog *itself*: re-anchor exemplars that have moved, repoint ones
  that were renamed or deleted, merge duplicates, drop dead entries. Reviews the conventions, never
  your code. Cheap; commits the whole tidy-up as one change.
- **`/check`** — enforce the catalog on your *code*: find violations (whoever wrote them), propose
  fixes, and apply them across similar files. **Diff-scoped by default** (only what changed); pass
  `--all` for a full-repo sweep. Runs `/refresh` first so it never enforces stale rules, leans on
  your existing linter for mechanical rules, remembers what you skip, and commits **one change per
  convention** — each one approval-gated.

## Where your conventions live

| Scope | Location |
|-------|----------|
| Personal / cross-project | `$CLAUDE_PLUGIN_DATA/conventions/` (persists across plugin updates) |
| Project (team-shared) | `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/` (checked into the repo) |

## License

[MIT](LICENSE)
