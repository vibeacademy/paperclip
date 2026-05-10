---
name: template-sync
description: Sync framework files from the latest upstream Agile Flow release. Wraps scripts/template-sync.sh — downloads the latest GitHub release tarball, copies files in syncDirectories, and opens a PR for human review. Use when forked repos need to pull in new framework versions.
---

# Template Sync

Pull the latest Agile Flow framework files from the upstream release and open a PR. User-owned content is never touched — only files in `syncDirectories` from `.agile-flow-version` are updated.

## When to use

- A new Agile Flow release is published and the fork wants to adopt it
- A scheduled/manual upgrade run (the `upgrade` skill calls into this same script)

## Pre-flight checks (do these before invoking the script)

1. **Working tree must be clean.** Run `git status --porcelain`. If it returns
   anything, STOP and tell the user to stash or commit first.
2. **GitHub CLI auth.** Run `gh auth status`. If unauthenticated, STOP — the
   script will try to open a PR and fail.
3. **`.agile-flow-version` must exist** in the repo root. If not, this isn't
   an Agile Flow fork.

## Invoke the script

The script is bundled alongside this SKILL.md at `scripts/template-sync.sh`.
Run it from the repo root:

```bash
bash scripts/template-sync.sh
```

(If the script isn't on disk in the agent's working directory, copy it from
this skill's `scripts/` folder first.)

## How to interpret the output

Parse the stdout. The script prints exactly one of these terminal states:

| Output line | Meaning | Reportable result |
|---|---|---|
| `No updates available. Local version (...) matches latest release.` | Already current | "Already up to date" |
| `Already up to date. All synced files match the latest release.` | Local already matches the new release | "Already up to date" |
| `PR created successfully for v<VERSION>.` | Update applied + PR opened | "Upgrade PR created" |
| Anything starting with `ERROR:` | Failure | Surface the error to the user |

When a PR is created, the script also prints `ADDED:` / `UPDATED:` / `SKIP:` lines
file-by-file. Capture these for the result block.

## Important guardrails

- **Do not modify the script.** Bug fixes go upstream to the source repo.
- **Do not auto-merge the PR.** The script intentionally only opens it.
- **The script uses unauthenticated GitHub API** to fetch release metadata —
  no token needed for that part. The PR creation does need `gh` auth.
- The script syncs only files listed in `.agile-flow-version`'s
  `syncDirectories`. User-owned app code, configs, and product docs are never
  touched.

## Output Format

End with a Result Block:

```
---

**Result:** Upgrade PR created
From: v0.9.0
To: v1.0.0
Files updated: 14
PR: https://github.com/owner/repo/pull/42
Action: Review and merge the PR to finalize the upgrade
```

Or if no update was needed:

```
---

**Result:** Already up to date
Version: v0.9.0
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
