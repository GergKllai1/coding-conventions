# Coding Conventions (Claude Code plugin)

Teach [Claude Code](https://code.claude.com) your team's coding conventions once, and have it
apply them automatically — without pasting the same style notes into every conversation.

It's language- and framework-agnostic: Kotlin, React, Terraform, Python, anything.

## What it does

- **Captures patterns, not one-off edits.** When you correct a style, structure, or test choice
  worth keeping, it offers to record it at a natural checkpoint. You approve, re-scope, or reshape
  it — nothing is saved behind your back.
- **Two scopes.** *Global* (your personal cross-project style) and *project* (how a specific
  codebase does things). It suggests which; you decide.
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

## Where your conventions live

| Scope | Location |
|-------|----------|
| Personal / cross-project | `$CLAUDE_PLUGIN_DATA/conventions/` (persists across plugin updates) |
| Project (team-shared) | `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/` (checked into the repo) |

## License

[MIT](LICENSE)
