---
name: diagnose-cloudrun
description: Print a structured diagnostic snapshot of a Cloud Run service — service URL, latest-ready vs latest-created revision, traffic split, currently-serving image (with placeholder-image trap detection), revision conditions, and last 50 log lines. Read-only. (GCP only.)
---

# Diagnose Cloud Run

Print a one-shot, structured diagnostic of a Cloud Run service so a facilitator can paste the output into a help channel and someone else can diagnose the issue without back-and-forth.

The output is deliberately formatted for **paste-and-share triage** — five sections, in a fixed order, each with a clear `=== ... ===` header.

## When to use

- A workshop participant reports their Cloud Run service is broken or stuck
- A facilitator needs a fast snapshot before deeper investigation
- Pre-flight check before declaring a service unhealthy

## What it prints (in order)

1. **Service summary** — namespace, region, URL, latest-ready vs latest-created revision (warns if they differ)
2. **Traffic split** — which revisions get how much traffic, flags STALE entries
3. **Currently-serving image** — the image in the template spec, with a "PLACEHOLDER" warning if it's still pointing at `cloudrun/container/hello`
4. **Latest revision conditions** — Ready / Active / ContainerHealthy with messages
5. **Last 50 log lines** — newest-first from CloudWatch, reversed for chronological reading

## Pre-flight checks

The script enforces these — agent should be aware:

1. **`gcloud` CLI must be on PATH.** If missing, the script exits 2 and tells the user how to install.
2. **`python3` must be on PATH** (for JSON parsing).
3. **Project ID required.** The script does NOT fall back to `gcloud config get project` (that would risk diagnosing the wrong project). Provide via `--project=<id>` or `GCP_PROJECT_ID` env var.
4. **Authenticated `gcloud`.** The script doesn't check this explicitly, but service describe will fail without auth.

## Invoke the script

The script is bundled alongside this SKILL.md at `scripts/diagnose-cloudrun.sh`.

```bash
# Using env vars (most common in workshop participant repos):
GCP_PROJECT_ID=af-tck517 bash scripts/diagnose-cloudrun.sh

# Or with explicit flags:
bash scripts/diagnose-cloudrun.sh \
  --project=af-tck517 \
  --service=tck517-app \
  --region=us-central1
```

Optional env vars:
- `GCP_PROJECT_ID` — required (unless `--project=` passed)
- `GCP_REGION` — defaults to `us-central1`
- `CLOUD_RUN_SERVICE` — defaults to `agile-flow-app`

## How to interpret the output

The script is **read-only** — it never changes any GCP state. Common patterns:

| What you see | What it usually means | Recommended next step |
|---|---|---|
| `WARN latest-created differs from latest-ready` | A new revision was deployed but isn't healthy yet | Check the conditions section for the revision name |
| `<- STALE (latest-ready is ...)` in traffic split | 100% traffic is going to an old revision while a newer one is ready | `gcloud run services update-traffic SERVICE --to-latest` |
| `<- PLACEHOLDER (Step 5.8 pre-create not yet replaced)` | Service still pointing at the GCP hello-world placeholder image | Build & deploy the actual app image |
| `Ready: False` in conditions with a message | Container failed to start | Check the message and the log lines section |
| Empty log lines section | No logs in retention window, or log query failed | Check service exists and project is correct |

## Important guardrails

- **Read-only.** Never modifies project state. Safe to run any time.
- **Do not modify the script.** Its output format is contract for paste-and-share triage.
- **Refuses to fall back to default project.** This is intentional — silently diagnosing the wrong project caused real incidents (see the script header for context).
- **Cleans up its temp files** via `trap` on EXIT.

## Output Format

The script's stdout IS the deliverable — paste it as-is into the report. Then add a Result Block:

```
---

**Result:** Cloud Run diagnostic captured
Service: tck517-app
Project: af-tck517 (us-central1)
Latest ready: tck517-app-00043-xyz
Status: degraded — traffic stale on old revision
Recommendation: gcloud run services update-traffic ... --to-latest
```

<!-- Source: Agile Flow GCP (https://github.com/vibeacademy/agile-flow-gcp) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
