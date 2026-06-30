---
name: coding-conventions
description: Use when writing or reviewing code, when the user corrects or steers a code style/structure/test choice worth remembering, or when starting code work in a project that has no project-level coding-conventions catalog.
---

# Coding Conventions

A living, per-scope catalog of how code should be written, plus the mechanics for capturing and applying it. This skill ships the **engine** (read-only); the conventions themselves live in two writable places, so they survive plugin updates and stay per-project:

- **Personal / cross-project** → `$CLAUDE_PLUGIN_DATA/conventions/<language>[-<framework>].md` — how you like to write code anywhere (persists across plugin updates).
- **Project** → `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/<area>.md` — how THIS codebase does things (checked into that repo).
- **Shipped examples** (read-only) → `$CLAUDE_PLUGIN_ROOT/skills/coding-conventions/conventions/` — format references only, not your content.

Discover what exists by listing the `conventions/` directory in the personal and project locations; read only the file matching what you're working on. (Resolve the `$CLAUDE_*` variables from the environment, e.g. `echo "$CLAUDE_PLUGIN_DATA"`.)

## Scope: global vs project
A convention is **global** if the sentence still makes sense in a different repo (only language/framework primitives). It's **project** if it names repo-specific types, modules, helpers, or architecture. Test: *"would this make sense in another project?"* Yes → global; mentions repo-specific names → project. One preference can have a global *principle* and a project *instantiation* — split them.

## Entry format (point, don't paste)
Each entry is terse: **rule** (one line) · **why** (one line) · **example →** a live pointer to the canonical exemplar `path/to/File.ext#symbol`, optionally **anchored** with the short commit SHA where it was last confirmed: `path/to/File.ext#symbol @a1b2c3d`. Never paste large code blocks — the repo is the source of truth.

The path is the **live pointer** (always resolves to the current shape at HEAD). The SHA is an **anchor, not a pin**: it records *when* the entry was last verified, so you can detect drift cheaply and recover a renamed/deleted exemplar — it does not freeze the entry to that version.

## Capturing a convention (proactive, at checkpoints)
This is **workflow-independent**: any time code is written, edited, reviewed, or rewritten — whether you wrote it, a subagent did, or the user is reshaping generated code — watch for a reusable pattern worth keeping. When you spot one, propose recording it at the next natural checkpoint (a commit-approval gate, the end of an edit, finishing a review) — not mid-edit:
1. Present it as a **numbered, selectable prompt** — in Claude Code use the `AskUserQuestion` tool so the user can approve with one keystroke (outside Claude Code, list the options as plain numbered text). Show the **proposed entry text** and **proposed scope** (+ one line on why global vs project), with options:
   1. **Record as-is** — save the entry to the proposed scope's catalog.
   2. **Reshape it** — the user describes changes; revise and re-ask.
   3. **Change scope** — flip global↔project, then record.
   4. **Skip** — don't record this one.
   The custom / "Other" reply lets the user type any tweak directly.
2. On *Reshape* or *Change scope*, revise and re-present (same selectable prompt) until the user picks *Record* — or *Skip*.
3. On *Record*, write the entry to the right location:
   - global → `$CLAUDE_PLUGIN_DATA/conventions/<language>[-<framework>].md`
   - project → `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/<area>.md`
   Create the file if new. Record the **anchor** — the current `git rev-parse --short HEAD` — next to the exemplar pointer.

**Bar for entry — capture patterns, not instances.** Record a reusable *rule / structure / approach* that will recur (how tests are organized, how modules are wired, how errors are modeled, a preferred construct for a recurring situation). Do **not** record a one-off local edit. Example: changing one field from a string to an enum is an *instance* → skip it; *"prefer a code enum over a string for an evolving set"* is the *pattern* → capture it. A lone edit earns an entry only when it expresses a general rule. Also skip anything already captured, obvious from the codebase, or enforced by lint.

## Keeping it current (no scanning)
- **Point, don't snapshot** — the path pointer tracks HEAD, so a pattern reshaping needs no edit. The anchor SHA is the last-confirmed commit, not a freeze.
- **Cheap staleness via the anchor** — before applying an entry, check whether its exemplar changed since the anchor: `git diff --quiet <sha> HEAD -- <path>`. Clean (exit 0) → unchanged since last confirmed; trust the entry without re-reading. Dirty → read the exemplar (verify-on-use), and once re-confirmed bump the anchor to the current short HEAD.
- **Recover a rotted pointer** — if `<path>` no longer exists at HEAD, retrieve the old shape with `git show <sha>:<path>` to understand the pattern, then pick a new live exemplar + fresh anchor.
- **Opportunistic update** — if already working in an area and the pattern has outgrown its entry, flag it at the next checkpoint. Never scan the whole repo for drift — the per-entry anchor diff is the only check, and only when you're about to use the entry.

## Bootstrapping a project's catalog
When starting substantive code work (or on `/init`) in a project that has no `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/SKILL.md`, offer to scaffold one: write a project `SKILL.md` carrying these same mechanics (scope test, entry format, capture loop, staleness rules) scoped to that repo, with an empty `conventions/` dir. Keep it **self-contained** — teammates may not have this plugin installed. Conventions captured there become that project's memory and are checked into the repo.

## Delivering conventions to delegated work
Generated code only follows these if they're in context when the code is written. When delegating code generation to another agent or subagent (any orchestration flow), either name the relevant `conventions/*.md` for it to read or inject the applicable entries into its prompt/brief — otherwise the catalog only helps when you write code directly.