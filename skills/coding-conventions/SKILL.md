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

**Choosing the exemplar — point at the latest, most-evolved instance.** A pattern often appears in several places at different maturity (an early `RepoFilter` and the refined one it grew into). Point at the *current best* expression — the shape you'd want copied — not an early iteration the code has since outgrown. When candidates exist, use git recency as a signal (`git log --oneline -- <path>` / compare which was touched most recently) and prefer the richest, most complete version. A convention is only as good as the exemplar it points to.

## Capturing a convention (proactive, batched at checkpoints)
This is **workflow-independent**: any time code is written, edited, reviewed, or rewritten — whether you wrote it, a subagent did, or the user is reshaping generated code — **and also the moment you scan for and adopt an existing pattern as a template** for new code (e.g. modeling a new service on an existing one) — watch for a reusable pattern worth keeping. Deliberately imitating an existing exemplar is itself capture-worthy: a pointer to it saves the next session from re-scanning. **Before you copy what you found, confirm it's the latest, most-evolved instance** — if the codebase has since grown a newer iteration (`XyzRepoFilter` → `LatestXyzRepoFilter`), learn from and imitate *that* one, so the new code is born in the current shape rather than an outdated one — and record that newer instance as the exemplar.

**Batch, never drip.** Collect candidates silently as you work — do **not** interrupt mid-edit with one prompt per pattern. Present them **once, together, at the next natural checkpoint** (a commit-approval gate, the end of an edit, finishing a review).

**Dedup before you present.** Load the existing entries for the target file(s) — they're terse and scoped, so this is cheap — **and also scan the repo's prose convention docs (`CLAUDE.md`, `AGENTS.md`, a `CONTRIBUTING`/style guide) for rules already stated there**. Then classify each candidate:
- **New** → include it.
- **Already stated in prose** (e.g. described in `CLAUDE.md`) → don't propose it as new; at most offer to add a pointer+anchor entry that upgrades the prose rule to a tracked exemplar, and only if that adds value.
- **Refines / overlaps an existing entry** → present it as an *update/merge* to that entry, not a second near-duplicate.
- **Conflicts** with an existing entry (contradicts it) → flag the conflict explicitly and let the user resolve it (replace the old · keep both *only* if they're genuinely different scopes · skip). Never silently store both.

<!-- ponytail: dedup is the model reading the scoped file and judging overlap — no embedding index. Fine for terse, area-scoped files; if one grows huge, split it by concern, don't add a dedup engine. -->

Then present the survivors as a **single batched review**:
1. List them as a numbered markdown list — each line: the **proposed entry** (rule · why · a clickable `path:line` to inspect the exemplar) and its **proposed scope** (+ a few words on why global vs project). Label merges and conflicts as such.
2. Ask **one** decision prompt — in Claude Code use `AskUserQuestion` (outside it, plain numbered text): **1) Save all · 2) Pick a subset · 3) Skip all**. *Pick a subset* = the user replies with the numbers to drop or reshape; the custom / "Other" reply lets them tweak any entry inline.
3. On reshape / scope-flip, revise just those entries and re-present; loop until the user saves or skips.

On save, write each kept entry to the right location:
- global → `$CLAUDE_PLUGIN_DATA/conventions/<language>[-<framework>].md`
- project → `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/conventions/<area>.md`

Create the file if new. Record the **anchor** — the current `git rev-parse --short HEAD` — next to each exemplar pointer. (Store the canonical `path/File.ext#symbol @sha`; the clickable `path:line` is only for the review list.)

**Bar for entry — capture patterns, not instances.** Record a reusable *rule / structure / approach* that will recur (how tests are organized, how modules are wired, how errors are modeled, a preferred construct for a recurring situation). Do **not** record a one-off local edit. Example: changing one field from a string to an enum is an *instance* → skip it; *"prefer a code enum over a string for an evolving set"* is the *pattern* → capture it. A lone edit earns an entry only when it expresses a general rule. Skip anything already captured or enforced by lint. **Being visible in the codebase is *not* a reason to skip** — recording *where* a canonical exemplar lives (a pointer) is not duplicating *what* it says, and is exactly the value (no re-scan next time). Only skip a pattern that's genuinely trivial or self-evident at a glance.

## Keeping it current (no scanning)
- **Point, don't snapshot** — the path pointer tracks HEAD, so a pattern reshaping needs no edit. The anchor SHA is the last-confirmed commit, not a freeze.
- **Cheap staleness via the anchor** — before applying an entry, check whether its exemplar changed since the anchor: `git diff --quiet <sha> HEAD -- <path>`. Clean (exit 0) → unchanged since last confirmed; trust the entry without re-reading. Dirty → read the exemplar (verify-on-use), and once re-confirmed bump the anchor to the current short HEAD.
- **Recover a rotted pointer** — if `<path>` no longer exists at HEAD, retrieve the old shape with `git show <sha>:<path>` to understand the pattern, then pick a new live exemplar + fresh anchor.
- **Opportunistic update** — if already working in an area and the pattern has outgrown its entry, flag it at the next checkpoint. Never scan the whole repo for drift — the per-entry anchor diff is the only check, and only when you're about to use the entry.

## Bootstrapping a project's catalog
When starting substantive code work (or on `/init`) in a project that has no `$CLAUDE_PROJECT_DIR/.claude/skills/coding-conventions/SKILL.md`, offer to scaffold one: write a project `SKILL.md` carrying these same mechanics (scope test, entry format, capture loop, staleness rules) scoped to that repo, with an empty `conventions/` dir. Keep it **self-contained** — teammates may not have this plugin installed. Conventions captured there become that project's memory and are checked into the repo.

A fresh repo starts with an empty catalog, so ask **once** up front — *"no conventions catalog here yet — capture patterns as we go?"* (yes/no). On yes, proceed with the **identical batched flow** as every other session; the only difference is the first few batches run larger. Do **not** do a special up-front harvest sweep by default — entries earned through real work beat ones guessed by a scan. (A user who *wants* a deliberate harvest can run the `/seed` command, which scans the repo once and feeds candidates through this same dedup + batched-review flow.)

## Delivering conventions to delegated work
Generated code only follows these if they're in context when the code is written. When delegating code generation to another agent or subagent (any orchestration flow), either name the relevant `conventions/*.md` for it to read or inject the applicable entries into its prompt/brief — otherwise the catalog only helps when you write code directly.

## Commands (deliberate, opt-in)
Capture is the passive, everyday path. Three explicit commands cover the heavier, on-demand operations — all manual, none ever run automatically:
- **`/seed`** — populate a fresh catalog with a one-time harvest pass over the repo.
- **`/refresh`** — maintain the catalog itself: re-anchor drifted exemplars, repoint rotted ones, merge dups, drop dead entries. Reviews the *conventions*, not your code.
- **`/check`** — enforce the catalog on your *code*: find violations, propose fixes, apply across similar files. Diff-scoped by default (`--all` for a full sweep); runs `/refresh` first. Remembers skipped violations in `.check-skips.md` (re-surfaced only if the file changed since). Commits one-per-convention.