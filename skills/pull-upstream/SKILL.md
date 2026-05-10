---
name: pull-upstream
description: Apply latest framework changes from upstream vibeacademy/agile-flow into a fork. Wraps scripts/pull-upstream.sh. Safe to run mid-workshop. Respects .agile-flow-overrides for files the fork has intentionally diverged on. (GCP fork only.)
---

# Pull Upstream

Apply the latest framework changes from `vibeacademy/agile-flow` into a downstream fork (e.g., `agile-flow-gcp`). Files listed in `.agile-flow-overrides` are intentionally NOT touched — those represent local customizations the fork wants to preserve.

This is different from `template-sync`:
- **template-sync** pulls from a **release tarball** and opens a **PR** for human review.
- **pull-upstream** pulls from upstream's **main branch** and **commits directly** to the current branch (no PR). Designed for fast in-workshop fork maintenance.

## When to use

- The fork needs to pick up a fix or improvement from upstream `main` immediately
- A workshop is running and you want participants to get the update on next pull
- You're maintaining a GCP/AWS variant of the Agile Flow framework

## Pre-flight checks (do these before invoking the script)

1. **Working tree must be clean.** The script checks this — if dirty, it exits 1.
2. **`.agile-flow-version` must exist** in the repo root. If not, this isn't an Agile Flow fork.
3. **Network access to GitHub** — the script fetches from upstream main.

## Invoke the script

The script is bundled alongside this SKILL.md at `scripts/pull-upstream.sh`.
Run it from the repo root:

```bash
bash scripts/pull-upstream.sh
```

The script will:
1. Add the upstream remote (`https://github.com/vibeacademy/agile-flow.git`) if missing
2. Fetch upstream/main
3. For each path in `syncDirectories` (from `.agile-flow-version`):
   - For each file present on upstream/main, compare blob hashes against local HEAD
   - If a file is in `.agile-flow-overrides`, SKIP it (local intentional divergence)
   - If local matches upstream, leave it alone
   - Otherwise, write the upstream version and `git add` it
4. If anything changed, commit with message
   `chore(upstream): sync framework files from agile-flow@<short-sha>`

## How to interpret the output

Parse the stdout. Terminal states:

| Output | Meaning | Reportable result |
|---|---|---|
| `Already up to date with upstream.` | Nothing to do | "Already up to date" |
| `Applied N upstream change(s) — committed.` | Files updated, commit created on current branch | "Sync committed locally" |
| `ERROR: Working tree has uncommitted changes.` | Pre-flight failed — needs clean tree | Tell user to stash/commit |
| `ERROR: Could not fetch from upstream` | Network or auth issue | Surface to user |

Per-file lines:
- `UPDATED : <path>` — file existed locally, content replaced
- `ADDED   : <path>` — file didn't exist locally, created
- `Skipped (local overrides)` summary at end — list of files NOT touched because of `.agile-flow-overrides`

## Important guardrails

- **Do not modify the script.** It's intentionally minimal and predictable.
- **Commit goes on the current branch.** If you want a PR-based flow instead, use `template-sync`.
- **Push is not automatic.** The script tells the user to `git push origin HEAD` themselves.
- **Files in `.agile-flow-overrides` are sacred.** This is the mechanism for keeping a GCP/AWS fork's local customizations safe across upstream syncs.

## Output Format

End with a Result Block:

```
---

**Result:** Pulled upstream sync
Upstream: agile-flow@a1b2c3d
Files updated: 7
Files preserved by override: 4
Committed to: feature/upstream-sync (HEAD)
Next step: git push origin HEAD
```

Or if no update was needed:

```
---

**Result:** Already up to date with upstream
Files current: 42
Skipped (overrides): 4
```

<!-- Source: Agile Flow GCP (https://github.com/vibeacademy/agile-flow-gcp) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
