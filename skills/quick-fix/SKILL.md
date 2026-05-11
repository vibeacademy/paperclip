---
name: quick-fix
description: Apply a small bug fix, content update, or config tweak without full ticket ceremony. Skips ticket creation and board management but keeps branch / PR / test / account-verification discipline.
---

# Quick Fix

Make a quick, targeted change (bug fix, content update, config tweak) using a
lightweight workflow that skips ticket creation and board management.

## Ontology

Produces a **PullRequest** entity *without* a linked **Ticket** — this is the explicit exception to the usual Ticket → PullRequest chain. See [agile-ontology](../agile-ontology/SKILL.md). Enforces:

- **Invariant 8** — feature branch + PR is still required; you never commit to `main` even for a quick fix.
- **Invariant 9** — humans still merge. This skill stops at "PR opened."

Because there is no linked Ticket, do not move any project board items.

## Pre-Flight Verification (REQUIRED)

Before any work, verify the following. STOP and report if any check fails.

1. **GitHub account is correct** — Run `gh auth status` and confirm the active
   account matches the expected worker/bot account. If only a personal account
   is active, STOP and ask the user to switch.
2. **GitHub access works** — Try a `gh` call. If it fails, STOP.

## When to Use This Skill

- Bug fixes found during development (not from a ticket)
- Content or copy updates (data files, presets, text changes)
- Config tweaks (linter rules, CI fixes, dependency bumps)
- Any change the user explicitly requests without a ticket

**Do NOT use this for feature work.** If the change introduces new behavior,
touches more than 3 files, or takes longer than ~1 hour, create a ticket with
the `create-ticket` skill and use `work-ticket` instead.

## Workflow

1. **Confirm scope** — Describe the change to the user in 1-2 sentences. If
   the user hasn't specified what to fix, ask before proceeding.
2. **Create branch** — `fix/short-description` or `content/short-description`
3. **Implement** — Follow project standards, write clean code
4. **Test locally** — Run lint and tests. Do NOT push if any fail.
5. **Push and create PR** — Include "Quick fix — no linked ticket" in the PR
   description body
6. **Skip board updates** — Do NOT move any board items. There is no linked
   ticket. Do NOT guess which ticket this corresponds to.

## What This Skill Does NOT Skip

- Branch requirement (never commit to main)
- PR requirement (never merge without review)
- Test requirement (never push with failing tests)
- Account verification (still use worker bot account)
- Human merge (agent never merges)

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
