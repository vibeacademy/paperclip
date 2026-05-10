---
name: doctor
description: Read-only health check of the local dev environment and remote GitHub / Render / Supabase configuration. Surfaces issues that block workshop participants. Wraps scripts/doctor.sh plus remote checks.
---

# Doctor — Agile Flow Health Check

Run a comprehensive diagnostic of the local environment and remote
configuration. Surfaces every issue that could block a workshop participant.

## Instructions

1. Run the local diagnostic script and capture the full output:

   ```bash
   bash scripts/doctor.sh
   ```

2. Parse the machine-readable summary block between `=== DOCTOR_SUMMARY ===`
   and `=== END_SUMMARY ===`. Extract PASS, WARN, FAIL, and SKIP counts.

3. Perform these **remote checks** that the shell script cannot do:

   a. **Branch protection rulesets** — run:

      ```text
      gh api repos/{owner}/{repo}/rulesets
      ```

      - PASS if at least one ruleset exists targeting `main`
      - WARN if no rulesets found

   b. **Repository secrets** — run:

      ```text
      gh secret list
      ```

      Check for presence (not values) of:
      - `RENDER_API_KEY` — WARN if missing
      - `RENDER_SERVICE_ID` — WARN if missing
      - `SUPABASE_ACCESS_TOKEN` — WARN if missing
      - `SUPABASE_PROJECT_REF` — WARN if missing

   c. **GitHub Project board** — run:

      ```text
      gh project list --owner {owner} --format json
      ```

      - PASS if at least one project exists
      - WARN if no projects found

4. Format a **health report table** combining local + remote results:

   ```text
   ## Agile Flow Health Report

   ### Local Checks (from scripts/doctor.sh)
   PASS: {n}  WARN: {n}  FAIL: {n}  SKIP: {n}

   ### Remote Checks
   | Check | Status | Details |
   |-------|--------|---------|
   | Branch protection | PASS/WARN | ... |
   | Repo secrets | PASS/WARN | ... |
   | Project board | PASS/WARN | ... |

   ### Overall
   Ready for workshop: **YES** / **NO**
   ```

5. If there are any FAILs or WARNs, list **actionable fix instructions**
   for each one at the bottom of the report.

## Important

- This is a **read-only diagnostic**. Do not modify any files or settings.
- Run all checks inline.
- Derive `{owner}` and `{repo}` from `git remote get-url origin`.
- **Non-admin users**: `gh api rulesets` and `gh secret list` may return
  404 or 403 for users without admin access. Map these responses to
  WARN or SKIP rather than FAIL — the checks are informational and do
  not indicate a broken setup.

### Output Format

End your output with a Result Block:

```
---

**Result:** Health check complete
Local: 8 pass, 1 warn, 0 fail
Remote: 3 pass, 1 warn, 0 fail
Ready for workshop: YES
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
<!-- Note: This skill assumes scripts/doctor.sh is present in the agent's working directory. If it isn't, vendor it alongside this skill or adjust the path. -->
