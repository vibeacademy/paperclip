---
name: log-session
description: Write a session journal at end of a development session — tickets delivered, challenges and mitigations, insights, metrics, and what's next. Produces a SessionJournal and promotes classified insights to LessonLearned or PatternDiscovered entities. Saved to reports/session-journals/.
---

# Log Session

Write a session journal for the current development session and save it to `reports/session-journals/YYYY-MM-DD.md`.

If a journal already exists for today's date, append a session number suffix (e.g., `2026-02-15-2.md`).

## Ontology

Produces a **SessionJournal** entity. Optionally promotes classified Insights to **LessonLearned** and **PatternDiscovered** entities — this is the team's feedback loop, turning per-session experience into reusable knowledge. See [agile-ontology](../agile-ontology/SKILL.md). Enforces:

- **Invariant 10** — every session that ships work (merges PRs, marks Tickets `done`, opens PullRequests) must produce a SessionJournal before ending.
- **Invariant 11** — every entry in `insights[]` must have `kind` set to `lesson`, `pattern`, or `operational`. Classify or drop.
- **Invariant 12** — dedup against existing LessonLearned / PatternDiscovered (by `domain` + `short_name`) before creating a new one.

## What to Capture

### 1. Session Summary (2-3 sentences)
High-level narrative of what the session accomplished and its strategic significance.

### 2. Tickets Delivered
For each ticket completed (merged to main) during this session:

| Field | Description |
|-------|-------------|
| Ticket # and title | Issue number and short description |
| PR # | Pull request number |
| What changed | 1-2 sentence summary of the implementation |
| Files touched | Key files modified (not exhaustive) |
| Tests added | Count and nature of new tests |

### 3. Tickets In Review
Same format as above, but for PRs that are created and reviewed but not yet merged.

### 4. Challenges and Mitigations
Document every significant obstacle encountered and how it was resolved:

| Field | Description |
|-------|-------------|
| Challenge | What went wrong or blocked progress |
| Root cause | Why it happened |
| Mitigation | How it was resolved |
| Prevention | What would prevent this in the future (if applicable) |

Examples: merge conflicts, CI failures, migration errors, test failures, architectural decisions that needed revision.

### 5. Insights and Learnings
Capture knowledge that will help in future sessions. **Every Insight must be classified** with a `kind` (Invariant 11):

- **`lesson`** — a gotcha worth remembering. Something that went wrong, plus the root cause, workaround, and prevention. Candidate for promotion to a LessonLearned entity.
- **`pattern`** — a reusable approach. Something that worked well and could apply to future similar problems. Candidate for promotion to a PatternDiscovered entity.
- **`operational`** — a one-off observation about this session that has no broader relevance. Stays in the journal; no promotion.

These should be concrete and actionable, not generic observations. "We should be more careful" is not an Insight. "Magic-link auth needs both `/api/auth/callback` (server) and `/(auth)/auth/callback` (client) handlers" is.

### 6. Tickets Created
New tickets created during the session with brief context on why they were created.

### 7. Metrics
Quick quantitative summary:
- PRs created / merged / reviewed
- Tickets completed / created
- Tests added
- Board state changes

### 8. Next Up
Prioritized list of what should be tackled next, with context on dependencies and blockers.

## Format

Use the template structure from existing journals in `reports/session-journals/`. Keep the tone factual and concise — this is a working document for project continuity, not a blog post.

## After Writing

1. Read the journal back to verify completeness
2. Cross-reference against the git log and board state to catch anything missed

## Promote insights to knowledge entities

This step closes the feedback loop. Each Insight in the journal is a candidate for a LessonLearned or PatternDiscovered entity.

For each `insights[]` entry:

1. **Classify** — every Insight already has `kind` set to one of `lesson`, `pattern`, or `operational`. If you find an entry without a kind, classify it now (Invariant 11). If you can't classify it, it isn't worth keeping.

2. **Skip `operational`** — those stay only in the journal. No promotion.

3. **Dedup check (Invariant 12)** — for `lesson` and `pattern` entries:
   - Search `docs/knowledge/lessons/` (for lessons) or `docs/knowledge/patterns/` (for patterns) for existing entries matching the `domain` + `short_name` you'd assign.
   - If a matching `active` entry exists:
     - **Reference it** — set the Insight's `promoted_to` to the existing entity's ID and move on.
     - Or **supersede it** — if your new understanding replaces the old, create a new entity with `superseded_by` pointing at the old one, and mark the old one's `status` as `superseded`.
   - If no match: proceed to create a new entity.

4. **Create the entity** — write a new file with frontmatter matching the Zod schema in [agile-ontology](../agile-ontology/SKILL.md):
   - `docs/knowledge/lessons/lesson-{domain}-{short-name}.md` (for LessonLearned)
   - `docs/knowledge/patterns/pattern-{domain}-{short-name}.md` (for PatternDiscovered)

   Each file's frontmatter mirrors the schema; the body explains the lesson or pattern in prose.

5. **Update the SessionJournal** — set the Insight's `promoted_to` field to the new entity's ID so the journal records the lineage.

6. **Present proposed entities to the user** — show a preview (name, summary, kind, status). Create only those the user confirms.

If no Insights warrant promotion (all `operational`), report:

```
-> No new knowledge entities proposed — all insights were operational
```

## Final summary

Present a brief summary including the journal path, entity counts (promoted lessons / patterns / operational-only), and any validation issues flagged.

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->
