---
name: upgrade
description: Sync framework files from the latest upstream Agile Flow release. Wraps scripts/template-sync.sh — creates a PR for human review. Use when forked repos need to pull in new framework versions.
---

# Upgrade — Agile Flow Framework

Check for a newer version of Agile Flow and sync framework files from the
latest upstream release. User content is never modified.

## Instructions

1. **Verify clean working tree** — run `git status --porcelain`. If there are
   uncommitted changes, STOP and report:

   ```
   Your working tree has uncommitted changes. Please commit or stash them
   before upgrading:
     git stash
   ```

2. **Verify GitHub CLI authentication** — run `gh auth status`. If not
   authenticated, STOP and report:

   ```
   GitHub CLI is not authenticated. Run:
     gh auth login
   ```

3. **Run the sync script**:

   ```bash
   bash scripts/template-sync.sh
   ```

4. **Parse the output** and report what happened. The script will print one of:

   - **Already up to date** — no action needed.
   - **Update available: X -> Y** followed by file-level ADDED/UPDATED/SKIP
     lines and a PR URL.
   - **ERROR** — report the error message to the user.

5. **If a PR was created**, remind the user:

   ```
   A sync PR has been created. Review the changes, then merge when ready:
     gh pr view <PR_NUMBER> --web
   ```

## Important

- This skill calls `scripts/template-sync.sh` as-is. Do not modify the script.
- The sync only updates framework-controlled files. User content (app code,
  config customizations, product docs) is never touched. See
  `docs/DISTRIBUTION.md` for the full classification.
- The created PR requires human review and merge. Do not auto-merge.
- For details on what gets synced and troubleshooting, see `docs/UPGRADING.md`.

### Output Format

End your output with a Result Block:

```
---

**Result:** Upgrade complete
From: v0.9.0
To: v1.0.0
PR: #42 — chore(sync): update Agile Flow framework to v1.0.0
Action: Review and merge the PR to finalize the upgrade
```

Or if already up to date:

```
---

**Result:** Already up to date
Version: v0.9.0
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
<!-- Note: This skill assumes scripts/template-sync.sh is present in the agent's working directory. The Tier 2 `template-sync` skill bundles the script alongside SKILL.md if you prefer that approach. -->
