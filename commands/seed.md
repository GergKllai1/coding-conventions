---
description: Seed the coding-conventions catalog — one deliberate harvest pass over this repo, then the normal batched review.
---

Do a **one-time catalog harvest** for the current repo. This is the deliberate, opt-in counterpart to the skill's ongoing capture: instead of waiting for patterns to surface during real work, scan the codebase now for the dominant conventions worth recording.

Follow the **coding-conventions** skill's mechanics exactly — do not reinvent them:

1. **Scan for patterns, not instances.** Look across the repo for recurring, reusable decisions: how tests are organized, how modules/services are wired, how errors are modeled, naming and layout conventions, the shape of external clients/services, etc. Pick patterns that recur — skip one-offs, anything trivially obvious, and anything already enforced by lint.
2. **Pick a canonical exemplar + anchor for each** — `path/File.ext#symbol @<short-sha>` (current `git rev-parse --short HEAD`), per the skill's entry format. Point, don't paste. When the pattern appears in several places at different maturity, point at the **latest, most-evolved** instance (use git recency as a signal), not an early iteration the code has outgrown.
3. **Apply the scope test** per candidate (global vs project); a seed of a specific repo is usually project-scoped, but split out any genuinely global principle.
4. **Dedup before presenting** — load the existing catalog *and* scan the repo's prose convention docs (`CLAUDE.md`, `AGENTS.md`, a `CONTRIBUTING`/style guide), then classify each candidate New / Merge / Conflict / Already-stated-in-prose, exactly as the skill's capture flow does. Never propose a duplicate of something already captured or already written down in prose — for prose rules, at most offer a pointer+anchor upgrade.
5. **Present as one batched review** — numbered list with clickable `path:line` per candidate, then a single `Save all · Pick a subset · Skip all` decision. Expect this batch to be larger than a normal checkpoint batch; that is the point of a deliberate seed.
6. On save, write entries to the right locations and create the catalog files if missing (bootstrap a project `SKILL.md` first if the repo has none).

After this pass, ongoing capture takes over normally — `/seed` is meant to be run once per repo (or occasionally when entering a large untracked area), not routinely.
