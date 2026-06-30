# Coding Conventions (Claude Code plugin)

An on-demand, per-scope catalog of coding conventions for [Claude Code](https://code.claude.com).
It ships the *engine* — the mechanics for capturing reusable patterns and applying them — while
the conventions themselves live in writable, per-scope locations so they survive plugin updates
and stay project-specific.

It is language- and framework-agnostic: use it for Kotlin, React, Terraform, Python, anything.

## What it does

- **Captures patterns, not one-off edits.** When you steer a code style/structure/test choice
  worth keeping, it proposes recording it at a natural checkpoint; you approve, re-scope, or
  reshape it.
- **Two scopes.** *Global* (your cross-project style) and *project* (how a specific codebase does
  things). It suggests which, you decide.
- **Bootstraps each repo.** Starting work in a project with no catalog, it offers to scaffold a
  self-contained project-local one (checked into that repo, so teammates get it).
- **Stays lean.** Entries are terse (rule · why · pointer to a canonical exemplar) — no pasted
  code. It loads on demand, so it doesn't bloat every conversation.

## Where conventions are stored

| Scope | Location | Notes |
|-------|----------|-------|
| Shipped examples (read-only) | `$CLAUDE_PLUGIN_ROOT/skills/coding-conventions/conventions/` | format references only |
| Personal / cross-project | `$CLAUDE_PLUGIN_DATA/conventions/` | persists across plugin updates |
| Project | `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/` | checked into the repo |

## Install

```
/plugin marketplace add gergkllai1/coding-conventions-plugin
/plugin install coding-conventions@gergo-claude-plugins
```

Updates arrive automatically on session start, or run `/plugin marketplace update`.

### Team auto-enable (optional)

Pin it in a project's `.claude/settings.json`:

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

## Layout

```
.claude-plugin/
  plugin.json          # plugin manifest
  marketplace.json     # marketplace manifest (this repo is its own marketplace)
skills/
  coding-conventions/
    SKILL.md           # the engine (mechanics)
    conventions/
      _example.md      # shipped format example (not real conventions)
```

## License

MIT
