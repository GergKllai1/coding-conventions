---
description: Maintain the coding-conventions catalog itself — refresh drifted exemplars, repoint rotted ones, merge dups, drop dead entries. Reviews the conventions, never your code.
---

Maintain the **convention catalog itself** — this reviews the *entries*, not your code. Use the **coding-conventions** skill's mechanics; do not reinvent them.

Work through the personal and project catalogs (`$CLAUDE_PLUGIN_DATA/conventions/` and `$CLAUDE_PROJECT_DIR/.claude/conventions/`). For each entry:

1. **Drift check (objective)** — `git diff --quiet <anchor-sha> HEAD -- <exemplar-path>`.
   - Clean → entry is current, leave it.
   - Dirty → re-read the exemplar; if it still demonstrates the rule, just **bump the anchor** to the current `git rev-parse --short HEAD`; if the rule's shape has genuinely changed, propose a wording update too.
   - Path gone at HEAD → recover the old shape with `git show <sha>:<path>`, then **repoint** to a fresh live exemplar + new anchor.
2. **Better exemplar exists?** — even when the current pointer hasn't drifted, a newer, more-evolved instance of the pattern may now exist elsewhere (the early `RepoFilter` you pointed at, vs. the refined one it grew into). If so, **repoint** to the latest/most-evolved instance + fresh anchor. Use git recency as a signal; keep this light — don't exhaustively scan, just check obvious siblings of the current exemplar.
3. **Hygiene** — flag near-duplicate entries to **merge**, obsolete/dead entries to **drop**, and vague wording to **tighten**. Keep entries terse (rule · why · pointer).

**Pointer-free global principles** (the portable half of a "both" rule) have no exemplar/anchor, so there's nothing to drift-check — **skip steps 1–2 for them** and apply hygiene only. Keep them current *through their linked project instantiation*: when this run re-anchors or reshapes an instantiation whose shape changed enough that the linked global principle now reads wrong, propose a wording update to the global entry too. The concrete project entry is the canary; the global principle follows it. (Whether a global principle is still *best practice* as the language evolves is out of scope here — that's a separate, deliberate review, not drift.)

Present everything as **one batched review** (numbered list; each line = the entry and the proposed change: re-anchor / repoint / merge / drop / reword). Then a single decision: **Save all · Pick a subset · Skip all** (subset = reply with numbers to drop or reshape).

**Commit:** this is catalog maintenance — one logical chore → **one commit for the whole run**, e.g. `Refresh conventions: re-anchor 3, repoint 1, merge 1, drop 1`. Always approval-gated (show the diff summary + proposed message; approve / edit message / skip) — never auto-commit. Keep this commit separate from any code changes.

Pure re-anchoring (exemplar moved but still valid) needs no approval prompt of its own — fold it into the run's single commit. Only wording/merge/drop changes need explicit review, since those alter meaning.
