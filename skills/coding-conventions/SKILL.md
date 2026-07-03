---
name: coding-conventions
description: Use when writing, reviewing, planning, or designing code; when the user corrects or steers a code style/structure/test choice worth remembering; or when starting code work in a repo that has no project catalog.
---

# Coding Conventions

A living, per-scope catalog of how code should be written, plus the mechanics for capturing and applying it. The skill ships the **engine**; conventions live in writable files so they survive plugin updates:

- **Personal / cross-project** → `$CLAUDE_PLUGIN_DATA/conventions/<language>[-<framework>].md` — how you write code anywhere.
- **Project** → `$CLAUDE_PROJECT_DIR/.claude/conventions/<area>.md` — how THIS repo does things (checked in). Plain data only: **never write a project `SKILL.md`** — a checked-in mechanics copy would drift from the plugin.
- **Shipped examples** (read-only) → `$CLAUDE_PLUGIN_ROOT/skills/coding-conventions/conventions/` — format reference, not content.

Discover by listing the `conventions/` dirs (personal + project); read only the file matching what you're working on.

## Conventions only help when they're in context
The one thing that makes or breaks this skill: a convention is followed only if it's loaded when the code is planned or written.
- **Before planning, designing, or writing code in an area, read the applicable `conventions/*.md` and conform** — plans included (propose tests/code in the recorded shapes, not generic ones). A plan written without reading them is born non-conforming.
- The **`CLAUDE.md` pointer** (see Bootstrapping) is the always-on carrier: it keeps the read-and-capture triggers in context even when this skill hasn't auto-loaded, and reaches subagents (which inherit project instructions).
- When **delegating** code or planning to a subagent, name the relevant `conventions/*.md` in its brief — it won't consult the catalog on its own.

## Scope: global vs project
**Global** if the sentence still makes sense in another repo (only language/framework primitives); **project** if it names repo-specific types/modules/architecture. Test: *"would this make sense elsewhere?"*
- **Precedence:** when both bear on the code, **project wins**; global fills the gaps.
- **A rule can be both** (not a duplicate): the portable principle lives in global (pointer-free); its concrete, enforceable instantiation lives in project (`File.kt#symbol @sha`). They're *linked* — never merge or flag them against each other. (Global is naturally pointer-free: a `path#symbol` only resolves in its own repo.)

## Entry format & staleness (point, don't paste)
Each entry: **rule** (one line) · **why** (one line) · **example →** `path/File.ext#symbol`, optionally anchored `@a1b2c3d`. Never paste code — the repo is the source of truth.
- The path is a **live pointer** (tracks HEAD); the SHA is an **anchor, not a pin** (when last confirmed).
- **Point at the latest, most-evolved instance** of a pattern — the shape you'd want copied, not an early iteration the code outgrew (use `git log` recency as a signal).
- **Staleness check before applying:** `git diff --quiet <sha> HEAD -- <path>`. Clean → trust it. Dirty → re-read, re-confirm, bump the anchor. Path gone → recover with `git show <sha>:<path>`, then repoint. Never scan the whole repo — check per-entry, only when about to use an entry.

## Capturing a convention
Two sources, two tempos:
- **User corrects/steers a choice, or says "remember this" — eager.** Highest signal: apply the change, then offer to record it **at the end of that turn** — never defer to a checkpoint that may not come.
- **You notice a reusable pattern while writing/reviewing/adopting code — batched.** Collect silently; present **once, together, at the next checkpoint** (commit gate, end of edit/review) — don't drip one prompt per pattern. (When adopting an existing pattern as a template, imitate and record its *latest* instance.)

**Dedup before presenting:** read the target `conventions/*.md` and prose docs (`CLAUDE.md`/`AGENTS.md`/style guide), then classify each candidate — **New** / **already in prose** (offer only a pointer+anchor upgrade) / **refines existing** (merge, don't duplicate) / **conflicts** (flag; user resolves; never store both).
<!-- ponytail: dedup is the model reading a scoped file, no index. Fine while files stay small; split by concern if one grows huge. -->

**Present as one batched review:** a numbered list (each: rule · why · clickable `path:line` · proposed scope). Fold a "both" candidate into a single item (project instantiation + global principle, default save both). Then one decision — **Save all · Pick a subset · Skip all** — looping on reshape/scope-flip until saved or skipped. On save, write to the right location and record the anchor (`git rev-parse --short HEAD`) beside each pointer.

**Bar for entry:** capture a reusable *rule/structure/approach*, not a one-off edit (changing one field = instance → skip; "prefer an enum for an evolving set" = pattern → capture). Skip what's already captured, trivial, or lint-enforced. Being visible in the codebase is **not** a reason to skip — a pointer records *where* the exemplar is, which is the value.

## Bootstrapping a repo
No `.claude/conventions/` yet? Create it on first save — nothing else (no project `SKILL.md`). Ask **once**: *"no catalog here yet — capture as we go?"*; on yes use the normal batched flow (or `/seed` for a deliberate up-front harvest).

Ensure `CLAUDE.md` has this idempotent managed block (insert/update between the markers; create a minimal `CLAUDE.md` if none) — a pointer, not a mechanics copy, so it never drifts:
```
<!-- coding-conventions:start -->
Coding conventions for this repo live in `.claude/conventions/`. Before planning or writing code, read the relevant `<area>.md` and follow it; project conventions override personal/global. When the user corrects or steers a code style/structure/test choice (or asks to remember one), offer to record it in `.claude/conventions/` at the end of the turn.
<!-- coding-conventions:end -->
```

## Commands (deliberate, opt-in — never automatic)
- **`/seed`** — one-time harvest to populate a fresh catalog.
- **`/refresh`** — maintain the catalog: re-anchor, repoint, merge, drop. Reviews conventions, not code.
- **`/check`** — enforce the catalog on code: find violations, propose fixes, apply across similar files. Diff-scoped (`--all` for full); runs `/refresh` first; remembers skips in `.check-skips.md`; commits one-per-convention.
